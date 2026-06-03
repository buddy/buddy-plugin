# Buddy Plugin

Plugin and skill for seamless integration with Buddy.works — compatible with Claude Code and other AI agents that support skills. Deploy applications, publish artifacts, expose localhost services, manage domains, distributions, and CI/CD pipelines.

## Features

### Skill

- **buddy** — Consolidated skill covering sandbox deployment, artifacts, tunnels, domains, distributions, and pipelines

### Commands

- `/deploy [name] [path]` — Deploy application (auto-detects static vs dynamic)
- `/expose [port]` — Create Buddy tunnel for locally running application

## Installation

**Prerequisites**

1. **Buddy CLI (bdy)** 
   ```bash
   sudo npm install -g bdy
   ```
2. **Buddy Account** — Sign up at [buddy.works](https://buddy.works)

**Plugin for Claude Code** — from marketplace (recommended):

```bash
claude plugin marketplace add buddy/buddy-plugin
claude plugin install buddy@buddy-plugin
```

Or for development/testing:

```bash
git clone https://github.com/buddy/buddy-plugin.git ~/buddy-plugin
cd ~/my-app
claude --plugin-dir ~/buddy-plugin
```

**Skill for any agent** (Cursor, Codex, Gemini CLI and 51 more):

```bash
npx skills add buddy/buddy-plugin
```

This installs only the `buddy` skill (without Claude Code-specific commands), making Buddy deployment knowledge available to any compatible agent.

## Quick Start

### 1. Authenticate with Buddy

```bash
# Interactive login (recommended — run in separate terminal)
bdy login

# Or use token
bdy login --token YOUR_TOKEN --workspace YOUR_WS --region us

# Link your project to a Buddy project
cd any-of-yours-project
bdy proj link
```

### 2. Deploy an Application

```
/deploy
```

Claude will auto-detect your project type:
- **Static site** (HTML/CSS/JS) → published as a Buddy Artifact with versioned URL
- **Dynamic app** (Node.js, Python, Go, etc.) → deployed to a Buddy Sandbox with public endpoint

### 3. Expose Localhost

```
/expose
```

Claude will detect your running service and create a Buddy tunnel with a public URL.

## Usage Examples

### Deploy Node.js Application

```
/deploy my-api
```

Claude deploys with auto-detected dependencies, start command, port, and public HTTPS endpoint.

### Deploy Static Site

```
/deploy my-site ./dist
```

Claude publishes your build output as a versioned Buddy Artifact with a public URL.

### Expose Local API for Webhook Testing

```bash
npm run dev     # Start your API
```

```
/expose 3000
```

Use the provided public URL for webhook configuration (Stripe, GitHub, etc.).

### Deploy Multiple Apps from Monorepo

```
/deploy frontend ./apps/web
/deploy backend ./apps/api
```

Each gets its own deployment and public URL.


