# N-gram sparsity

**What this is:** why a counts table gets worse, not better, when you give it more context.
**Why it matters:** it's the first wall a simple model hits, and the reason neural nets exist.
**After reading:** you can say why a big-N table copies its training data.

---

A table needs one row per possible context. Every extra letter of context multiplies the rows by 27:

```
1 letter  →         27 rows
3 letters →     19,683 rows
5 letters → 14,348,907 rows
```

The data doesn't grow. `names.txt` has 228,146 next-letter examples in total. So at 5 letters of context, only 0.27% of rows ever get filled.

The rows that *do* get filled mostly have one example each. A row with one example has one possible next letter: it's **forced**. Step into a forced row and the generator replays the one name it came from.

Measured in [[00-markov-chain]]: at N = 6, 73% of the seen contexts were forced, and 85% of the generated names were copies.

Contexts that were never seen have no row at all. The table can't say "unlikely". It says nothing.

Related: [[sharing-across-contexts]] (the fix), [[PLAN]].
