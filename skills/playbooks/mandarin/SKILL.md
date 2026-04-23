---
name: mandarin-playbook
description: Reference playbook for Mandarin Chinese as a target language. Loaded by build-dictionary.
---

# Mandarin playbook

## Orthography
- Default to **Simplified** characters unless the source indicates Traditional.
- `phonetic` field holds Hànyǔ Pīnyīn **with tone marks** (`nǐ hǎo`, not `ni hao` or `ni3 hao3`).
- Keep characters and pinyin consistent; do not mix scripts in `word`.

## POS inventory
`名词 (noun), 动词 (verb), 形容词 (adj), 副词 (adv), 代词 (pron), 介词 (prep), 连词 (conj), 助词 (particle), 量词 (classifier/measure word), 数词 (numeral), 叹词 (interjection)`.

## Morphology notes
- No inflection. Grammar via particles and word order.
- **Classifiers** are mandatory: `一个人, 两本书, 三只猫`. Pair each countable noun with its canonical classifier in `meanings[*].example`.
- Aspect markers: `了` (completed/change of state), `过` (experiential), `着` (ongoing state).
- Topic-comment structure common; example may lead with the topic.

## Closed-class overrides

| word (EN) | translation (ZH) | POS | notes |
|---|---|---|---|
| the | — | — | no articles; classifiers + demonstratives `这/那` |
| a (indef) | 一 + classifier | — | e.g. `一本书` |
| and | 和 / 与 / 跟 | conjunction | `和` most common; between nouns, not clauses |
| or | 或者 / 还是 | conjunction | statement vs question |
| but | 但是 / 可是 | conjunction | |
| of | 的 | particle | attributive marker |
| to | 到 / 给 | preposition/verb | destination/recipient |
| in | 在 | preposition | |
| be | 是 | verb | identity only; not for adjectives |
| have | 有 | verb | |
| not | 不 / 没 | adverb | `不` for general/future, `没` for past/existence |
| I | 我 | pronoun | |
| you | 你 / 您 | pronoun | polite |

## Example-quality rule
- 5–12 characters (CJK sentences shorter than Indo-European).
- Natural conversational Mandarin, include correct classifier and aspect marker where needed.
- Humor allowed for A1–A2; wordplay via tones or homophones encouraged if clean.

## Known garbage patterns
- Missing classifier (`两书` instead of `两本书`).
- `是` used before an adjective (`他是高` instead of `他很高`).
- Pinyin without tone marks.
- Traditional/Simplified mixed in one entry.
