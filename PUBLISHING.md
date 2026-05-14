# Publishing this skill to ClawHub

Until the skill is approved on ClawHub, `openclaw skill add engram` does **not** work for end users. To make it actual:

## Prerequisites

- Push this repo to `github.com/lumetra-io/engram-openclaw-skill` (public).
- A ClawHub account at https://clawhub.openclaw.ai linked via GitHub OAuth (the GitHub identity must own or be a member of `lumetra-io`).
- The `clawhub` CLI installed (`npm i -g @openclaw/clawhub` or follow the [ClawHub README](https://github.com/openclaw/clawhub#install)).

## Steps

```bash
# From a clean checkout of engram-openclaw-skill
clawhub login                              # opens browser, GitHub OAuth
clawhub whoami                             # confirm the right identity is active
clawhub skill publish skills/engram        # submits for review (server-side validates frontmatter)
```

There is no local `clawhub skill validate` subcommand as of the current CLI — frontmatter is validated server-side when you publish. If the publish is rejected, the error message tells you what to fix; iterate and re-run `clawhub skill publish`.

ClawHub slugs are **globally unique** (not namespaced under the publisher), and `engram` was already taken by another publisher. The skill is therefore published as `engram-memory`:

```bash
openclaw skill add engram-memory
# or
clawhub install engram-memory
```

## After approval

Once moderators approve the submission:

1. **Update `engram-mcp/README.md` OpenClaw section** — drop the "pending publication" footnote and promote the one-liner.
2. **Update `slm-memory-test/PLAN.md` Phase 2.4** — mark the OpenClaw skill task complete.
3. **Verify the live install**: in a clean OpenClaw workspace, run `openclaw skill add engram` and confirm the `ENGRAM_API_KEY` prompt fires and the MCP tools appear.
4. **Announce**: post to `#clawhub` Discord (per ClawHub CONTRIBUTING.md), and tag @steipete in a short demo (per `slm-memory-test/PLAN.md` Phase 3.2 — direct engagement with @steipete is highest-ROI distribution).

## Known unknowns

- **Exact MCP frontmatter schema.** The ClawHub docs reference `metadata.openclaw.requires.env` for env-var prompts but don't publish the full MCP-server schema. The `SKILL.md` frontmatter in this repo follows the patterns observed in `steipete/mcporter` and the OpenClaw skills docs. If `clawhub skill validate` rejects the manifest, the most likely fix is renaming `metadata.openclaw.mcpServers` to whatever the current schema expects (`mcp_servers`, `servers`, or a top-level `mcp:` block). Check the validator output and adjust.
- **Author namespace.** The slug `lumetra/engram` requires the GitHub identity submitting the skill to be associated with the `lumetra-io` org (or for ClawHub to allow a different author handle). If ClawHub maps namespace to the submitting user, ship under a personal account first and request an org transfer.
