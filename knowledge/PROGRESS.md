# Progress

**What this is:** where the course stands right now. Read first, update last, every session.
**After reading:** you know the current lesson and what to do next.

Full map: [[PLAN]].

## Where to find things
- Transcripts: `transcripts/lectureN-transcript.txt` (N = 1-6). Single line each, no timestamps.
- Karpathy's notebooks: `lectures/micrograd/`, `lectures/makemore/` (read-only).
- My notebooks: `my-work/`. Python: `.venv/bin/jupyter lab`.

## Current position
- **Lesson 0** — done. See [[00-markov-chain]].
- **Phase 1, item 1** — done. See [[01-what-is-a-derivative]].
- **Phase 1, item 2** — done. See [[02-value-object]]. Explained back in full.
- **Phase 1, item 3** — visualising the graph: `my-work/03-visualise-graph.ipynb` scaffolded (Claude wrote `draw_dot` as a drawing helper). You add `label` and build `L = (a*b + c) * f`. Drawing needs the system Graphviz binary (see Not yet installed).

## Goal and preferences (agreed in session 2)
- Goal: understand what a neural net is and why we need one. You've used Markov chains before.
- Lesson 0 prologue, with the payoff in Lectures 2-3. Refresher as a tiny notebook you build.
- Depth: the full course, to "can build and debug one".
- Pace: small notebooks, one idea each, with frequent explain-it-back checks.

## Session log

### Session 1 — 2026-11-19
- Set up workspace: venv, `.gitignore`, folders, `PLAN.md`.
- Started Phase 1 item 1.
- Built: —
- Stuck on: —
- Next: finish item 1, explain it back, then item 2 (the `Value` object).

### Session 2 — 2026-11-20
- Rewrote [[PLAN]] to give context: the destination, the course in three sentences, how the pieces connect, and a "why now" and "principle" for every lesson. Item numbering is unchanged. Search phrases for Lectures 2-6 checked against the transcripts.
- Rewrote the markdown in `01-nudge-a-number.ipynb` so each section says how it connects to training a net. Code cells untouched.
- Built so far in 01: `f(x)`. Sections 2-5 still empty.
- Then set preferences (see above) and added Lesson 0 (Markov chain prologue) to [[PLAN]]. Created `00-markov-chain.ipynb` skeleton and downloaded `names.txt`.
- Next: do lesson 0, explain it back, then finish notebook 01.

### Session 3 — 2026-11-21
- Finished Lesson 0. Built a character N-gram model (N = 2..6) and measured possible / seen / forced contexts and the copy rate per N.
- Finding: big N doesn't make better names, it makes copies (85% at N = 6), because the table explodes (×27 per letter) and the filled rows go forced. Notes: [[00-markov-chain]], [[n-gram-sparsity]], [[sharing-across-contexts]].
- Stuck on: Q2 (why treating `ann`/`enn` as unrelated is wasteful). It clicked with real counts for `ann`/`enn`/`unn`/`ynn`.
- Bugs: capitalised output vs lowercase data, wrong denominator, `zzq` test at the wrong N (shape mismatch). All in [[gotchas]].
- Section 6 answers were polished by Claude at your request. The ideas are yours, the examples were added.
- Next: Phase 1 item 1, finish `01-nudge-a-number.ipynb`.

