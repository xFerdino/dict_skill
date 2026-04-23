---
name: english-playbook
description: Reference playbook for English as a target language. Loaded by build-dictionary.
---

# English playbook

## Orthography & diacritics
- ASCII-only headwords. Loanwords may carry diacritics (`café`, `naïve`) — preserve.
- Normalize curly quotes and em/en dashes.

## POS inventory
`noun, verb, adjective, adverb, pronoun, determiner, preposition, conjunction, interjection, numeral, auxiliary`.

## Morphology notes
- Articles `a / an / the`. `an` before vowel sound, `a` otherwise.
- Verb forms: base / -s / -ed / -ing / past participle. Store base form as headword.
- Irregular plurals and past tenses: include notes in `meanings[*].example` so the learner sees the form.
- No grammatical gender.

## Closed-class overrides (locked glosses)

| word | translation (cue/POS) | notes |
|---|---|---|
| a | indefinite article | before consonant sound |
| an | indefinite article | before vowel sound |
| the | definite article | |
| and | conjunction | |
| or | conjunction | |
| but | conjunction | |
| of | preposition | belonging, part |
| to | preposition / infinitive marker | |
| in | preposition | inside |
| on | preposition | surface |
| at | preposition | point |
| be | verb (am/is/are/was/were) | |
| have | auxiliary / verb | |
| do | auxiliary / verb | |
| not | particle | |
| that | conjunction / pronoun / determiner | |
| this | determiner / pronoun | |

## Example-quality rule
- 6–14 tokens, one finite clause.
- Natural contemporary English, not textbook English.
- Humor allowed for A1–A2 function words.

## Known garbage patterns
- Stilted passives (`Instructions for recharging should be carried on board.`) — rewrite to something a learner would actually say.
- EU-corpus technical sentences.
- Missing subject (`Also have the same problem.`).
