---
name: build-dictionary
description: Orchestrate end-to-end dictionary build from a noisy source JSONL to a verified JSONL + Convex TS export. Use when the user asks to build, rebuild, improve, or fix a learner dictionary.
---

# build-dictionary

You are the top-level orchestrator for turning a noisy source dictionary into a high-quality learner dictionary.

## Inputs

Parse from the user message (ask only if missing):

- `source` — path to input file. Accepts JSONL **or** a TypeScript import file shaped like `D:\Downloads\convex_import_english_polish.ts` (then extract the array literal to JSONL first via Read + Write into `data/<run-id>/source.jsonl`).
- `runId` — short slug, default `build-<yyyymmdd-hhmm>`.
- `targetLang` — one of: `polish, english, spanish, portuguese, italian, russian, mandarin, japanese, french, german, korean`.
- `sourceLang` — optional, same vocabulary. Used for source-side mojibake/grammar sanity checks.
- `maxRows` — optional cap for smoke tests.

## Pipeline (execute in order)

1. **Setup.** Create `data/<runId>/` via Bash `mkdir -p`. Copy/extract source into `data/<runId>/source.jsonl`.
2. **Load context.** Read into your context, once:
   - `prompts/system.md`
   - `skills/playbooks/<targetLang>/SKILL.md`
   - If `sourceLang` provided: `skills/playbooks/<sourceLang>/SKILL.md`
3. **Intake.** Invoke the `intake-source` skill. It writes `data/<runId>/staged.jsonl` with a `tier` field per row (`easy|medium|hard`).
4. **Draft loop.** Read `staged.jsonl` in order. Partition:
   - `easy` rows → copy straight into `data/<runId>/auto_easy.jsonl` (no LLM).
   - `medium` + `hard` → chunk into batches of 20; for each batch invoke `draft-entry` with the playbook + `prompts/draft-batch.md`. Append patches to `data/<runId>/drafts.jsonl`.
5. **Verify gate.** For every drafted row where `touchedExample == true` OR `confidence != "high"` OR `tier == "hard"`, invoke `verify-example` in batches of 20. Append to `data/<runId>/verified.jsonl` (verdict `accept` or `revise` with revisedPatch applied) and `data/<runId>/flagged.jsonl` (verdict `reject`). Rows that skipped verification are merged into `verified.jsonl` as-is.
6. **Review flagged.** If `flagged.jsonl` is non-empty, invoke `review-flagged`. Accepted rows move to `verified.jsonl`.
7. **Export.** Invoke `export-convex-ts`. It writes `data/<runId>/out.ts`.
8. **Report.** Write `data/<runId>/report.md` with: total rows, easy-skipped, drafted, verified-accept, verified-revise, flagged, plus 5 sample before/after pairs (favor the worst inputs — mojibake, nonsense examples).

## Two-pass flow (pseudocode)

```
rows = intake(source)
for batch in chunks(medium+hard rows, 20):
  drafts = draft_entry(batch, playbook)
  to_verify = [d for d in drafts
               if d.touchedExample
               or d.confidence != "high"
               or row.tier == "hard"]
  verify_example(to_verify) -> accepts | revises | rejects
merge auto_easy + accepts + revises -> verified.jsonl
flagged.jsonl -> review_flagged -> merge accepted back
export_convex_ts(verified.jsonl) -> out.ts
```

## Token-smart rules

- **Prompt caching is mandatory.** Every LLM call (draft and verify) sends the **same cached prefix**: `prompts/system.md` + target-language playbook + (for draft) `prompts/draft-batch.md` / (for verify) `prompts/verify-batch.md`. The variable batch payload goes *after* the cache boundary.
  - Anthropic API: put a `cache_control: {type: "ephemeral"}` marker on the last block of the prefix (playbook/task-prompt). The 5-minute TTL means back-to-back batches hit cache. Run draft batches sequentially without long pauses to keep the cache warm.
  - OpenAI / Gemini / Codex-hosted models: caching is automatic on identical prefixes ≥1024 tokens — keep the prefix byte-identical.
  - Cached-read tokens are billed at ~10% of normal input, so a warm run over ~2k-token prefix × 250 batches saves hundreds of thousands of tokens.
- Keep the prefix **byte-identical** across all calls in one run. Never inject per-batch variables into the cached portion; put them in the user message after the cache boundary.
- Easy-tier rows never hit an LLM at all.
- Batch size 20. Never send full rows when a diff suffices.
- Verify-pass is conditional, never universal.
- If a batch LLM call returns malformed JSON, retry once with `"Return JSON array only, no prose."`; after a second failure, mark the whole batch as flagged.

## Output summary to user

After the run, print:
- Path to `out.ts`.
- Counts by tier and verdict.
- 3 before/after highlights (pick the most dramatic fixes).
