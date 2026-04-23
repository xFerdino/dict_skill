# Draft batch prompt

You receive:
1. The loaded target-language playbook (closed-class overrides, example-quality rule, known garbage patterns).
2. An array of up to 20 staged rows: `{wordKey, word, tier, row: {...existing entry fields...}}`.

For each row, decide the minimal patch needed to make the entry high quality. **Return a diff only, never a full row.**

Output contract (strict):

```json
[
  {
    "wordKey": "a",
    "confidence": "high" | "medium" | "low",
    "touchedExample": true | false,
    "patch": {
      "translation": "optional — only if clearly wrong",
      "example": "optional — only if example was nonsense or missing",
      "exampleTr": "optional — paired with example",
      "meanings": "optional — stringified JSON array of {pos, translation, example, exampleTr}",
      "phonetic": "optional",
      "article": "optional, nouns only",
      "gender": "optional, nouns only",
      "cefr": "include if you are confident (A1..C2); assign-cefr will fill any nulls in a dedicated pass",
      "tags": ["optional"]
    }
  }
]
```

Rules:

- Skip the LLM reasoning; emit only the JSON array, no prose, no code fences.
- If a field is already correct, omit it from the patch.
- If the source example is nonsense (mojibake, ALL-CAPS pronoun mid-sentence, token-ratio far off, literal word-for-word garbage like `"JA był okładzina ten problem z pewien dawno"`), replace both `example` and `exampleTr` with a new playbook-compliant pair.
- Closed-class words from the playbook table use the locked gloss — never drift.
- For basic / A1–A2 words, lean into the humor guidance from the system prompt. For technical words, stay neutral.
- Set `touchedExample: true` whenever you change `example` or `exampleTr`. The orchestrator uses this to decide whether to run verify-example.
- Never output more than 20 patches. Never output fields outside the schema.
