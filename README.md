# n8n Workflows Collection

A collection of major n8n workflows I've created for automation, AI agents, and integrations.

---

## 🦊 FoxBrain AI Agent

**RAG-Powered Knowledge Assistant for Team Foxtrot GIKI**

An AI chatbot that helps freshers understand Team Foxtrot's codebase by searching through GitHub repositories and answering questions using Gemini LLM + Pinecone vector search.

**Tech:** n8n, Google Gemini, Pinecone, HuggingFace, React, Supabase

### Workflows

#### RAG Agent
The main chat interface - receives user messages via webhook, retrieves relevant context from Pinecone, and generates answers using Gemini.

![RAG Agent](FoxBrain_AI_Agent/rag-agent-workflow.png)

#### Data Ingestion Pipeline (Parent → Child → GrandChild)

A three-tier workflow architecture that crawls **all branches** (not just main) from every repository in the GitHub organization and embeds files into Pinecone.

**Architecture:**
```
Parent (Lists Repos) → Child (Lists Branches) → GrandChild (Fetches & Embeds Files)
```

**Parent Workflow** — Fetches all repositories from the Team-Foxtrot-GIKI GitHub org and loops through each, calling the Child workflow.

![Parent Workflow](FoxBrain_AI_Agent/foxtrot-dataflow-parent.png)

**Child Workflow** — Receives a repo name, fetches all branches for that repo via GitHub API, and loops through each branch calling the GrandChild with repo + branch info.

![Child Workflow](FoxBrain_AI_Agent/foxtrot-dataflow-child.png)

**GrandChild Workflow** — Receives repo name and branch name, recursively lists directory contents, filters supported file types (`.py`, `.md`, `.lua`, `.txt`, `.yaml`, `.ipynb`, `.waypoints`), fetches raw file content, and embeds into Pinecone with full metadata (repo, branch, file path).

![GrandChild Workflow](FoxBrain_AI_Agent/foxtrot-dataflow-GrandChild.png)

#### Live GitHub Commit Sync
Automatically keeps the knowledge base up-to-date. When new commits are pushed to any repo, this workflow triggers via webhook, deletes outdated vectors from Pinecone, and embeds the fresh code - ensuring FoxBrain always has the latest codebase.

![Live GitHub Sync](FoxBrain_AI_Agent/Live_GitHub_Commit_Pinecone_Update.png)

#### Google Drive Ingestion
Ingests documentation files from Google Drive into the vector database.

![Google Drive](FoxBrain_AI_Agent/dataflow-google-drive.png)

---

## 💰 NayaPay Ledger - Automated Expense Tracker

Automatically logs NayaPay SMS transactions to Google Sheets using AI-powered data extraction.

**Flow:** Phone (MacroDroid) → Webhook → Gemini AI → Google Sheets

**Tech:** n8n, AWS EC2, Terraform, Google Gemini, Google Sheets

![NayaPay Ledger](NayapayLedgerSystem/workflow.png)

---

## 📧 Event Auto Confirmation

Automates event registration by classifying attendees (Internal/External based on email), sending personalized confirmation emails, and logging to Google Sheets.

**Tech:** n8n, Gmail API, Google Sheets

![Event Confirmation](EventAutoConfirmation/event-confirmation-workflow.png)

---

## 🤖 Discord-ClickUp-AI Bot

**AI-Powered Discord Bot for Team Foxtrot's Task Management**

A Discord bot that integrates with ClickUp to help team members easily access their tasks and get notified when new tasks are assigned. Works as both a general-purpose chatbot and a task management assistant.

**Tech:** n8n, ClickUp API, Discord Bot, Google Gemini, Google Sheets

### Workflows

#### AI Chatbot Agent
A conversational Discord bot powered by Gemini. Team members can DM the bot to ask general questions or request their ClickUp tasks. The bot looks up the user's ClickUp ID from Google Sheets and uses an AI agent to autonomously fetch and format their tasks when requested.

![Discord Bot Agent](Discord-Clickup-AI-Bot/Discordbot_agent.png)

#### Task Creation Notifier
Automatically notifies team members via Discord DM when a new task is created and assigned to them in ClickUp. Cross-references user email with Discord ID via Google Sheets, fetches full task details, and sends a formatted notification.

![Task Creation](Discord-Clickup-AI-Bot/TaskCreation.png)

---

## 📱 NonPTA HackAgent

![NonPTA HackAgent Workflow](NonPTA_HackAgent/NonPTA_HackAgent.png)

**Problem Statement:** Using a non-PTA iPhone in Pakistan creates a major communication gap. High taxation fees force users to keep a secondary Android phone for cellular services, but managing two devices is inefficient and often leads to missing urgent SMS or calls on the secondary phone while active on the primary one.

NonPTA HackAgent automates two-way communication between Discord and a phone gateway:

- Takes a Discord DM, uses a Gemini AI agent to extract recipient + message, and triggers an SMS via MacroDroid.
- Receives SMS/call callbacks from MacroDroid via webhook and forwards clean notifications back to Discord.
- Uses an integrated Google Contacts tool to resolve names into phone numbers when needed.

**Flow:** Discord DM → AI Agent (Gemini + Contacts Tool) → MacroDroid API, and Webhook Callback → Discord Notification

**Tech:** n8n, Google Gemini, Google Contacts, MacroDroid, Discord Bot, HTTP Webhooks

## 📄 License

See [LICENSE](LICENSE) file.
