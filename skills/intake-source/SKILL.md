---
name: intake-source
description: Deterministic first stage — normalize, dedupe, and tier rows by quality so the LLM only sees rows that need it. Invoked by build-dictionary.
---

# intake-source

Purely deterministic. **Do not call the LLM.** Use Read, Write, and small Bash filters.

## Inputs

- `data/<runId>/source.jsonl` — input rows (one JSON object per line).
- Target language (for locale-aware expectations).

## Steps

1. **Read** every line of `source.jsonl`. Skip blank lines and non-JSON lines.
2. **Normalize** each row in memory:
   - Trim all string fields.
   - Repair mojibake: if a field contains sequences like `Ã³`, `Å‚`, `â€™`, `Ä™` — run a UTF-8↔Latin-1 roundtrip fix. Replace `â€™ → ’`, `â€œ → "`, `â€ → "`, `â€“ → –`, `â€" → —`. For Polish/Czech roundtrip: try `buf = Buffer.from(s, 'latin1').toString('utf8')` mentally; if the result has valid Polish letters (`ąćęłńóśźż`) and the original had `Ã` sequences, prefer the repaired form.
   - Lowercase `wordKey`; strip accents only for the key (keep `word` intact).
   - Drop rows where `word` is empty or `translation` is empty.
3. **Dedupe** by `wordKey` — keep the first row encountered.
4. **Tier** each row into `easy | medium | hard` using these deterministic checks. Add a `tier` field on the row.

### Tiering rules

Count example tokens as whitespace-split units.

- **hard** (any of these triggers):
  - Mojibake still present after repair (`Ã`, `â€`, `Å`).
  - `exampleTr` contains ALL-CAPS standalone pronoun in the middle (`JA`, `TY`, `ON`, `ONA`, `MY`, `WY`, `ONI`) — signature of machine-translated word-salad.
  - `exampleTr` contains an English stem + target-language suffix combo or obvious literal word-by-word translation (heuristic: Polish example `exampleTr` containing English content words like `okładzina`, `pewien dawno` adjacent, or any token also present verbatim in English `example`).
  - token count of `example` < 4 or > 20.
  - length ratio `|exampleTr.tokens| / |example.tokens|` outside `[0.5, 2.5]`.
  - `translation` contains `,` with more than 4 comma-separated glosses (noisy multi-sense dump).
  - Closed-class word (see playbook's overrides table) whose current `translation` does NOT match the locked gloss.
- **medium** (any of):
  - Example 4–20 tokens but length ratio in `[0.5, 0.7]` or `[1.5, 2.5]`.
  - `phonetic` empty when the target language expects one (Mandarin, Japanese, Korean, Russian).
  - `meanings` empty or malformed JSON.
  - `tags` empty.
- **easy**: everything else.

5. **Write** all rows to `data/<runId>/staged.jsonl`, preserving input order. One JSON object per line. Include the new `tier` field.

6. **Print** a one-line summary: `intake: total=<N> easy=<E> medium=<M> hard=<H>`.

## Anti-patterns

- Do not call the LLM for any reason.
- Do not rewrite examples here — only tier them.
- Do not drop `hard` rows; they go to the LLM for repair.
