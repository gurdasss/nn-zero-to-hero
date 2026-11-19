# Learning Plan

**What this is:** the lesson-by-lesson map of the whole course, derived from the
lecture transcripts.
**Why it matters:** it's the order we go in, and where in each video to rewatch.
**After reading:** you know where you are and what's coming next.

Positions like "~26%" mean "about that far through the video". There are no
timestamps in the transcripts, so use the quoted phrase to find the spot in YouTube's
transcript panel.

Progress lives in [[PROGRESS]].

---

## Phase 1 — micrograd (Lecture 1)

Pure Python. No numpy, no torch (until item 10).

| # | Lesson | Lecture 1 position | Search phrase |
|---|---|---|---|
| 1 | Derivatives without calculus: nudge a number, measure the change | ~7-14% | "rise over run" |
| 2 | The `Value` object: wrap a number, `__add__`, `__mul__`, `__repr__`, remember children | ~14-18% | "class value takes a single scalar" |
| 3 | Visualising the graph (`draw_dot`, needs Graphviz) | ~18-24% | "visualize" |
| 4 | Manual backprop through a tiny graph, one node at a time | ~25-35% | "base case" |
| 5 | Chain rule: local sensitivity × incoming gradient. `+` passes gradient through unchanged | ~30-45% | "local derivative" |
| 6 | One neuron, and `tanh` as the squashing non-linearity | ~37-45% | "squashing" |
| 7 | Automating it: `_backward` per op, topological sort, `.backward()` | ~47-53% | "topological sort" |
| 8 | A bug that appears when a value is used twice (break it first) | ~55% | "here's the simplest case that shows the bug" |
| 9 | More ops (`exp`, `pow`, divide, subtract); splitting `tanh` into pieces | ~57-66% | "two times a" |
| 10 | Same thing in PyTorch: compare gradients | ~67-70% | "in pytorch" |
| 11 | Neuron → Layer → MLP | ~71-77% | "class neuron" |
| 12 | Loss and the training loop (gradient descent) | ~77-88% | "learning rate" |
| 13 | Karpathy's on-camera training bug (hit it yourself first) | ~88% | "most common neural net mistakes" |

**Exit test:** build a working autograd engine from an empty file, and explain why
backprop needs a topological order.

---

## Phase 2 — makemore (Lectures 2-6)

PyTorch tensors allowed. No `nn.Module` / `nn.Linear` / optimizers until built by hand.
Needs `names.txt` in `my-work/`.

### Lecture 2 — bigram language model
1. Load `names.txt`; what a bigram is; the counts matrix (~1-20%)
2. Sampling names from counts with `torch.multinomial` (~23%)
3. Broadcasting — the #1 source of bugs (~32%)
4. Measuring quality: likelihood → log likelihood → negative log likelihood (~46%)
5. Smoothing (~53%)
6. Same model as a neural net: one-hot, matmul, softmax (~62-75%)
7. Training it with gradient descent; it converges to the counts. Regularization = smoothing (~80-96%)

### Lecture 3 — MLP (Bengio et al. 2003)
1. Embeddings: a lookup table of learned vectors per character (~6%)
2. Hidden layer, tensor reshaping (`view`) (~10-30%)
3. `F.cross_entropy` and why (~45%)
4. Finding a learning rate; overfitting (~54%)
5. Minibatches (~59%)
6. Train / dev / test splits (~74%)

### Lecture 4 — activations, gradients, BatchNorm
1. Bad initial loss and how to fix it (~4%)
2. Saturated `tanh`: dead neurons (~21%)
3. Principled initialisation (~25-30%)
4. BatchNorm, running mean/std (~32-55%)
5. Torch-ifying the code into layer classes (~69%)
6. Diagnostic plots: activation/gradient stats, update-to-data ratio (~75-89%)

### Lecture 5 — backprop ninja
Manual backprop through the whole MLP, no autograd: every op, then cross-entropy and
BatchNorm as single fused steps (~8-15% lays out the exercises). Consolidates Phase 1.
Do not skip.

### Lecture 6 — WaveNet-style deeper model
1. Cleaning up into layer classes, `Sequential` (~11-24%)
2. Hierarchical architecture: fuse characters pairwise (~20-35%)
3. Relation to convolutions (~35%)

**Exit test:** state every tensor's shape at every step of the forward pass and why,
and backprop through the MLP by hand.

**Then stop.** Transformers are the next course.
