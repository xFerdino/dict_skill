---
name: portuguese-playbook
description: Reference playbook for Portuguese as a target language. Loaded by build-dictionary.
---

# Portuguese playbook

## Orthography & diacritics
- Expected: `áâãàéêíóôõúçü`. Nasal tildes `ã, õ` are critical.
- Default to **Brazilian Portuguese** (`pt-BR`) unless the source is clearly European.

## POS inventory
`substantivo, verbo, adjetivo, advérbio, pronome, determinante, preposição, conjunção, artigo, interjeição, numeral`.

## Morphology notes
- Articles: `o, a, os, as, um, uma, uns, umas`. Populate `article` and `gender`.
- Contractions: `de + o = do`, `em + a = na`, etc. Expand or contract per natural usage.
- Verbs: infinitive as headword, note group (`-ar/-er/-ir`).
- Distinguish `ser` vs `estar`; `ter` vs `haver` (possession vs existence).

## Closed-class overrides

| word (EN) | translation (PT) | POS | notes |
|---|---|---|---|
| the | o / a / os / as | article | |
| a (indef) | um / uma | article | |
| and | e | conjunction | |
| or | ou | conjunction | |
| but | mas | conjunction | |
| of | de | preposition | |
| to | para / a | preposition | direction/purpose |
| in | em | preposition | often contracted |
| be | ser / estar | verb | |
| have | ter / haver | verb | possession / existence |
| not | não | particle | |
| I | eu | pronoun | |
| you (sg, inf) | você | pronoun | `tu` in PT-EU and southern Brazil |

## Example-quality rule
- 6–14 tokens, one finite clause. Natural Brazilian register by default.
- Humor allowed for A1–A2.

## Known garbage patterns
- Missing tildes on `ão`/`ões` words.
- `ser`/`estar` confusion.
- European constructions forced into Brazilian examples (e.g. `tu` with BR conjugation).
