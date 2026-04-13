# 🧠 WordLift Knowledge Graph Expert — RLM-on-KG (v15)

An AI Edge Skill implementing the **Recursive Language Model on Knowledge Graph (RLM-on-KG)** protocol for entity-first, multi-hop exploration over the WordLift Knowledge Graph.

> *"Let the LLM explore but let the vectors decide."* — RLM-on-KG Paper

---

## 🚀 Key Features

### Entity-First 3-Phase Protocol
Aligned with the [RLM-on-KG paper](https://github.com/wordlift/rlm-on-kg):
1. **Discovery** — Broad search returning entities and articles with their types and co-mentions
2. **Neighborhood Expansion** — Follow `schema:mentions` links to discover co-mentioned entities (equivalent to `expand_neighbors`)
3. **Evidence Synthesis** — Grounded answers backed by graph-traversal evidence

### Discovery vs. Ranking Separation
The core architectural insight: the LLM's value is in **candidate discovery** (finding entities that vector search alone cannot reach), not ranking. Pure vector similarity (`matchScore`) handles ranking.

### Selective Escalation
Automatic controller selection based on question complexity:
- **Quick Search** (1 API call) — for single-entity lookups
- **RLM 3-Phase** (3-5 calls) — for multi-entity relationships
- **RLM Deep** (5-8 calls) — for scattered evidence requiring multi-hop traversal

### Smart Identity Merging
UI intelligently prioritizes: **Headline** > **Name** > **Label** for entity display.

### Type Badges & Co-Mention Map
Color-coded type badges (Person, Organization, Article, etc.) and a **Mentions** column showing co-mentioned entities as clickable pills.

---

## 🛠 Installation

1. Import the skill in your AI Agent environment using this repository URL
2. Enter your **WordLift API Key** when prompted
3. Ensure you are running **v15** for the RLM-on-KG aligned protocol

---

## ⚙️ Technical Stack
- **Core**: HTML5 + Vanilla JavaScript
- **Data**: WordLift GraphQL API (`schema:mentions` for co-mention expansion, `schema:sameAs` for cross-KG links)
- **Transport**: LocalStorage payload (zero-URL strategy)
- **Protocol**: RLM-on-KG Entity-First Exploration (3-phase)

---

## 📖 Reference
- [RLM-on-KG Paper](https://github.com/wordlift/rlm-on-kg) — *Heuristics First, LLMs When Needed*
- [WordLift](https://wordlift.io) — AI-powered SEO and Knowledge Graph platform

---
*Built with ❤️ for WordLift AI Explorers.*
