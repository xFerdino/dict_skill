# Convex TS import shape

`export-convex-ts` writes a file of this form (mirrors `D:\Downloads\convex_import_english_polish.ts`):

```ts
// Generated from <source jsonl>
export const importedDeck = [
  {
    "word": "a",
    "wordKey": "a",
    "phonetic": "",
    "translation": "a",
    "example": "I own a cat. The cat owns me.",
    "exampleTr": "Mam kota. Kot ma mnie.",
    "lang": "Polish",
    "tags": ["general"],
    "masteryScore": 0,
    "isNew": true,
    "cefr": "A1",
    "mnemonic": null,
    "sourceLanguage": "English",
    "meanings": "[{\"pos\":\"conjunction\",\"translation\":\"a\",\"example\":\"I own a cat. The cat owns me.\",\"exampleTr\":\"Mam kota. Kot ma mnie.\"}]",
    "createdBy": "import"
  }
];
```

Rules for the exporter:

- Emit valid TypeScript (string fields JSON-escaped via `JSON.stringify`).
- `meanings` is a **stringified** JSON array — double-escape.
- `masteryScore` is always `0`, `isNew` always `true`, `createdBy` always `"import"`.
- Omit `article`/`gender` unless the entry is a noun and the playbook supports them.
- The exported `const` name: `importedDeck_<sourceLang>_<targetLang>` (e.g. `importedDeck_english_polish`).
