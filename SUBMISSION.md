# HW1 submission

**Name:** Омар
**Student ID:** [Твой ID]
**Group:** CSS-4007
**Repository:** https://github.com/OmarSeilbekov/cs4007-hw1-i

## AI tool disclosure

State which AI tools you used and for what. Expected and fine; undisclosed use
is not.

> Assisted by Gemini as a personal collaborator for debugging Python scripts, analyzing tokenizer forensics data, and structuring the submission report.

---

## Sublab Easy — the registration bot and its bill

**How I laid the catalogue out inside the system prompt, and why:**

> The catalogue was laid out as a structured markdown table and bullet-point list specifying course codes, titles, credits, prerequisites, seat availability, and weekly schedules. This explicit format allows the LLM to parse logical constraints (prerequisite validation, seat tracking, and schedule conflicts) reliably without making arithmetic or lookup errors.

**My turn 5 (Kazakh or Russian):**

> Мен үшінші курс студентімін. Тіркелуге әлі қандай пәндерге құқығым бар?

### Run 1 — OpenAI, `gpt-5.6-luna`

| Turn | Input tokens | Output tokens | Cost $ |
|---|---|---|---|
| 1 | 1265 | 591 | 0.000962 |
| 2 | 1641 | 182 | 0.000547 |
| 3 | 1797 | 77 | 0.000452 |
| 4 | 1893 | 84 | 0.000479 |
| 5 | 1979 | 489 | 0.000983 |
| **total** | **8575** | **1423** | **0.003423** |

### Run 2 — OpenRouter, `google/gemma-4-26b-a4b-it:free`

| Turn | Input tokens | Output tokens | Cost $ |
|---|---|---|---|
| 1 | 1361 | 1856 | 0.000710 |
| 2 | 1899 | 652 | 0.000448 |
| 3 | 2166 | 333 | 0.000396 |
| 4 | 2308 | 510 | 0.000466 |
| 5 | 2596 | 1100 | 0.000671 |
| **total** | **10330** | **4451** | **0.002692** |

### Turn 4, verbatim

The turn where you asked for CSS-4090, which does not exist. Paste both replies
exactly as they came back — do not tidy them.

**OpenAI:**

I cannot add CSS-4090 – Quantum Machine Learning because that course is not listed in the 2026-FALL course catalogue. Its credits, prerequisites, schedule, and seat availability are not provided, so I cannot register or invent those details.

**OpenRouter:**

I cannot add CSS-4090 Quantum Machine Learning to your schedule because that course does not exist in the 2026-FALL catalogue. The only courses available for registration are:

CSS-4007 Artificial Intelligence

CSS-4102 Distributed Systems

CSS-4400 Natural Language Processing (full)

CSS-3011 Machine Learning Foundations (already completed)

CSS-3005 Operating Systems (already completed)

MAT-2020 Linear Algebra (already completed)

ECN-2101 Microeconomics (already completed)

FIN-3300 Financial Modelling

I cannot invent, import, or register you for any course not explicitly listed in the catalogue. If you believe CSS-4090 is a real offering, please verify the course code or provide the official catalogue entry, and I will re-check eligibility.

For your reference, based on the current catalogue, your only eligible courses are CSS-4007, CSS-4102, and FIN-3300 — but no combination of these reaches the 15-credit minimum, and CSS-4007 conflicts with CSS-4102. So under the current rules, no valid schedule is possible.
### Written answers

**1. The two providers used almost identical code. What actually changed, and
what did not?**

> The client initialization wrapper, message formatting logic, and prompt parameters remained identical because both services adhere to the OpenAI-compatible API standard. What changed was the remote endpoint routing (`base_url`), the specific model weights executed on the backend, and the internal token pricing schemes of the respective providers.

**2. Why did the input token count climb on every turn when your questions
stayed roughly the same length? Use the numbers from your own table. What
happens to the bill at fifty turns?**

> LLM APIs are stateless; they do not retain memory of previous turns on the server. Consequently, every turn transmits the *entire prior transcript* (system prompt + all past user and assistant messages) back over the wire. For instance, in OpenAI Run 1, input tokens grew from 1,265 (Turn 1) to 1,979 (Turn 5). If the conversation extended to 50 turns, this cumulative growth would cause input token volume to scale drastically, resulting in severe linear-to-quadratic cost inflation per turn.

**3. Turn 4: did the bot refuse, or did it invent CSS-4090?** If it refused, what
in your system prompt held the line? If it invented, what did it make up —
credits, a room, an instructor?

> Both bots firmly refused, stating that the course does not exist in the catalogue. This boundary was enforced by explicit system instructions in the prompt prohibiting the model from inventing courses, prerequisites, schedules, or credits outside the provided JSON data.

**4. Where else was either bot wrong?** Turn 2 asks for two courses that meet at
the same hour; two courses in the catalogue are full. Did the bots notice?

> Both bots noticed correctly. They immediately flagged the Tuesday 09:00–10:50 schedule collision between `CSS-4007` and `CSS-4102`, noted that `CSS-4400` was full, and correctly identified that no combination of eligible courses satisfied the 15-credit minimum. No logical errors were detected in their evaluations.

---

## Sublab Medium — one task, six models

Paste the per-model summary printed by `correct_kazakh.py`:

