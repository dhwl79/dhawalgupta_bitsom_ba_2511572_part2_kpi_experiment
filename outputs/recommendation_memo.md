# Recommendation Memo: Onboarding & Activation Campaign

**To:** Leadership / Growth Steering Committee
**From:** Business Analytics
**Re:** Decision on whether to launch the new onboarding/activation campaign (Treatment)
to all users

---

## Executive Summary

The new onboarding and activation campaign (Treatment) **more than doubled paid
conversion rate** versus the existing experience (Control) — 7.04% vs. 3.19%, a +120.9%
relative lift, statistically significant (z = 3.26, one-tailed p = 0.0005). The effect is
broad-based: every upstream funnel stage (landing page visits, trial starts, onboarding
completion) improved, users engaged more, and converters converted faster. The one
guardrail with strong statistical evidence of a negative shift is **support ticket
rate**, which rose from 14.8% to 24.8% — a real, broad-based increase that should be
resourced, not ignored. Refund rate and revenue-per-converted-user moved in directions
worth watching but are not statistically strong enough on this sample to outweigh the
primary result.

**Recommendation: Launch**, conditional on a support-capacity plan and continued
guardrail monitoring (see Section 8).

---

## 1. Business Problem Recap

The product team tested a new onboarding and activation flow (Treatment) against the
existing flow (Control) to determine whether it should become the default experience for
all new signups. The decision affects new users' first-30-day experience, the
Growth/Revenue team's conversion targets, Customer Support's ticket load, and Finance's
exposure to refunds and revenue quality. Full problem statement: `README.md`, Section 1.

## 2. North Star Metric

**Paid conversion rate** (`converted_to_paid` ÷ total users) was selected as the North
Star metric because it is the direct, business-relevant outcome the campaign was funded
to move — not a leading indicator of it. Full reasoning: `README.md`, Section 3.

## 3. KPI Tree Explanation

The KPI tree (`outputs/kpi_tree.png`) breaks the North Star into three drivers —
**Top-of-Funnel Activation** (landing page visit rate, trial start rate), **Onboarding
Effectiveness** (onboarding completion rate, engagement score), and **Conversion
Efficiency & Quality** (days to convert, revenue per converted user) — plus four
**guardrails** (refund rate, support ticket rate, revenue per converted user, segment
consistency) that exist purely to catch side effects, not to be optimized directly. This
structure is what shapes the rest of this memo: Sections 4–5 evaluate the drivers, and
Section 6 evaluates the guardrails, before a recommendation is made.

## 4. Experiment Result Summary

*(Cleaned data: n = 690 Control / 710 Treatment. Full table:
`outputs/experiment_summary.xlsx`, `Summary` sheet; screenshot:
`screenshots/summary_metrics.png`.)*

| Metric | Control | Treatment | Relative Lift |
|---|---|---|---|
| User count | 690 | 710 | — |
| Landing page visit rate | 63.6% | 72.4% | +13.8% |
| Trial start rate | 25.1% | 29.0% | +15.7% |
| Onboarding completion rate | 15.7% | 21.1% | +35.0% |
| **Paid conversion rate (North Star)** | **3.2%** | **7.0%** | **+120.9%** |
| Average revenue per user | $51.97 | $54.25 | +4.4% |
| Average revenue per converted user | $1,630.10 | $770.41 | −52.7% |
| Refund rate | 0.00% | 0.42% | n/a (from zero) |
| Support ticket rate | 14.8% | 24.8% | +67.7% |
| Average engagement score | 57.0 | 62.9 | +10.4% |
| Average days to convert | 8.9 days | 6.4 days | −27.8% (faster) |

Every funnel stage improved in Treatment, and the improvement compounds going down the
funnel (+13.8% at the top, +120.9% by the time users convert) — consistent with a flow
that doesn't just attract more lookers, but actually moves more of them all the way
through.

## 5. Hypothesis Test Interpretation

A one-tailed two-proportion z-test on paid conversion rate rejects the null hypothesis of
no difference: **z = 3.26, p = 0.0005**, well below the α = 0.05 threshold (and below
α = 0.01). The 95% confidence interval for the lift, **[+1.56 pp, +6.15 pp]**, excludes
zero. The probability of seeing a gap this large with no real underlying effect is about
1 in 2,000 — this is not noise. Full detail: `analysis/hypothesis_test_notes.md`;
calculation evidence: `screenshots/hypothesis_test_output.png`.

## 6. Guardrail Analysis

A conversion win is not evaluated in isolation. Four guardrails were checked:

