## 🔎 Knowledge Graph Exploration (Schemaless)

Use these patterns to explore any entity (Person, Organization, Place, etc.) by its IRI.

### 1. Get All String Properties for an Entity
```graphql
query {
  resource(iri: "https://data.wordlift.io/entity/andrea-volpini") {
    label: string(name: "rdfs:label")
    description: string(name: "schema:description")
    jobTitle: string(name: "schema:jobTitle")
    image: string(name: "schema:image")
  }
}
```

### 2. Find Related Entities (References)
```graphql
query {
  resource(iri: "https://data.wordlift.io/entity/wordlift") {
    name: string(name: "rdfs:label")
    founder: ref(name: "schema:founder")
    memberOf: refs(name: "schema:memberOf")
  }
}
```

### 3. Entity Search (Vector/Discovery)
```graphql
query {
  entitySearch(
    query: {
      search: { string: "Artificial Intelligence experts in Rome" }
    }
  ) {
    iri
    name: string(name: "seovoc:name")
    matchScore: float(name: "_:score")
  }
}
```

---

## 🔎 SEO & Keywords Reference

### 1. Schemaless Querying
```graphql
query {
  resource(iri: "http://example.org/persons/rupert") {
    name: string(name: "rdfs:label")
    place: ref(name: "schema:location")
  }
}
```

---

## 🛠 Special Filters (Constraint)
- `in`, `notIn`, `contains`
- `regex`, `between`, `gt`, `gte`, `lt`, `lte`
- `exists`

---

## 💡 Example GraphQL Queries

### 1: Top keyword for a URL
```graphql
query entities_top_query($url: String!) {
  data(query: { urlConstraint: { in: [$url] } }) {
    iri
    top_query: topN(
      name: "seovoc:hasQuery"
      sort: { field: "seovoc:impressions3Months", direction: DESC }
      limit: 1
    ) {
      name: string(name: "seovoc:name")
      impressions: int(name: "seovoc:impressions3Months")
    }
  }
}
```

### 2: Articles by author (regex)
```graphql
query {
  articles(
    rows: 25
    query: {
      authorConstraint: { regex: { pattern: "^(entity/andrea_volpini)$" } }
    }
  ) {
    id: iri
    author: string(name: "schema:author")
  }
}
```

### 3: Product details
```graphql
query {
  products(page: 0, rows: 20) {
    id: iri
    brand: string(name: "schema:brand")
    category: string(name: "eyewear:category")
  }
}
```

### 4: Filter by type
```graphql
query {
  entities(
    page: 1
    rows: 20
    query: {
      typeConstraint: {
        in: ["http://schema.org/WebPage", "http://schema.org/Product"]
      }
    }
  ) {
    id: iri
    types: refs(name: "rdf:type")
  }
}
```

### 5: FAQ with nested Q&A
```graphql
query {
  faqPages {
    pub_date: string(name: "schema:datePublished")
    questions: resources(name: "schema:mainEntity") {
      question: string(name: "schema:name")
      answer: resources(name: "schema:acceptedAnswer") {
        text: string(name: "schema:text")
      }
    }
  }
}
```

### 6: Entity by URL with top keywords
```graphql
query {
  entity(url: "https://www.example.com/product") {
    iri
    topKeywords: topN(
      name: "ns1:seoKeywords"
      sort: { field: "ns1:7DaysClicks", direction: DESC }
      limit: 4
    ) {
      sevenDaysClicks: int(name: "ns1:7DaysClicks")
    }
  }
}
```

### 7: Products with price and image
```graphql
query {
  products(page: 0, rows: 20) {
    id: iri
    brand: resource(name: "schema:brand") {
      brand: string(name: "schema:name")
    }
    price: resource(name: "schema:offers") {
      price: string(name: "schema:price")
    }
    image: string(name: "schema:image")
  }
}
```

### 8: Events with filters and sorting
```graphql
query {
  events(
    query: {
      name: "Music Festival"
      start: ["2023-06-01", "2023-08-31"]
      location: { name: "Berlin" }
    }
    page: 0
    rows: 20
    orderBy: [start_DESC]
  ) {
    iri
    name
    start
  }
}
```

