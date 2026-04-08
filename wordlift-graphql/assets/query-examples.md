# WordLift GraphQL — Full Query Reference

This reference contains validated query patterns for the WordLift GraphQL API, optimized for CreativeWorks (Articles) and Entities (People, Orgs, Places).

---

## 🏗 THE ROBUST IDENTITY BLOCK
For the best UI experience, always include these fields in your selection sets:
```graphql
id: iri
label: string(name: "rdfs:label")
name: string(name: "schema:name")
headline: string(name: "schema:headline")
url: string(name: "schema:url")
mainEntityOfPage: string(name: "schema:mainEntityOfPage")
```

---

## 🧠 RLM-on-KG Patterns (Multi-hop)

### 1. Discovery (Turn 1)
Find entities connected to a topic.
```graphql
query {
  entitySearch(query: { search: { string: "Andrea Volpini" } }, page: 0, rows: 3) {
    id: iri
    label: string(name: "rdfs:label")
    name: string(name: "schema:name")
    headline: string(name: "schema:headline")
    url: string(name: "schema:url")
    # Get IRIs of related entities (leaf nodes)
    related: refs(name: "schema:about")
  }
}
```

### 2. Deep reasoning / Pivot & Expand (Turn 2)
Fetch full details for one or more related IRIs discovered in Turn 1.
```graphql
query {
  resource(iri: "http://data.wordlift.io/wl0216/entity/andrea-volpini") {
    id: iri
    label: string(name: "rdfs:label")
    description: string(name: "schema:description")
    url: string(name: "schema:url")
    # Discover secondary connections
    related: refs(name: "schema:about")
  }
}
```

---

## ⚡️ Quick Search & Discovery

### 3. General Discovery Pattern
```graphql
query {
  entitySearch(query: { search: { string: "AI strategy" } }, page: 0, rows: 10) {
    id: iri
    label: string(name: "rdfs:label")
    name: string(name: "schema:name")
    headline: string(name: "schema:headline")
    url: string(name: "schema:url")
    mainEntityOfPage: string(name: "schema:mainEntityOfPage")
    matchScore: float(name: "_:score")
  }
}
```

---

## ⚙️ Data Analysis Rules

### 4. Key Lessons
- **REFS ARE LEAVES**: The `refs(name: "...")` field returns a list of IRI strings. You **cannot** use a sub-selection `{ id label }` on it. Doing so will trigger a `SubSelectionNotAllowed` error.
- **IDENTITY MERGE**: The UI merges `label`, `name`, and `headline` into a single **Entity** column.
- **LINKING**: Always map `iri` to `id` for automatic table linking.
