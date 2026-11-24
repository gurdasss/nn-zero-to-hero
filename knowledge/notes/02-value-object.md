# 02 — The `Value` object: numbers that remember where they came from

**What this is:** Phase 1 item 2. A wrapper around one number that also records how that number was made.
**Why it matters:** nudging ([[01-what-is-a-derivative]]) costs one full re-run per input. To do better, the expression has to remember its own shape so it can be walked backwards later.
**After reading:** you can say what three things a `Value` stores and why each one is there.

Notebook: `my-work/02-value-object.ipynb`. Plan: [[PLAN]]. Prereq: [[python-dunder-methods]].
Lecture 1, ~14%: *"class value takes a single scalar value that it wraps"*.

---

## The problem

```python
d = 2.0 * -3.0 + 10.0     # d is 4.0
```

Once this line runs, `d` is just `4.0`. It has no idea it came from a multiply and an add, or which numbers went in. You can't ask a plain float "who made you?"

## The idea

Make a number that **keeps a receipt**:

```
d = 4.0
  made by: +
  from:    (-6.0, 10.0)
                │
                └── -6.0
                      made by: *
                      from:    (2.0, -3.0)
```

Follow the receipts back from `d` and you reach every input. That chain of receipts is the **expression graph**. Item 3 draws it, and item 4 walks it backwards to get slopes.

## What a `Value` holds

| field | what it is | for `d` above |
|---|---|---|
| `data` | the plain number | `4.0` |
| `_prev` | the Values that made it ("children") | the `-6.0` and the `10.0` |
| `_op` | the operation that made it, as a string | `'+'` |

Inputs you create by hand (`2.0`, `-3.0`, `10.0`) have no children and no op. They're the leaves.

## How the receipt gets written

You never write it yourself. `__add__` and `__mul__` ([[python-dunder-methods]]) do the math on `.data`, then return a **new** `Value` that records `self` and `other` as its children and `'+'` or `'*'` as its op. So writing `a * b + c` builds the graph as a side effect.

Lecture 1, ~18%: *"we want to keep these expression graphs"*.

## What I found (so far)

- **Leaves need `_prev = ()`, not `None`.** Looping over `()` does nothing, so a recursive walk stops at the leaves on its own. `None` crashes the loop. See [[gotchas]].
- **The math happens once, at creation.** `y = x * b` computes `y.data` right then. Change `x.data` later and `y.data` stays stale, while the receipt still points at the changed `x`.
- **Breaking it:** `a + 1` fails *inside* `__add__`, on `self.data + other.data`, because `other` is the int `1` and has no `.data`. `1 + a` fails *before* reaching your code, with a different error. `a * a` gives a tuple holding `a` twice.
- **Why `1 + a` is different:** Python asks the left side first, `int.__add__(a)`. An int doesn't know `Value`, so it returns the special value `NotImplemented`. Python then asks the right side, `a.__radd__(1)`. `Value` has no `__radd__`, so Python raises `TypeError`. Adding `__radd__` (and `__rmul__`) later is what makes `2 * a` work.
- **Walking the receipt:** print a node's own `_op`, then recurse into each child. `d._prev` only gives `d`'s direct parents; you reach the leaves by repeating the step on each of them.

## In my words

> A plain float like `d = a * b + c` keeps only the result. How `d` was built is lost. A `Value` keeps the receipt (its children and the op), so starting from the final output you can walk back through every step.

Missing piece: *why* that matters. Backprop needs the receipt to work out every input's slope in one backward walk, instead of re-running the whole expression once per input like in [[01-what-is-a-derivative]].
