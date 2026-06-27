# Onboarding & Activation Campaign — Experiment Analysis

A business analyst project evaluating whether a subscription product's new onboarding and
activation campaign ("Treatment") should replace the existing onboarding experience
("Control") for all users.

---

## 1. Business Problem Statement

**Decision to be made:** Whether to launch the new onboarding/activation campaign
(Treatment) to all users, reject it, launch it only for selected segments, or continue
testing — replacing the existing onboarding experience (Control) as the default flow for
new signups.

**Who the decision impacts:**
- **New users / signups**, whose first-30-day experience (landing page, trial, onboarding,
  conversion) changes directly.
- **Revenue & Growth team**, who own the paid-conversion and revenue targets this campaign
  is meant to move.
- **Customer Support / Success team**, who absorb any change in support-ticket volume.
- **Finance**, who care about refund exposure and revenue quality (not just unit count of
  conversions).
- **Product/Engineering**, who would need to maintain the new flow at scale if it launches.

**What metric should improve:** **Paid conversion rate** (share of new users who become
paying customers) is the primary metric the campaign is designed to move. Supporting
funnel metrics (landing page visit rate, trial start rate, onboarding completion rate) and
quality/engagement metrics (engagement score, revenue per user) are expected to improve
alongside it.

**What risks must be monitored:** A higher conversion rate is not sufficient justification
to launch on its own. The following guardrails must not regress in a way that offsets the
gain:
- Refund rate (are the new conversions "sticky," or do they reverse?)
- Support ticket rate (is the new flow creating confusion or extra service cost?)
- Revenue per converted user (is the new flow converting users into lower-value customers?)
- Segment-level consistency (does any region, device, traffic source, or plan type move
  in the *opposite* direction, implying the campaign should be scoped rather than launched
  broadly?)

**What evidence is required before making a recommendation:**
1. A clean, de-duplicated dataset with documented handling of missing values and outliers.
2. A descriptive comparison of Control vs. Treatment across the full funnel and guardrail
   metrics.
3. A formal hypothesis test on the primary metric (paid conversion rate) with a p-value
   and an explicit decision rule, not just an eyeballed percentage difference.
4. A guardrail review showing whether any risk metric moved against the business in a
   material, evidence-backed way.
5. A segment-level breakdown to check whether the effect is consistent or concentrated in
   particular regions, devices, traffic sources, or plan types.

This is summarized further in `outputs/recommendation_memo.md`.

---

## 2. Dataset Description

Source file: `data/campaign_experiment_data.xlsx`, sheet `experiment_data`
(business definitions are documented in the accompanying `business_context` sheet).

- **1,408 raw rows**, one row per user, randomly assigned to **Control** (existing
  onboarding) or **Treatment** (new campaign) at signup, between 2025‑01‑01 and
  2025‑05‑31.
- **16 columns** covering: identifiers (`user_id`, `signup_date`), assignment
  (`experiment_group`), segments (`region`, `device_type`, `traffic_source`,
  `plan_type`), funnel events (`visited_landing_page`, `started_trial`,
  `completed_onboarding`, `converted_to_paid`), and outcomes
  (`revenue_30d`, `support_tickets_30d`, `refund_requested`, `days_to_convert`,
  `engagement_score`).
- After cleaning (Section 4), the analysis dataset has **1,400 rows**:
  **690 Control / 710 Treatment**.

---

## 3. North Star Metric Selected

**North Star Metric: Paid Conversion Rate** (`converted_to_paid` users ÷ total users in
the group).

- **Why it's the main success metric:** it is the closest measurable proxy to the
  business outcome the campaign was funded to drive — turning a signup into a paying
  customer. Every funnel metric upstream (landing page visit, trial start, onboarding
  completion) is a leading indicator of this outcome, not the outcome itself.
- **Why other metrics are supporting, not North Star:** landing page visit rate, trial
  start rate, and onboarding completion rate measure *progress toward* conversion but can
  improve without producing a single additional paying customer (e.g., more people look,
  fewer buy). Revenue and engagement metrics matter, but they are *quality checks* on
  conversion, not substitutes for it — a small number of converted users with very high
  revenue could mask a campaign that fails to grow the *customer base*, which is the
  longer-run growth lever.
