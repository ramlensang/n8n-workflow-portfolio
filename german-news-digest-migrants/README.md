# German News Daily Digest — Migrants

## Problem

Non-EU migrants integrating into Germany need to track visa/residence law changes, integration programs, housing, and labor market news — but that signal is buried inside general German news feeds full of unrelated sports, entertainment, and tabloid content. This workflow filters three major outlets down to a daily top-5, translated and explained for exactly that audience.

## Approach

1. **Trigger:** Scheduled, daily at 7:00 AM
2. **Fetch:** Pulls RSS feeds from three sources in parallel — tagesschau, DW English, Zeit Online
3. **Merge and score:** A weighted keyword-tiering system scores every article (title matches count double weight) across three relevance tiers — e.g. visa/asylum/deportation terms score highest, housing/labor-market/law terms score mid, general politics/weather scores lowest — then drops anything older than 48 hours and keeps the top 40 by score
4. **AI Agent (Claude):** Takes the pre-scored shortlist and selects the final top 5, translating German headlines/summaries into English and writing a "why it matters" line specific to migrants navigating German bureaucracy
5. **Format:** Renders the agent's plain-text output into a styled HTML email
6. **Deliver:** Sends via Gmail to a distribution list

## Why it's non-trivial

- **Two-stage filtering, not just "ask the LLM to pick."** A deterministic, tunable keyword-tiering pass narrows ~100+ raw RSS items down to a scored shortlist of 40 before the LLM ever sees them — cheaper, faster, and the scoring logic (which terms matter, how much) is transparent and editable without touching the AI prompt at all.
- **Staleness handling.** RSS feeds carry backlog; the workflow explicitly filters out anything older than 48 hours so the digest never surfaces stale news as if it were current.
- **Audience-specific relevance, encoded as an explicit prompt contract** — the agent's system prompt hard-codes what to skip (celebrity, sports, entertainment) and what to prioritize (visa rules, integration programs, discrimination cases), rather than leaving "relevance" to the model's general judgment.
- **Deterministic output parsing.** The agent is constrained to a strict plain-text format (`N. Title / Source & Date / Link / Summary / Why it matters`) that a downstream Code node parses reliably into HTML — no JSON mode needed, no fragile prose-scraping.

## Stack

- n8n (Schedule Trigger, RSS Feed Read ×3, Merge, Code)
- Claude Sonnet (Anthropic) via n8n's LangChain AI Agent node
- Gmail (HTML digest delivery)

## Screenshot

*(Add a canvas screenshot here)*

## Sanitization note

All recipient email addresses (originally a live distribution list) and credential IDs have been replaced with placeholders in `workflow.json`. This export illustrates the logic, not a drop-in deployable file.
