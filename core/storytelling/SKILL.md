---
name: storytelling
description: Use this skill whenever you have insights and charts from an analytical skill and need to
  structure them into a coherent narrative for a data dashboard or storytelling report.
---

# Storytelling Dashboard Skill

This skill defines **how to structure and sequence a data story**. This is to answer:
*given a set of findings, what is the most compelling order and framing to present them?*

Output is a structured narrative that will be consumed and render into the final HTML page.

---


## Step 0: Plan Before You Write

Before generating any HTML, produce a narrative blueprint in this format:

```
CENTRAL QUESTION: <the single question this dashboard answers>
KEY FINDING: <the most surprising or important result, in plain language>
COMPLEXITY: simple | complex  (see Step 1)
SLIDE PLAN:
  1. [slide title] → [claim] → [leads to next because...]
  2. ...
WHAT WE ARE NOT SHOWING: <findings deliberately omitted and why>
```

Do not proceed to HTML generation until this blueprint is complete.

---

## Step 1: Choose an Opening Strategy

The opening determines whether a reader continues. Choose based on insight complexity:

### Simple insight → Sledgehammer opening
If the key finding can be stated in one sentence without prior context, **lead with it**.
Put the most surprising stat or chart first. Subsequent slides become more compelling
because the reader already cares about the topic.

> Example pattern: "Rapper X has the largest vocabulary in hip-hop" → show the chart immediately
> → then add sub-questions (by era, by genre) that the reader now wants answered.

### Complex insight → Single data point opening
If the finding requires context to be understood, **start with one concrete example** from
the data before stating the general finding. This is the data equivalent of a character
in long-form journalism — it makes an abstract pattern relatable before the abstraction is revealed.

> Example pattern: Start with truck drivers specifically → then reveal the broader automation finding
> → reader understands the human scale before the statistical scale.

**Never open with methodology, caveats, or background context.** These kill momentum.

---

## Step 2: Sequence the Narrative Arc

Use this arc for most dashboards. Each element maps to a slide:

```
1. HOOK        → Slide 1: surprising finding or concrete data point (Step 1)
2. CONTEXT     → Slide 2: minimum background needed to understand what follows
3. EVIDENCE    → Slides 3–5: 2–4 supporting findings, each with one chart
4. COMPLICATION → Slide 6: the nuance, exception, or "but wait" moment
5. CONCLUSION  → Slide 7: so what? What does this mean?
6. METHOD      → Slide 8: brief transparency section (data source, key assumptions)
```

Rules:
- Each slide should make the reader want to see the next one.
- If a slide doesn't change what the reader thinks or feels, cut it.
- Maximum 2 key claims per slide. More than that and the reader loses the thread.

---

## Step 3: Structure Each Slide

Within the Evidence and Complication slides, use a consistent pattern per finding:

```
[Claim sentence — state the finding directly, no build-up]
[Chart — visual evidence for that claim, if available and relevant]
[1–2 sentences of interpretation — what to notice, what it means]
[Transition — one sentence bridging to the next slide]
```

**Not every slide needs a chart.** Some insights are better conveyed through text alone (e.g., a correlation coefficient, a percentage change, or a data quality observation). Only include a chart when it meaningfully strengthens the point.

**The claim comes before the chart, not after.** Readers should know what they are
looking at before they look at it. Do not write "as you can see in the chart below..."

---

## Step 4: Decide What Not to Show

The difference between a data dump and a story is omission. After generating the blueprint:

- Keep only findings that advance the central question.
- If a finding is interesting but tangential, cut it. It dilutes the narrative.
- If two findings say the same thing, keep the more surprising one.

Aim for 4–6 charts total in a dashboard. More than 8 is almost always a sign that the
central question is not focused enough.

---

## Step 5: Write the Conclusion

The conclusion must do two things:
1. Restate the central finding in plain language (readers skim, and many jump to the end).
2. State the implication — what should the reader think or do differently because of this?

