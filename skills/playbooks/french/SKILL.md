---
name: french-playbook
description: Reference playbook for French as a target language. Loaded by build-dictionary.
---

# French playbook

## Orthography & diacritics
- Expected: `àâæçéèêëîïôœùûüÿ`. Preserve `œ`, `æ`.
- Preserve non-breaking space before `: ; ? !` where source is French-typographic (optional, not required).

## POS inventory
`nom, verbe, adjectif, adverbe, pronom, déterminant, préposition, conjonction, article, interjection, numéral`.

## Morphology notes
- Articles: `le, la, les, un, une, des, du, de la, de l'`. Elision before vowel/h-muet (`l'ami`, not `le ami`).
- Gender `m/f` on every noun; populate `article` + `gender`.
- Verbs: infinitive as headword (`-er/-ir/-re`). Auxiliary for passé composé: `avoir` or `être`.
- Clitic pronouns before the verb (`je le vois`); liaison rules affect spoken examples but not orthography.

## Closed-class overrides

| word (EN) | translation (FR) | POS | notes |
|---|---|---|---|
| the | le / la / l' / les | article | |
| a (indef) | un / une | article | |
| and | et | conjunction | |
| or | ou | conjunction | |
| but | mais | conjunction | |
| of | de / du / des | preposition | contractions |
| to | à / pour | preposition | |
| in | dans / en | preposition | |
| be | être | verb | |
| have | avoir | verb | |
| not | ne … pas | discontinuous particle | both halves in example |
| I | je | pronoun | elides to `j'` |
| you (sg, inf) | tu | pronoun | |
| you (pol/pl) | vous | pronoun | |

## Example-quality rule
- 6–14 tokens, one finite clause.
- Use full `ne … pas` in written examples.
- Humor allowed for A1–A2.

## Known garbage patterns
- `ne` dropped in negation (`je sais pas` is spoken, not learner-written).
- Wrong auxiliary in passé composé.
- Article–noun gender mismatch.
- Missing elision (`le ami`).
