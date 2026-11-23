# 01 — What a derivative is (nudge a number)

**What this is:** Phase 1 item 1. Getting a slope by pushing an input and measuring how the output moves, with no calculus.
**Why it matters:** training is "turn every knob the way that lowers the loss". The slope tells you which way and how hard. Every later lesson is a faster way to get the same numbers.
**After reading:** you can measure a slope in 6 lines of Python and say what its sign and size mean.

Notebook: `my-work/01-nudge-a-number.ipynb`. Plan: [[PLAN]]. Bridge from lesson 0: [[shared-knobs-cant-be-counted]].
Lecture 1, ~10%: *"we have the rise over run to get the slope"*.

---

## The recipe

```python
h = 0.0001
slope = (f(x + h) - f(x)) / h   # rise / run
```

Push the input by a tiny `h`, see how far the output moved (the rise), divide by `h`. Dividing scales the answer up to "output change **per 1 unit** of push". See [[slope-is-sensitivity]].

## What I measured, on `f(x) = 3*x**2 - 4*x + 5`

| x | slope | means |
|---|---|---|
| 3.0 | +14 | push right → output goes up, steeply |
| -3.0 | -22 | push right → output goes down, even steeper |
| 0.5 | -1 | still going down, gently |
| 0.8 | +0.8 | now going up: the flat spot is between 0.5 and 0.8 |

The flat spot (slope 0) is the bottom of the curve. Training is a search for that kind of spot, with millions of knobs instead of one.

## Breaking it: tiny h

| h | slope at x = 3 (true answer: 14) |
|---|---|
| 0.1 | 14.3 |
| 1e-6 | 14.000003 |
| 1e-10 | 14.0000012 (worse!) |
| 1e-15 | 10.66 |
| 1e-20 | 0.0 |

Too big and you measure the curve's bend. Too small and you measure rounding noise. See [[step-size-h]].

## Several inputs: one slope each

`d = a*b + c` at a = 2, b = -3, c = 10. Push **one** input, keep the others fixed, re-run the whole expression:

| push | slope | why |
|---|---|---|
| a | -3 | `a` is multiplied by `b` = -3 |
| b | +2 | `b` is multiplied by `a` = 2 |
| c | +1 | `c` is just added on |

These per-input slopes are the **gradients**, and they're what `a.grad` will hold from item 2 on. The multiply rule is in [[slope-of-a-product]].

Lecture 1, ~12%: *"we're going to get a bit hacky here"*. Karpathy reasons it out the same way: "b is negative three, so we're actually going to be adding less to d".

## The cost that motivates item 2

One extra run of the expression per input. With 3 inputs that's fine. With a million weights it's a million runs per training step. Backprop gets every slope in one backward pass, and the `Value` object (item 2) is the first step toward it.

My mistake in section 5 (nudging the wrong function) is in [[gotchas]].