### 9: Filter products by brand and creation date
```graphql
query {
  products(
    query: {
      brandConstraint: { in: ["BrandA", "BrandB"] }
      createdConstraint: {
        between: { lower: "2023-01-01", upper: "2023-12-31" }
      }
    }
    page: 0
    rows: 10
  ) {
    iri
    brand: string(name: "schema:brand")
  }
}
```

### 10: New keywords gaining traction (past week)
```graphql
query {
  keywords(
    sort: { field: "seovoc:impressions7Days", direction: DESC }
    filter: { field: "seovoc:age", operator: "LT", value: 7 }
    limit: 10
  ) {
    keyword: string(name: "seovoc:name")
    impressions: int(name: "seovoc:impressions7Days")
  }
}
```

### 11: Top keywords for articles by author (Andrea Volpini)
```graphql
query {
  articles(
    query: {
      authorConstraint: { regex: { pattern: "^(entity/andrea_volpini)$" } }
    }
  ) {
    topKeywords: topN(
      name: "seovoc:hasQuery"
      sort: { field: "seovoc:impressions30Days", direction: DESC }
      limit: 5
    ) {
      name: string(name: "seovoc:name")
    }
  }
}
```

### 12: Biggest drop in impressions (past month)
```graphql
query {
  keywords(
    sort: { field: "seovoc:impressionsChange", direction: ASC }
    filter: { field: "seovoc:impressionsChange", operator: "LT", value: 0 }
    limit: 10
  ) {
    keyword: string(name: "seovoc:name")
    change: int(name: "seovoc:impressionsChange")
  }
}
```

### 13: High impressions, low competition
```graphql
query {
  keywords(
    sort: { field: "seovoc:impressions30Days", direction: DESC }
    filter: { field: "seovoc:competition", operator: "LT", value: 0.2 }
    limit: 10
  ) {
    keyword: string(name: "seovoc:name")
    impressions: int(name: "seovoc:impressions30Days")
    competition: float(name: "seovoc:competition")
  }
}
```

### 14: Pages with high impressions, low CTR
```graphql
query {
  entities(
    sort: { field: "seovoc:impressions28Days", direction: DESC }
    filter: { field: "seovoc:ctr28Days", operator: "LT", value: 0.02 }
    limit: 10
  ) {
    pageTitle: string(name: "seovoc:title")
    url: string(name: "schema:url")
    ctr: float(name: "seovoc:ctr28Days")
  }
}
```

### 15: Top performing keywords for 'Technology' category
```graphql
query {
  entities(
    query: { categoryConstraint: { in: ["Technology"] } }
  ) {
    topKeywords: topN(
      name: "seovoc:hasQuery"
      sort: { field: "seovoc:impressions3Months", direction: DESC }
      limit: 10
    ) {
      name: string(name: "seovoc:name")
    }
  }
}
```

### 16: Significant YoY increase
```graphql
query {
  keywords(
    sort: { field: "seovoc:impressions28Days", direction: DESC }
    filter: { field: "seovoc:impressionsYearOverYearChange", operator: "GT", value: 0.5 }
    limit: 10
  ) {
    keyword: string(name: "seovoc:name")
    change: float(name: "seovoc:impressionsYearOverYearChange")
  }
}
```

### 17: Highest bounce rate pages
```graphql
query {
  entities(
    sort: { field: "seovoc:bounceRate28Days", direction: DESC }
    limit: 10
  ) {
    pageTitle: string(name: "seovoc:title")
    bounceRate: float(name: "seovoc:bounceRate28Days")
  }
}
```

### 18: Keywords driving traffic to blog section
```graphql
query {
  entities(
    query: { urlConstraint: { regex: { pattern: "^/blog/" } } }
  ) {
    topKeywords: topN(
      name: "seovoc:hasQuery"
      sort: { field: "seovoc:clicks28Days", direction: DESC }
      limit: 10
    ) {
      name: string(name: "seovoc:name")
      clicks: int(name: "seovoc:clicks28Days")
    }
  }
}
```

### 19: Sarah Johnson article performance (Aggregate)
```graphql
query {
  articles(
    query: { authorConstraint: { regex: { pattern: "^(entity/sarah_johnson)$" } } }
  ) {
    totalImpressions: sum(field: "seovoc:impressions3Months")
    averageCtr: avg(field: "seovoc:ctr3Months")
  }
}
```

