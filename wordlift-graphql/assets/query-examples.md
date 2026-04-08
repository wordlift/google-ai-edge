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

### 1. Neighborhood Expansion
Find entities connected to a topic with full SEO metadata expansion.
```graphql
query {
  entitySearch(query: { search: { string: "Andrea Volpini" } }, page: 0, rows: 3) {
    id: iri
    label: string(name: "rdfs:label")
    name: string(name: "schema:name")
    headline: string(name: "schema:headline")
    url: string(name: "schema:url")
    # Expand neighborhood
    neighbors: refs(name: "schema:about") {
      id: iri
      label: string(name: "rdfs:label")
      name: string(name: "schema:name")
      headline: string(name: "schema:headline")
    }
  }
}
```

### 2. Article Context Exploration
Retrieve an article and its related semantic concepts.
```graphql
query {
  resource(iri: "$IRI") {
    id: iri
    headline: string(name: "schema:headline")
    description: string(name: "schema:description")
    url: string(name: "schema:url")
    related: refs(name: "schema:about") {
      id: iri
      label: string(name: "rdfs:label")
      types: refs(name: "rdf:type")
    }
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

### 4. Match Score Interpretation
- **Score > 0.52**: Strong semantic match.
- **Score < 0.48**: Likely noise or irrelevant.

### 5. Type-Specific Fallbacks
- **CreativeWorks (Articles)**: Use `headline`.
- **Entities (People/Orgs)**: Use `name` or `label`.
- **Linking**: Always map `iri` to `id` for automatic table linking.
