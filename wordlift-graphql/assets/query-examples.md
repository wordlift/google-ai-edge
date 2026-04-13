# WordLift GraphQL — Validated Query Patterns (RLM-on-KG)

All patterns below have been tested against the live WordLift GraphQL API.

---

## Phase 1: Entity Discovery (entitySearch)

Broad search returning entities AND articles with types and co-mentions.

```graphql
query {
  entitySearch(query: { search: { string: "Andrea Volpini" } }, page: 0, rows: 20) {
    id: iri
    label: string(name: "rdfs:label")
    name: string(name: "schema:name")
    headline: string(name: "schema:headline")
    description: string(name: "schema:description")
    types: refs(name: "rdf:type")
    mentions: refs(name: "schema:mentions")
    matchScore: float(name: "_:score")
  }
}
```

**IRI patterns:**
- `/entity/` → core entity (Person, Organization, Place, CreativeWork)
- `/post/` → article/blog post

---

## Phase 2a: Expand Neighbors (via schema:mentions)

Given an article IRI, discover which entities it mentions. This is the
equivalent of `expand_neighbors` in the RLM-on-KG paper.

```graphql
query {
  resource(iri: "http://data.wordlift.io/wl0216/post/unraveling-the-mystery-of-the-voynich-manuscript-28093") {
    id: iri
    headline: string(name: "schema:headline")
    description: string(name: "schema:description")
    mentions: refs(name: "schema:mentions")
  }
}
```

**Returns** (example): `["umberto_eco", "artificial_intelligence", "knowledge_graph", "llm-25790"]`

---

## Phase 2b: Read Entity Profile

Fetch the full profile of a specific entity IRI discovered via mentions.

```graphql
query {
  resource(iri: "http://data.wordlift.io/wl0216/entity/wordlift") {
    id: iri
    name: string(name: "schema:name")
    description: string(name: "schema:description")
    types: refs(name: "rdf:type")
    sameAs: refs(name: "schema:sameAs")
    url: string(name: "schema:url")
  }
}
```

---

## Phase 2c: Search for Co-Mentioned Entities

Once you have entity IRIs from mentions, search for content that references them.

```graphql
query {
  entitySearch(query: { search: { string: "knowledge graph" } }, page: 0, rows: 10) {
    id: iri
    headline: string(name: "schema:headline")
    description: string(name: "schema:description")
    types: refs(name: "rdf:type")
    mentions: refs(name: "schema:mentions")
  }
}
```

---

## Technical Rules

1. **`refs(...)` are LEAF NODES** — they return IRI strings. NO sub-selections `{ id label }`.
2. **`schema:mentions`** = article → entities it references (co-mention graph).
3. **`schema:about`** = typed "about" relations (often empty, less useful for expansion).
4. **`schema:sameAs`** = cross-KG links (Wikidata, DBpedia, Google KG).
5. **Identity merge**: UI combines `headline > name > label` into one "Entity" column.
6. **Always map `iri` to `id`** for automatic table linking.
