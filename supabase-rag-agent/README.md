# Supabase RAG AI Agent

## Problem

A chat agent that can only answer from what it already knows is limited to general knowledge — it can't answer questions about a specific, private corpus of documents (meeting notes, internal docs) unless that content is retrievable and kept in sync automatically as documents change.

## Approach

**Ingestion pipeline** (event-driven):
1. **Trigger:** Google Drive folder watcher fires on file created *or* file updated (polled every minute)
2. **Deduplicate old data:** Before re-indexing, deletes any existing vector rows tagged with that file's ID from the Supabase `documents` table — prevents duplicate/stale chunks from a previous version of the same file
3. **Download + extract:** Pulls the file from Drive (with Google Docs auto-converted to plain text) and extracts its text content
4. **Chunk + embed:** Splits text via a recursive character splitter, embeds each chunk with OpenAI embeddings, and inserts into Supabase's `pgvector`-backed `documents` table, tagged with the source file ID as metadata

**Chat pipeline** (on-demand):
1. **Trigger:** A public chat webhook receives a user message
2. **Agent:** A LangChain AI Agent (GPT-4o-mini) answers using conversational memory (Postgres-backed chat history) plus a **vector store retrieval tool** — it decides for itself when to query the document corpus versus answering from general knowledge
3. **Retrieval:** The tool queries the same Supabase vector store via a `match_documents` similarity search function

## Why it's non-trivial

- **File-ID-scoped deletion before re-insert** is what makes the ingestion pipeline safe to run repeatedly — updating a file doesn't leave orphaned old chunks polluting retrieval results, a common gap in naive RAG setups.
- **Tool-based retrieval, not hardcoded RAG.** The agent is given retrieval as a callable tool rather than always prepending retrieved context to every prompt — it can choose not to search when a question doesn't need the document corpus, keeping responses faster and cheaper for general queries.
- **Persistent chat memory** (Postgres-backed) alongside vector retrieval, so the agent maintains conversational context across turns rather than treating every message as a cold start.
- **Two independent triggers writing to the same vector store** (file-created and file-updated) means the corpus stays current without any manual re-indexing step.

## Stack

- n8n (Google Drive Trigger ×2, Extract From File, LangChain nodes)
- OpenAI (`gpt-4o-mini` for chat, `text-embedding-3-small` for embeddings)
- Supabase (Postgres + `pgvector` for the vector store, plus chat memory table)
- LangChain's recursive character text splitter

## Screenshot

*(Add a canvas screenshot here)*

## Sanitization note

Credential IDs have been replaced with placeholders in `workflow.json`. The Google Drive folder ID is a real folder reference from the original build — replace with your own if reusing this. This export illustrates the logic, not a drop-in deployable file.
