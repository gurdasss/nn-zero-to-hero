# 03 — Visualising the graph

**What this is:** Phase 1 item 3. Drawing the receipt that `Value` keeps ([[02-value-object]]) as a picture.
**Why it matters:** printing the graph works for 3 nodes and fails at 30. The picture is also where item 4 writes a slope into every box.
**After reading:** you can point at a `draw_dot` picture and say which way data flows, which way slopes flow, and why.

Notebook: `my-work/03-visualise-graph.ipynb`. Plan: [[PLAN]].
Lecture 1, ~19%: *"we'd like a way to nicely visualize these expressions"*.

---

## The expression

```python
a, b, c, f = 2.0, -3.0, 10.0, -2.0
e = a * b      # -6
d = e + c      #  4
L = d * f      # -8
```

## Reading the picture

```
a ─┐
   (*)─▶ e ─┐
b ─┘        (+)─▶ d ─┐
       c ───┘        (*)─▶ L
                f ───┘
```

- **Box** = one `Value`: its label and its data.
- **Op circle** = the operation that made the box to its right. It isn't a `Value`; the drawing adds it.
- **Arrow** = "this was used to make that". It goes from a box into the op that uses it, and from the op to the box it made.
- **Data flows left to right** (the forward pass). **Slopes flow right to left**, starting from `L`.

## Why slopes go right to left

`c` never touches `L` directly. It only reaches `L` through `d`:

```
c  +0.001  ──(+)──▶  d  +0.001  ──(* f)──▶  L  -0.002
```

So `c`'s slope is -2, not 1. You can't know a box's slope until you know the slope of the box it feeds. Start at `L` and work back. You can't skip a box.

## The slopes, by hand (preview of item 4)

| box | slope | why |
|---|---|---|
| `L` | 1 | nudge `L` by h, `L` moves h |
| `d` | -2 | `f.data` × `L`'s slope |
| `f` | 4 | `d.data` × `L`'s slope |
| `c`, `e` | -2 | `+` passes `d`'s slope through: nudge `c` by h and `d` moves exactly h |
| `a` | 6 | `b.data` × `e`'s slope = -3 × -2 |
| `b` | -4 | `a.data` × `e`'s slope = 2 × -2 |

Rule for `*`: **my slope = the other side's data × the slope coming in from my parent.** At `L` the incoming slope is 1, so the second half is invisible there. Don't drop it.
Rule for `+`: **my slope = the slope coming in**, unchanged (the local slope is 1).

## What I found by breaking it

- `draw_dot(e)` shows only `a`, `b`, `e`. `_prev` points back to inputs, and nothing points forward, so `e` can't see `d`, `c`, `f` or `L`.
- `g = a * a` draws **one** `a` box and **one** arrow into `*`. `_prev` holds `a` twice, but `trace` stores edges in a set, so the second `(a, g)` vanishes. The picture hides that `a` was used twice. This matters in item 6.
- `h = a * b + a`: one `a` box with two arrows out. `a` has two parents, so this is not a tree. It's a graph with one-way arrows and no loops.
- `a.data = 100` and then redrawing `L`: only `a`'s box changes. `L` stays -8. Nothing re-runs; you have to rebuild the expression (a new forward pass).

## In my words

> Each box is a `Value`. The op circle made the box to its right. Data flows left to right, slopes flow right to left, parent to child. `a`'s slope is `b`'s data (-3) times `e`'s slope (-2) = 6. Nudging `c` moves `d` by exactly the same amount, so `+` passes -2 through unchanged.

Mistakes along the way are in [[gotchas]] (Lesson 3). Related: [[slope-of-a-product]], [[slope-is-sensitivity]], [[forward-backward-update]].
