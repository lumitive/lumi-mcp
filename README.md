# Lumi MCP

> Agent-ready commerce gateway. Standard Model Context Protocol — no
> per-vendor SDK. Five surface-equivalent transports (stdio, streamable-HTTP,
> WhatsApp, web companion, browser-use) all share one tool catalog and one
> audit ledger.

**Live site:** <https://lumitive.github.io/lumi-mcp/>

## What's here

This repo serves the agent-facing entry point for Lumi MCP. The server
itself lives in a separate (private) repository; this site is the public
manifest agents and human integrators land on.

| File | Purpose |
|---|---|
| [`index.html`](./index.html) | Human (and skim-friendly for agents) overview |
| [`llms.txt`](./llms.txt) | Agent crawl manifest — start here if you're an LLM |
| [`tools.json`](./tools.json) | Live tool catalog (18 primary tools, full JSON Schema) |
| [`mcp.json`](./mcp.json) | Paste-ready MCP client config snippet |

## Quick links for agents

```
endpoint (stdio):    python -m services.mcp_external
endpoint (http):     POST <host>/mcp/mcp
manifest:            tools.json
auth:                Authorization: Bearer <jwt from identity.issue>
```

## Tool inventory

Seven namespaces, 18 primary tools (5 snake_case aliases preserved for
back-compat).

```
merchant.*  search                                discovery
menu.*      get                                   catalog
order.*     calculate, place, list                two-stage commit
cart.*      create, add, update, remove, view, checkout
session.*   handoff, resume                       cross-surface continuity
payment.*   authorize, charge, refund, audit      agentic + Mastercard ACP
identity.*  issue                                 bearer issuance
```

## Updating the catalog

When tools change in the upstream server, regenerate `tools.json` locally:

```bash
# in the upstream private repo
uv --directory backend run python - <<'PY'
import sys; sys.path.insert(0,'.')
import asyncio, json
from services.mcp_external import build_server
mcp = build_server()
tools = asyncio.run(mcp.list_tools())
# … (full regen script in the upstream repo)
PY
```

Push the new `tools.json` to this repo. The deploy workflow at
[`.github/workflows/pages.yml`](./.github/workflows/pages.yml) republishes
the site on every push to `main`.
