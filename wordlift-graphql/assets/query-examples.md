# WordLift GraphQL — Full Query Reference

This reference contains the latest, validated query patterns for the WordLift GraphQL API.

---

## 🔎 Discovery & Search (entitySearch)

### 1. Basic Working Pattern (Recommended)
This is the most reliable base for general discovery. It ensures URIs resolve and types are included.
```graphql
query {
  entitySearch(query: { search: { string: "Artificial Intelligence experts" } }, page: 0, rows: 20) {
    id: iri
    matchScore: float(name: "_:score")
    label: string(name: "rdfs:label")
    url: string(name: "schema:url")
    types: refs(name: "rdf:type")
    datePublished: string(name: "schema:datePublished")
  }
}
```

### 2. With Nested SEO Insights
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
      clicks: int(name: "seovoc:clicks28Days")
    }
  }
}
```

### 3. Schemaless Fallback (Entity Discovery)
Best for exploring unknown graphs or pulling arbitrary metadata.
```graphql
query {
  entitySearch(query: { search: { string: "Andrea Volpini" } }, page: 0, rows: 20) {
    id: iri
    matchScore: float(name: "_:score")
    label: string(name: "rdfs:label")
    name: string(name: "schema:name")
    description: string(name: "schema:description")
    author: string(name: "schema:author")
    image: string(name: "schema:image")
  }
}
```

---

## 🔎 Deep Enrichment (resource)

Take an IRI found from a search and drill into it to get a full semantic profile.

### 4. Full Drill-down Pattern
```graphql
query {
  resource(iri: "http://data.wordlift.io/wl0216/post/...") {
    id: iri
    label: string(name: "rdfs:label")
    types: refs(name: "rdf:type")
    relatedEntities: refs(name: "schema:about")
    keywords: strings(name: "seovoc:keywords")
    mainContent: string(name: "schema:articleBody")
  }
}
```

---

## 🔎 Data Analysis Rules

### 5. Match Score Interpretation
WordLift `entitySearch` scores tend to cluster tightly (e.g., 0.48 to 0.52). 
- **Score > 0.52**: Strong semantic match.
- **Score < 0.48**: Likely noise or irrelevant.

### ⚙️ Pagination & ID Standard
- **ALWAYS** use `(page: 0, rows: X)` for root fields.
- **ALWAYS** map `iri` to `id`.

---

## 🔎 SEO & Keywords Reference

### 6. Top Keywords by Performance
```graphql
query {
  keywords(
    sort: { field: "seovoc:impressions28Days", direction: DESC }
    page: 0, rows: 20
  ) {
    id: iri
    keyword: string(name: "seovoc:name")
    impressions: int(name: "seovoc:impressions28Days")
    clicks: int(name: "seovoc:clicks28Days")
  }
}
```
