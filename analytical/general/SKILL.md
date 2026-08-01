---
name: general
description: >
  Use for general cross-sectional tabular data that doesn't fit a more specific lens — no dominant time dimension (time-series), no repeated entity-over-time structure (panel-data), and no free-text column to mine (free-text). One row per observation, a mix of numeric and categorical columns.
---

# Tabular EDA: Insight-Driven Exploration

You are a data detective looking at a single snapshot. Your job is to **find the story in the rows and columns** — what varies, what's related, what's unusual — then surface 5–10 specific, quantified, contextual insights.

**This is NOT a checklist.** The sections below are lenses for looking at data, not steps to complete in order. You do NOT need to cover every dimension. Skip sections that yield nothing interesting. Go deeper on the ones that do. A strong analysis might only explore 3–4 angles deeply, and that's fine. You do not need to strictly follow the lenses here but use them as a guide and also iteratively explore the data, form a judgement, and generate insights.

**Charts are optional.** Produce a Plotly chart only when a visual makes an insight significantly easier to grasp or reveals a pattern numbers alone obscure. A well-quantified sentence often beats a chart.

**Quality over quantity.** One deep, surprising insight backed by evidence is worth more than five generic observations anyone could guess.

---

## Exploration Strategy: How to Decide What to Look At

1. **Load and sniff.** Read the columns, dtypes, row count, and a few sample rows. Which columns are numeric, which are categorical, which look like identifiers? Form an intuition: what's this table *about*, and what would someone want to know from it?
2. **Form a question.** What would the business owner want to know? "What drives price?" "Which segment underperforms?" Let the question guide which lens you use.
3. **Follow the scent.** When you find something odd — a skewed distribution, a strong correlation, a category that behaves differently — chase it. Don't move to the next lens. Dig into *why*.
4. **Stop when the returns diminish.** If an angle isn't yielding anything new, move on. If you've found 5 strong insights and the 6th feels forced, stop.

---

## Analytical Lenses

Use these as needed. Pick the few lenses most promising for this dataset and the user's implicit question.

### Lens 1: Shape & Distributions
- For numeric columns: center, spread, skew. Is the distribution roughly normal, long-tailed, bimodal?
- For categorical columns: how many levels, how balanced or imbalanced (one category dominating)?
- Are there natural units or caps (e.g., percentages bounded 0–100, ages bounded 0–120) that constrain interpretation?

### Lens 2: Relationships Between Variables
- Which numeric pairs are strongly correlated — and does the relationship look linear, or is it driven by outliers/a subgroup?
- Do categorical variables show a clear association with a numeric target (group means differ meaningfully)?
- Is there a variable that looks like it should predict another but doesn't (a non-finding worth noting if the user's implicit question expected it)?
- **Guardrail:** correlation between two variables that are both downstream of a third (confounder) can mislead — flag when this is plausible.

### Lens 3: Group Comparisons
- Break the data down by a categorical column: how do groups compare on the metric that matters?
- Is the difference between groups large relative to within-group spread, or is it noise given group sizes?
- Is one group's average being pulled by a small number of extreme rows?

### Lens 4: Outliers & Extremes
- Are there rows far outside the typical range? Are they data errors, or genuinely extreme (and interesting) cases?
- Do outliers cluster in a particular category, or appear at random?
- Does excluding outliers change the story (e.g., a correlation that only holds because of a few extreme points)?

### Lens 5: Missingness & Completeness
- Which columns have missing values, and how much?
- Is missingness random, or does it correlate with another column (e.g., a field only populated for one segment)? Non-random missingness is often itself an insight.
- Are there placeholder values masquerading as real data (0, -1, "unknown", "N/A") that should be treated as missing?

### Lens 6: Composition & Concentration
- If the table represents a population (customers, products, transactions), how concentrated is a metric — do a few rows account for a disproportionate share of the total?
- Does the composition (mix of categories) look expected, or is one category over/under-represented relative to what you'd assume?

### Lens 7: Data Quality & Consistency
- Are there duplicate rows, or rows that should be unique on some key but aren't?
- Are categorical values inconsistent (e.g., "NY", "New York", "ny" as separate levels)?
- Do numeric columns have implausible values (negative ages, dates in the future) that suggest upstream errors?

---

## Common Pitfalls to Avoid

- **Mean without checking the distribution:** a skewed or outlier-driven distribution makes the mean misleading; check the median too.
- **Correlation without checking confounders:** two variables both driven by a third can look related when they aren't causally connected.
- **Group comparisons on small or unequal groups:** a striking group difference can be noise if one group has few rows.
- **Treating placeholder values as real:** "-1", "0", or "unknown" strings often mean missing, not a real measurement.
- **Ignoring non-random missingness:** missing-not-at-random is often more informative than the observed values themselves.
- **Overlooking category inconsistency:** near-duplicate category labels silently fragment what should be one group.
- **Reporting a global average when composition varies:** an average across a mixed population can hide that the "average" describes no actual subgroup well.

