# Python dunder methods: how `a + b` works on your own objects

**What this is:** a 5-minute prerequisite for the `Value` object (Phase 1 item 2).
**Why it matters:** `Value` has to support `a * b + c` between objects you defined, and Python only knows how to do that if you tell it.
**After reading:** you know which method Python calls for `+`, `*` and printing, and what `self` and `other` are.

---

"Dunder" = **d**ouble **under**score, e.g. `__add__`. Python turns operators into method calls:

| you write | Python actually calls |
|---|---|
| `a + b` | `a.__add__(b)` |
| `a * b` | `a.__mul__(b)` |
| `a` in a notebook, or `repr(a)` | `a.__repr__()` |

So in `a + b`, inside `__add__`, **`self` is `a`** (the left side) and **`other` is `b`** (the right side). Whatever the method returns *is* the result of `a + b`.

See it happen:

```python
class Loud:
    def __add__(self, other):
        print("__add__ called")
        return 42

Loud() + Loud()   # prints "__add__ called", result is 42
```

Nothing forces the result to make sense. `+` means whatever your method does.

**Without `__add__`:** `a + b` raises `TypeError: unsupported operand type(s)`.
**Without `__repr__`:** printing shows `<__main__.Loud object at 0x7f...>`, which tells you nothing.

**Chaining:** `a * b + c` is `(a.__mul__(b)).__add__(c)`. The multiply runs first and returns something, then `+` is called **on that result**. So for the chain to keep working, what does `__mul__` need to return?

Used in: [[02-value-object]].
