---
name: engram-memory
description: Persistent, explainable memory for your OpenClaw agent — store facts and recall them later via the hosted Engram MCP server (by Lumetra).
user-invocable: true
metadata:
  openclaw:
    version: "0.1.0"
    homepage: "https://lumetra.io"
    repository: "https://github.com/lumetra-io/engram-openclaw-skill"
    license: "MIT"
    author: "Lumetra <hi@lumetra.io>"
    keywords: ["memory", "mcp", "engram", "lumetra", "context"]
    requires:
      env:
        - name: ENGRAM_API_KEY
          description: "Your Engram API key from https://lumetra.io (looks like eng_live_...)."
          secret: true
          required: true
    mcpServers:
      engram:
        url: "https://mcp.lumetra.io/mcp/sse"
        headers:
          Authorization: "Bearer ${ENGRAM_API_KEY}"
---

# Engram Memory

You have access to **Engram**, a hosted memory service for AI agents. Engram lets you remember facts, decisions, and context across conversations using a hybrid retrieval engine (BM25 + vector + knowledge graph) and returns an explanation trace with every recall.

## When to use

- **Before answering** anything that may rely on prior context: call `query_memory` first and ground your answer in the results.
- **When the user shares a fact** worth remembering (preferences, project details, decisions, deadlines): call `store_memory` to capture it.
- **At the end of a useful conversation**: capture stable takeaways with `store_memory`.

## Tools

| Tool | Description |
|---|---|
| `store_memory(content, bucket?)` | Save a fact. `bucket` defaults to `"default"`. |
| `query_memory(question, bucket?)` | Hybrid retrieval + synthesized answer with citations. |
| `list_memories(bucket, limit?)` | List memories in a bucket, newest first (`limit` 1–100, default 20). |
| `list_buckets()` | Show all buckets in the tenant. |
| `delete_memory(memory_id, bucket)` | Delete one memory by ID. |
| `clear_memories(bucket)` | Delete every memory in a bucket (destructive!). |

## Style

- Store **atomic, declarative facts**, one concept per memory. Good: `"User prefers dark mode."` Bad: `"The user mentioned they like dark mode, also they live in Seattle, also..."`
- Use **buckets** to separate contexts: `"work"`, `"personal"`, `"project-alpha"`. If no bucket fits, omit it and the default bucket is used.
- Quote citations from the explanation trace when the user asks "how do you know that?".

## BYOK note

Engram is bring-your-own-key end-to-end — inference (embeddings, synthesis, graph extraction) runs through the user's OpenAI / Anthropic / Groq / Together / Fireworks key configured at https://lumetra.io/models. Without a provider key, every `store_memory` and `query_memory` returns HTTP 412. If you see that error, tell the user to visit the models page.
