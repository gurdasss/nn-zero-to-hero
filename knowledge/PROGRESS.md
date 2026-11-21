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
- **Phase 1, item 1** — derivatives without calculus: `my-work/01-nudge-a-number.ipynb`. Sections 1-3 built, 4-5 empty.
- Next: the **"Pick up here"** cell in notebook 01 (between sections 3 and 4). It has three open questions. Answer them, explain section 3 back, then do section 4.

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

## Revisit later
- Unanswered leading question: why can the generator never land on an unseen context? (5,000 names, zero errors.) Hint: `(*current_context[1:], next_char)`.
- At large N, short names produce no N-grams at all (`range(len(word) - (N - 1))`). How many names does N = 9 silently drop?

## Not yet installed
- System Graphviz (`sudo apt install -y graphviz`): needed at item 3.
- PyTorch CPU wheel: needed at item 10.
