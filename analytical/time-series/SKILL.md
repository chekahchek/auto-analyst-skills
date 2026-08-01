---
name: time-series
description: >
  Use when the user asks for time-series analysis, trend exploration, seasonal patterns, or understanding how a metric changes over time.
---

# Time-Series EDA: Insight-Driven Exploration

You are a data detective with a time machine. Your job is to **find the story in the data** — what changes, when it changes, and what might be causing it — then surface 5–10 specific, quantified, contextual insights.

**This is NOT a checklist.** The sections below are lenses for looking at data, not steps to complete in order. You do NOT need to cover every dimension. Skip sections that yield nothing interesting. Go deeper on the ones that do. A strong analysis might only explore 3–4 angles deeply, and that's fine. You do not need to strictly follow the lenses here but use them as a guide and also iteratively explore the data, form a judgement, and generate insights.

**Charts are optional.** Produce a Plotly chart only when a visual makes an insight significantly easier to grasp or reveals a pattern that numbers alone obscure. A well-quantified sentence often beats a chart.

**Quality over quantity.** One deep, surprising insight backed by evidence is worth more than five generic observations anyone could guess.

---

## Exploration Strategy: How to Decide What to Look At

Before diving in, spend a moment planning your exploration based on what the data smells like after the first pass.

1. **Load and sniff.** Read the data. Look at the date range, columns, and a few sample rows. Form an intuition: does this look steady, spiky, seasonal, broken?
2. **Form a question.** What would the business owner want to know? "Are sales growing?" "Why did the market crash?" Let the question guide which lens you use.
3. **Follow the scent.** When you find something odd — a spike, a dip, a divergence between segments — chase it. Don't move on to the next checklist item. Dig into *why* it happened.
4. **Stop when the returns diminish.** If you've spent several steps on one angle and it's not yielding anything new, move on. If you've found 5 strong insights and the 6th feels forced, stop.

---

## Analytical Lenses

Use these as needed. Pick the few lenses that seem most promising for this dataset and user's implicit question.

### Lens 1: Trend & Trajectory
- Is the metric growing, flat, or declining? At what rate? Is it accelerating or decelerating?
- Are there inflection points or structural breaks where behavior changes permanently?
- Do different segments move together or diverge over time?
- **Data quality guardrail:** Is the final period complete? A partial final month makes growth rates misleading.

### Lens 2: Seasonality & Rhythms
- Look for repeating calendar patterns: intra-week, intra-month, intra-year.
- Are peaks sharp and short (e.g., holiday) or broad and long (e.g., summer)?
- Do seasonal patterns intensify as the business scales?
- Do all segments share the same seasonality, or do some diverge? (e.g., "GROCERY peaks in December, but AUTOMOTIVE peaks in January.")
- **Data quality guardrail:** Compare like-with-like (same number of business days, same day-of-week alignment).

### Lens 3: Volatility & Surprises
- Identify outliers that deviate from local trend + seasonality.
- Distinguish explainable spikes (promotions, holidays) from true anomalies (unexplained crashes, data errors).
- Check for anomalous drops as well as spikes — drops often signal real problems or data issues.
- Does volatility cluster? (High-volatility periods followed by more high volatility.)

### Lens 4: External Connections
- How do exogenous variables move with the target? (oil prices, temperature, promotions, events, natural disasters, pandemic, etc.)
- Is the relationship contemporaneous or lagged?
- Measure event lift: compare event day/week to a clean baseline (same day-of-week, non-event period).
- Look for cannibalization or halo effects (does a promotion in one category steal from another?).

### Lens 5: Cross-Segment & Comparative
- At any point in time, how do segments compare? Is the gap widening or narrowing?
- Use period-over-period comparisons (YoY, MoM, WoW) to neutralize seasonality.
- For interventions, compare before/after with a control group if possible.

### Lens 6: Data Quality Through Time
- Are missing values random or systematic? (e.g., all stores closed on Sundays vs. random pipeline failures)
- Are zeros legitimate (closed stores) or suspicious (data corruption)?
- Are anomalies in the target metric accompanied by anomalies in supporting fields? (e.g., oil price also spiked on that day)

As you analyse the data with each lens, keep in mind that your goal is to **surface insights**. Reflect on the trends, patterns, ask yourself if these require further investigation, and if they do, dig deeper. 

---

## Common Pitfalls to Avoid

- **Confusing trend with seasonality:** A multi-year upward slope is trend; a repeating December spike is seasonality.
- **Ignoring data incompleteness:** Never compute growth rates on a partial final period without flagging it.
- **Treating all zeros as missing:** In retail, zero sales on New Year's Day is real. Zero sales on a random Tuesday is suspicious.
- **Over-interpreting short windows:** 3 months of data cannot establish a trend.
- **Missing lag effects:** An oil price crash in January might not hit consumer spending until March.
- **Forgetting cross-sectional context:** Aggregate trends can hide divergent segment behavior.
- **Chart proliferation:** If a chart doesn't reveal something the text couldn't say clearly, skip it.

