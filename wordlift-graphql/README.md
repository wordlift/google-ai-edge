# 🧠 WordLift Knowledge Graph Expert — RLM-on-KG (v16)

An AI Edge Skill implementing the **Recursive Language Model on Knowledge Graph (RLM-on-KG)** protocol, optimized for **edge devices** with limited 128K context windows (e.g., Gemma 4B).

## 🚀 Key Features

### ⚡️ Context Optimization (v16)
To handle the limited 128K context window on mobile devices, v16 introduces **LLM-specific pruning**:
- **UI Full, LLM Lean**: The runner script saves 100% of the raw GraphQL data to `localStorage` for the **Webview**, but returns a **distilled snippet** to the model's context.
- **Snippet Distillation**: Descriptions are truncated to 200 chars and co-mention arrays (`schema:mentions`) are squashed into simple counts to prevent token saturation.
- **Identity Merging**: `Headline`, `Name`, and `Label` are normalized into a single `name` field in the model's view, reducing redundant JSON keys.

### Entity-First 3-Phase Protocol
Aligned with the [RLM-on-KG paper](https://github.com/wordlift/rlm-on-kg):
1. **Discovery** — Broad search returning lean snippets of entities and articles.
2. **Selective Deep Read** — The model identifies an interesting node and explicitly calls `resource(iri)` to "read" the full, untruncated profile.
3. **Evidence Synthesis** — Grounded answers backed by graph-traversal evidence.

### Discovery vs. Ranking Separation
The LLM serves as an **Autonomous Navigator** (discovery), while the system handles ranking via vector similarity (`matchScore`). This allows the model to focus on exploring the graph for "scattered evidence."

### Smart Webview UI
- **Type Badges**: Color-coded badges for Person, Organization, Article, etc.
- **Co-Mention Map**: A dedicated **Mentions** column showing co-mentioned entities as pills.
- **Executive Summary**: Renders grounded findings directly from the model's `[SUMMARY]` tag.

---

## 🛠 Usage & Escalation

- **Quick Search** — Default search for single-entity lookups.
- **RLM Protocol** — Automatically triggered for multi-hop reasoning or relationship discovery.
- **Deep Reads** — Use Phase 2 (`resource(iri)`) to bypass snippet truncation when full context is needed for synthesis.

---

## ⚙️ Technical Stack
- **Core**: HTML5 + Vanilla JS
- **Data**: WordLift GraphQL API (`schema:mentions` for co-mention expansion)
- **Token Control**: Distilled snippets for Gemma 4B / 128K window compatibility.

---
*Built with ❤️ for WordLift AI Explorers.*
