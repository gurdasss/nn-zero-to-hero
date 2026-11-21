# Sharing across contexts

**What this is:** the thing a counts table can't do, and a neural net can.
**Why it matters:** it's the core idea behind embeddings in Lecture 3.
**After reading:** you can explain why `unn` should learn from `ann`.

---

Real counts at N = 4 (3 letters of context):

```
context  examples  top next letters
ann        825     a  e  i  .  y
enn        240     a  e  i  .  y
onn        194     a  i  e  .  y
unn         15     i  a  e  y  .
ynn        516     .  e  l  a  o    ← looks similar, behaves differently
```

To a table, `ann` and `unn` are unrelated keys. So `unn`'s guess rests on 15 examples, and the 1,200+ examples from its siblings count for nothing.

A model with a **fixed set of knobs shared across all contexts** fixes two things:
- **Sharing:** similar contexts get similar guesses, so rare or unseen ones borrow from common ones.
- **No explosion:** more context doesn't multiply the model's size by 27 per letter. See [[n-gram-sparsity]].

`ynn` (Lynn, Flynn) shows that "similar" can't be a hand-written rule like "same last letters". The model has to **learn** which contexts behave alike. That's what training does.

Lecture 3 opens here: search "exponentially with the length of the context" (~1% in).

Related: [[00-markov-chain]], [[PLAN]].
