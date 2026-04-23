# Verify batch prompt

You receive:
1. The loaded target-language playbook.
2. An array of drafted rows that need verification: `{wordKey, word, tier, row: {...after patch...}}`.

Your job is a strict quality gate on `example` / `exampleTr`. For each row answer:

1. Does `exampleTr` naturally and accurately translate `example` into the target language?
2. Does `example` illustrate the primary sense of `word` (the sense in `translation`)?
3. Is `example` grammatical in the source language, 6–14 tokens, one finite clause, no jargon?
4. Is `exampleTr` grammatical learner-register target language, 6–14 tokens?
5. If the word is A1–A2, is the tone light/memorable without sacrificing grammar or sense?
6. If the word is technical/formal, is the tone appropriately neutral?

Output contract (strict — JSON array only, no prose, no code fences):

```json
[
  {
    "wordKey": "a",
    "verdict": "accept" | "revise" | "reject",
    "reason": "short string, only if not accept",
    "revisedPatch": {
      "example": "...",
      "exampleTr": "..."
    }
  }
]
```

Rules:

- `accept` — ship as-is.
- `revise` — propose a better example pair in `revisedPatch`; must still match the primary sense.
- `reject` — the row cannot be auto-fixed; route to flagged queue.
- Never touch any field other than `example` / `exampleTr` from here.
- Translation humor must carry across: if the English example is a joke, the target-language example must be a natural joke too, not a literal translation that lands dead.
