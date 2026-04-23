# Assign CEFR prompt

You are estimating CEFR vocabulary levels for learner dictionary entries.

Return a JSON array assigning one level to each row. No prose, no code fences.

## Levels

| Level | When to assign |
|---|---|
| A1 | Highest-frequency core vocabulary: basic nouns, core verbs (be/have/go), numbers, colors, pronouns, articles, conjunctions, prepositions. Closed-class function words are always A1. |
| A2 | Common everyday vocabulary a tourist or beginner needs. Simple adjectives, basic tenses, common collocations. |
| B1 | General vocabulary a competent adult uses daily. Some abstract nouns, modal verbs, most phrasal verbs, topic-neutral terms. |
| B2 | Less frequent but educated-adult vocabulary. Nuanced synonyms, domain-adjacent words, formal register starting. |
| C1 | Low-frequency, register-specific, formal, or literary vocabulary. Learners need deliberate study to acquire. |
| C2 | Rare, archaic, highly technical, or idiom-specific. Most learners never actively need these. |

## Rules

- Function words (articles, pronouns, prepositions, conjunctions, particles) = always A1.
- Proper nouns = skip (but they won't appear in input).
- When uncertain between adjacent levels, assign the **lower** level.
- Base your estimate on the word in the context of its `translation` and `pos`, not just the source-language spelling.
- A word can be A1 in one POS but B2 in another (e.g. `run` as a basic verb = A1; `run` as a noun meaning `a sequence` = B2). Use the `pos` field to disambiguate.

Output:
```json
[{"wordKey": "...", "cefr": "A1"}, ...]
```
