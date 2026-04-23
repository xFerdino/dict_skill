---
name: assign-cefr
description: Assign a CEFR level (A1–C2) to every entry that still has cefr=null after drafting. Runs once per build after the draft loop, before verify-example. Invoked by build-dictionary.
---

# assign-cefr

Fills `cefr` for every row where it is `null` or missing. Uses corpus frequency, morphological complexity, and register to estimate level.

## Inputs

- `data/<runId>/drafts.jsonl` (and `data/<runId>/auto_easy.jsonl` for easy-tier rows).
- Target language (for language-specific CEFR anchors).

## Cached prefix (same pattern as draft-entry)

```
SYSTEM (cached):
  <prompts/system.md>
  <skills/playbooks/<targetLang>/SKILL.md>
  <prompts/assign-cefr.md>
  // cache_control: {type: "ephemeral"} marker here

USER (per batch):
  ROWS:
  [{"wordKey":"...","word":"...","translation":"...","pos":"...","tags":[...]}, ...]
```

Send only the fields needed: `wordKey, word, translation, pos` (from primary meaning), `tags`. No example text — irrelevant for CEFR.

Batch size: **40 rows** (lighter payload than draft batches, so larger batches are safe).

## Output contract

JSON array, one entry per input row:

```json
[
  {"wordKey": "cat", "cefr": "A1"},
  {"wordKey": "melancholy", "cefr": "C1"}
]
```

No other fields. No prose. No code fences.

## CEFR anchor guidelines (baked into prompts/assign-cefr.md)

| Level | Vocabulary profile |
|---|---|
| A1 | Highest-frequency ~500 words: basic nouns (house, water, food), core verbs (be, have, go, eat), numbers, colors, pronouns, articles, basic prepositions |
| A2 | Next ~1000 words: common daily nouns, simple adjectives, basic connectors, simple tenses |
| B1 | General vocabulary a competent adult uses: abstract nouns starting to appear, modal verbs, most common phrasal verbs |
| B2 | Less frequent vocabulary, nuanced adjectives, domain-adjacent terms, compound structures |
| C1 | Low-frequency, register-specific, formal or literary vocabulary |
| C2 | Rare, archaic, highly technical, or idiom-dependent |

Closed-class items (articles, pronouns, prepositions, conjunctions from the playbook overrides table) are always **A1**.

## Procedure

1. Read `auto_easy.jsonl` and `drafts.jsonl`. Collect every row where `cefr` is `null`, `""`, or missing.
2. If none, skip entirely — print `assign-cefr: all rows already have CEFR` and return.
3. Batch 40 rows at a time. Call model with cached prefix + batch payload.
4. Parse response. For each returned `{wordKey, cefr}`, update the matching row in `drafts.jsonl` / `auto_easy.jsonl` in memory.
5. After all batches, re-write both files with the updated `cefr` fields.
6. Validate: if any row still has `cefr=null` after all batches (parse failure, missing wordKey), assign `cefr: "B1"` as a safe fallback and mark `_cefrFallback: true`.
7. Print summary: `assign-cefr: filled=<N> already-set=<M> fallback=<K>`.

## Don'ts

- Do not change any field other than `cefr`.
- Do not re-run on rows that already have a CEFR value.
- Do not guess wildly — when uncertain between two adjacent levels, pick the lower one (better to under-level than over-level for a learner).