### Session 4 — 2026-11-21
- Bridge from lesson 0 to Phase 1: counting works when each knob belongs to one context. Shared knobs get conflicting counts, so you nudge toward the best compromise. Note: [[shared-knobs-cant-be-counted]] (the one-liner is yours).
- Stopped mirroring to Obsidian. `CLAUDE.md` updated: everything stays in this repo.
- Built in 01: section 2 (plot, with a `while` loop stepping 0.25) and section 3 (rise over run at x = 3, -3, 0.5, 0.8). Slopes measured: +14.003 at x = 3, -21.997 at x = -3.
- Stuck on / open (all in the notebook's "Pick up here" cell):
  1. The flat spot: 0.5 (slope -1) undershoots and 0.8 (slope +0.8) overshoots. It's in between.
  2. Why 14.003 and not 14? You said floating point. It isn't: the error shrinks with h. Run the experiment.
  3. Which way to turn the knob? You said "push and check". The point is to read it from the slope's sign without re-measuring.
- Bugs: mixing up slope (a rate) with rise (`slope * h`), and calling a negative slope's size negative. Both in [[gotchas]]. Claude rewrote the section 3 comments to fix the wording; the code is yours.
- Side finding: stepping by 0.25 lands exactly on 5.0, stepping by 0.1 doesn't (`4.999999999999997`), because 0.25 is stored exactly and 0.1 isn't. Connects to section 4.
- Next: the three open questions, explain-back, section 4 (tiny h), section 5 (three inputs).

### Session 5 — 2026-11-23
- Finished notebook 01: section 4 (tiny h) and section 5 (three inputs).
- Explained back: slope = rate of change per push, and h can't be tiny because floats are finite. Both right in spirit. Corrected: `h` is the push, the slope is per **1 unit**; the sign comes from the output's change, not x's; and too-small h fails because the rise becomes rounding noise (`3.0 + 1e-20 == 3.0`).
- Bug: section 5 first used `slope_at_x` (which nudges `f`) and fed its slopes into `g` as inputs. Fixed with one function per input that calls `g` twice. In [[gotchas]].
- Stuck on: why nudging `b` moves `d` by `a`. Clicked with the shopping-bill picture and whole-number pushes. Then got `a`'s slope (-3) on your own.
- Notes: [[01-what-is-a-derivative]], [[slope-is-sensitivity]], [[step-size-h]], [[slope-of-a-product]].
- Next: item 2, the `Value` object.

### Session 5, continued — item 2 started
- Committed lesson 1 (`dd605fb`). Wrote [[python-dunder-methods]], [[02-value-object]] and scaffolded notebook 02.
- Big-picture questions: why `Value`? (Keep the receipt so slopes can be read off the formula, not re-measured.) Then the training loop. Your summary was close, with two corrections: no "if the loss is bad" gate, and the update moves *against* the slope by a small step, not "apply the slope". Note: [[forward-backward-update]]. Your analogy: the graph is like an AST (a graph, really, since a value can be reused).
- Built: `Value` with `data`, `_prev`, `_op`, `__repr__`, `__add__`, `__mul__`, and a recursive `traverse_graph`. All in one cell (jumped ahead).
- Bugs: `_prev = None` for leaves, fixed to `()`. Section 6 answers written as guesses, 3 of 4 wrong. Both in [[gotchas]]. Claude fixed stale comments in notebook 02 at your request.
- Answered: the math happens once, when `y` is created.

### Session 6 — 2026-11-24
- Finished item 2. Fixed `traverse_graph` so it prints each node's own op. Answered section 6: `a + 1` fails on `other.data` (`other` is `1`); `1 + a` tries `int.__add__`, then `a.__radd__`, then raises.
- Explained item 2 back: a float loses how it was made, a `Value` keeps the receipt. Right. Didn't say what backprop uses it for.
- Slips: cited the `*` line for `a + 1` (it's the `+` line in `__add__`); wrote `NotImplementedError` for `NotImplemented`. In [[gotchas]].
- Claude tidied notebook 02 at your request: markdown, Q&A layout, explain-back moved into a text cell. Your code and answers unchanged.
- Graphviz: pip package already in `.venv`; system binary not installed.
- Explain-back, round 2: nudging = one re-run per weight; the receipt lets you walk back from the loss through the ops in order. Passed. You said "apply each weight's slope" again: the update moves *against* the slope, by a small step ([[forward-backward-update]]).

## Revisit later
- `traverse_graph`: drop the dead `is not None` check.
- Flat spot of `f`: you bracketed it between 0.5 and 0.8 but never pinned it down. Narrow it by nudging (hint: it's a simple fraction).
- Where did the +39,970 prediction for `a`'s slope come from? Never explained.
- Unanswered leading question: why can the generator never land on an unseen context? (5,000 names, zero errors.) Hint: `(*current_context[1:], next_char)`.
- At large N, short names produce no N-grams at all (`range(len(word) - (N - 1))`). How many names does N = 9 silently drop?

## Not yet installed
- System Graphviz binary (`dot`): needed at item 3. The pip `graphviz` 0.21 is already in `.venv`, but it only calls `dot` and can't render without it. You asked for venv-only installs, so this is your call: `! sudo apt install -y graphviz`.
- PyTorch CPU wheel: needed at item 10.