- **How it connects to business growth:** paid conversion rate, multiplied by signup
  volume, is the direct driver of new-customer count and, downstream, recurring revenue
  and LTV. Improving it is one of the few growth levers that does not require increasing
  top-of-funnel spend.
- **What could go wrong if optimized blindly:** a team could inflate conversion by
  discounting aggressively, lowering plan quality, or pressuring users into trials they
  later abandon or refund. That is exactly why this metric is never evaluated alone —
  Section 5 below pairs it with guardrails (refund rate, support cost, revenue quality,
  segment consistency) so a conversion "win" that is actually a quality or cost problem
  gets caught before launch.

---

## 4. KPI Tree Summary

Full diagram: `outputs/kpi_tree.png` (preview also in `screenshots/kpi_tree_preview.png`).

```
North Star: Paid Conversion Rate
├── Driver 1 — Top-of-Funnel Activation
│   ├── Landing page visit rate
│   └── Trial start rate
├── Driver 2 — Onboarding Effectiveness
│   ├── Onboarding completion rate
│   └── Average engagement score
├── Driver 3 — Conversion Efficiency & Quality
│   ├── Average days to convert
│   └── Average revenue per converted user
└── Guardrails (monitored, not optimized)
    ├── Refund rate
    ├── Support ticket rate
    ├── Revenue per converted user (revenue quality)
    └── Segment-level consistency
```

The tree separates metrics the team should actively try to move (drivers, all the way up
to the North Star) from metrics that exist purely to catch unintended side effects
(guardrails) — the same revenue-per-converted-user figure appears as both a Driver 3
sub-metric *and* a guardrail because it is informative about funnel efficiency **and**
about whether the new conversions are worth as much as the old ones.

---

## 5. Experiment Analysis Approach

1. **Clean the raw data** (`analysis/experiment_analysis.xlsx`): check missing values,
   group counts, duplicate user IDs, invalid binary values, revenue outliers, and segment
   balance across groups. See Section 6 below for what was found and how it was handled.
2. **Build a Control vs. Treatment summary** (`outputs/experiment_summary.xlsx`,
   `Summary` sheet) across all 11 required metrics, using live formulas against the
   cleaned dataset.
3. **Break out at least 3 metrics by segment** (`Segment_Analysis` sheet): paid
   conversion rate and support ticket rate by region, device type, traffic source, and
   plan type; engagement score and refund rate by region.
4. **Run a formal hypothesis test** on the primary metric (`Hypothesis_Test` sheet) —
   see Section 6.
5. **Evaluate guardrails** independently of the headline conversion result — see
   Section 7.
6. **Synthesize into a recommendation** (`outputs/recommendation_memo.md`).

### Data cleaning findings (`analysis/experiment_analysis.xlsx`)

| Check | Finding | Handling |
|---|---|---|
| Group counts | 693 Control / 715 Treatment (raw) | Reconciles to total rows; no unassigned group labels |
| Duplicate user IDs | 8 user IDs appeared twice, and each pair was an **exact full-row duplicate** (not just the ID) | Kept one copy of each, dropped the 16th→8 redundant rows. Cleaned dataset = 1,400 rows |
| Missing values | `device_type` (18), `traffic_source` (24), `engagement_score` (14), `days_to_convert` (1,336) | `device_type`/`traffic_source` recoded to `"Unknown"` rather than dropped; `engagement_score` left blank (Excel `AVERAGE`/`AVERAGEIF` ignore blanks); `days_to_convert` missing is **expected** — only converted users have a conversion date, this is not a data quality issue |
| Invalid binary values | 0 invalid entries found across all 5 binary flag columns (`visited_landing_page`, `started_trial`, `completed_onboarding`, `converted_to_paid`, `refund_requested`) | No action needed |
| Outliers in revenue | 18 rows with `\|z-score\| > 3` on `revenue_30d` (max $8,610.72) | Confirmed every high-revenue row belongs to a genuine converted user (`converted_to_paid = 1`, revenue > 0 in 100% of cases) — retained as real data, not removed; flagged as a guardrail input rather than an error |
| Segment distribution | Region, device type, traffic source, and plan type are balanced within a few percentage points between Control and Treatment | Confirms randomization was not compromised; supports a clean group comparison |

---

## 6. Hypothesis Test Summary