If you cannot write a one-paragraph conclusion that does both, the story's central question
was not narrow enough. Flag this.

---

## Step 6: Methodology Section

Always include a brief methodology section at the end. This is not optional — it is how
readers decide whether to trust the analysis. Include:
- Data source and recency
- Key assumptions or exclusions
- Any known limitations

Keep it under 100 words. This is a trust signal, not a technical appendix.

---

## Output Contract

Your final response must be a single, parseable JSON object. 

### Agent Response Format

Return exactly one JSON object. Do not wrap it in markdown code blocks or add any prose before or after it.

```json
{
  "central_question": "How have Ecuadorian store sales evolved from 2013 to 2017?",
  "slides": [
    {
      "slide_number": 1,
      "title": "Sales doubled in three years — then hit a wall",
      "contents": [
        { "type": "text", "content": "From 2013 to 2016, daily store sales in Ecuador grew 110%, driven by new store openings and rising consumer demand." },
        { "type": "chart", "chart_index": 0 }
      ]
    },
    {
      "slide_number": 2,
      "title": "December dominates the calendar",
      "contents": [
        { "type": "text", "content": "December daily sales average $849K, 40% above the annual mean. The peak is driven by holiday shopping and bonus-season spending." },
        { "type": "chart", "chart_index": 1 },
        { "type": "text", "content": "Not all categories benefit equally: FROZEN FOODS spikes 4.3x while SCHOOL SUPPLIES collapses to 0.34x." }
      ]
    },
    {
      "slide_number": 3,
      "title": "Oil prices and sales move in opposite directions",
      "contents": [
        { "type": "text", "content": "As Ecuadorian oil prices fell from $98 to $43, store sales rose. The correlation is -0.63." },
      ]
    }
  ],
  "charts": [
    {
      "title": "Monthly Sales Trend (2013-2017)",
      "description": "Overall upward trend with a sharp deceleration in 2017",
      "figure": { /* Plotly figure JSON spec from input */ }
    },
    {
      "title": "December Seasonality by Product Family",
      "description": "FROZEN FOODS spikes 4.3x in December while SCHOOL SUPPLIES drops",
      "figure": { /* Plotly figure JSON spec from input */ }
    }
  ]
}
```

### Field Definitions

- **`central_question`** — string. The single question this dashboard answers.
- **`slides`** — array of slide objects, each representing one screenful of content:
  - `slide_number`: integer, 1-indexed, in display order
  - `title`: slide heading string
  - `contents`: array of content items, in the order they should appear on the slide. A slide can be text-only, chart-only, or a mix. Each item is one of:
    - `{ "type": "text", "content": "..." }` — a paragraph of narrative text
    - `{ "type": "chart", "chart_index": 0 }` — Optional, references a chart by zero-based index into the top-level `charts` array
- **`charts`** — array of chart objects if any are referenced in the slides, each containing:
  - `title` — chart title string
  - `description` — brief description of what the chart shows
  - `figure` — the Plotly figure JSON spec (passed through from the analytical skill). Only include charts that are referenced by `chart_index` in the slides.

### Rules
- Every `chart_index` in `slides.contents` must point to a valid entry in the `charts` array.
- The `charts` array should only contain charts actually used in the slides. Drop unused charts from the analytical input.
- A slide may contain zero charts. Text-only slides are valid and often appropriate for conclusions, methodology, or insights best conveyed through numbers alone.
- Do not invent or alter the Plotly figure specs. Pass them through unchanged from the analytical skill.

---

## Anti-Patterns

| Anti-pattern | Why it fails |
|---|---|
| Opening with background/context | Reader has no reason to care yet |
| Chart with no claim | Reader doesn't know what to look for |
| More than 2 takeaways per slide | Reader loses the thread |
| Saving the best finding for last | Most readers don't get there |
| Showing everything the analysis found | Signals no editorial judgment |
| Conclusions that just summarise | Should add meaning, not restate |

