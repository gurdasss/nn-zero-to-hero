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
- Next: **Phase 1, item 1** — derivatives without calculus: `my-work/01-nudge-a-number.ipynb` (`f(x)` done, sections 2-5 empty).

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

## Revisit later
- Unanswered leading question: why can the generator never land on an unseen context? (5,000 names, zero errors.) Hint: `(*current_context[1:], next_char)`.
- At large N, short names produce no N-grams at all (`range(len(word) - (N - 1))`). How many names does N = 9 silently drop?

## Not yet installed
- System Graphviz (`sudo apt install -y graphviz`): needed at item 3.
- PyTorch CPU wheel: needed at item 10.
