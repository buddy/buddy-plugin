# Buddy Plugin

This plugin lets coding agents deploy applications, publish artifacts, expose localhost services, and manage domains, distributions, and CI/CD pipelines and workflows on the Buddy Works platform.

## Features

### Skill

- **buddy** — Consolidated skill covering sandboxes, artifacts, tunnels, domains, distributions, and pipelines.

### Commands

- `/deploy [name] [path]` — Deploy static website or server-side application
- `/expose [port]` — Create Buddy tunnel for locally running application
- `/status [name]` — Overview of running sandboxes, static sites (artifacts), tunnels, and pipeline runs
- `/logs [name] [app-id]` — Show status and logs of a deployed app or pipeline run, diagnose failures
- `/domain [target] [domain]` — Attach a custom or free Buddy domain to a deployment
- `/ci [pipeline]` — Run a CI/CD pipeline and watch the result, or set up a new one for the project

## Installation

**Install Buddy CLI (bdy)**

```bash
sudo npm install -g bdy
```

**Install plugin for Claude Code**

```bash
claude plugin marketplace add buddy/buddy-plugin
claude plugin install buddy@buddy-plugin
```

**Install skill for other coding agents**

```bash
npx skills add buddy/buddy-plugin
```

This installs only the `buddy` skill (without Claude Code-specific commands), making Buddy Works platform knowledge available to any compatible agent.

## Quick Start

### 1. Authenticate with Buddy Works

Run authentication yourself in a **separate terminal** — never have the agent enter tokens or passwords, and avoid pasting secrets into shared sessions or chat.

```bash
# Interactive login (recommended — run in separate terminal)
bdy login

# Or use a token (keep it out of shell history; use an env var / CI secret)
bdy login --token "$BUDDY_TOKEN" --workspace YOUR_WS --region us

# Link directory with a Buddy Works project
cd your-project
bdy proj link
```

### 2. Deploy an Application

```
/deploy
```

Claude will auto-detect your project type:
- **Static site** (HTML/CSS/JS) → published as a versioned Buddy Works artifact with a public URL.
- **Server-side application** (Node.js, Python, Go, etc.) → deployed to a Buddy Works sandbox with a public endpoint.

### 3. Expose Localhost Port

```
/expose
```

Claude will detect your running service and create a Buddy Works tunnel with a public URL.

## Usage Examples

### Deploy Node.js Application

```
/deploy my-api
```

Deploys an app with auto-detected dependencies, start command, port, and public HTTPS endpoint.

### Deploy Static Site

```
/deploy my-site ./dist
```

Publishes your build output as a versioned Buddy Works artifact with a public URL.

### Expose Local API for Webhook Testing

```bash
npm run dev     # Start your API
```

```
/expose 3000
```

Returns a public URL to your locally hosted service.

### Deploy Multiple Apps from Monorepo

```
/deploy frontend ./apps/web
/deploy backend ./apps/api
```

Each gets its own deployment and public URL.
