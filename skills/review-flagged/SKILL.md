---
name: review-flagged
description: Second-chance pass over rows the verifier rejected — stricter prompt, smaller batches, optional user confirmation. Invoked by build-dictionary when flagged.jsonl is non-empty.
---

# review-flagged

## Inputs

- `data/<runId>/flagged.jsonl` — rows with `{row, reason}`.
- Loaded playbook.

## Procedure

1. Read all flagged rows.
2. Batch into groups of 10 (smaller than draft batches — these are hard cases).
3. For each batch call the model with a stricter system message:

   ```
   You are on the second-chance pass. The previous verifier rejected these rows
   for the given reason. Propose a conservative fix that addresses the reason
   exactly — or decline. Prefer declining over guessing.

   For each row return:
   {wordKey, verdict: "fixed" | "give_up", revisedPatch?: {...}}
   ```

4. Apply results:
   - `fixed` → merge `revisedPatch` into the row, append to `verified.jsonl`.
   - `give_up` → append to `data/<runId>/unresolved.jsonl` with the original reason.

5. If `unresolved.jsonl` is non-empty, print a summary table (word | reason) so the user can hand-edit or skip.

## Budget

- Cap this skill at 100 rows per run to avoid runaway token spend. Remaining flagged rows stay in `flagged.jsonl` for a future run.
