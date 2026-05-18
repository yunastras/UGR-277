
# Capstone Project Context

## Project
- **Name:** AI-Powered Emergency Response Coordinator
- **Team:** Astra (Component 2 — AI Core), Hala (Component 1 — Protocol 
  Knowledge Base), Farida (Component 3 — Task Assignment), 
  MD (Component 4 — Integration & Dashboard)
- **What it does:** Receives free-text emergency incident reports, 
  classifies them by type (Fire, Medical, Lockdown), matches them to 
  the correct response protocol, and outputs structured data for task 
  assignment and dashboard visualization. A report like "smoke coming 
  from the cafeteria" becomes a structured, actionable classification 
  in seconds.
- **Project type:** Emergency Response Protocol System

## Architecture
- **Ingestion (Component 1 — Hala):** Protocol Knowledge Base — loads 
  emergency protocols into Flowise vector store; writes to Airtable 
  `Incident_Reports` table with `status = "new"`
- **AI Core (Component 2 — Astra):** n8n detects new records in 
  `Incident_Reports`, sends incident text to Flowise RAG chatflow via 
  HTTP request, parses the classification response, writes structured 
  output to `Incidents` table with `status = "classified"`
- **Specialist (Component 3 — Farida):** Task Assignment — reads records 
  from `Incidents` where `status = "classified"`, assigns tasks to 
  responders
- **Integration (Component 4 — MD):** Dashboard — displays classified 
  incidents and task status from shared Airtable base

## Tech Stack
- n8n Cloud (workflow automation)
- Flowise Cloud (RAG chatflow — Conversational Retrieval QA Chain)
- Groq API (LLM inference — llama-3.3-70b-versatile, temperature 0.1)
- HuggingFace Inference API (embeddings — 
  sentence-transformers/distilbert-base-nli-mean-tokens)
- Airtable (shared database — Incident_Reports + Incidents tables)
- GitHub (repo, documentation)

## Airtable Schema

### Incident_Reports
| Field | Type | Written By | Status Values |
|-------|------|-----------|---------------|
| incident_text | Long Text | Component 1 / manual | — |
| status | Single Select | Component 1 | new |

### Incidents
| Field | Type | Written By | Status Values |
|-------|------|-----------|---------------|
| incident_text | Long Text | Component 2 (n8n) | — |
| incident_type | Single Select | Component 2 (n8n) | Fire / Medical / Lockdown |
| protocol_id | Single Line | Component 2 (n8n) | e.g. FIRE_001 |
| severity | Single Select | Component 2 (n8n) | High / Medium / Low |
| matched_keywords | Long Text | Component 2 (n8n) | — |
| recommended_steps | Long Text | Component 2 (n8n) | — |
| confidence | Single Select | Component 2 (n8n) | High / Medium / Low |
| status | Single Select | Component 2 (n8n) | classified / error |
| source | Single Line | Component 2 (n8n) | n8n |

## Conventions
- Field names: snake_case
- Status values: lowercase
- Boolean fields use is_ prefix

## Current State
- **What's working:** Flowise chatflow built and tested; all 3 emergency 
  types classifying correctly; n8n workflow connecting Airtable → 
  Flowise → Airtable; 20+ incidents processed; integrated with shared 
  Airtable base
- **What's in progress:** N/A — capstone completed
- **Known issues:** None
- **Next milestone:** Capstone complete

## Repository Structure
- component-1-protocol-knowledge-base/
- component-2-incident-classifier/
- component-3-task-assignment-and-tracking/
- component-4-integration-testing-and-presentation/
- data/
- docs/
- .github/copilot-instructions.md
