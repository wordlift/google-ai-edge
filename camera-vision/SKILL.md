---
name: gemma-camera-vision
description: Integration for device camera analysis using Gemma 4 natively
metadata:
  require-secret: false
---

# Camera Vision Skill

As per the Gemma 4 model card, Gemma models support multimodal media inputs natively. This skill allows the model to capture image frames directly from the device camera and use them as media context for analyzing real-world scenes.

## 🚀 CAMERA SNAPSHOT PROTOCOL
To analyze the user's environment or what they are looking at, you will trigger a camera snapshot.
- **Usage**: `run_js(data: "{ \"action\": \"take_picture\", \"device\": \"front\" }")`
- **Result**: The system will automatically snap a picture, pass it as a multimodal image attachment, and return the `MEDIA_ATTACHED` signal along with the image properties.

---

## 🛠 ADVANCED PROTOCOL (CUSTOM CONFIGURATION)
For more specific camera requests, you can specify resolution or camera direction (front/back) using JSON format inside `run_js`:

`run_js(data: "{\n  \"action\": \"take_picture\",\n  \"device\": \"back\",\n  \"resolution\": \"high\"\n}")`

### Core Principles
1. **DEVICE SELECTION**: Default to `back` (environment) unless the user specifically asks for a selfie, in which case use `front`.
2. **RESOLUTION**: Valid options are `low`, `medium`, and `high`. Use `high` when the user asks to read text or needs detailed analysis. Use `low` or `medium` for general scene descriptions to save processing time.
3. **ONLY ASK ONCE**: Do not ask the user for permission if they already requested you to "look at this" or "what do you see". Automatically execute the snapshot.

---

## 🏗 PATTERN REFERENCE

### 1. Scene Description
The user asks you what is around them or what they are holding.
- **Tool call**: `run_js(data: "{ \"action\": \"take_picture\", \"device\": \"back\", \"resolution\": \"medium\" }")`

### 2. Reading Text (OCR) / Detailed Analysis
The user wants you to read a document, menu, or sign.
- **Tool call**: `run_js(data: "{ \"action\": \"take_picture\", \"device\": \"back\", \"resolution\": \"high\" }")`

### 3. User Selfie / Emotion Recognition
The user asks "how do I look?" or "take a selfie".
- **Tool call**: `run_js(data: "{ \"action\": \"take_picture\", \"device\": \"front\", \"resolution\": \"medium\" }")`

---

## 💡 Expert Examples

- **User**: "What do you see in front of me?"
  - **Tool call**: `run_js(data: "{ \"action\": \"take_picture\", \"device\": \"back\" }")`

- **User**: "Can you read this receipt for me?"
  - **Tool call**: `run_js(data: "{ \"action\": \"take_picture\", \"device\": \"back\", \"resolution\": \"high\" }")`

- **User**: "Do I look tired?"
  - **Tool call**: `run_js(data: "{ \"action\": \"take_picture\", \"device\": \"front\", \"resolution\": \"high\" }")`

### Interpretation & Summary
After the tool returns `MEDIA_ATTACHED`:
1. **Analyze**: Rely entirely on the newly attached image to answer the user's prompt.
2. **Summarize**: Provide a descriptive, accurate, and helpful response based solely on the visual content.
