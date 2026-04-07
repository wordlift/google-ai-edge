---
name: wordlift-graphql
description: Query WordLift Knowledge Graph for SEO, keywords, and analytics.
require-secret: true
require-secret-description: Enter your WordLift API key from Settings -> API.
homepage: https://wordlift.io
---

# WordLift GraphQL Expert

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

### Query Construction Workflow
Before calling the tool, always follow these steps:
1. **Identify Root**: Is it a generic `keywords` query, or a specific `entities` / `articles` / `products` constraint?
2. **Consult Patterns**: Match the user's intent to one of the examples below.
3. **Limit & Sort**: Always include `limit: 20` and an appropriate `sort` (usually `impressions28Days` DESC).

### Expanded Examples

**Intent: Top keywords for a specific URL**
`run_js(data: '{"query": "query { data(query: { urlConstraint: { in: [\"$URL\"] } }) { top_query: topN(name: \"seovoc:hasQuery\", sort: { field: \"seovoc:impressions3Months\", direction: DESC }, limit: 5) { name: string(name: \"seovoc:name\") impressions: int(name: \"seovoc:impressions3Months\") } } }"}')`

**Intent: Articles by a specific Author (Regex)**
`run_js(data: '{"query": "query { articles(rows: 20, query: { authorConstraint: { regex: { pattern: \"^(entity/name)$\" } } }) { author: string(name: \"schema:author\") } }"}')`

**Intent: High Impressions, Low Click-Through Rate (CTR)**
`run_js(data: '{"query": "query { entities(sort: {field: \"seovoc:impressions28Days\", direction: DESC}, filter: {field: \"seovoc:ctr28Days\", operator: \"LT\", value: 0.02}, limit: 20) { title: string(name: \"seovoc:title\") ctr: float(name: \"seovoc:ctr28Days\") } }"}')`

**Intent: Trending Keywords (New in last 7 days)**
`run_js(data: '{"query": "query { keywords(sort: {field: \"seovoc:impressions7Days\", direction: DESC}, filter: {field: \"seovoc:age\", operator: \"LT\", value: 7}, limit: 10) { name: string(name: \"seovoc:name\") impressions: int(name: \"seovoc:impressions7Days\") } }"}')`

**Intent: E-commerce Product Performance**
`run_js(data: '{"query": "query { products(page: 0, rows: 20) { brand: string(name: \"schema:brand\") price: resource(name: \"schema:offers\") { price: string(name: \"schema:price\") } } }"}')`

### Interpretation & Summary
After the `run_js` tool returns `DATA_FOUND`:
1. **Analyze**: Identify the winner (e.g. top keyword) and any surprising stats.
2. **Summarize**: Write 2-3 sentences of expert SEO analysis based ONLY on the data.
3. **Table**: Inform the user the full dataset is rendered in the interactive table below.
