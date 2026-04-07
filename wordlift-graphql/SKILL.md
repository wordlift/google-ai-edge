---
name: wordlift-graphql
description: Expert Knowledge Graph Explorer. Use for entity discovery (People, Places, Organizations), SEO performance, and schemaless data extraction.
metadata:
  require-secret: true
  require-secret-description: Enter your WordLift API key (found in Settings -> API).
  homepage: https://wordlift.io
---

# WordLift Knowledge Graph Expert

## 🚀 SMART SEARCH (RECOMMENDED)
For all discovery or "who/what/where" questions, you can simply call `run_js` with the search term.
- **Usage**: `run_js(data: "Andrea Volpini")`
- **Result**: Automatically performs a high-quality vector search.

---

## 🛠 ADVANCED PROTOCOL (TAGGED TEXT)
For specific metric queries or complex filters, use the tagged format:
`run_js(data: "[QUERY] query { ... } [/QUERY] [QUESTION] ... [/QUESTION]")`

### Core Principles
1. **PAGINATION**: Always use `(page: 0, rows: 20)`. NEVER use `limit`.
2. **ID MAPPING**: Always map `iri` to `id` (e.g., `id: iri`).
3. **LABELS**: Use `label: string(name: \"rdfs:label\")` as the primary name field.
4. **SCORE INTERPRETATION**: 
   - **`> 0.52`**: Strong semantic match.
   - **`< 0.48`**: Potential noise/low relevance.

---

## 🏗 PATTERN REFERENCE

### 1. Discovery (Entity Search)
The best way to start is to search for a name or topic.
- **Pattern**: `query { entitySearch(query: { search: { string: \"$SEARCH\" } }, page:0, rows:20) { id: iri label: string(name: \"rdfs:label\") url: string(name: \"schema:url\") matchScore: float(name: \"_:score\") } }`

### 2. Deep Enrichment (Resource Drill-down)
If you have an entity's IRI (from a search), use `resource()` to pull all its details.
- **Pattern**: `query { resource(iri: \"$IRI\") { id: iri label: string(name: \"rdfs:label\") description: string(name: \"schema:description\") related: refs(name: \"schema:about\") keywords: strings(name: \"seovoc:keywords\") } }`

---

## 💡 Expert Examples

- **User**: "Who is Andrea Volpini?"
  - **Tool call**: `run_js(data: "Andrea Volpini")` (Using Smart Search)

- **User**: "Get full details for http://data.wordlift.io/andrea"
  - **Tool call**: `run_js(data: "[QUERY] query { resource(iri: \"http://data.wordlift.io/andrea\") { id: iri label: string(name: \"rdfs:label\") desc: string(name: \"schema:description\") } } [/QUERY] [QUESTION] Full profile lookup [/QUESTION]")`

- **User**: "Trending keywords for my articles?"
  - **Tool call**: `run_js(data: "[QUERY] query { keywords(sort: {field: \"seovoc:impressions28Days\", direction: DESC}, page: 0, rows: 20) { id: iri keyword: string(name: \"seovoc:name\") impressions: int(name: \"seovoc:impressions28Days\") } } [/QUERY] [QUESTION] Performance keywords [/QUESTION]")`

### 🔎 Full Reference
See **`assets/query-examples.md`** for 30+ validated patterns, including nested SEO keywords and post-query filtering.

---

### Interpretation & Summary
After the tool returns `DATA_FOUND`:
1. **Analyze**: Check the `matchScore` (strong if `> 0.52`).
2. **Summarize**: Provide a concise summary of the found entity.
3. **Table**: Inform the user the full data is in the interactive table.
