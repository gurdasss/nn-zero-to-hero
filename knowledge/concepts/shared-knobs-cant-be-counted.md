# Shared knobs can't be counted

**What this is:** the bridge from the Markov chain (lesson 0) to micrograd (Phase 1).
**Why it matters:** it explains why we need derivatives and backprop at all.
**After reading:** you can say why counting works for a table and fails for a neural net.

---

**In my words:** counting gives conflicting answers, so we nudge toward the best compromise.

## Table: one knob, one context

Each cell in the [[00-markov-chain]] table belongs to exactly one context. "How often does `a` follow `ann`?" has an answer you can count, so one pass sets every knob.

## Shared: one knob, many contexts

To fix [[n-gram-sparsity]] and get [[sharing-across-contexts]], knobs have to be shared. But then the contexts disagree (numbers made up for illustration):

```
ann's examples say:  knob should be 0.30
unn's examples say:  knob should be 0.20
one shared knob:     ???
```

Counting gives two answers for one knob. What we want is the **compromise** that makes the total wrongness (the loss) smallest. With many knobs, each shared by many contexts, the compromises are tangled together: moving one knob changes the best value for the others.

## So: nudge downhill

```
random knobs → guess → score wrongness (loss) → which way to turn each knob? → turn a bit → repeat
```

"Which way" for one knob = nudge it and watch the loss. That's a derivative ([[PLAN]] Phase 1 item 1). Doing it for every knob at once = backprop (Phase 1).

## The payoff

In Lecture 2, a net trained this way on the 1-letter table ends up with **the same table counting gave**: "piece by piece by following the loss" (~93% through Lecture 2). Counting and nudging reach the same answer. Only nudging works once knobs are shared.

Related: [[00-markov-chain]], [[sharing-across-contexts]], [[n-gram-sparsity]].
