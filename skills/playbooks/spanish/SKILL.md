---
name: spanish-playbook
description: Reference playbook for Spanish as a target language. Loaded by build-dictionary.
---

# Spanish playbook

## Orthography & diacritics
- Expected: `áéíóúüñ¿¡`. Mojibake residue = `Ã©, Ã±, Â¿`.
- Preserve opening `¿` `¡` in questions / exclamations.

## POS inventory
`sustantivo (noun), verbo, adjetivo, adverbio, pronombre, determinante, preposición, conjunción, artículo, interjección, numeral`.

## Morphology notes
- Articles: `el, la, los, las, un, una, unos, unas`. Populate `article` on noun entries.
- Gender: `m`, `f`. Nouns ending `-o` usually m, `-a` usually f (exceptions: `el día`, `la mano`).
- Verbs: store the **infinitive** as headword. Note conjugation group (`-ar/-er/-ir`).
- Distinguish `ser` vs `estar` in example choices.
- Preterite vs imperfect: example should unambiguously illustrate one.

## Closed-class overrides

| word (EN) | translation (ES) | POS | notes |
|---|---|---|---|
| the | el / la / los / las | article | gender-matched |
| a (indef) | un / una | article | |
| and | y | conjunction | `e` before `i-/hi-` |
| or | o | conjunction | `u` before `o-/ho-` |
| but | pero | conjunction | |
| of | de | preposition | |
| to | a | preposition | motion; also infinitive marker via plain infinitive |
| in | en | preposition | |
| on | en / sobre | preposition | |
| be | ser / estar | verb | sense-dependent |
| have | tener / haber | verb | possess vs auxiliary |
| not | no | particle | |
| I | yo | pronoun | |
| you (sg, inf.) | tú | pronoun | |
| you (sg, form.) | usted | pronoun | |

## Example-quality rule
- 6–14 tokens, one finite clause.
- Latin American neutral register by default unless the source clearly targets Spain.
- Humor welcome for A1–A2 function words.

## Known garbage patterns
- `exampleTr` missing `¿/¡`.
- Gender-mismatched articles (`el mesa`, `la problema`).
- `ser/estar` swapped (`Soy cansado` instead of `Estoy cansado`).
