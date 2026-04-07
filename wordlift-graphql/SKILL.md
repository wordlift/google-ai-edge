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

### 📜 STRICT RULES FOR TOOL CALLS
- **SINGLE LINE ONLY**: The `data` JSON must be a single line. NO NEWLINES.
- **ESCAPING**: Escape all double quotes within the query string: `\"`.
- **LIMIT**: Always specify `limit: 20` or `rows: 20`.

---

## 🛠 Query Patterns

### 1. Schemaless / Specific Entity Access
Use `resource(iri: "...")` when you have an entity's IRI. This is the most robust way to get all properties (`string`, `int`, `ref`).
- **Pattern**: `query { resource(iri: \"$IRI\") { name: string(name: \"rdfs:label\") description: string(name: \"schema:description\") types: refs(name: \"rdf:type\") } }`

### 2. Search & Discovery
Use `entitySearch` for general questions about "who", "what", or "where".
- **Pattern**: `query { entitySearch(query: { search: { string: \"$SEARCH_TERM\" } }) { iri name: string(name: \"seovoc:name\") score: float(name: \"_:score\") } }`

### 3. Entity Collections by Type
Use `entities` with `typeConstraint` for categories.
- **Pattern**: `query { entities(query: { typeConstraint: { in: [\"$TYPE_URL\"] } }, limit: 20) { title: string(name: \"seovoc:title\") url: string(name: \"schema:url\") } }`

---

## 💡 Examples (Expert Usage)

- **User**: "Tell me about the person at http://data.wordlift.io/andrea"
  - **Tool call**: `run_js(data: "{\"query\": \"query { resource(iri: \\\"http://data.wordlift.io/andrea\\\") { label: string(name: \\\"rdfs:label\\\") desc: string(name: \\\"schema:description\\\") } }\", \"question\": \" Andrea Volpini details\"}")`

- **User**: "Which organizations are in Rome?"
  - **Tool call**: `run_js(data: "{\"query\": \"query { entitySearch(query: { search: { string: \\\"organizations in Rome\\\" } }) { name: string(name: \\\"seovoc:name\\\") iri } }\", \"question\": \"Organizations in Rome\"}")`

- **User**: "Show me the top performing keywords last month"
  - **Tool call**: `run_js(data: "{\"query\": \"query { keywords(sort: {field: \\\"seovoc:impressions28Days\\\", direction: DESC}, limit: 20) { name: string(name: \\\"seovoc:name\\\") impressions: int(name: \\\"seovoc:impressions28Days\\\") } }\", \"question\": \"Top keywords this month\"}")`

### 🔎 Reference
For complex filters, SEO metrics, and custom predicates, see **`assets/query-examples.md`**.

---

### Interpretation & Summary
After the tool returns `DATA_FOUND`:
1. Analyze the JSON entity data. 
2. Summarize the key facts conversationally.
3. Inform the user the full dataset is rendered in the table below.
