# Buddy Plugin for Claude Code

Claude Code plugin for seamless integration with Buddy.works. Deploy applications, publish artifacts, expose localhost services, manage domains, distributions, and CI/CD pipelines.

## Features

### Skill

- **buddy** — Consolidated skill covering sandbox deployment, artifacts, tunnels, domains, distributions, and pipelines

### Commands

- `/deploy [name] [path]` — Deploy application (auto-detects static vs dynamic)
- `/expose [port]` — Create Buddy tunnel for locally running application

## Installation

### Prerequisites

1. **Claude Code CLI** — Install from [claude.ai/claude-code](https://claude.ai/claude-code)
2. **Buddy CLI (bdy)** — Installed automatically via plugin or manually:
   ```bash
   sudo npm install -g bdy
   ```
3. **Buddy Account** — Sign up at [buddy.works](https://buddy.works)

### Install Plugin

**From Plugin Marketplace (recommended)**

```bash
claude plugin marketplace add buddy/buddy-plugin
claude plugin install buddy@buddy-plugin
```

**Development/Testing**

```bash
git clone https://github.com/buddy/buddy-plugin.git ~/buddy-plugin
cd ~/my-app
claude --plugin-dir ~/buddy-plugin
```

## Quick Start

### 1. Authenticate with Buddy

```bash
# Interactive login (recommended — run in separate terminal)
bdy login

# Or use token
bdy login --token YOUR_TOKEN --workspace YOUR_WS --region us
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

## Configuration

### Buddy CLI

```bash
# Global
bdy login -w workspace -p project --region eu

# Environment variables
export BUDDY_TOKEN="your-token"
export BUDDY_WORKSPACE="your-workspace"
```

## Troubleshooting

### "Not authenticated with Buddy"

Run `bdy login` in a separate terminal. Credentials persist across sessions.

### Application not accessible via public URL

Ensure your app binds to `0.0.0.0`, not `127.0.0.1`:

```javascript
app.listen(PORT, '0.0.0.0');        // Node.js
```
```python
app.run(host='0.0.0.0', port=5000)  # Flask
```

### Tunnel connection refused

- Verify app is running: `lsof -i :[port]`
- Test locally first: `curl http://localhost:[port]`



