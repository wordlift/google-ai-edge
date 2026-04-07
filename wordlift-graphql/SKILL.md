---
name: wordlift-graphql
description: Use this skill to query the WordLift Knowledge Graph for SEO data, keyword performance, and article analytics.
metadata:
  require-secret: true
  require-secret-description: Enter your WordLift API key (found in WordLift dashboard under Settings -> API).
  homepage: https://wordlift.io
---

# WordLift GraphQL Explorer

This skill allows you to query your WordLift Knowledge Graph using natural language. It translates your questions into GraphQL queries and displays the results in an interactive table.

## Instructions

Whenever the user asks a question about their SEO performance, keywords, pages, or entities in WordLift, call the `run_js` tool with the following exactly:

- **script name**: `index.html`
- **data**: A JSON string with the following fields:
  - `query`: The translated GraphQL query.
  - `question`: The original natural language question.

### Translation Guide

| If the user asks about... | Use this root field | Key predicates |
| :--- | :--- | :--- |
| Keywords, impressions, clicks | `keywords` | `seovoc:name`, `seovoc:impressions28Days`, `seovoc:clicks28Days`, `seovoc:ctr28Days` |
| Page performance, URLs | `entities` | `seovoc:title`, `schema:url`, `seovoc:impressions28Days` |
| Articles, authors | `articles` | `authorConstraint`, `seovoc:hasQuery` |
| Semantic/Vector search | `entitySearch` | `search: { string: "..." }` |

**Always apply a `limit: 20` to the root fields unless specified otherwise.**

### Examples

**User**: "Top keywords this month?"
**Tool call**: `run_js(data: '{"query": "query { keywords(sort: {field: \"seovoc:impressions28Days\", direction: DESC}, limit: 20) { name: string(name: \"seovoc:name\") impressions: int(name: \"seovoc:impressions28Days\") clicks: int(name: \"seovoc:clicks28Days\") } }", "question": "Top keywords this month?"}')`

**User**: "Pages with low CTR (< 2%)?"
**Tool call**: `run_js(data: '{"query": "query { entities(filter: {field: \"seovoc:ctr28Days\", operator: \"LT\", value: 0.02}, limit: 20) { title: string(name: \"seovoc:title\") url: string(name: \"schema:url\") ctr: float(name: \"seovoc:ctr28Days\") } }", "question": "Pages with low CTR (< 2%)?"}')`

**User**: "Trending keywords last 7 days?"
**Tool call**: `run_js(data: '{"query": "query { keywords(sort: {field: \"seovoc:impressions7Days\", direction: DESC}, filter: {field: \"seovoc:age\", operator: \"LT\", value: 7}, limit: 10) { name: string(name: \"seovoc:name\") impressions: int(name: \"seovoc:impressions7Days\") } }", "question": "Trending keywords last 7 days?"}')`

### Interpretation & Summary
After the `run_js` tool returns a result starting with `DATA_FOUND`:
1. **Analyze**: Read the provided JSON snippet. Identify the top performing keyword, page, or any notable metric.
2. **Summarize**: Provide a 2-3 sentence conversational summary of the findings (e.g., "The top keywords this month are led by 'seo strategy' with 5,400 clicks and a healthy 4.2% CTR."). 
3. **Confirm**: Mention that the full data set is available in the interactive table below.
4. **Tone**: Be professional, analytical, and supportive of the user's SEO goals.
