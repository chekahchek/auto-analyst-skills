---
name: profile-data
description: Profiles an uploaded dataset to infer its type of data (e.g. Time series, free text etc.)
---

## Overview

Analyse the dataset to deduce the type of data. This will help the agent load up relevant analytical skills and memories to better understand the data.

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


## List of data types

Pick the data type based on what the rows represent and how they relate to each other.

| Data Type       | What it looks like | Examples |
| --------------- | ------------------ | -------- |
| time-series     | Rows are ordered by date/time and track the same metric(s) over time. | daily revenue; hourly sensor readings; monthly active users; stock prices |
| cross-sectional | A snapshot of many entities at one point in time. Think "many entities, one time". | a one-time customer survey; a product catalogue; today's store sales |
| panel           | The same entities measured repeatedly over time. Think "the same entities, many times". Needs an entity ID and a time column. | monthly sales per store; weekly user activity; quarterly employee scores |
| event-log       | Each row is one action or event with a timestamp and an actor. | web clicks; transactions; error logs; support tickets |
| free-text       | Important columns contain natural language (more than short labels). | product reviews; survey open answers; chat transcripts; articles |
| cohort          | Entities can be grouped by when they started, so you can compare groups over time. | users who signed up in Jan and their retention; Q1 first-purchase customers |
| relational      | Rows link to other rows in the same data, like a hierarchy. | org chart (employee → manager); category tree; bill of materials |
| general         | The data does not clearly fit any of the above. | mixed or unclear spreadsheets |


## Output Format
When you have enough information, output a JSON object as your final message — no other text, no markdown fences:
{"data_type": ["time-series", "cross-sectional"]}