Full detail and worked calculation: `analysis/hypothesis_test_notes.md` and the
`Hypothesis_Test` sheet in `outputs/experiment_summary.xlsx`
(screenshot: `screenshots/hypothesis_test_output.png`).

- **Test:** one-tailed two-proportion z-test on paid conversion rate.
- **H0:** p(Treatment) = p(Control). **H1:** p(Treatment) > p(Control).
- **Result:** Control 3.19% (22/690) vs. Treatment 7.04% (50/710) — a **+120.9% relative
  lift**. **z = 3.26, one-tailed p = 0.0005**, significant at α = 0.05 (and at α = 0.01).
- **Interpretation:** the observed lift is very unlikely to be due to random chance. The
  campaign has a real, positive effect on paid conversion.

---

## 7. Guardrail Metrics Considered

Full detail: `outputs/recommendation_memo.md`, Section "Guardrail Analysis."

| Guardrail | Control | Treatment | Statistically significant? | Risk assessment |
|---|---|---|---|---|
| Refund rate | 0.00% | 0.42% | No (p ≈ 0.09) | Low risk — directionally up but not statistically distinguishable from zero at this sample size |
| Support ticket rate | 14.8% | 24.8% | **Yes** (p < 0.001) | **Real risk** — a genuine, broad-based increase in service load; needs a support-capacity plan before full launch |
| Revenue per converted user | $1,630.10 | $770.41 | Borderline (p ≈ 0.08, two-sided) | Worth monitoring — directionally lower, consistent with converting a broader, lower-tier mix of users (Free/Basic), not a data error |
| Engagement score | 57.0 | 62.9 | **Yes** (p < 0.001), favorable | Not a risk — supports that the new flow is working as intended |
| Days to convert | 8.9 days | 6.4 days | **Yes** (p ≈ 0.008), favorable | Not a risk — faster activation is a positive signal |
| Segment-level consistency | — | — | One small reversal (Social traffic source) is **not statistically significant** (p ≈ 0.59) | Low risk — consistent with sampling noise in a small subgroup, not a true negative effect |

**Net guardrail conclusion:** the only guardrail with strong statistical evidence of a
negative shift is **support ticket rate**. Refund rate and revenue-per-converted-user
moved in directions worth watching but are not strong enough, on this sample size, to
override the very strong primary result.

---

## 8. Final Recommendation

**Launch the Treatment experience to all users, conditional on a support-capacity plan
for the higher ticket volume, with revenue-per-converted-user and refund rate tracked as
live guardrails for the first full quarter post-launch.**

This is supported by: a statistically significant, large (+120.9%) lift in the North Star
metric; consistent positive direction across nearly every funnel stage, region, device
type, and plan type; faster time-to-convert; and higher engagement. The one real risk
identified — increased support ticket rate — is a cost/operations issue to plan for, not
evidence the campaign itself is harmful to users or the business. See
`outputs/recommendation_memo.md` for the full reasoning, including segment-level detail
and limitations.

---

## 9. Assumptions and Limitations

- Group assignment is assumed to be random at signup; the balanced segment distribution
  (Section 5) supports this but cannot fully rule out unmeasured confounds.
- "Support ticket rate" is interpreted as the share of users with **at least one** ticket
  (matching the spec's "users with support tickets" wording), not the average ticket
  count per user.
- `days_to_convert` is only meaningful for converted users; comparing it across groups
  implicitly conditions on conversion, so it should be read as "how fast converters
  convert," not as a metric defined for the whole population.
- The revenue-per-converted-user guardrail is based on a small number of converted users
  per group (22 Control / 50 Treatment), so its p-value (~0.08) is borderline — more
  post-launch data would sharpen this read.
- The dataset covers a 5-month window (Jan–May 2025) in one product; results may not
  generalize to seasonal periods or other products without re-testing.
- "Unknown" recoding for missing `device_type`/`traffic_source` assumes those are
  genuinely missing-at-random tracking gaps, not a systematic data-collection issue tied
  to one group.

---

## 10. Screenshots Included

| File | Shows |
|---|---|
| `screenshots/summary_metrics.png` | Control vs. Treatment summary table (`Summary` sheet) |
| `screenshots/hypothesis_test_output.png` | Full two-proportion z-test calculation and decision (`Hypothesis_Test` sheet) |
| `screenshots/kpi_tree_preview.png` | KPI tree diagram |

---