| Guardrail | Result | Statistically significant? | Verdict |
|---|---|---|---|
| **Refund rate** | 0.00% → 0.42% | No (p ≈ 0.09) | Watch, not a blocker — too small a sample of refund events to distinguish from zero |
| **Support ticket rate** | 14.8% → 24.8% | **Yes** (p < 0.001) | **Real risk** — broad-based increase (consistent across all four regions tested), implies higher support cost per cohort |
| **Revenue per converted user** | $1,630 → $770 | Borderline (p ≈ 0.08) | Watch — consistent with converting a broader mix of Free/Basic-tier users rather than a data problem (every high-revenue row checked back to a genuine converted user) |
| **Engagement score** | 57.0 → 62.9 | Yes (p < 0.001), favorable | Not a risk — supports the mechanism behind the conversion lift |
| **Days to convert** | 8.9 → 6.4 days | Yes (p ≈ 0.008), favorable | Not a risk — faster activation, good for cash flow and LTV timing |

**The only guardrail with strong evidence of a negative shift is support ticket rate.**
This is an operational cost to plan for (staffing, macros, FAQ content for the new flow),
not evidence that the new flow harms users or revenue. Refund rate and revenue quality
should be watched post-launch but do not currently clear the bar to block a launch.

## 7. Segment-Level Insight

*(Full breakdown: `outputs/experiment_summary.xlsx`, `Segment_Analysis` sheet.)*

- **Region:** all four regions (North, South, East, West) show a positive conversion
  lift in Treatment, ranging from roughly +50% to +160% relative improvement.
- **Device type:** Desktop, Mobile, and Tablet all improve; Mobile (62% of users) shows
  one of the largest lifts.
- **Traffic source:** four of five sources (Email, Organic, Paid Search, Referral) show
  large positive lifts. **Social traffic is the one segment where Treatment's raw
  conversion rate is lower than Control's** (6.06% vs. 7.75%) — but this is based on only
  10 vs. 8 conversions in that slice and is **not statistically significant** (p ≈ 0.59).
  It reads as sampling noise in an underpowered subgroup, not a true reversal, but is
  worth re-checking with more data before assuming it will scale the same way as other
  channels.
- **Plan type:** Free and Premium users show strong lifts; Basic-plan users show only a
  small, non-significant lift (p ≈ 0.88) — likely underpowered at this sample size rather
  than evidence the campaign doesn't work for that tier.
- **Support ticket rate increase is not isolated to one segment** — it rises in every
  region checked, indicating it's a property of the new flow itself rather than a
  one-off cohort issue.

## 8. Final Recommendation

### Launch

Launch the Treatment experience as the default onboarding/activation flow for all new
users, **conditional on**:
1. Customer Support sizing additional capacity (or updating macros/FAQs) ahead of full
   rollout, given the statistically confirmed rise in ticket rate.
2. Refund rate and revenue-per-converted-user being tracked monthly for at least one
   full quarter post-launch, since both moved in a direction worth watching even though
   neither is statistically conclusive yet.
3. A light follow-up check on the Social traffic source segment once more data
   accumulates, to confirm the small reversal there was noise and not an early signal.

This is a "launch with conditions" rather than an unconditional launch because the
guardrail review surfaced one real risk (support cost) that should be resourced, not
because the core result is in doubt — the North Star result is large, statistically
significant, and consistent across nearly every cut of the data.

### Why not the other options
- **Do not launch** is not supported: the primary effect is large, significant, and
  consistent across segments; no guardrail shows a severe enough regression to justify
  forgoing the conversion gain.
- **Launch only for selected segments** was considered given the Social-traffic and
  Basic-plan softness, but neither is statistically distinguishable from the overall
  effect — scoping the launch on noise would forgo most of the upside for no proven
  benefit.
- **Continue testing** was considered, but the primary test is already well-powered
  (p = 0.0005, comfortably below threshold); further delay mainly defers a confirmed gain
  while support-readiness work could be happening in parallel.

## 9. Risks and Limitations

- Revenue-per-converted-user and refund-rate guardrails are based on a small number of
  converted users (22 Control / 50 Treatment), so their p-values are borderline; a launch
  should not be treated as "case closed" on revenue quality — it should be monitored with
  fresh data.
- The experiment covers five months (Jan–May 2025) for one product; seasonal effects or
  other products are not validated by this data.
- Segment-level conclusions (Social traffic, Basic plan) are based on small subgroup
  sample sizes and should be treated as hypotheses to keep watching, not settled findings.
- Group assignment is assumed random; balanced segment distribution between groups
  supports this but cannot fully rule out unmeasured confounds.

## 10. Next Steps

1. Brief Customer Support on expected ticket-volume increase and agree on a staffing/
   content plan before full rollout.
2. Launch Treatment to 100% of new signups, retaining the ability to roll back quickly if
   guardrails move further during full-scale rollout.
3. Stand up a monthly guardrail dashboard (refund rate, support ticket rate, revenue per
   converted user) using the same definitions as `outputs/experiment_summary.xlsx`.
4. Re-run the Social-traffic and Basic-plan segment cuts after 60–90 days of full-scale
   data to confirm whether those patterns persist or were sampling noise.
