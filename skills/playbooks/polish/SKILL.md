---
name: polish-playbook
description: Reference playbook for Polish as a target language. Loaded once by build-dictionary. Contains orthography, POS, morphology, closed-class overrides, example-quality rule, and garbage patterns.
---

# Polish playbook

## Orthography & diacritics
- Expected letters beyond ASCII: `ąćęłńóśźż` (and uppercase). Any `Ã`, `Å`, `â€` residue = mojibake.
- Common mojibake map: `Ä… → ą`, `Ä™ → ę`, `Å‚ → ł`, `Ã³ → ó`, `Å„ → ń`, `Å› → ś`, `Å¼ → ż`, `ÅŸ → ź`, `Ä‡ → ć`.
- Normalize curly quotes `’” →  '"`.

## POS inventory
`noun, verb, adjective, adverb, pronoun, determiner, preposition, conjunction, particle, numeral, interjection`. Polish verbs carry aspect — add it as a tag (`perfective`/`imperfective`) when known, not as POS.

## Morphology notes
- Polish has **no articles**. Never translate English `a/an/the` with a literal article — use the demonstrative `ten/ta/to` only when `the` points at something already mentioned.
- Seven cases. When adding a gloss for a noun, use the **nominative singular** form.
- Aspect pairs for verbs: prefer imperfective as the primary gloss (e.g. `robić` over `zrobić`).
- Diminutives end in `-ek/-ka/-ko` — keep the base form as the headword.
- Do not set `article` or `gender` fields on Polish entries unless the row is a noun and gender is known (`m`, `f`, `n`, `pl`).

## Closed-class overrides (locked glosses)

| word (EN) | translation (PL) | POS | notes |
|---|---|---|---|
| a | a | conjunction | contrast "and/but"; not `pewien` |
| the | — | — | no Polish equivalent; drop or use `ten/ta/to` context-dependently |
| and | i | conjunction | |
| or | lub | conjunction | |
| but | ale | conjunction | |
| of | — | — | expressed via genitive case; no single word |
| to | do | preposition | motion toward |
| in | w | preposition | |
| on | na | preposition | |
| at | w / na | preposition | context |
| be | być | verb | imperfective |
| have | mieć | verb | imperfective |
| do | robić | verb | imperfective |
| that | że | conjunction | complementizer |
| this | to / ten | pronoun/determiner | |
| not | nie | particle | |
| I | ja | pronoun | lowercase |
| you (sg) | ty | pronoun | informal |
| he | on | pronoun | |
| she | ona | pronoun | |
| we | my | pronoun | |
| they | oni / one | pronoun | animate/inanimate |

## Example-quality rule
- 6–14 tokens, one finite clause.
- Natural spoken Polish. No bureaucratic EU-translation register.
- `exampleTr` must feel like something a Polish native would say, not a word-by-word gloss.
- Humor allowed for A1–A2 closed-class items. Example: `a` → `"I own a cat. The cat owns me." / "Mam kota. To kot ma mnie."`.

## Known garbage patterns (flag as hard-tier)
- Standalone ALL-CAPS `JA`, `TY`, `ON`, `ONA`, `MY`, `ONI` mid-sentence.
- English stems hallucinated as Polish: `okładzina`, `pewien dawno`, `JA był okładzina`.
- `exampleTr` containing English content words verbatim.
- Missing diacritics on every Polish word (suggests stripped encoding).
