---
name: draft-entry
description: LLM pass 1 — draft minimal diff patches for a batch of up to 20 staged rows. Uses the loaded target-language playbook and prompts/draft-batch.md. Invoked by build-dictionary.
---

# draft-entry

## Inputs

- One batch of ≤20 rows from `staged.jsonl` where `tier ∈ {medium, hard}`.
- Already-loaded playbook (do not re-Read it).
- `prompts/draft-batch.md` (already loaded by orchestrator).

## Procedure

1. Build the request with an explicit **cache boundary**:

   ```
   SYSTEM (cached):
     <contents of prompts/system.md>
     <contents of skills/playbooks/<targetLang>/SKILL.md>
     <contents of prompts/draft-batch.md>
     // <-- cache_control: {type: "ephemeral"} marker on this final block

   USER (not cached, varies per batch):
     ROWS:
     [
       {"wordKey": "...", "word": "...", "tier": "hard", "row": { ...entry fields... }},
       ...
     ]
   ```

   Keep every byte above the marker **identical** across all draft batches in the run. This is what makes prompt caching actually work — the provider hashes the prefix and reuses it.

2. Call the model with `temperature=0` and `response_format: json_object` (or the provider equivalent). Expect a **JSON array of patch objects**. Run batches back-to-back (no long sleeps) to stay inside the 5-minute cache TTL.

3. Parse strictly. If parsing fails once, retry with the exact system message `"Return JSON array only, no prose, no code fences."` On a second failure, mark the whole batch's rows with `verdict=reject, reason=malformed` and append to `flagged.jsonl`; do not continue the batch.

4. For each parsed patch:
   - Merge `patch` fields into the original row (non-patched fields unchanged).
   - If `patch.meanings` present, validate it parses as JSON and each item has `{pos, translation, example, exampleTr}`.
   - If `touchedExample` is true, set a transient marker `_touchedExample: true` on the drafted row.
   - Carry `confidence` forward as `_confidence`.
5. Append every drafted row to `data/<runId>/drafts.jsonl`.

## Humor handling

- The system prompt + playbook already specify when humor is allowed. Do not add extra voice instructions here.
- If a row is marked `cefr: A1` or `A2` or the `word` appears in the playbook's "basic humor list", the model should lean playful. For anything tagged `technical`/`formal` or `cefr ≥ C1`, the model stays neutral.

## Don'ts

- Never request the model to return full rows — patches only.
- Never exceed 20 rows per batch.
- Never call this skill with `easy`-tier rows; those bypass the LLM entirely.
