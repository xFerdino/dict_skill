# dict_skill

Codex plugin that builds high-quality learner dictionaries (voca.cards / Convex schema) from noisy JSONL sources. Skills-only — Codex itself orchestrates intake → draft → verify → export, using a two-pass quality gate so nonsense examples are caught and rewritten before they ever ship.

## Why

Imports like `convex_import_english_polish.ts` contain entries like:

```
"word": "a",
"translation": "pewien",
"example": "I was facing this problem from a long time.",
"exampleTr": "JA był okładzina ten problem z pewien dawno."
```

That's gibberish. This plugin fixes it.

## Supported target languages

Polish, English, Spanish, Portuguese, Italian, Russian, Mandarin, Japanese, French, German, Korean. Each has a dedicated playbook with closed-class overrides, morphology rules, example-quality constraints, and known garbage patterns.

## Layout

```
dict_skill/
  .codex-plugin/plugin.json
  .agents/plugins/marketplace.json
  skills/
    build-dictionary/      # orchestrator
    intake-source/         # deterministic: normalize, dedupe, tier
    draft-entry/           # LLM pass 1: minimal diffs
    verify-example/        # LLM pass 2: quality gate
    export-convex-ts/      # deterministic: JSONL → TS
    review-flagged/        # second-chance pass
    playbooks/<lang>/      # one reference skill per language
  prompts/                 # shared system, draft-batch, verify-batch
  schemas/                 # JSON schema + TS shape doc
  data/<runId>/            # per-run staged, drafts, verified, flagged, out.ts
```

## Install (local marketplace)

1. Open Codex.
2. Point Codex at the local marketplace: `C:\Users\Luk\Saved Games\dict_skill\.agents\plugins\marketplace.json` (either add this to the repo-scoped `$REPO_ROOT/.agents/plugins/marketplace.json` or link from `~/.agents/plugins/marketplace.json`).
3. Restart Codex. Open the plugin directory, pick the *Dict Skill (local)* marketplace, install `dict_skill`.

## Use

```
/build-dictionary source=D:\Downloads\convex_import_english_polish.ts \
                  runId=smoke01 \
                  targetLang=polish \
                  sourceLang=english
```

Outputs land in `data/smoke01/`:

- `staged.jsonl` — deterministically tiered
- `drafts.jsonl` — after LLM pass 1 (diffs merged)
- `verified.jsonl` — after LLM pass 2
- `flagged.jsonl` / `unresolved.jsonl` — anything that didn't pass
- `out.ts` — Convex import ready to paste into your app
- `report.md` — counts + before/after highlights

## Two-pass flow

```
intake (no LLM, tiers rows)
  easy  ─────────────────────────→ verified.jsonl
  medium/hard → draft-entry (LLM 1, diffs only, batch 20)
                  │
                  ├── touchedExample or conf<high or tier=hard
                  │         → verify-example (LLM 2)
                  │               ├─ accept  → verified.jsonl
                  │               ├─ revise  → verified.jsonl
                  │               └─ reject  → flagged.jsonl → review-flagged
                  └── else → verified.jsonl
export-convex-ts → out.ts
```

## Token-smart rules

- **Tier before LLM.** Easy rows never hit a model.
- **Diff patches, not full rows.** Saves ~70% tokens per draft call.
- **Prompt caching.** Every draft call shares a byte-identical prefix (system + playbook + draft task). Verify calls share their own prefix. The provider caches it (Anthropic: explicit `cache_control: {type: "ephemeral"}` marker, 5-min TTL; OpenAI/Gemini: automatic on identical ≥1024-token prefixes). Cache reads bill at ~10% of normal input. Run batches back-to-back to keep cache warm.
- **Verify is conditional.** Only runs on rows that actually need it.
- **Batch size 20.** Strict JSON-array output contract.

### Estimated cost for 5,000 rows (noisy source)

Without caching: ~1.6–2.0M tokens. With caching warm across the run: **~800K–1.1M effective tokens** (prefix billed once fully, then ~10% on ~260 subsequent batches).

| Model | Uncached | With cache |
|---|---|---|
| Haiku 4.5 | ~$2–4 | ~$1–2 |
| Sonnet 4.6 | ~$5–10 | ~$3–5 |
| Opus 4.7 | ~$25–50 | ~$12–20 |

## Voice

For basic / A1–A2 function words the examples crack a light joke or lean mildly sarcastic — memorable beats corporate. For technical, formal, or advanced vocabulary, tone stays neutral. Humor never overrides grammar or sense-alignment; see `prompts/system.md`.

## Smoke test

1. Run the command above on `convex_import_english_polish.ts` with `targetLang=polish`.
2. Inspect `data/smoke01/report.md`. The `a` row should appear in the before/after highlights:
   - **Before:** `example: "I was facing this problem from a long time."` / `exampleTr: "JA był okładzina ten problem z pewien dawno."`
   - **After:** something like `example: "I own a cat. The cat owns me."` / `exampleTr: "Mam kota. To kot ma mnie."`
3. `a.translation` should be `"a"` (per closed-class override), not `"pewien"`.
4. `out.ts` should parse and match the exact shape of the original Convex import.
5. Repeat with `targetLang=spanish / japanese / german / …` against matching source slices to confirm every playbook loads cleanly.

## Credits

Spirit (not code) lifted from `C:\Users\Luk\Saved Games\Agent_Dic` — conservative cleanup philosophy, two-step reviewer idea, mojibake-repair approach, playbook structure.
