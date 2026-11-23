# Slope through a multiply: it's the other number

**What this is:** the slope of `a * b` with respect to each input.
**Why it matters:** it's half of backprop's rulebook. The `*` node in item 4 uses exactly this.
**After reading:** you can write down the slopes of `a*b + c` without running anything.

---

A shopping bill: `d = a * b + c`.
- `a` = price per item ($2)
- `b` = number of items (-3)
- `c` = flat fee ($10)

```
d = 2 * -3 + 10 = 4
b up by 1 → 2 * -2 + 10 = 6     d up by 2 = a
a up by 1 → 3 * -3 + 10 = 1     d down by 3 = b
c up by 1 → 2 * -3 + 11 = 5     d up by 1
```

- **Slope for `b` = `a`.** Every extra unit of `b` costs one more "price".
- **Slope for `a` = `b`.** Every extra unit of `a` adds one more `b`. Here `b` is -3, so `d` goes **down**.
- **Slope for `c` = 1.** Added straight on: +1 in, +1 out.

The push size doesn't matter. For a straight multiply the rate is the same everywhere.

Lecture 1, ~13%: *"differentiating a times b plus c with respect to a gives you just b"*.

Related: [[slope-is-sensitivity]], [[01-what-is-a-derivative]].
