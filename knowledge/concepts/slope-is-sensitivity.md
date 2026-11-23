# A slope is a sensitivity

**What this is:** what the number from "nudge and measure" actually means.
**Why it matters:** every gradient in the course is one of these. If you can read a slope, you can read `a.grad`.
**After reading:** given a slope, you can say which way the output moves and by roughly how much.

---

**In my words:** the slope is the rate of change per push.

Slope = output change **per 1 unit** of push. It's a rate, like a price per item.

- **Sign = direction.** You always push the input the same way (up). Positive: the output went up too. Negative: the output went down.
- **Size = how sensitive.** Slope 14: the output moves about 14 times as far as the input. Slope 0.1: the output barely cares.
- **Rise for a given push:** `rise ≈ slope * h`. Slope 14 and h = 0.001 give a rise of about 0.014.

Slope 0 means flat: a bottom (or top) of the curve. Training looks for bottoms.

You measure with a tiny push because curves bend. A big push measures the bend as well as the slope ([[step-size-h]]).

Used in: [[01-what-is-a-derivative]]. Next: [[slope-of-a-product]], and later the chain rule.
