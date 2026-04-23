---
name: italian-playbook
description: Reference playbook for Italian as a target language. Loaded by build-dictionary.
---

# Italian playbook

## Orthography & diacritics
- Expected: `àèéìòóù`. Accent direction matters (`è` = is / `é` rare).

## POS inventory
`sostantivo, verbo, aggettivo, avverbio, pronome, preposizione, congiunzione, articolo, interiezione, numerale`.

## Morphology notes
- Articles: `il/lo/la/l'/i/gli/le` (def); `un/uno/una/un'` (indef). Choice depends on first sound of noun — spell it right.
- Gender: `m`, `f`. Plural shifts `-o→-i`, `-a→-e`, irregular `-co/-go`.
- Contractions with prepositions: `di + il = del`, `a + i = ai`, `in + la = nella`.
- Verbs: infinitive in `-are/-ere/-ire`. `essere` vs `avere` as auxiliary for passato prossimo.
- Clitic pronouns attach to infinitives (`vederlo`, `darglielo`).

## Closed-class overrides

| word (EN) | translation (IT) | POS | notes |
|---|---|---|---|
| the | il / lo / la / i / gli / le | article | pick by noun |
| a (indef) | un / uno / una | article | |
| and | e | conjunction | `ed` before vowel |
| or | o | conjunction | `od` occasionally |
| but | ma | conjunction | |
| of | di | preposition | often contracts |
| to | a / per | preposition | |
| in | in | preposition | |
| be | essere | verb | |
| have | avere | verb | |
| not | non | particle | |
| I | io | pronoun | |
| you (sg, inf) | tu | pronoun | |

## Example-quality rule
- 6–14 tokens, one finite clause. Natural conversational Italian.
- Humor allowed for A1–A2.

## Known garbage patterns
- Wrong article choice (`il amico` instead of `l'amico`).
- Missing accent on final syllable (`citta` instead of `città`).
- Clitics in wrong position.
