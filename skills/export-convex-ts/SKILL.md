---
name: export-convex-ts
description: Pure templating — convert verified JSONL into a TypeScript import file matching the voca.cards Convex shape. No LLM calls.
---

# export-convex-ts

Deterministic. **Do not call the LLM.**

## Inputs

- `data/<runId>/verified.jsonl`
- `targetLang`, `sourceLang` (for the `const` name and `lang`/`sourceLanguage` fields).

## Procedure

1. Read every line of `verified.jsonl`.
2. For each row, build the output object with these fields (drop anything else):
   - `word, wordKey, phonetic` (default `""`), `translation`.
   - `example, exampleTr` (default `""`).
   - `lang` = capitalized `targetLang` (e.g. `"Polish"`).
   - `tags` = existing tags or `["general"]`.
   - `masteryScore: 0`, `isNew: true`.
   - `cefr` = existing or `null`.
   - `mnemonic` = existing or `null`.
   - `sourceLanguage` = capitalized `sourceLang` (default `"English"`).
   - `meanings` = **stringified** JSON of the meanings array. If `row.meanings` is already a string that parses as valid JSON, reuse it. Otherwise build from the primary entry: `[{pos: <inferred or stored>, translation, example, exampleTr}]`.
   - `createdBy: "import"`.
   - For nouns where the playbook supports it, include `article` and `gender`.
3. Emit TypeScript:

   ```ts
   // Generated from <source path> on <ISO date>
   // Target: <targetLang>, Source: <sourceLang>
   export const importedDeck_<sourceLang>_<targetLang> = [
     { ...row1... },
     { ...row2... },
   ];
   ```

   Use `JSON.stringify(obj, null, 2)` per row, then wrap. The `meanings` field inside must itself be a JSON-stringified string (so the file contains `"meanings": "[{\"pos\":..."`).

4. Write to `data/<runId>/out.ts`.
5. Print the output path and row count.

## Validation

After writing, re-read `out.ts` and confirm:
- It parses as valid TypeScript (run `node --check out.ts` via Bash if Node is available; if not, do a textual sanity check that brackets balance).
- `meanings` fields are strings that `JSON.parse` successfully.
- No row is missing required fields from the schema (`word, wordKey, translation, lang, sourceLanguage, meanings, masteryScore, isNew, createdBy`).