| Model | Exact | Failed | Tokens | Cost $ |
|---|---|---|---|---|
| google/gemma-4-26b-a4b-it:free | 0 | 8 | 0 | 0.00000 |
| qwen/qwen3.8-27b | 7 | 0 | 33040 | 0.10086 |
| deepseek/deepseek-v4-flash-0731 | 7 | 0 | 19573 | 0.00520 |
| gpt-5.6-luna | 4 | 0 | 3251 | 0.00264 |
| gpt-5.6-terra | 3 | 0 | 1923 | 0.01048 |
| gpt-5.6-sol | 3 | 0 | 2976 | 0.05778 |

### Which error types did each model repair?

Rows are error labels, columns are models. Write "yes", "no" or "partial".

| Error type | gemma | qwen | deepseek | luna | terra | sol |
|---|---|---|---|---|---|---|
| kaz_to_rus | no | yes | yes | partial | partial | partial |
| latin_homoglyph | no | partial | partial | no | no | no |
| drop_hyphen | no | yes | yes | yes | yes | yes |
| join_words | no | yes | yes | yes | yes | yes |
| double_letter | no | yes | yes | yes | yes | yes |

**The `latin_homoglyph` row: what happened?** Describe what you observed. The
explanation is Sublab Harder's job, not this one's.

> Models frequently failed or struggled to detect and repair subtle `latin_homoglyph` errors because visually identical Latin characters (such as `c`, `o`, `a`) masquerading as Cyrillic letters bypassed simple orthographic checks and altered token representations.

**Where a model returned good Kazakh that was not identical to the original,
say so here.** Exact match is not correctness.

> Several models occasionally restored suffixes using alternative acceptable morphological forms or rearranged adjunct order while keeping the semantic meaning completely valid and grammatically correct in Kazakh.

**Cheapest model that was good enough, and why:**

> `deepseek/deepseek-v4-flash-0731` was the best performing cost-effective model, scoring 7 exact matches out of 8 with a total cost of only $0.00520, vastly outperforming expensive proprietary models while maintaining high accuracy.

---

## Sublab Harder — open the tokenizer

### A. What a language costs

**`cl100k_base`:**

| Language | Tokens | Chars | Tok/char | × English | $ per 1,000 sentences |
|---|---|---|---|---|---|
| kk | 200 | 263 | 0.760 | 3.75 | 0.190 |
| ru | 129 | 277 | 0.466 | 2.30 | 0.116 |
| en | 59 | 291 | 0.203 | 1.00 | 0.051 |

**`o200k_base`:**

| Language | Tokens | Chars | Tok/char | × English | $ per 1,000 sentences |
|---|---|---|---|---|---|
| kk | 84 | 263 | 0.319 | 1.58 | 0.080 |
| ru | 74 | 277 | 0.267 | 1.32 | 0.067 |
| en | 59 | 291 | 0.203 | 1.00 | 0.051 |

### B. What a homoglyph does

One row per `latin_homoglyph` sentence in the dataset. Paste the actual decoded
token strings around the divergence point, not a description of them.

| Sentence id | Foreign char (index, name) | Tokens correct | Tokens corrupted | Δ | Diverges at |
|---|---|---|---|---|---|
| KZ-03 | (0, 'LATIN CAPITAL LETTER A'), (2, 'LATIN SMALL LETTER A'), (5, 'LATIN SMALL LETTER T') | 16 | 20 | +4 | 0 |
| KZ-08 | (1, 'LATIN SMALL LETTER O'), (3, 'LATIN SMALL LETTER A'), (9, 'LATIN CAPITAL LETTER T') | 21 | 24 | +3 | 1 |

**Token pieces around the divergence:**

correct  : ['А', 'лая', 'қ', 'тарға', ' ақша']
corrupted: ['A', 'л', 'a', 'я', 'қ']


### C. Did it get better?

| Language | cl100k_base | o200k_base | Change |
|---|---|---|---|
| kk | 0.760 | 0.319 | -0.441 |
| ru | 0.466 | 0.267 | -0.199 |
| en | 0.203 | 0.203 | 0.000 |

### Written answers

**1. What is the Kazakh tax?** The ratio against English in both encodings, the
dollar figure from A, and how much it changed between the two tokenizers.

> In the legacy `cl100k_base` tokenizer, Kazakh cost 3.75x more than English (0.760 vs 0.203 tok/char), translating to about $0.190 per 1,000 reference sentences. With the newer `o200k_base` tokenizer, this ratio dropped to 1.58x (0.319 tok/char) and cost decreased to $0.080 per 1,000 sentences, substantially reducing the efficiency gap.

**2. Why did the models repair `kaz_to_rus` but struggle with
`latin_homoglyph`?** Both are single-letter substitutions and both look almost
identical on screen. Use your token streams from B as the evidence. Say what the
model actually received in each case.

> A `kaz_to_rus` substitution replaces a Kazakh-specific letter (like ә or қ) with a common Cyrillic letter (а or к), which the tokenizer still maps into standard Cyrillic token structures, preserving semantic context. In contrast, a `latin_homoglyph` introduces foreign Latin code points (e.g., Latin 'A' or 'o' instead of Cyrillic). As shown in measurement B, this completely breaks the BPE merging rules, forcing the tokenizer to shatter the word into disjoint character-level fragments or byte pairs, scrambling the internal token stream.

**3. Name one thing this measurement does not explain about your Sublab Medium
results.** You measured OpenAI's tokenizers; three of your six models were not
OpenAI's. What follows, and what would you have to do to close the gap?

> Our tokenizer analysis only accounted for OpenAI's `tiktoken` vocabulary structures. Models like Qwen and DeepSeek utilize entirely distinct, proprietary tokenizers with different vocabulary sizes and subword merging strategies. To fully explain their behavior, one would need to inspect and analyze the specific tokenizer configuration files corresponding to each of those third-party models.