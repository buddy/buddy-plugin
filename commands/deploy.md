---
description: Deploy app to Buddy cloud
argument-hint: [name] [path]
allowed-tools: Bash(bdy:*), Read, Glob, AskUserQuestion
model: sonnet
---

Deploy application to Buddy cloud.

**Arguments:**
- `$1`: Optional path to deploy (default: current directory)

## Workflow

1. **Analyze project** to auto-detect deployment type:

   **Static site** (→ artifact):
   - Directory contains only HTML/CSS/JS/image files
   - Has build output directory (`dist/`, `build/`, `out/`, `public/`) with `index.html`
   - Has `package.json` with build script but no server start script
   - Frameworks: Vite, CRA, Next.js with `output: "export"`, Astro static, plain HTML

   **Dynamic app** (→ sandbox):
   - Has server entry point (`server.js`, `app.js`, `app.py`, `main.py`, `main.go`, `Dockerfile`)
   - Has `package.json` with `start` script running a server (express, fastify, nest, etc.)
   - Needs database, background workers, or persistent processes

   **If ambiguous:** fall back to AskUserQuestion with options "Static site" / "Dynamic application"

2. **Deploy** using the buddy skill — follow the artifact or sandbox workflow

3. **Show results:** deployment URL and how to update/manage it

> **CLI tool:** `bdy` (not "buddy")
