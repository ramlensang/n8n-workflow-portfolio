# Brainstorm Bash — Telegram

## Problem

A single AI response to "what do you think of this idea?" tends toward agreeable, shallow feedback. This workflow forces a real deliberation: five differently-biased agents attack the same idea from different angles in parallel, then a synthesizer reconciles their disagreement into a sharper v2 — delivered straight to Telegram, with every session archived to a personal knowledge base.

## Approach

1. **Trigger:** Any message sent to a Telegram bot
2. **Optional grounding:** If the message starts with `/search [idea]`, a SerpAPI lookup runs first to ground the agents in live web context; a plain message skips straight to the agents
3. **Five parallel personas**, each a separate Claude agent with a distinct system prompt:
   - **Devil's Advocate** — hunts for every flaw
   - **Pragmatist** — stress-tests execution feasibility
   - **First Principles** — strips away assumptions
   - **Market Realist** — checks it against real-world market conditions
   - **Synthesizer** — reconciles all four perspectives into a refined v2 idea plus concrete next actions
4. **Aggregate:** Merges all four persona outputs before passing them to the Synthesizer
5. **Output, dual delivery:**
   - Structured Markdown note written to GitHub (`AIOS/Brainstorms/YYYY-MM-DD-slug.md`) — syncs into an Obsidian vault
   - A direct reply back to the Telegram chat with the refined idea and next steps

## Why it's non-trivial

- **Deliberate adversarial design.** The agents aren't redundant instances asked the same neutral question — each has a genuinely different, sometimes conflicting mandate (Devil's Advocate vs. Pragmatist will often disagree), which is what makes the Synthesizer's job meaningful rather than a formality.
- **Parallel fan-out / fan-in orchestration** — five agent branches run independently and get merged before synthesis, rather than a slow sequential chain.
- **Two delivery surfaces from one run** — instant, low-friction feedback in Telegram (where the idea originated) and a permanent, searchable record in the knowledge vault, without asking the user to do the filing themselves.
- **Conditional grounding.** Web search only fires when explicitly requested via `/search`, keeping the default path fast and cheap while still allowing live-context grounding when the idea needs it.

## Stack

- n8n (Telegram Trigger, IF, Merge)
- SerpAPI (optional grounding search)
- Claude (Anthropic) — 5 parallel agent instances with distinct personas + 1 synthesizer
- GitHub API (Markdown archive, credential reused from the Automotive Intel workflow)
- Telegram (trigger + reply)

## Screenshot

*(Add a canvas screenshot here)*

## Sanitization note

Credential IDs and webhook IDs have been replaced with placeholders in `workflow.json`. The original also contained a `REPLACE_ME` placeholder for the Telegram credential — left as-is since it's illustrative of the intended setup flow.
