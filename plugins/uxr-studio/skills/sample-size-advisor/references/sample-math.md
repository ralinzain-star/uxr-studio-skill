# Sample math reference

## Proportion tests (experiments), 80% power, 95% confidence, two-sided

| Baseline | Absolute lift to detect | Approx. n per arm |
|---|---|---|
| 40% | 5 points | ~1,500 |
| 40% | 3 points | ~4,200 |
| 40% | 2 points | ~9,500 |
| 20% | 5 points | ~1,000 |
| 20% | 2 points | ~5,800 |
| 10% | 2 points | ~3,400 |
| 5% | 1 point | ~6,700 |

Always report the n for the stated effect and for one effect half that size, so the team sees
the cost of precision before committing.

## Surveys reporting a proportion

| Margin of error (95% confidence, worst case p=0.5) | Completes per reported segment |
|---|---|
| ±10 points | ~96 |
| ±7 points | ~196 |
| ±5 points | ~384 |
| ±3 points | ~1,067 |

Every segment you plan to cut by multiplies this. Decide the cuts before fielding, not after.

## Completes to invites

| Channel | Typical rate | Multiply completes by |
|---|---|---|
| Emailed survey | 2–5% | 20–50x |
| In-product intercept | 5–15% | 7–20x |
| Panel survey (`~~recruiting panel`) | 20–40% | 2.5–5x |
| Scheduled interview (show rate) | 70–85% | 1.2–1.4x |
| Diary study (completion through final entry) | 70–80% | 1.25–1.4x |

Recruit to the invite number, not the complete number. Over-recruit diary studies by at least
25% and schedule one float slot per interview day.

## Continuous outcomes

For a difference in means, n per arm is roughly 16 divided by the squared standardized effect
(difference divided by standard deviation), at 80% power and 95% confidence. A 0.3 standard
deviation difference needs roughly 175 per arm; 0.2 needs roughly 390.

## Cluster and repeated measures

If participants contribute multiple observations (repeated sessions, repeated tasks, repeated
uses of the core action), the unit of analysis is the participant, not the observation. Do not
inflate n by counting observations.
