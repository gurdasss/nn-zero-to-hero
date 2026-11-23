# The push size h: too big and too small both fail

**What this is:** why `(f(x + h) - f(x)) / h` has a sweet spot for `h`.
**Why it matters:** it's why nudging is only for checking. Real libraries compute exact slopes with backprop.
**After reading:** you can predict what a slope measurement does as `h` shrinks.

---

At x = 3 the true slope of `3*x**2 - 4*x + 5` is 14.

| h | measured | what went wrong |
|---|---|---|
| 0.1 | 14.3 | **too big**: the curve bends inside the gap |
| 1e-6 | 14.000003 | about right |
| 1e-10 | 14.0000012 | worse again: rounding starts to show |
| 1e-15 | 10.66 | **too small**: the rise is mostly rounding noise |
| 1e-20 | 0.0 | the push vanished completely |

**Why too small fails:** a Python float keeps about 16 significant digits.

```python
3.0 + 1e-20 == 3.0          # True: the push is lost, so rise = 0 and slope = 0
f(3.0 + 1e-15) - f(3.0)     # a tiny rise, and most of its digits are rounding error
```

Dividing that noisy rise by a tiny `h` blows the noise up into a wrong answer.

Lecture 1, ~10%: *"the representations of all these numbers in computer memory is finite"*.

Related: [[slope-is-sensitivity]], [[01-what-is-a-derivative]]. The 0.1 vs 0.25 stepping quirk in notebook 01, section 2 (`4.999999999999997`) is the same thing: 0.1 can't be stored exactly.
