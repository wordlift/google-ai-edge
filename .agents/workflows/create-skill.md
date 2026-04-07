---
description: How to create a new Google AI Edge Skill for Gemma models
---

Creating a new Google AI Edge Skill allows you to extend the capabilities of the Gemma model (including text, multimodal, and external tool usage). Follow these steps to scaffold a new skill:

1. **Create the Skill Folder**
   Determine the root directory of your project (e.g., `google-ai-edge-skills/`) and create a new folder named after your skill.
   ```bash
   mkdir -p my-new-skill
   ```

2. **Initialize `SKILL.md`**
   Inside the new folder, create a `SKILL.md` file. This file acts as the primary configuration and documentation entry point for your skill.
   ```bash
   touch my-new-skill/SKILL.md
   ```

3. **Add YAML Frontmatter**
   Open `SKILL.md` and define the metadata at the top. This metadata configures the agent/model on how to treat the skill.
   ```markdown
   ---
   name: my-new-skill
   description: A short description of what this skill enables Gemma to do.
   metadata:
     require-secret: false # Set to true if an API key is required
   ---
   ```

4. **Define the Prompt & Protocol Structure**
   Below the YAML frontmatter in `SKILL.md`, write clear Markdown documenting how the skill should be triggered and used.
   - Outline the intended **use cases**.
   - Define exact **patterns/commands** the LLM should use (e.g., specific JSON formats or JS functions).
   - If using external APIs or tool calls (e.g., `run_js(data: "...")`), document exactly what inputs are expected and what the system will return.

5. **(Optional) Add Assets or Helper Scripts**
   If the skill uses a webview, images, or examples, create subdirectories for them:
   ```bash
   mkdir -p my-new-skill/assets
   mkdir -p my-new-skill/scripts
   ```
   *Example: In the `wordlift-graphql` skill, we included robust query examples in `assets/query-examples.md` and a clean UI in `assets/webview.html`.*

6. **Validate the Setup**
   Test the new skill by invoking the Gemma model within the context of the folder. Ensure the model correctly reads the prompt instructions from the `SKILL.md` and executes the defined protocol seamlessly.
