---
name: profile-data
description: Profiles an uploaded dataset to infer type of data (e.g. Time series, free text etc.) as well as the business domain (e.g. finance, user-survey etc.)
---

## Overview

Analyse the dataset to deduce the type of data and the business domain. This will help the agent load up relevant analytical skills, domain knowledge, and memories to better understand the data. 

## Steps

1. Sample the data by generating python code to inspect the column names, schema, and up to 5 rows of data. 
Note that when you use tool `execute_python_script`, each callis isolated so variables do not persist between calls. You need to reimport libraries and redefine variables in each call.

```python
df = pd.read_csv('<path_to_file>')
print(df.columns)
print(df.dtypes)
print(df.head(5))
```

2. Infer the type of data, available list of data types is shown in the next section. You can output more than 1 data type if the data fits multiple categories, but do not simply output all of them. Be as specific as possible, generate more code to deduce the type of data if needed. If it is really unclear, then return 'general' as the data type.

3. Infer the business domain, available list of domains is shown in the next section. If it is really unclear, then return 'general' as the business domain.


## List of data types
|     Data Type   | Description |
|  -------------- | ----------- |
| time-series     | Rows indexed by date/time. Analysis focuses on trends, seasonality, WoW/MoM/YoY comparisons, and forecasting. |
| cross-sectional | Each row is an independent entity (customer, product, store) at a single point in time. Analysis focuses on distributions, segmentation, and ranking. |
| panel           | Each entity appears across multiple time periods. Requires tracking the same individuals over time — train/test splits must respect entity boundaries. |
| event-log       | Each row is an action or event (clicks, transactions, errors). Analysis focuses on funnels, session reconstruction, and time-to-event. |
| free-text       | One or more columns contain natural language. Requires frequency analysis, topic modelling, and sentiment before any quantitative work.|
| cohort          | Entities grouped by a shared start event (signup month, first purchase). Analysis focuses on retention curves and cohort-over-cohort comparison. |
| relational      | Rows imply a parent-child structure (org chart, product category tree). Aggregations must respect the hierarchy to avoid double-counting. |
| general         | Data that doesn't fit well into the above categories, or when the type is unclear. Analysis may require a more exploratory approach. |


## List of business domains
| Business Domain   | Description |
| ----------------- | ----------- |
| finance           | Data related to financial performance, accounting, revenue, costs, margins, etc. |
| operations        | Data related to operational performance, supply chain, logistics, manufacturing, etc. |
| growth            | Data related to user acquisition, marketing performance, conversion funnels, etc. |
| hr                | Data related to human resources, employee performance, attrition, etc. |
| general           | When the business domain is unclear or doesn't fit well into the above categories.

## Output Format
When you have enough information, output a JSON object as your final message — no other text, no markdown fences:
{"data_type": ["time-series", "cross-sectional"], "business_domain": "finance"}