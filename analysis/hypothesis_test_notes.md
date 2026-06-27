# Hypothesis Test Notes

## 1. Business Connection

Leadership needs to decide whether to launch the new onboarding/activation campaign
(Treatment) to all users. The campaign was built specifically to increase **paid
conversion rate**, so that is the metric this test is built around. A statistically
sound answer to "did conversion really improve, or could this be noise?" is the single
piece of evidence the launch decision most depends on — everything else in this project
(KPI tree, guardrails, segment cuts) exists to contextualize this one result, not replace
it.

## 2. Metric Being Tested

**Paid conversion rate** — `converted_to_paid` (1/0) averaged within each experiment
group. Chosen because:
- It is the North Star metric defined for this project (see `README.md`, Section 3).
- It is a binary, well-defined outcome per user, which makes it directly testable with a
  standard proportion test (no ambiguity about what counts as "converted").
- It is the metric the campaign was explicitly designed to move, so it is the most
  business-relevant single number to validate before a launch decision.

## 3. Hypotheses

- **H0 (null hypothesis):** The paid conversion rate is the same for Treatment and
  Control. `p_treatment = p_control`
- **H1 (alternate hypothesis):** The paid conversion rate is higher for Treatment than
  Control. `p_treatment > p_control`

## 4. One-Tailed vs. Two-Tailed

**One-tailed.** The business question is specifically "does the new campaign *improve*
conversion enough to justify launching it?" — not "does it differ in either direction."
A campaign that *decreased* conversion would lead to the same practical decision (do not
launch) as one with no effect, so the relevant test is directional. For transparency, the
two-tailed p-value is also reported alongside the one-tailed result in the workbook, but
the one-tailed value is the basis for the decision rule below.

## 5. Significance Level

**α = 0.05.** This is the conventional threshold for a single confirmatory test on a
primary launch-decision metric, balancing the cost of a false launch (shipping a flow
that doesn't actually help) against the cost of a missed opportunity (not launching a
flow that does help). The significance level is stored as an input cell in the
`Hypothesis_Test` sheet, so the test can be re-run at a stricter threshold (e.g., 0.01) if
leadership wants more conservative evidence before a full rollout.

## 6. Test Used

**Two-proportion z-test** comparing the conversion rate of two independent groups. This
is the standard test for comparing a binary rate between two groups when sample sizes are
reasonably large (here, n = 690 and n = 710 — comfortably large enough for the normal
approximation to hold).

### Test Inputs

| Input | Control | Treatment |
|---|---|---|
| Users (n) | 690 | 710 |
| Converted (x) | 22 | 50 |
| Conversion rate (p) | 3.19% | 7.04% |

### Calculation

```
p_pool   = (22 + 50) / (690 + 710)              = 0.0514
SE       = sqrt(p_pool * (1 - p_pool) * (1/690 + 1/710))   = 0.0118
z        = (0.0704 - 0.0319) / SE               = 3.264
p (one-tailed)  = 1 - Φ(z)                       = 0.0005
p (two-tailed)  = 2 * (1 - Φ(|z|))               = 0.0011
95% CI on the difference (p2 - p1): [+1.56%, +6.15%]
Relative lift: +120.9%
```

(Φ = standard normal CDF. Full live formulas are in the `Hypothesis_Test` sheet of
`outputs/experiment_summary.xlsx`; calculation evidence is captured in
`screenshots/hypothesis_test_output.png`.)

## 7. Decision Rule

```
IF one-tailed p-value < α (0.05):
    REJECT H0 — the data support a real improvement in conversion rate
ELSE:
    FAIL TO REJECT H0 — not enough evidence of an improvement
```

## 8. Result and Output

- **z = 3.264**
- **One-tailed p-value = 0.0005**
- **Decision: REJECT H0.** 0.0005 < 0.05 (and also < 0.01), so the result clears even a
  stricter-than-usual bar for significance.
- The 95% confidence interval for the difference in conversion rate, **[+1.56%, +6.15%]**,
  excludes zero, corroborating the z-test result from a second angle.

## 9. Business Interpretation

With 690 Control and 710 Treatment users, paid conversion rose from 3.19% to 7.04% — more
than double. The probability of seeing a gap this large if the new campaign had no real
effect is about 0.05% (1 in ~2,000), so this is not a result that is plausibly explained
by random variation in who happened to land in each group. **The campaign has a real,
positive, and large effect on paid conversion.**

This result on its own would support a launch decision. However, per the project's KPI
tree, a North Star win is necessary but not sufficient — it must be checked against
guardrail metrics (refund rate, support ticket rate, revenue per converted user,
segment-level consistency) before a final recommendation is made. That guardrail review is
in `outputs/recommendation_memo.md`, and it is the reason the final recommendation
includes specific monitoring conditions rather than an unconditional "launch and move on."
