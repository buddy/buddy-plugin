---
description: Expose localhost app via Buddy tunnel
argument-hint: [port]
allowed-tools: Bash(bdy:*,lsof:*), AskUserQuestion
model: sonnet
---

Expose locally running application via Buddy tunnel.

**Arguments:** `$1` — optional port (auto-detect if not provided)

## Workflow

1. **Detect port:** Use `lsof -iTCP -sTCP:LISTEN -P -n` to find listening services, or use provided `$1`
2. **Ask about auth:** Use AskUserQuestion — "HTTP Basic Auth" / "Buddy Auth" / "No auth (public)"
3. **Create tunnel:** Follow the tunnel workflow from the buddy skill — CRITICAL: use `run_in_background: true`
4. **Show results:** public URL

> **CLI tool:** `bdy` (not "buddy")
