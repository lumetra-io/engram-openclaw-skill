# engram-openclaw-skill

OpenClaw skill for [Engram](https://lumetra.io) — durable, explainable memory for your local-first AI agent.

Wires OpenClaw into the hosted Engram MCP server. Your agent gets `store_memory`, `query_memory`, `list_memories`, `list_buckets`, `delete_memory`, and `clear_memories` with no HTTP plumbing on your side.

## Install

### From ClawHub (recommended once published)

```bash
openclaw skill add engram-memory
# or, equivalent:
clawhub install engram-memory
```

OpenClaw will prompt for `ENGRAM_API_KEY` once and store it in its secret store.

### Manual install (today)

While the skill is pending publication on [ClawHub](https://github.com/openclaw/clawhub):

```bash
# From your project root, into your OpenClaw skills directory
mkdir -p .openclaw/skills
curl -fsSL https://codeload.github.com/lumetra-io/engram-openclaw-skill/tar.gz/refs/heads/main \
  | tar -xz --strip-components=2 -C .openclaw/skills engram-openclaw-skill-main/skills/engram

# Set your API key (OpenClaw will read it on first invocation)
export ENGRAM_API_KEY="eng_live_..."
```

Or `git clone` this repo and copy `skills/engram/` into your OpenClaw `skills/` directory manually.

## What you need before installing

- An [Engram account](https://lumetra.io) and an API key (`eng_live_...`).
- A BYOK provider key configured on the [models page](https://lumetra.io/models). Engram is bring-your-own-key end-to-end — without one, every `store_memory` / `query_memory` returns HTTP 412.

## Tools exposed

| Tool | What it does |
|---|---|
| `store_memory(content, bucket?)` | Save a fact to a bucket (defaults to `"default"`) |
| `query_memory(question, bucket?)` | Hybrid retrieval + synthesized answer with citations |
| `list_memories(bucket, limit?)` | List memories in a bucket, newest first (limit 1–100, default 20) |
| `list_buckets()` | All buckets in your tenant |
| `delete_memory(memory_id, bucket)` | Remove a single memory |
| `clear_memories(bucket)` | Empty a bucket (destructive!) |

Same surface as the [Claude Code plugin](https://github.com/lumetra-io/engram-claude-plugin), the [Codex plugin](https://github.com/lumetra-io/engram-codex-plugin), and the official [TypeScript](https://github.com/lumetra-io/engram-js), [Python](https://github.com/lumetra-io/engram-py), and [Go](https://github.com/lumetra-io/engram-go) SDKs.

## Repository layout

```
skills/engram/
└── SKILL.md   # Frontmatter declares the MCP server + ENGRAM_API_KEY env var; body is the agent prompt
```

The skill is **MCP-backed** — it doesn't ship any binaries. It wires OpenClaw to the hosted Engram MCP server at `https://mcp.lumetra.io/mcp/sse` and forwards `ENGRAM_API_KEY` as a bearer token on every request.

## Publishing to ClawHub

This repo is the source of truth for the skill. To publish it on [ClawHub](https://github.com/openclaw/clawhub):

1. Push this repo to `github.com/lumetra-io/engram-openclaw-skill`.
2. From a checkout, run `clawhub skill publish skills/engram` (requires a ClawHub account linked via GitHub OAuth).
3. Wait for moderator approval. After approval, `openclaw skill add engram` (or `clawhub install lumetra/engram`) is live for everyone.

See [ClawHub CONTRIBUTING](https://github.com/openclaw/clawhub/blob/main/CONTRIBUTING.md) for the full publishing flow.

## License

MIT — see [LICENSE](./LICENSE).
