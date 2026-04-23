---
name: russian-playbook
description: Reference playbook for Russian as a target language. Loaded by build-dictionary.
---

# Russian playbook

## Orthography & diacritics
- Cyrillic `а–я`, `ё`. Always restore `ё` when phonetically present (`всё`, not `все` for "everything").
- Mojibake residue: `Ð?`, `Ñƒ` — indicates UTF-8 read as Latin-1.
- `phonetic` field holds a stress-marked Latin transliteration (e.g. `pri·vyét`) or IPA.

## POS inventory
`существительное (noun), глагол, прилагательное, наречие, местоимение, предлог, союз, частица, числительное, междометие`.

## Morphology notes
- Six cases: `Nom, Gen, Dat, Acc, Instr, Prep`. Headword = nominative singular.
- Gender: `m, f, n`. For nouns store `gender`.
- Aspect pairs: imperfective as primary gloss (`делать`), perfective in a note (`сделать`).
- Prepositions govern specific cases — example must obey.

## Closed-class overrides

| word (EN) | translation (RU) | POS | notes |
|---|---|---|---|
| the | — | — | no articles in Russian |
| a (indef) | — | — | no articles |
| and | и | conjunction | |
| or | или | conjunction | |
| but | но | conjunction | |
| of | — | — | genitive case, no word |
| to | в / на / к | preposition | direction |
| in | в | preposition | + prep/acc |
| on | на | preposition | + prep/acc |
| be | быть | verb | present tense usually omitted |
| have | у … есть | construction | not a single verb |
| not | не | particle | |
| I | я | pronoun | |
| you (sg, inf) | ты | pronoun | |
| you (pol/pl) | вы | pronoun | |

## Example-quality rule
- 6–14 tokens, one finite clause.
- Include correct case endings; example must illustrate the governing case of the preposition in the gloss.
- Humor allowed for A1–A2.

## Known garbage patterns
- Missing `ё` where required (changes meaning).
- Wrong case after preposition.
- Articles inserted from machine translation (`Я иду в the магазин`).
