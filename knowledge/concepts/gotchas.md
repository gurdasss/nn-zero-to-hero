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
