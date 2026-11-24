# Gotchas

**What this is:** bugs I actually hit, with what I expected, what happened, and why.
**Why it matters:** the same mistakes come back in bigger code, where they are harder to see.
**After reading:** you can spot these before they bite again.

---

## Lesson 0 — Markov chain ([[PLAN]], `my-work/00-markov-chain.ipynb`)

### Capitalised output never matches the data
- **Expected:** `generated_name in words` to catch copied names.
- **Happened:** it could never be `True` while the generator returned `name.capitalize()`.
- **Why:** `names.txt` is all lowercase. `'Rony' != 'rony'`. Compare like with like; format only when printing.

### Wrong denominator
- **Expected:** "225 copies out of 32,000" = copies are rare.
- **Happened:** I generated 1,000 names, not 32,000. The real rate was 22.5%.
- **Why:** the denominator must be *what you sampled*, not the size of the dataset. Put the variable in the f-string (`/{NUM_NAMES_TO_GENERATE}`), never a typed number.

### A test that passes for the wrong reason
- **Expected:** `ngram_dict[('z','z','q')]` raising `KeyError` at N = 3 shows `zzq` was never seen.
- **Happened:** at N = 3 every key is 2 letters long. A 3-letter key can *never* be found, whatever the data says.
- **Why:** check the shape of the question matches the shape of the table before trusting the answer. This is the same bug class as tensor shape mismatches later in [[PLAN]] Phase 2.

## Lesson 1 — Nudge a number ([[PLAN]], `my-work/01-nudge-a-number.ipynb`)

### Mixing up slope and rise
- **Expected:** "slope 14 means for every 1 unit push (e.g. 0.001) the output increases by 0.014".
- **Happened:** that sentence treats "1 unit" and "0.001" as the same push.
- **Why:** the slope is a **rate**: output change per 1 unit of push. The rise is what you get for *this* push: `rise ≈ slope * h`. Slope 14 with h = 0.001 gives a rise of about 0.014.

### Calling a negative slope's "size" negative
- **Expected:** "size of the slope was -21.99", "output will increase by -0.022".
- **Why it's wrong:** sign and size are separate. Sign = direction (negative means the output goes down when x goes right). Size = how steep (about 22). Say "decreases by 0.022".

### Nudging the wrong function, then feeding the slope back in as an input
- **Expected:** `g(a=slope_at_x(a), b=b, c=c)` to give the slope of `d = a*b + c` with respect to `a`.
- **Happened:** printed `slope of a = 8.0003`. The real answer is -3.
- **Why:** `slope_at_x` calls `f` (the quadratic from section 1), not `g`. So it measured the slope of `f` at 2 (that's the 8), then passed that slope into `g` as if it were `a`. A slope is a measurement *about* an input, not a new value *for* it. The fix: call `g` twice, once as is and once with only `a` pushed by `h`, then subtract and divide by `h`. See [[slope-of-a-product]].
- **Related:** I also predicted +39,970 for `a`'s slope. The real answer is -3. Read the slope off the expression first ("what is `a` multiplied by?") before computing anything.

## Lesson 2 — The Value object ([[PLAN]], `my-work/02-value-object.ipynb`)

### `None` for "no children"
- **Expected:** `self._prev = None` on leaves to mean "nothing made me".
- **Happened:** fine until anything loops over it: `for child in a._prev` gives `TypeError: 'NoneType' object is not iterable`.
- **Why:** use an empty collection (`()`) for "none". The loop then does nothing, and a recursive walk stops at the leaves by itself, with no special case needed.

### Writing predictions down as results
- **Expected:** `a + 1` and `1 + a` give the same `TypeError`, and `y.data` updates when `x.data` changes.
- **Happened:** `a + 1` gives `AttributeError` (inside my `__add__`), `1 + a` gives a different `TypeError`, and `y.data` didn't change.
- **Why:** the comments were guesses, not observations. Write the prediction, run it, *then* write what printed. Also save before asking for review: twice the file on disk didn't match what I'd run.

### `NotImplemented` vs `NotImplementedError`
- **Expected:** "if the left side can't add a `Value`, it returns `NotImplementedError`".
- **Why it's wrong:** these are two different things. `NotImplemented` is a plain value that a dunder *returns* to say "not me, ask the other side", and Python then tries `__radd__`. `NotImplementedError` is an exception you *raise*, and it stops everything. See [[python-dunder-methods]].

### A dead check left over from `None`
- **Happened:** `traverse_graph` kept `if value._prev is not None:` after `_prev` changed from `None` to `()`. `()` is never `None`, so the check is always true and does nothing. Leaves print a blank line for their empty `_op`.
- **Why:** when you change a field's "empty" value, look for code that tested the old one.

## Lesson 3 — Visualising the graph ([[PLAN]], `my-work/03-visualise-graph.ipynb`)

### Auto-built labels lose the brackets
- **Did:** `__add__` / `__mul__` build the result's label from the children: `f"{self.label}+{other.label}"`.
- **Happened:** `L = (a*b + c) * f` got the label `a*b+c*f`. Read normally, that means `a*b + (c*f)`, which is a different expression.
- **Why:** the string remembers the order of the ops but not the grouping. The graph (`_prev`) has the real structure, and the label is only a rough copy of it. Labels also get longer with every layer, so in an MLP they'd be unreadable. Karpathy names the in-between values by hand (`e.label = 'e'`).
