# 00 — Markov chain, and where it breaks

**What this is:** the prologue. A next-letter model built from a counts table, pushed until it broke.
**Why it matters:** it's the honest answer to "why do we need neural nets?" The simple thing works, then stops scaling.
**After reading:** you can explain, with numbers, why a lookup table can't use much context, and what a neural net does differently.

Notebook: `my-work/00-markov-chain.ipynb`. Plan: [[PLAN]].

---

## What I built

- Count every N-letter chunk in `names.txt`, with `.` marking the start and end of a name. Store it as `context → [(next_letter, count), ...]`.
- To generate: start from a `.` context, pick the next letter weighted by its count (`random.choices(..., weights=...)`), slide the context along, and stop at `.`.

This is the whole "model". No learning, just a lookup table.

## What I saw

At N = 2 (1 letter of context): `Kekokyneranamiellan`. That's gibberish.
At N = 6 to 9: `Adelheid`, `Oluwatobi`, `Emperor`. They look great, **but they're copies**. 9 of those 10 names were in `names.txt`.

## The numbers (1,000 generated names per N)

| N | letters of context | possible contexts | seen | seen % | forced | copies |
|---|---|---|---|---|---|---|
| 2 | 1 | 27 | 27 | 100% | 0% | 9% |
| 3 | 2 | 729 | 601 | 82% | 12% | 21% |
| 4 | 3 | 19,683 | 5,659 | 29% | 29% | 32% |
| 5 | 4 | 531,441 | 21,757 | 4% | 55% | 57% |
| 6 | 5 | 14,348,907 | 38,062 | 0.27% | 73% | 85% |

- **possible** = 27 ^ (letters of context). Grows ×27 per letter.
- **seen** = `len(ngram_dict)`. Capped by the data: only 228,146 next-letter examples exist.
- **forced** = seen contexts with only one possible next letter.
- **copies** = generated names already in `names.txt`.

The forced and copies columns climb together. That's the mechanism behind the copying. See [[n-gram-sparsity]].

The 9% copies at N = 2 are coincidence (short names like `ava`), not memorisation.

## The conclusion

1. More context **narrows the guess**: after `q` the next letter is `u` about 9 times in 10.
2. But a table needs a row per context, and the rows **explode** (×27 per letter) while the data stays fixed. Most rows go empty, and the filled ones go forced, so the model copies. See [[n-gram-sparsity]].
3. The table also **can't share**: `unn` (15 examples) can't borrow from `ann` (825), even though they behave alike. See [[sharing-across-contexts]].
4. The fix is a **fixed number of knobs, shared across contexts**, tuned automatically. That's a neural net. To tune knobs you need to know which way to turn each one, which is Lesson 1: derivatives.

Lecture 3 opens on exactly this problem: search "exponentially with the length of the context" (~1% in). Lecture 2 patches the "unseen context" hole with fake counts: search "model smoothing" (~50% in).

## Bugs I hit

Logged in [[gotchas]]: capitalised output never matching the data, the wrong denominator, and a `zzq` test that passed because of a shape mismatch.
