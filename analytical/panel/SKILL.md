---
name: panel
description: Use when the dataset has repeated observations per entity over time (customers, stores, users, accounts, assets) — i.e. both a cross-sectional dimension and a time dimension
---

# Panel Data EDA: Insight-Driven Exploration

You are a data detective looking at many time machines running in parallel. Your job is to **find the story in how entities differ from each other, and how they change over time** — then surface 5–10 specific, quantified, contextual insights.

**This is NOT a checklist.** The sections below are lenses for looking at data, not steps to complete in order. You do NOT need to cover every dimension. Skip sections that yield nothing interesting. Go deeper on the ones that do. A strong analysis might only explore 3–4 angles deeply, and that's fine. You do not need to strictly follow the lenses here but use them as a guide and also iteratively explore the data, form a judgement, and generate insights.

**Charts are optional.** Produce a Plotly chart only when a visual makes an insight significantly easier to grasp or reveals a pattern numbers alone obscure. A well-quantified sentence often beats a chart.

**Quality over quantity.** One deep, surprising insight backed by evidence is worth more than five generic observations anyone could guess.

---

## Exploration Strategy: How to Decide What to Look At

1. **Load and sniff.** Identify the entity key and time key. How many entities, how many periods, is the panel balanced (every entity observed every period) or unbalanced? Form an intuition: is variation mostly *between* entities, or mostly *within* entity *over* time?
2. **Form a question.** What would the business owner want to know? "Which customers are growing?" "Is the whole book declining, or a few accounts dragging it down?" Let the question guide which lens you use.
3. **Follow the scent.** When you find something odd — an entity that diverges from the pack, a cohort that behaves differently, a period where everyone moves together — chase it. Don't move to the next lens. Dig into *why*.
4. **Stop when the returns diminish.** If an angle isn't yielding anything new, move on. If you've found 5 strong insights and the 6th feels forced, stop.

---

## Analytical Lenses

Use these as needed. Pick the few lenses most promising for this dataset and the user's implicit question.

### Lens 1: Panel Structure & Balance
- How many unique entities, how many periods, and is the panel balanced or unbalanced?
- Where entities are missing periods: is that entry/exit (new customer, churned account) or a data gap?
- What's the distribution of panel length per entity (some observed for years, some for one period)? Short-lived entities can distort aggregates.
- **Data quality guardrail:** check for duplicate entity-period keys before any aggregation — they silently double-count.

### Lens 2: Within vs Between Variation
- How much of the total variation in the metric is *between* entities (some are just bigger/smaller) vs *within* entity over time (an entity changing)?
- If between-variation dominates, cross-sectional comparisons matter more than trends. If within-variation dominates, entities are genuinely changing over time — investigate why.
- Does this ratio differ across segments?

### Lens 3: Entity Heterogeneity & Ranking
- Who are the top and bottom entities, and how large is the spread between them?
- Is aggregate performance driven broadly, or by a small number of outlier entities (concentration)?
- Are there entities whose behavior is structurally different (e.g., persistently high-variance, persistently flat)?

### Lens 4: Common Time Effects vs Entity-Specific Effects
- Do all entities move together in certain periods (a common shock — seasonality, macro event, platform-wide change), or do movements look entity-specific?
- After removing a common time trend, do meaningful entity-level differences remain?
- Are common-time effects the same magnitude across entities, or does the shock hit some harder than others (interaction effect)?

### Lens 5: Transitions & Persistence
- Do entities stay in the same relative position over time (persistence), or churn/reshuffle between periods (mobility)?
- If there's a natural state (tier, status, quantile/decile of the metric), what fraction of entities transition between states period to period?
- Is a metric this period a strong predictor of next period (autocorrelation), or does it revert quickly?

### Lens 6: Cohort Analysis
- If entities have a natural entry point (signup date, first purchase, onboarding date), do outcomes differ by cohort?
- Do later cohorts perform better/worse than earlier ones at the same relative age (e.g., "month 3 since signup")?
- Watch for conflating calendar time and entity age — align cohorts on relative time before comparing.

### Lens 7: Attrition & Survivorship
- Do entities that exit differ systematically (in prior behavior) from those that stay? That's a survivorship signal, and it biases any "current entity" average.
- Is attrition concentrated in a particular cohort, segment, or period?
- Does computing an aggregate only on surviving entities overstate performance versus including exited ones?

### Lens 8: Data Quality Through the Panel
- Are entity identifiers stable over time, or do renames/merges/reissued IDs fragment what should be one entity?
- Are there implausible period-over-period jumps for a single entity that suggest a data error rather than real change?
- Are zeros/missing values structural (entity not yet active) or a pipeline gap?

---

## Common Pitfalls to Avoid

- **Reading aggregate trend as entity trend:** an aggregate can rise while most entities decline, if a few large entities dominate or low performers exit.
- **Ignoring survivorship bias:** averages computed only on entities still present overstate performance.
- **Conflating calendar time and entity age:** cohorts should be compared at the same relative age, not the same calendar period.
- **Treating an unbalanced panel as balanced:** missing periods change denominators; don't average across entities with wildly different panel lengths without noting it.
- **Fragmented entity identity:** an ID reissue or merge can make one entity look like two, or two look like one.
- **Overfitting to a handful of entities:** a "trend" driven by 2–3 large entities is a concentration story, not a broad pattern — say so explicitly.
- **Ignoring common shocks:** an entity-specific-looking dip that actually hit every entity in the same period is a market/platform event, not entity behavior.

