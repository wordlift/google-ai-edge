# WordLift GraphQL — Full Query Reference

This reference contains the latest, validated query patterns for the WordLift GraphQL API, including advanced RLM-on-KG (Recursive Language Model on Knowledge Graph) multi-hop navigation.

---

## 🧠 RLM-on-KG Patterns (Multi-hop)

### 1. Neighborhood Expansion (Advanced Reasoning)
Use this to find entities connected to a specific topic via shared mentions or properties.
```graphql
query {
  entitySearch(query: { search: { string: "Andrea Volpini" } }, page: 0, rows: 3) {
    id: iri
    label: string(name: "rdfs:label")
    # Expand neighborhood of each found entity
    neighbors: refs(name: "schema:about") {
      neighborId: iri
      neighborLabel: string(name: "rdfs:label")
      neighborDesc: string(name: "schema:description")
    }
  }
}
```

### 2. Multi-hop Relationship Drill-down
Deep exploration of a specific entity's context and its related keywords.
```graphql
query {
  resource(iri: "$IRI") {
    id: iri
    label: string(name: "rdfs:label")
    description: string(name: "schema:description")
    # Hop 1: Related entities
    related: refs(name: "schema:about") {
      id: iri
      label: string(name: "rdfs:label")
      # Hop 2: Keywords of related entities
      keywords: strings(name: "seovoc:keywords")
    }
  }
}
```

---

## ⚡️ Quick Search & Discovery

### 3. Basic Working Pattern (Recommended)
This is the most reliable base for general discovery.
```graphql
query {
  entitySearch(query: { search: { string: "Artificial Intelligence experts" } }, page: 0, rows: 20) {
    id: iri
    matchScore: float(name: "_:score")
    label: string(name: "rdfs:label")
    url: string(name: "schema:url")
    types: refs(name: "rdf:type")
  }
}
```

### 4. Nested SEO Insights
Shows the top performing search queries associated with the entities.
```graphql
query {
  entitySearch(query: { search: { string: "SEO strategy articles" } }, page: 0, rows: 20) {
    id: iri
    label: string(name: "rdfs:label")
    topKeywords: topN(
      name: "seovoc:hasQuery"
      sort: { field: "seovoc:impressions28Days", direction: DESC }
      page: 0, rows: 3
    ) {
      keyword: string(name: "seovoc:name")
      impressions: int(name: "seovoc:impressions28Days")
    }
  }
}
```

---

## ⚙️ Data Analysis Rules

### 5. Match Score Interpretation
- **Score > 0.52**: Strong semantic match.
- **Score < 0.48**: Likely noise or irrelevant.

### ⚙️ Pagination & ID Standard
- **ALWAYS** use `(page: 0, rows: X)` for root fields.
- **ALWAYS** map `iri` to `id`.
