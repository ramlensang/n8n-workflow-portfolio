# n8n Automation Portfolio

A collection of automation workflows built with n8n, combining API integrations, LLM-powered agents (Claude, GPT-4o-mini), and multi-agent orchestration to solve real research, monitoring, and knowledge-management problems.

## Why this exists

These are shipped, working pipelines — not tutorials or copied templates — built with real constraints in mind: cost control, idempotency, failure handling, and staged rollout. Each one reflects a decision made on purpose, documented in its own README.

## Workflows

| Workflow | Problem It Solves | Stack | Pattern |
|---|---|---|---|
| [Automotive Intel Daily Ingest](./workflows/automotive-intel-daily-ingest) | Daily research across 4 automotive tech domains, written to a version-controlled knowledge base | n8n, SerpAPI, Claude, GitHub, Gmail | Scheduled multi-source research → structured storage |
| [Brainstorm Bash — Telegram](./workflows/brainstorm-bash-telegram) | Forces real deliberation on an idea via 5 adversarial AI personas instead of one agreeable response | n8n, Telegram, SerpAPI, Claude (5 agents + synthesizer) | Parallel multi-agent debate → synthesis |
| [German News Daily Digest — Migrants](./workflows/german-news-digest-migrants) | Filters German news down to what matters for non-EU migrants navigating visas, housing, integration | n8n, RSS, Claude, Gmail | Two-stage filtering (deterministic scoring + LLM curation) |
| [Competitor Price Check — Biweekly](./workflows/competitor-price-check) | Automated competitor pricing checks across multiple retailers with margin-risk analysis | n8n, SerpAPI, HTTP scraping, Claude, Google Sheets, Gmail | Dual-source data reconciliation |
| [Supabase RAG AI Agent](./workflows/supabase-rag-agent) | Chat agent that answers from a private, auto-synced document corpus | n8n, Google Drive, OpenAI, Supabase/pgvector | Event-driven RAG with tool-based retrieval |

## How each workflow is documented

Every workflow folder contains:
- `README.md` — problem, approach, stack, and what makes it a non-trivial engineering decision (not just "dragged some nodes together")
- `workflow.json` — sanitized n8n export (credentials, webhook IDs, and personal emails replaced with placeholders)

## Stack summary across this portfolio

- **Automation:** n8n
- **LLM integration:** Claude (Anthropic) for most agent/reasoning work, OpenAI for embeddings and one chat use case
- **Patterns used:** multi-agent parallel orchestration + synthesis, event-driven RAG, deterministic pre-filtering before LLM calls, dual-source data reconciliation, GitHub-as-storage
- **Data layer:** Supabase/pgvector, Google Sheets, GitHub (as a structured content store)
- **Delivery surfaces:** Gmail, Telegram

## Contact

Ram Len Sang · https://www.linkedin.com/in/ram-len-sang-90030585/ · ramlensang10792@gmail.com
