# AI Gmail Auto-Reply Automation (n8n + Ollama)

This project is a fully automated Gmail reply system powered by **n8n**, **Ollama**, and **Google Sheets**.  
Incoming emails are parsed, processed by a local LLM, replied to automatically, and finally logged into a Google Sheet for tracking.

---

## 🚀 Features

- **Auto-detects new Gmail messages**
- **Uses a local LLM via Ollama** to generate smart, context-aware replies  
- **Parses and cleans LLM output** using a robust Response parser node  
- **Automatically replies to the sender**
- **Logs every interaction** (email, reply, metadata) into Google Sheets  
- **Works fully offline** except Gmail + Sheets integrations  
- **No OpenAI API required**

---

## 🧩 Workflow Structure

Gmail Trigger
↓
Prompt (extract email + build prompt)
↓
Ollama Chat Model (local LLM)
↓
Response (LLM output parser)
↓
Google Sheets (log email + reply)
↓
Reply to Email



---

## 📁 Logged Data (Google Sheets)

The automation stores each interaction with these fields:

| Column         | Description                                 |
|----------------|---------------------------------------------|
| `date`         | Timestamp of reply                          |
| `from`         | Sender email address                        |
| `to`           | Recipient address (your account)            |
| `subject`      | Email subject                               |
| `original_body`| Plain text extracted from Gmail email       |
| `reply_body`   | AI-generated response                       |
| `message_id`   | Gmail message ID / thread reference         |

Expressions reference the `Prompt` and `Response` nodes for consistent data flow.

---

## 🛠 Configuration Overview

### **1. Gmail Trigger**
Pulls new incoming emails and exposes:
- `from`
- `to`
- `subject`
- `body.plainText`
- `messageId`

### **2. Prompt Node (JS)**
Cleans Gmail data and builds a structured prompt for Ollama.

### **3. Ollama Chat Model**
Runs a local model (e.g., `phi3`, `llama3`, `mistral`).

### **4. Response Node (JS)**
Parses unpredictable Ollama output formats and returns a clean:

``{
  "replyText": "...",
  "from": "...",
  "subject": "...",
  "threadId": "...",
  "messageId": "..."
}``

### **5. Google Sheets Node**
Appends logged data using expressions such as:
``
{{ $node["Prompt"].json["from"] }}
{{ $node["Response"].json["replyText"] }}
``

### **6. Reply Node**
Sends the generated reply back to Gmail using the message/thread ID.

📦 Requirements
n8n (self-hosted or desktop)
Ollama installed locally
Google Cloud connection for Gmail + Sheets
Working Gmail address
