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
- **Phase 1, item 3** — done. See [[03-visualise-graph]]. Explained back: the full slope chain, with `a` = 6 and `b` = -4 worked out yourself.
- **Phase 1, item 4** — next: add `.grad` to `Value`, set each slope by hand, see it in `draw_dot`, check each one by nudging.

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

### Session 6, continued — item 3 started
- Graphviz `dot` installed (from outside Claude Code: `!` can't answer sudo's password prompt).
- Built in 03: `Value` with a `label` that `+` / `*` fill in automatically from the children's labels. Built `L = (a*b + c) * f` = -8.0 and drew it with `draw_dot(L)`.
- Bug: the auto label `a*b+c*f` drops the brackets. In [[gotchas]].
- Not done yet: section 4's question, section 5 (break it), the findings explain-back.
- Next: those, then explain item 3 back, then item 4 (manual backprop through this exact graph).

## Revisit later
- `traverse_graph`: drop the dead `is not None` check. Still there in notebook 03.
- Item 3, section 4: which box does item 4 fill in first, and which last?
- Flat spot of `f`: you bracketed it between 0.5 and 0.8 but never pinned it down. Narrow it by nudging (hint: it's a simple fraction).
- Where did the +39,970 prediction for `a`'s slope come from? Never explained.
- Unanswered leading question: why can the generator never land on an unseen context? (5,000 names, zero errors.) Hint: `(*current_context[1:], next_char)`.
- At large N, short names produce no N-grams at all (`range(len(word) - (N - 1))`). How many names does N = 9 silently drop?

## Not yet installed
- PyTorch CPU wheel: needed at item 10.

### Session 6, continued — item 3
- Built: `label` on `Value`, with `__add__`/`__mul__` building bracketed labels like `((a*b)+c)` automatically. Built `L = (a*b + c) * f` and drew it with `draw_dot`: -6, 4, -8, correct.
- Breaks: break 1 (`draw_dot(e)` shows only `a`, `b`) and break 3 (one `a` box, two arrows out) both right.
- Wrong: (1) Section 4 order. You tied it to `*` vs `+` and to "check if the loss improves". This repeats the session 5 gate mistake. The backward pass only fills in slopes, starting from `L`. (2) Break 4. You predicted `L` recomputes when `a.data = 100`, but nothing re-runs. This repeats notebook 02. You didn't see the actual result because cells ran out of order. All three are in [[gotchas]].
- Open: why `e` can't see downstream boxes, `a * a` (one box, one arrow, because edges are a set; watch for this in item 6), tree vs graph, the stale label comment in cell 6, and the findings cell.
- Claude added a review cell to notebook 03. Your code is unchanged.
- Next: the open items above, explain item 3 back, then item 4 (manual backprop).

### Session 7 — 2026-11-24
- You said "all done" on notebook 03. Reviewed it. Not done yet.
- Fixed: break 4 (`L` stays -8, only `a`'s box changes; rebuild the expression to get a new `L`). Stale label comment in cell 6.
- Section 4: "start from the loss, flow parent to child". Right direction, but it doesn't name the first box (`L`) or the last ones (the leaves), say what goes in `L`'s box, or answer "can you get `e`'s slope before `d`'s?".
- Findings cell: says slopes travel **left to right**. Wrong: data goes left to right, slopes go right to left. The same paragraph also says "parent to child", which is right, so it contradicts itself. In [[gotchas]]. It also doesn't say what an arrow means.
- Still unanswered: break 1 *why* (which way does `_prev` point?), break 2 (how many `a` boxes and arrows; you noted the edge set but didn't say what it does), break 3 (tree or not?).
- Cells 10 and 12 were saved without outputs, so no prediction was checked against a picture. All breaks are still in one cell.
- Next: those, explain item 3 back, then item 4 (manual backprop).
- Recheck: findings cell now says right to left (fixed). Section 4 now names `L` first and `a` last (`b` is last too). Breaks cell, `L`'s box value, "`e` before `d`?", and what an arrow means: still unchanged.
- Round 3: `L`'s box = 1 (nudge `L` by 0.001, it moves 0.001). Right. Arrow = data flow. Right. Break 3: "not a tree" is right, but your reason cited `g = a * a` (break 2, which draws one arrow). The right example is `h`: `a` feeds two ops, so it has two parents. Claude added your answers to notebook 03 with wording fixed, split the breaks into one cell each, and replaced the review cell. Still open: `e` before `d`?, break 1 why, break 2 run, run the breaks.
- Round 4: break 1 why: right. Break 2 ran (one `a` box, one arrow in), but the wrong prediction (two boxes) isn't noted and the edge-set question is still open. Breaks 3 and 4 match. **Wrong:** "`e`'s slope before `d`'s? Yes", with `c`'s slope = 1 (it's -2; this skipped `d` and the `* f`). In [[gotchas]]. Sent to run `c = 10.001` and measure. Explain-back for item 3 is still pending.
- Round 5: "Yes" changed to "No", but the reasoning underneath still says `c`'s slope = 1, and the `c = 10.001` experiment wasn't run. New slip: for `L = d * f`, wrote "set one's `.grad` to the other's `.grad` × L's slope". It's the other's **data** (`d`'s slope = `f.data` × `L`'s slope). Watch out: `a.data` is 100 after break 4, so any rebuild has to use fresh `Value`s. Cell 10 label comment and break 2 are still unchanged.
- Round 5, continued: at your request Claude wrote out the corrected reasoning in cell 10 (slopes: L 1, d -2, f 4, c -2, e -2) and the break 2 answer. This was the last-resort step, so the answer was given, not worked out. Your mistakes are quoted inline. Also in [[gotchas]]. `a`'s and `b`'s slopes are left for item 4. Next: explain item 3 back.
- Explain-back (spoken): walked the whole chain right to left and got `a` = 6 and `b` = -4 on your own (ahead of item 4). Right. Gaps: "set the other side's data" leaves out "× the incoming slope" (it was × 1 at `L`, so it didn't show), and no reason given for why `+` passes -2 through unchanged. Asked for both before marking item 3 done.
- Final explain-back: `a`'s slope = `b.data` (-3) × `e`'s slope (-2). `+` passes -2 through because nudging `c` by h moves `d` by exactly h (local slope 1). Passed. Item 3 done. Note: [[03-visualise-graph]].
- Next: item 4, manual backprop with `.grad` in code.

### Session 7, continued — item 4 started
- Committed and pushed lesson 3 (`49962f2`).
- Scaffolded `my-work/04-manual-backprop.ipynb`: add `grad`, rebuild the graph, a nudge checker (`lol()`), fill the grads right to left as expressions, one step up, five breaks.
