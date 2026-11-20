# Learning Plan

**What this is:** the map of the whole course: where we're going, why, and the order
we go in, lesson by lesson. It's built from Karpathy's lecture transcripts.
**Why it matters:** each lesson only makes sense once you know which problem it
solves. This is where that problem is written down.
**After reading:** you can say what the course builds, why each step is there, and
what you're working on today.

Progress lives in [[PROGRESS]].

---

## Your goal

**Understand what a neural network is, and why we need one.** You've used Markov
chains before, and that's the way in: a Markov chain is the simple thing, and a
neural net is what you reach for when the simple thing stops scaling. Lesson 0 makes
you see that break with your own numbers. Lectures 2-3 come back to it properly.

"Understood" here means you can build one and debug one, not just describe one. So
the plan runs the full course.

## The destination

By the end you will have **built, by hand, a neural net that invents new names**
("dontel", "irot", "zhendi"). You'll understand every line, including the parts
PyTorch normally hides from you.

Karpathy's own pitch for the series (Lecture 1, ~0%): *"what neural network training
looks like under the hood"*.

## The whole course in three sentences

Karpathy says it outright (Lecture 1, ~3%: *"neural networks are just mathematical
expressions"*) and again in the wrap-up at ~89%:

1. **A neural net is just a big math expression.** Numbers go in (data and weights),
   one number comes out. That number is the *loss*, a score for how wrong it is.
2. **Training means nudging every weight a little in the direction that makes the
   loss smaller, and repeating that thousands of times.**
3. **Backprop is the trick that tells you which way to nudge every weight, all at
   once, cheaply.**

Everything below is one of those three ideas, made concrete. If you get lost, come
back here and ask: which of the three is this lesson about?

## How the pieces connect

```
Markov chain: guess the next letter from a counts table   (lesson 0)
        │   problem: the table explodes as you add context
        │   so: we want a fixed set of knobs, shared across contexts,
        │       tuned automatically. That's a neural net.
        │       To tune knobs you need to know which way to turn each one, so:
        ▼
nudge a number, see the output move     (what a slope is)
        │
        ▼
Value objects that remember their math  (an expression you can walk backwards)
        │
        ▼
backprop: slopes for every input at once (chain rule + topological order)
        │
        ▼
neurons → MLP → loss → training loop     (micrograd done: a tiny net that learns)
        │
        ▼  ── same ideas, but numbers in arrays (tensors) instead of one at a time ──
        │
bigram: guess the next letter by counting
        │   problem: only sees 1 previous letter
        ▼
MLP: learned embeddings, sees 3 letters
        │   problem: deeper nets train badly
        ▼
activations, init, BatchNorm            (keeping the training healthy)
        │   problem: you're trusting loss.backward() blindly
        ▼
backprop ninja: backprop by hand, on tensors
        │   problem: 3 letters squashed in one go is crude
        ▼
WaveNet-style: deeper, fuses letters gradually   ← finish line
```

## How to use this plan

- **Position** like "~26%" means "about that far through the video". The transcripts
  have no timestamps, so search the **phrase** in YouTube's transcript panel to find
  the spot.
- **Why now** = the problem from the previous lesson that this one solves.
- **Principle** = the one thing to keep from it. If you can't explain the principle
  back in your own words, the lesson isn't done.
- Every lesson ends with you **breaking something on purpose** and predicting what
  happens first.
- **Pace:** small notebooks, one idea each, about 15-30 minutes, with frequent
  explain-it-back checks. A lesson can span several notebooks.

---

## Lesson 0 — Prologue: Markov chain, and where it breaks

Not a lecture. It's here to answer "why neural nets?" before we build one.
Notebook: `my-work/00-markov-chain.ipynb`. Pure Python.

- **Build:** a character-level Markov chain on `names.txt`. Count which letter
  follows which, then generate names by sampling from the counts.
- **Break:** give it 2, 3, 4, 5 letters of context. Measure how many contexts are
  possible, how many you actually saw, and how many you saw only once.
- **Principle:** a counts table gives every context its own row and learns nothing
  about one row from another. The table grows exponentially with context and most
  rows go empty. A neural net replaces the table with a fixed set of adjustable
  knobs (weights) shared across all contexts, so similar contexts can share what
  they learn.
- **Which leads to:** to adjust knobs you have to know which way to turn each one.
  That's a slope, which is Phase 1, item 1.
- **Payoff later:** Lecture 2's bigram *is* this Markov chain with one letter of
  context, rebuilt with tensors. Lecture 3 opens with this exact explosion (~1%,
  "exponentially with the length of the context") and fixes it with a neural net.

**Lesson 0 exit test:** in your own words, with your measured numbers, explain why
a table can't use much context, and what a neural net does instead.

---

## Phase 1 — micrograd (Lecture 1)

**Goal:** build a tiny autograd engine (the thing inside PyTorch that computes
slopes) from an empty file, then use it to train a tiny neural net.
**Rules:** pure Python. No numpy, no torch (until item 10).

**1. Derivatives without calculus.** ~7-14%, "rise over run"
- Why now: training means "nudge each weight the right way". First you need a way
  to measure which way is right.
- Principle: nudge an input by a tiny `h`, see how far the output moves, and divide
  by `h`. That's the slope. Its sign tells you the direction and its size tells you
  how sensitive the output is.

**2. The `Value` object.** ~14-18%, "class value takes a single scalar"
- Why now: nudging works, but you'd have to re-run the whole expression once per
  input. With a million weights that's hopeless. The expression needs to remember
  how it was built, so it can be walked backwards.
- Principle: wrap each number in an object that records which numbers and which
  operation produced it. Python dunder methods (`__add__`, `__mul__`) make `a + b`
  build that record automatically.

**3. Visualising the graph.** ~18-24%, "visualize"
- Why now: the record from item 2 is a graph, and you can't debug what you can't
  see.
- Principle: a computation is a graph. Data flows forward through it, and later the
  slopes will flow backward through it.

**4. Manual backprop through a tiny graph.** ~25-35%, "base case"
- Why now: you have the graph. Now fill in "how much does the final output care
  about this node?" for every node, by hand, starting at the end.
- Principle: start at the output (its slope with respect to itself is 1.0) and work
  backwards one node at a time. Check each answer by nudging (item 1).

**5. The chain rule.** ~30-45%, "local derivative"
- Why now: doing item 4 by hand reveals a pattern. Each node only needs its own
  local slope and the slope coming back from above.
- Principle: a node's slope = its local slope × the slope arriving from the output
  side. `+` passes the slope through unchanged. `*` sends each input the *other*
  input's value.

**6. One neuron, and `tanh`.** ~37-45%, "squashing"
- Why now: you can backprop through any expression, so it's time to build the
  expression we actually care about.
- Principle: a neuron is a weighted sum plus a bias, pushed through a squashing
  function. Without the squash, stacking neurons is no more powerful than one
  weighted sum.

**7. Automating it: `.backward()`.** ~47-53%, "topological sort"
- Why now: doing backprop by hand doesn't scale. Each operation should know its own
  local rule.
- Principle: each op stores a small `_backward` function. Call them in reverse
  topological order, so a node only runs after every node that uses it has
  finished.

**8. The used-twice bug.** ~54%, "simplest case that shows the bug"
- Why now: your `.backward()` looks right but has a hole in it. Find it by breaking
  it.
- Principle: when a value feeds into two places, it gets slope from both, so
  gradients must be added (`+=`), not overwritten (`=`).

**9. More operations.** ~57-66%, "two times a"
- Why now: a real net needs `exp`, division, powers and subtraction, and you'll
  learn the most by building them yourself.
- Principle: you can implement an op however you like, at any granularity, as long
  as you know its local slope. Splitting `tanh` into pieces gives the same
  gradients.

**10. The same thing in PyTorch.** ~67-70%, "in pytorch"
- Why now: check that your engine agrees with the real one.
- Principle: PyTorch does exactly what you built, just on arrays of numbers
  (tensors) instead of one number at a time, for speed.

**11. Neuron → Layer → MLP.** ~70-77%, "class neuron"
- Why now: you have one neuron. A net is many of them, arranged in layers.
- Principle: a layer is a list of neurons and an MLP is a list of layers. The whole
  thing is still just one big expression made of `Value`s.

**12. Loss and the training loop.** ~77-88%, "learning rate"
- Why now: the net produces outputs, but they're wrong. Turn "how wrong" into one
  number and push it down.
- Principle: loss = one number for how wrong the net is. Then loop: forward, zero
  the grads, backward, nudge every weight a little against its slope.

**13. Karpathy's on-camera bug.** ~88%, "most common neural net mistakes"
- Why now: your loop "works". Find out whether it works for the right reason.
- Principle: gradients accumulate (item 8), so you must reset them every step. A
  net can train on a buggy loop by accident, and easy problems hide bugs.

**Phase 1 exit test:** build a working autograd engine from an empty file, and
explain why backprop needs a topological order.

---

## Phase 2 — makemore (Lectures 2-6)

**Goal:** a character-level language model. It reads 32,000 names, learns to
predict the next letter, and then generates new name-like names. In Karpathy's
words (Lecture 2, ~0%): *"makes more of things that you give it"*.

**Rules:** PyTorch tensors are allowed. No `nn.Module` / `nn.Linear` / optimizers
until you've built that thing by hand. Needs `names.txt` in `my-work/`.

The whole phase is one task: **given some letters, guess the next one.** Each
lecture is a better guesser.

### Lecture 2 — bigram: guess from one previous letter
Why: the simplest possible guesser, so you know what "a model" and "how good is it"
mean before any neural net gets involved. This is lesson 0's Markov chain again,
rebuilt with tensors, plus something lesson 0 didn't have: a single number that
says how good the model is.

1. Load `names.txt`. What a bigram is (a pair of letters). Build the counts matrix.
   ~5-20%, "bigram"
2. Sample names from the counts. ~22%, "multinomial"
3. Broadcasting: how tensors of different shapes combine. It's the #1 source of
   bugs. ~31%, "broadcasting"
4. Score the model: likelihood → log likelihood → negative log likelihood. ~48%,
   "negative log likelihood"
5. Smoothing: never assign zero probability. ~52%, "smoothing"
6. The same model as a neural net: one-hot, matmul, softmax. ~60-75%, "one hot"
7. Train it with gradient descent. It arrives at the counts. Regularization ≈
   smoothing. ~80-96%, "regularization"

Principle: counting and gradient descent find the same answer. The neural net
version wins because it can grow, and counting can't.

### Lecture 3 — MLP: guess from three previous letters
Why: one letter of context gives bad names, and a counts table explodes with more
context (Lecture 3, ~1%: "exponentially with the length of the context"). You
measured that explosion in lesson 0, and this is where it gets fixed.

1. Embeddings: each letter gets a small learned vector. ~5%, "embedding"
2. Hidden layer, and reshaping tensors with `view`. ~10-33%, "view"
3. `F.cross_entropy` and why to use it. ~45%, "cross entropy"
4. Finding a learning rate. Overfitting. ~53%, "learning rate"
5. Minibatches. ~58%, "mini batch"
6. Train / dev / test splits. ~73%, "training split"

Principle: learned vectors let similar letters share what they learn. A low
training loss means nothing until you check it on data the model hasn't seen.

### Lecture 4 — activations, gradients, BatchNorm
Why: deeper nets train badly unless the numbers flowing through them stay
well-behaved (Lecture 4, ~1%: "activations and the gradients").

1. Bad initial loss, and how to fix it. ~4-5%, "confidently wrong"
2. Saturated `tanh` and dead neurons. ~16-20%, "dead neuron"
3. Principled initialisation. ~26-30%, "fan in"
4. BatchNorm and its running mean/std. ~31-55%, "batch normalization"
5. Restructuring the code into layer classes. ~67%, "modules"
6. Diagnostic plots: activation and gradient stats, update-to-data ratio. ~75-97%,
   "update ratio"

Principle: training only works if the activations and gradients stay a sensible
size. Measure them, don't guess.

### Lecture 5 — backprop ninja
Why: `loss.backward()` is a *"leaky abstraction"* (Lecture 5, ~1%). When it goes
wrong, you can only debug it if you can do it yourself.

Manual backprop through the whole MLP with no autograd: every op, then
cross-entropy and BatchNorm as single fused steps. The exercises are laid out at
~10-12%, "exercise one". This consolidates Phase 1, so don't skip it.

Principle: it's the same chain rule as micrograd, applied to whole tensors at
once. The shape of each gradient always matches the shape of what it's the
gradient of.

### Lecture 6 — WaveNet-style deeper model
Why: cramming all the context letters into one layer *"squashes too much
information too quickly"* (Lecture 6, ~1%).

1. Clean up into layer classes, plus `Sequential`. ~11-24%, "sequential"
2. Hierarchical architecture: fuse the letters two at a time, layer by layer.
   ~20-35%, "two characters"
3. How this relates to convolutions. ~34%, "convolution"

Principle: combine information gradually and the net goes deeper without choking.

**Phase 2 exit test:** state the shape of every tensor at every step of the forward
pass and why, and backprop through the MLP by hand.

---

**Then stop.** Transformers are the next course.
