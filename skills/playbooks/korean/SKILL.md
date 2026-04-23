---
name: korean-playbook
description: Reference playbook for Korean as a target language. Loaded by build-dictionary.
---

# Korean playbook

## Orthography
- Hangul only in `word`. No romanization leaking into native-script fields.
- `phonetic` holds Revised Romanization (RR) — e.g. `안녕하세요` → `annyeonghaseyo`.

## POS inventory
`명사 (noun), 동사 (verb), 형용사 (adjective — verb-like), 부사 (adverb), 대명사 (pronoun), 조사 (particle), 어미 (ending), 감탄사 (interjection), 수사 (numeral), 관형사 (determiner)`.

## Morphology notes
- Particles attach to nouns: subject `이/가`, topic `은/는`, object `을/를`, location `에/에서`, instrumental `으로/로`. Choice depends on final consonant of preceding syllable.
- Verbs and adjectives conjugate; store the **dictionary form** (끝나다, 크다) as headword.
- Politeness levels — default to `해요체` (polite informal, `-아/어요`) for learner examples.
- SOV word order. Verb is final.

## Closed-class overrides

| word (EN) | translation (KO) | POS | notes |
|---|---|---|---|
| the | — | — | no articles |
| a (indef) | — / 한 + counter | — | |
| and | 그리고 / -고 / -와/과 | conj/ending | word vs clause level |
| or | 또는 / -거나 | conjunction/ending | |
| but | 하지만 / -지만 | conjunction/ending | |
| of | 의 | particle | genitive, often omitted |
| to | 에 / -에게 | particle | place / person |
| in | 에 / 에서 | particle | static vs action |
| be | 이다 / 있다 | copula/verb | identity / existence |
| have | 있다 | verb | possession via 있다 + 이/가 |
| not | 안 / -지 않다 | adverb/ending | |
| I | 나 / 저 | pronoun | casual / humble |
| you | 너 / 당신 | pronoun | avoid `당신` in neutral examples; prefer name + 씨 |

## Example-quality rule
- 6–14 eojeol (space-separated chunks).
- Use `해요체` by default. Particle choice must match final consonant of the preceding noun.
- Verb-final word order. Humor allowed for A1–A2.

## Known garbage patterns
- Wrong particle allomorph (`사람이` vs `사람가`).
- SVO word order imported from English.
- Romaja leaking into Hangul fields.
- `당신` overused as "you".
