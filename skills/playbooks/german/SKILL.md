---
name: german-playbook
description: Reference playbook for German as a target language. Loaded by build-dictionary.
---

# German playbook

## Orthography & diacritics
- Expected: `äöüß` (ß lowercase only; uppercase context = `SS`).
- **All nouns capitalized.** A lowercase headword for a noun is wrong.

## POS inventory
`Substantiv, Verb, Adjektiv, Adverb, Pronomen, Präposition, Konjunktion, Artikel, Partikel, Numerale, Interjektion`.

## Morphology notes
- Gender: `der, die, das` — **required** on every noun. Store in `article` + `gender` (`m/f/n`).
- Four cases: Nom, Acc, Dat, Gen. Headword = nominative singular.
- Plurals irregular — include in `meanings[*].example` when possible.
- Verbs: infinitive as headword. Flag **separable** prefixes (`anrufen`, `aufstehen`) — example must show the prefix split in main clauses (`Ich rufe dich an.`).
- Auxiliary: `haben` (most) or `sein` (motion/state-change) for Perfekt.

## Closed-class overrides

| word (EN) | translation (DE) | POS | notes |
|---|---|---|---|
| the | der / die / das / die | article | gender + case |
| a (indef) | ein / eine / ein | article | |
| and | und | conjunction | |
| or | oder | conjunction | |
| but | aber | conjunction | |
| of | von / des | preposition/genitive | |
| to | zu / nach | preposition | direction |
| in | in | preposition | + Dat/Akk |
| be | sein | verb | |
| have | haben | verb | |
| not | nicht / kein | particle/determiner | verb vs noun negation |
| I | ich | pronoun | |
| you (sg, inf) | du | pronoun | |
| you (pol) | Sie | pronoun | capitalized |

## Example-quality rule
- 6–14 tokens, one finite clause.
- Verb-second word order (V2) in main clauses — respect it in examples.
- Separable-prefix verbs must be shown split when finite.
- Humor allowed for A1–A2.

## Known garbage patterns
- Lowercase nouns.
- Missing `ß` / `ä ö ü`.
- Wrong article gender.
- Separable prefix left attached in finite clause (`Ich anrufe dich.`).
- `nicht` used where `kein` is required.
