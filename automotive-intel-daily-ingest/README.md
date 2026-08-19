# Automotive Intel Daily Ingest

## Problem

Tracking daily developments across four fast-moving automotive technology areas (SDV zonal architecture, SiC semiconductors, ADAS sensor fusion, EV batteries) by hand doesn't scale — too many sources, too much noise. This workflow runs the research automatically and turns it into a structured, version-controlled knowledge base plus a daily digest.

## Approach

1. **Trigger:** Scheduled, daily at 8:00 AM
2. **Parallel search:** Four separate SerpAPI queries run concurrently, one per technology domain
3. **Normalize:** Merges and deduplicates results from all four searches into one list
4. **AI Agent (Claude):** Filters, ranks, and summarizes the day's relevant articles per domain
5. **Format:** Converts the agent's output into structured Markdown with consistent frontmatter
6. **Write to GitHub:** Commits each article as a date-prefixed file (`YYYY-MM-DD-slug.md`) to a personal knowledge repo
7. **Digest:** Sends a summary email of the day's additions via Gmail

## Why it's non-trivial

- **GitHub as the storage layer**, not a database — every article becomes a versioned, diffable, git-trackable file, which plugs directly into an Obsidian vault for personal knowledge management.
- **Staged rollout built into the workflow itself.** GitHub and Gmail nodes ship disabled by default, with the setup guide walking through: dry-run first (inspect Markdown output only) → enable GitHub only → verify the file lands correctly → enable Gmail → activate. That's a deliberate "verify each side-effect independently before trusting the full chain" discipline, not just a working demo.
- **Explicit idempotency tradeoff documented.** Same-day re-runs will fail on already-created files by design (prevents duplicate/conflicting commits); the setup notes call out switching to an `edit` operation if idempotent re-runs are needed instead — a real engineering decision, stated rather than left implicit.
- **Domain-specific parallel search** rather than one broad query — keeps signal-to-noise high across four genuinely different technical areas instead of one generic feed.

## Stack

- n8n (Schedule Trigger, Merge, Split In Batches)
- SerpAPI (4 parallel domain-specific searches)
- Claude (Anthropic) via n8n's LangChain AI Agent node
- GitHub API (commit-as-storage)
- Gmail (digest delivery)

## Screenshot

<img width="761" height="264" alt="image" src="https://github.com/user-attachments/assets/147a6243-6234-4994-8af1-749fc38e5fcb" />

## Sanitization note

Credential IDs and webhook IDs have been replaced with placeholders in `workflow.json`. This export illustrates the logic, not a drop-in deployable file.
