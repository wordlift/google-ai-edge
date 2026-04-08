# 🪄 WordLift Knowledge Graph Expert (v13)

A sophisticated AI Edge Skill designed for **Deep Exploratory Search** and **Relational Reasoning** over the WordLift Knowledge Graph. This skill enables the agent to navigate complex semantic relationships (RLM-on-KG) and present them in a premium, interactive interface.

---

## 🚀 Key Features

### 🧠 Advanced RLM-on-KG Reasoning
Supports the **Recursive Language Model (RLM)** protocol for multi-hop discovery.
*   **Discovery**: Find initial nodes via vector and keyword search.
*   **Expansion**: Navigate through `refs` to uncover connected entities.
*   **Synthesis**: Grounded answers backed by graph evidence.

### 🎭 Smart Identity Merging
The UI intelligently prioritizes identifiers to ensure articles and entities are always recognizable:
1.  **Headline** (SEO titles for articles)
2.  **Name** (Specific names for People/Orgs)
3.  **Label** (Generic fallback)

### ⛽ Zero-Payload URL Strategy
Eliminates "URI Too Long" errors by using a unified `localStorage` transport. Large GraphQL result sets are packed into a local buffer, keeping the WebView URL stable and minimal.

### 🔗 interactive KG Linking
All results include clickable **KG Link (IRI)** references, allowing direct navigation to the raw knowledge base resources.

---

## 🛠 Installation & Setup

1.  **Import**: In your AI Agent environment, import the skill using the URL to this repository.
2.  **Secret Management**: When prompted, enter your **WordLift API Key** (found in your WordLift dashboard settings).
3.  **Version**: Ensure you are running **v13** for the latest protocol fixes and UI performance.

---

## ⚡ Technical Protocol (Tagged Text)

The skill uses a custom **Tagged Text** protocol to coordinate search, summary, and next-best-question discovery:

```graphql
[QUERY]
query {
  entitySearch(...) {
    id: iri
    headline: string(name: "schema:headline")
  }
}
[/QUERY]
[SUMMARY] ... Natural language findings ... [/SUMMARY]
[QUESTION] ... Next exploration path ... [/QUESTION]
```

---

## 🛠 Technical Stack
*   **Core**: HTML5 + Vanilla JavaScript
*   **styling**: CSS3 (Modern Glassmorphism & Type Scales)
*   **Data**: WordLift GraphQL API
*   **Transport**: Unified LocalStorage Payload (Zero-Payload Strategy)

---

## ⚙️ Maintenance
To reset the API key or upgrade:
1.  Delete the existing `wordlift-ai-expert` skill.
2.  Re-import from the repository URL.
3.  Refresh the WebView to v13.

---
*Built with ❤️ for WordLift AI Explorers.*