### 20: Biggest decline in clicks
```graphql
query {
  keywords(
    sort: { field: "seovoc:clicksChange", direction: ASC }
    filter: { field: "seovoc:clicksChange", operator: "LT", value: 0 }
    limit: 10
  ) {
    keyword: string(name: "seovoc:name")
    clicksChange: int(name: "seovoc:clicksChange")
  }
}
```

### 21: Most inbound links (year)
```graphql
query {
  entities(
    sort: { field: "seovoc:inboundLinksYear", direction: DESC }
    limit: 10
  ) {
    pageTitle: string(name: "seovoc:title")
    inboundLinks: int(name: "seovoc:inboundLinksYear")
  }
}
```

### 22: Blog traffic keywords (Alternative)
```graphql
query {
  entities(
    query: { hasURL: { regex: "^/blog/" } }
    sort: { field: "seovoc:clicks28Days", direction: DESC }
    limit: 10
  ) {
    keywords: topN(
      name: "seovoc:hasQuery"
      sort: { field: "seovoc:clicks28Days", direction: DESC }
      limit: 10
    ) {
      name: string(name: "seovoc:name")
    }
  }
}
```

### 23: Videos with highest impressions
```graphql
query {
  entities(
    query: { containsVideo: { exists: true } }
    sort: { field: "seovoc:impressions28Days", direction: DESC }
    page: 0, rows: 5
  ) {
    id: iri
    title: string(name: "seovoc:title")
    impressions: int(name: "seovoc:impressions28Days")
  }
}
```

### 24: Jane Smith article keywords (Lowest CTR)
```graphql
query {
  entities(
    query: { hasAuthor: { name: "Jane Smith" } }
    sort: { field: "seovoc:ctr3Months", direction: ASC }
    limit: 10
  ) {
    title: string(name: "seovoc:title")
    keywords: topN(
      name: "seovoc:hasQuery"
      sort: { field: "seovoc:ctr3Months", direction: ASC }
      limit: 10
    ) {
      name: string(name: "seovoc:name")
      ctr: float(name: "seovoc:ctr3Months")
    }
  }
}
```

### 25: Products with highest clicks
```graphql
query {
  entities(
    query: { type: "Product" }
    sort: { field: "seovoc:clicks28Days", direction: DESC }
    limit: 10
  ) {
    productName: string(name: "seovoc:title")
    clicks: int(name: "seovoc:clicks28Days")
  }
}
```

### 26: Products with high impressions, low CTR
```graphql
query {
  entities(
    query: { type: "Product" }
    sort: { field: "seovoc:impressions28Days", direction: DESC }
    filter: { field: "seovoc:ctr28Days", operator: "LT", value: 0.02 }
    limit: 10
  ) {
    productName: string(name: "seovoc:title")
    ctr: float(name: "seovoc:ctr28Days")
  }
}
```

### 27: Top performing products in 'Electronics'
```graphql
query {
  entities(
    query: { categoryConstraint: { in: ["Electronics"] }, type: "Product" }
    sort: { field: "seovoc:clicks3Months", direction: DESC }
    limit: 5
  ) {
    productName: string(name: "seovoc:title")
    clicks: int(name: "seovoc:clicks3Months")
  }
}
```

### 28: Keywords driving traffic to product pages
```graphql
query {
  entities(
    query: { type: "Product" }
    sort: { field: "seovoc:clicks28Days", direction: DESC }
    limit: 10
  ) {
    productName: string(name: "seovoc:title")
    keywords: topN(
      name: "seovoc:hasQuery"
      sort: { field: "seovoc:clicks28Days", direction: DESC }
      limit: 10
    ) {
      keyword: string(name: "seovoc:name")
    }
  }
}
```

### 29: Product pages with highest bounce rate
```graphql
query {
  entities(
    query: { type: "Product" }
    sort: { field: "seovoc:bounceRate", direction: DESC }
    limit: 10
  ) {
    productName: string(name: "seovoc:title")
    bounceRate: float(name: "seovoc:bounceRate")
  }
}
```

### 30: Metrics for top keywords (Multi-window)
```graphql
query {
  entities(page: 0, rows: 10) {
    seoKeywords: resources(name: "seovoc:hasQuery") {
      name: string(name: "seovoc:name")
      sevenDaysClicks: int(name: "seovoc:clicks7Days")
      twentyEightDaysClicks: int(name: "seovoc:clicks28Days")
      threeMonthsClicks: int(name: "seovoc:clicks3Months")
    }
  }
}
```
