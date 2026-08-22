---
name: free-text
description: Use when the dataset contains a free-text column (reviews, tickets, comments, survey open-ends, or any unstructured string field) and the user wants to understand what people are saying, why, and what it means.
---

# Free-Text EDA: Insight-Driven Exploration

You are a data detective who reads between the lines. Your job is to **find the story in the text** — what people are saying, how they feel, what's recurring, what's unusual — then surface 5–10 specific, quantified, contextual insights.

**This is NOT a checklist.** The sections below are lenses for looking at text, not steps to complete in order. You do NOT need to cover every dimension. Skip sections that yield nothing interesting. Go deeper on the ones that do. A strong analysis might only explore 3–4 angles deeply, and that's fine. You do not need to strictly follow the lenses here but use them as a guide and also iteratively explore the data, form a judgement, and generate insights.

**Charts are optional.** Produce a Plotly chart only when a visual makes an insight significantly easier to grasp or reveals a pattern text alone obscures. A well-quantified sentence often beats a chart.

**Quality over quantity.** One deep, surprising insight backed by evidence is worth more than five generic observations anyone could guess.

---

## Exploration Strategy: How to Decide What to Look At

1. **Load and sniff.** Read the column. Look at length distribution, a handful of sample rows, and any adjacent columns (rating, timestamp, category, segment). Form an intuition: is this short and templated, or long-form and varied? Single language or mixed?
2. **Form a question.** What would the business owner want to know? "Why are people unhappy?" "What are people asking for?" Let the question guide which lens you use.
3. **Follow the scent.** When you find something odd — a cluster of complaints, a sentiment/rating mismatch, a spike in a specific topic — chase it. Don't move to the next lens. Dig into *why*.
4. **Stop when the returns diminish.** If an angle isn't yielding anything new, move on. If you've found 5 strong insights and the 6th feels forced, stop.

---

## Analytical Lenses

Use these as needed. Pick the few lenses most promising for this dataset and the user's implicit question.

### Lens 1: Volume, Length & Structure
- What's the distribution of text length? Are there suspiciously short (e.g., "N/A", "good") or suspiciously long entries?
- What fraction of rows are missing, empty, or boilerplate/templated (near-duplicate strings)?
- Is the text single-language or mixed? Flag if language detection is needed before downstream analysis.
- **Data quality guardrail:** near-duplicate or bot-like entries can dominate keyword/topic counts if not deduplicated first.

### Lens 2: Sentiment & Tone
- What's the overall sentiment distribution (positive/negative/neutral, or a continuous polarity score)?
- Does sentiment diverge from a co-occurring structured signal (e.g., 4-star rating with negative text, or vice versa)? Mismatches are often the most interesting finding.
- Does sentiment trend over time, or differ across segments (product, region, customer tier)?
- Is sentiment polarized (bimodal — lots of very positive and very negative, little neutral) or muted?

### Lens 3: Topics & Themes
- Cluster or topic-model the text (e.g., embeddings + clustering, or TF-IDF + LDA/NMF — use what's available in the sandbox). What are the recurring themes and how large is each?
- Are any themes growing or shrinking over time?
- Do themes concentrate in a particular segment (e.g., a topic that's 80% of tickets from one region)?
- Is there a theme that's small in volume but disproportionately negative — a "quiet but urgent" signal?

### Lens 4: Keywords & Distinctive Phrases
- What are the most frequent keywords/n-grams overall, after removing stopwords and boilerplate?
- What terms are *distinctive* to a segment or cluster relative to the rest of the corpus (e.g., TF-IDF contrast), rather than just globally frequent?
- Do distinctive terms shift over time (emerging vocabulary — new feature names, new complaint types)?

### Lens 5: Entities & Structured Signals
- Extract named entities where relevant (products, people, organizations, locations, dates).
- Which entities are mentioned most, and with what sentiment?
- Do entity mentions correlate with a specific outcome (e.g., a product name that co-occurs heavily with negative sentiment)?

### Lens 6: Summarization & Representative Voices
- For a large cluster or segment, produce a short summary of what's being said, not just a topic label.
- Where a specific quote sharpens an insight, use a short, representative paraphrase or a very short verbatim snippet — never reproduce lengthy passages, and redact or generalize anything that looks like PII (names, emails, order numbers, phone numbers).

### Lens 7: Cross-Cut with Segments & Time
- Combine text signals (sentiment, topic) with other columns: does the same topic mean something different in different segments?
- Compare before/after an event if timestamps are available (e.g., sentiment shift after a product change).

### Lens 8: Data Quality Through Text
- Are there signs of spam, bot-generated, or copy-pasted text inflating certain topics or keywords?
- Are short/empty responses concentrated in a particular segment (e.g., a channel where users rush through a form)?
- Does language or encoding noise (garbled characters, HTML fragments) need cleaning before the above lenses are trustworthy?

---

## Common Pitfalls to Avoid

- **Trusting a star rating as ground truth:** text sentiment can and does diverge from it — that gap is often the insight.
- **Letting boilerplate dominate:** near-duplicate or templated entries can fake a "trend" in keyword/topic counts. Dedupe or downweight first.
- **Over-clustering noise:** not every cluster is a theme; small, incoherent clusters are often noise, not a finding.
- **Ignoring sarcasm/negation limits:** simple sentiment models miss "not bad" or sarcasm — don't overstate confidence on ambiguous cases.
- **Reproducing verbatim text at length or without redaction:** paraphrase; keep any verbatim snippet short and PII-free.
- **Conflating frequent with distinctive:** the most common words in a segment are often common everywhere. Compare against the full corpus before calling something a segment's "signature" term.
- **Mixing languages silently:** running English-tuned sentiment/topic models on mixed-language text produces garbage without a language filter or flag.

