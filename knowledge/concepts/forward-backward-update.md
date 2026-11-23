# Forward, backward, update: one training step

**What this is:** the three steps of training, and what each one does.
**Why it matters:** every lesson from here on is one of these three steps.
**After reading:** you can say what each step takes in and gives out.

---

```
1. FORWARD:  current weights → run the expression → loss (one number)
2. BACKWARD: walk the graph from the loss back    → a slope for EVERY weight
3. UPDATE:   w = w - 0.01 * slope                 → each weight moves a little downhill
repeat, every step, all three. No "if the loss is bad" check.
```

- **Backward only measures.** It changes nothing. It replaces nudge-and-re-run ([[01-what-is-a-derivative]]) with one walk back through the receipts ([[02-value-object]]).
- **Update moves *against* the slope.** Slope +6: pushing up raises the loss, so push down. Slope -4: push up.
- **Small step (0.01, the learning rate)**, because a slope is only accurate close to where it was measured ([[step-size-h]]).

Worked example (Lecture 1's `L = (a*b + c) * f`, at a=2, b=-3, c=10, f=-2):

| | a | b | c | f | L |
|---|---|---|---|---|---|
| before | 2 | -3 | 10 | -2 | -8 |
| slope | 6 | -4 | -2 | 4 | |
| after (step 0.01) | 1.94 | -2.96 | 10.02 | -2.04 | -8.73 |

The loss went down.

**Two kinds of "nudge":** nudging to *measure* (slow, replaced by backprop) and nudging to *improve* (the update, which stays).

Related: [[slope-is-sensitivity]], [[slope-of-a-product]], [[shared-knobs-cant-be-counted]].
