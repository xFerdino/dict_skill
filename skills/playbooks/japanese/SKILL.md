---
name: japanese-playbook
description: Reference playbook for Japanese as a target language. Loaded by build-dictionary.
---

# Japanese playbook

## Orthography
- Mix of kanji + hiragana + katakana. `word` in the customary written form for that entry (usually kanji+okurigana for nouns/verbs).
- `phonetic` holds hiragana reading (e.g. `食べる` → `たべる`); optionally Hepburn romaji in parentheses.
- Preserve native script; do not romanize the headword.

## POS inventory
`名詞 (noun), 動詞 (verb), 形容詞 (i-adj), 形容動詞 (na-adj), 副詞 (adverb), 助詞 (particle), 助動詞 (auxiliary), 接続詞 (conjunction), 代名詞 (pronoun), 数詞 (numeral), 感動詞 (interjection)`.

## Morphology notes
- Verbs: store **dictionary form** (辞書形). Group: 五段 (godan), 一段 (ichidan), irregular (する/来る).
- Adjectives: split i-adj (`高い`) vs na-adj (`静か（な）`).
- Particles are POS=助詞: `は, が, を, に, で, と, へ, から, まで, の, も, や, か, ね, よ`.
- Politeness: prefer `〜ます` polite forms in learner examples unless the entry is an interjection or casual marker.

## Closed-class overrides

| word (EN) | translation (JA) | POS | notes |
|---|---|---|---|
| the | — | — | no articles |
| a (indef) | — | — | no articles |
| and | と / そして | particle / conjunction | `と` between nouns, `そして` between clauses |
| or | または / か | conjunction/particle | |
| but | でも / しかし | conjunction | |
| of | の | particle | attributive |
| to | に / へ | particle | direction |
| in | で / に | particle | location of action vs existence |
| be | です / だ / いる / ある | copula/verb | |
| have | ある / いる / 持つ | verb | inanimate / animate / possession |
| not | 〜ない / 〜ません | aux | attach to verb stem |
| I | 私 (わたし) | pronoun | |
| you | あなた | pronoun | avoid in examples if possible; prefer name + さん |

## Example-quality rule
- 6–14 morphemes (rough token count).
- Polite `〜ます` register by default; plain for casual/child-directed entries.
- Humor allowed for A1–A2; avoid kanji above N4 in examples unless the headword itself is advanced.
- Include the expected particle usage for each verb.

## Known garbage patterns
- `は`/`が` swapped in new-information contexts.
- Dictionary form used where polite form expected in a learner example.
- Romaji leaking into native-script fields.
- Missing small kana (`っ`, `ゃゅょ`).
