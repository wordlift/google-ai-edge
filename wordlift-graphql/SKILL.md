---
name: wordlift-graphql
description: Query the WordLift Knowledge Graph for entities (People, Places, Organizations), products, and SEO performance.
metadata:
  require-secret: true
  require-secret-description: Enter your WordLift API key (found in Settings -> API).
  homepage: https://wordlift.io
---

# WordLift Knowledge Graph Expert

Use this skill to query and explore the Knowledge Graph. It translates natural language into WordLift GraphQL queries.

### 🛡 INVINCIBLE PROTOCOL
To execute a query, you MUST provide the tool call in this exact **Tagged Text** format:
1. Start with `[QUERY]` and provide the raw GraphQL query.
2. End with `[/QUERY]`
3. Start with `[QUESTION]` and provide the original user question.
4. End with `[/QUESTION]`

**Example**:
`run_js(data: "[QUERY] query { entities(page:0, rows:20) { id: iri label: string(name: \"rdfs:label\") } } [/QUERY] [QUESTION] List entities [/QUESTION]")`

---

## 🛠 Query Patterns & Rules

### 1. Root Field Pagination
- **NEVER use `limit`**.
- **ALWAYS use `(page: 0, rows: 20)`** for root fields like `entities`, `products`, `keywords`, and `articles`.

### 2. ID Mapping
- **ALWAYS map `iri` to `id`** (e.g., `id: iri`).

### 3. Schemaless / Specific Entity Access
Use `resource(iri: "...")` for direct access to a known entity's properties.
- **Pattern**: `query { resource(iri: \"$IRI\") { id: iri label: string(name: \"rdfs:label\") description: string(name: \"schema:description\") types: refs(name: \"rdf:type\") } }`

### 4. Search & Discovery
Use `entitySearch` for general questions.
- **Pattern**: `query { entitySearch(query: { search: { string: \"$SEARCH\" } }, page: 0, rows: 20) { id: iri name: string(name: \"seovoc:name\") score: float(name: \"_:score\") } }`

---

## 💡 Expert Examples

- **User**: "Tell me about the person at http://data.wordlift.io/andrea"
  - **Tool call**: `run_js(data: "[QUERY] query { resource(iri: \"http://data.wordlift.io/andrea\") { id: iri label: string(name: \"rdfs:label\") desc: string(name: \"schema:description\") } } [/QUERY] [QUESTION] Get Andrea Volpini details [/QUESTION]")`

- **User**: "Which organizations are in Rome?"
  - **Tool call**: `run_js(data: "[QUERY] query { entities(query: { typeConstraint: { in: [\"http://schema.org/Organization\"] } }, page: 0, rows: 20) { id: iri name: string(name: \"seovoc:name\") } } [/QUERY] [QUESTION] Organizations in Rome [/QUESTION]")`

- **User**: "Show me my top 10 keywords by clicks"
  - **Tool call**: `run_js(data: "[QUERY] query { keywords(sort: {field: \"seovoc:clicks28Days\", direction: DESC}, page: 0, rows: 10) { id: iri keyword: string(name: \"seovoc:name\") clicks: int(name: \"seovoc:clicks28Days\") } } [/QUERY] [QUESTION] Top keywords by clicks [/QUESTION]")`

---

### Interpretation & Summary
After the tool returns `DATA_FOUND`:
1. Analyze the JSON entity data.
2. Summarize the key facts conversationally.
3. Inform the user the full dataset is rendered in the table below.
