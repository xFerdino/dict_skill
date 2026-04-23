---
name: verify-example
description: LLM pass 2 — verify example/exampleTr alignment, grammar, and sense-fit. Runs only on rows where the draft touched the example, confidence<high, or tier=hard. Invoked by build-dictionary.
---

# verify-example

## Inputs

- Batch of ≤20 drafted rows needing verification.
- Loaded playbook for the target language.
- `prompts/verify-batch.md` (already loaded by orchestrator).

## Trigger rule (enforced by build-dictionary)

Run only when:
- `_touchedExample == true`, or
- `_confidence != "high"`, or
- `tier == "hard"`.

## Procedure

1. Build the request with the same cache-prefix pattern as `draft-entry`, swapping in the verify task prompt:
   ```
   SYSTEM (cached, byte-identical across all verify batches in the run):
     <prompts/system.md>
     <skills/playbooks/<targetLang>/SKILL.md>
     <prompts/verify-batch.md>
     // cache_control: {type: "ephemeral"} marker here

   USER (per-batch):
     ROWS:
     [{"wordKey":"...","word":"...","tier":"...","row":{...after draft patch...}}, ...]
   ```
   The verify prefix is a different cache entry than the draft prefix (different task prompt), so each stays warm independently.
2. Call the model with `temperature=0`, JSON object response.
3. Expected output: JSON array of `{wordKey, verdict, reason?, revisedPatch?}`.
4. Apply results:
   - `accept` → append row unchanged to `data/<runId>/verified.jsonl`.
   - `revise` → merge `revisedPatch` into the row, append to `verified.jsonl`, mark `_revised: true`.
   - `reject` → append row (with `reason`) to `data/<runId>/flagged.jsonl`.
5. Strip transient `_*` fields before writing unless routing to `flagged.jsonl`.

## Quality bar

The verifier rejects any example where:
- `exampleTr` is a literal word-for-word translation that doesn't sound natural in the target language.
- The example doesn't illustrate the primary sense in `translation`.
- Token count is outside 6–14 (allow 4–16 for CJK where sentence length differs).
- A joke (if present) forces ungrammaticality or changes the meaning.

If rejecting, provide a short `reason` so `review-flagged` knows what to fix.
