# engram-openclaw-skill

OpenClaw skill for [Engram](https://lumetra.io) — durable, explainable memory for your local-first AI agent.

Your agent gets `store_memory`, `query_memory`, `list_memories`, `list_buckets`, `delete_memory`, and `clear_memories` against the hosted Engram MCP server, surfaced through `mcporter`.

## Setup

You need three things in place: an Engram API key, a BYOK provider key, and the `engram` MCP server registered with `mcporter`. Then install the skill.

### 1. Get an Engram API key

Sign up at <https://lumetra.io> — free tier, no card. You'll see an `eng_live_…` token in your dashboard.

```bash
export ENGRAM_API_KEY="eng_live_..."
```

### 2. Configure a BYOK provider key

Engram is bring-your-own-key end-to-end for the LLM that handles extraction and synthesis. Configure one provider at <https://lumetra.io/models>. DeepSeek is what we recommend — cheap and fast and handles memory workloads well. Without a provider key, every `store_memory` / `query_memory` returns HTTP 412.

### 3. Register the Engram MCP server with mcporter

```bash
# Install mcporter if you don't have it (OpenClaw will offer to do this for you on first run too)
npm i -g mcporter

# Register the hosted Engram MCP server (the name matters — see note below)
mcporter config add engram-lumetra https://mcp.lumetra.io/mcp/sse \
  --transport sse \
  --header "Authorization=Bearer $ENGRAM_API_KEY"

# Smoke-test
mcporter list                                # should show 'engram-lumetra' with 6 tools
mcporter call engram-lumetra.list_buckets    # should return JSON
```

The skill's prompt expects the server to be named `engram-lumetra`. We avoid the shorter `engram` because `mcporter` auto-imports MCP servers from `~/.cursor/mcp.json`, `~/.codeium/windsurf/mcp_config.json`, and similar editor configs — and many of those already have an entry called `engram` pointing at older or offline endpoints, which would shadow this one. If you really want `engram` as the name, remove any conflicting auto-imported entries first.

### 4. Install the skill

```bash
openclaw skills install lumetra-engram
```

That's it. Run `openclaw skills check` to confirm the skill is eligible (`✓ ready`).

## Tools exposed (namespaced through mcporter)

| Tool | What it does |
|---|---|
| `engram-lumetra.store_memory(content, bucket?)` | Save a fact to a bucket (defaults to `"default"`) |
| `engram-lumetra.query_memory(question, bucket?)` | Hybrid retrieval + synthesized answer with citations |
| `engram-lumetra.list_memories(bucket, limit?)` | List memories in a bucket, newest first (limit 1–100, default 20) |
| `engram-lumetra.list_buckets()` | All buckets in your tenant |
| `engram-lumetra.delete_memory(memory_id, bucket)` | Remove a single memory |
| `engram-lumetra.clear_memories(bucket)` | Empty a bucket (destructive!) |

Same surface as the [Claude Code plugin](https://github.com/lumetra-io/engram-claude-plugin), the [Codex plugin](https://github.com/lumetra-io/engram-codex-plugin), the [Paperclip plugin](https://github.com/lumetra-io/engram-paperclip-plugin), and the official [TypeScript](https://github.com/lumetra-io/engram-js), [Python](https://github.com/lumetra-io/engram-py), and [Go](https://github.com/lumetra-io/engram-go) SDKs.

## Repository layout

```
skills/lumetra-engram/
└── SKILL.md   # Frontmatter declares ENGRAM_API_KEY + mcporter dependency; body is the agent prompt
```

The skill is **MCP-backed via mcporter** — it doesn't ship any binaries. The MCP server registration is a one-time `mcporter config add` step; the skill then teaches the agent when and how to call the resulting tools.

## Publishing to ClawHub

This repo is the source of truth for the skill. To publish (or republish a new version):

```bash
clawhub login                                                           # GitHub OAuth, must own lumetra-io
clawhub skill publish skills/lumetra-engram --version 0.1.1 --slug lumetra-engram
```

See [ClawHub CONTRIBUTING](https://github.com/openclaw/clawhub/blob/main/CONTRIBUTING.md) for the full publishing flow.

## License

MIT — see [LICENSE](./LICENSE).
