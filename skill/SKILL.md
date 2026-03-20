---
name: mac-dev-setup
description: |
  **Mac Developer Environment Setup**: Automated diagnostic and installation of a complete macOS development environment with Docker, n8n, Claude Code, GitHub, and organized project structure.
  - MANDATORY TRIGGERS: mac setup, dev environment, install docker, install claude code, n8n setup, developer tools, configure mac, new mac setup, dev machine, development environment, homebrew setup, setup my mac, organize developer folders
  - Also triggers when the user mentions setting up a second Mac, replicating an environment, or syncing dev tools across machines
  - Use this skill whenever someone wants to go from a fresh or partially-configured Mac to a fully working dev environment
---

# Mac Developer Environment Setup

This skill automates the complete setup of a macOS development environment. It diagnoses what's already installed, installs what's missing, and configures everything to work together.

Think of it like a mechanic's checklist for a car inspection — you go through every system, note what's working, fix what's not, and at the end everything runs together.

## What this skill sets up

1. **Core tools**: Homebrew, Node.js, npm, Git, GitHub CLI, Python
2. **Docker Desktop**: Container runtime for services like n8n
3. **Claude Code CLI**: AI coding assistant in the terminal
4. **n8n self-hosted**: Workflow automation via Docker, connected to Claude Code via MCP
5. **Project structure**: Organized `~/Developer/` with `activos/`, `archivados/`, `sandbox/`, `config/`
6. **GitHub sync**: Repos created and pushed, ready to clone on other machines
7. **Multi-Mac support**: Generates a replication guide for additional Macs

## Step 1: Diagnostic

Before installing anything, run a full diagnostic. This avoids reinstalling things and catches version issues early.

```bash
echo "=== Sistema ===" && sw_vers
echo "=== Arquitectura ===" && uname -m
echo "=== Homebrew ===" && brew --version 2>/dev/null || echo "NO instalado"
echo "=== Node ===" && node --version 2>/dev/null || echo "NO instalado"
echo "=== npm ===" && npm --version 2>/dev/null || echo "NO instalado"
echo "=== Git ===" && git --version 2>/dev/null || echo "NO instalado"
echo "=== GitHub CLI ===" && gh --version 2>/dev/null || echo "NO instalado"
echo "=== GitHub auth ===" && gh auth status 2>&1
echo "=== Docker ===" && docker --version 2>/dev/null || echo "NO instalado"
echo "=== Claude Code ===" && claude --version 2>/dev/null || echo "NO instalado"
echo "=== Claude Desktop ===" && ls /Applications/Claude.app 2>/dev/null && echo "Instalado" || echo "NO instalado"
echo "=== Python ===" && python3 --version 2>/dev/null || echo "NO instalado"
echo "=== Developer dir ===" && ls ~/Developer 2>/dev/null || echo "NO existe"
```

Present results as a table showing what's installed vs what's missing.

## Step 2: Install missing tools

Install ONLY what the diagnostic shows as missing. Order matters (dependencies first):

### Homebrew (if missing)
```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

### Node.js + npm (if missing)
```bash
brew install node
```

### Git configuration
```bash
git config --global user.name "<user's name>"
git config --global user.email "<noreply GitHub email>"
git config --global init.defaultBranch main
```

### GitHub CLI (if missing)
```bash
brew install gh && gh auth login
```

### Docker Desktop (if missing)
```bash
brew install --cask docker
```
Docker CLI path: `/Applications/Docker.app/Contents/Resources/bin/docker` — add to PATH if needed:
```bash
echo 'export PATH="/Applications/Docker.app/Contents/Resources/bin:$PATH"' >> ~/.zshrc
```

### Claude Code CLI (if missing)
```bash
npm install -g @anthropic-ai/claude-code
```
If `claude` not found, add npm global bin to PATH:
```bash
echo 'export PATH="$HOME/.npm-global/bin:$PATH"' >> ~/.zshrc
```

## Step 3: Create project structure
```bash
mkdir -p ~/Developer/{activos,archivados,sandbox,config}
```

## Step 4: Set up n8n via Docker
```bash
docker run -d --name n8n -p 5678:5678 -v n8n_data:/home/node/.n8n docker.n8n.io/n8nio/n8n
```

Create account and API key via REST API (avoids UI copy-paste issues):
```bash
# Create owner account
curl -s -c /tmp/n8n_cookies.txt -X POST http://localhost:5678/rest/owner/setup \
  -H "Content-Type: application/json" \
  -d '{"email":"<email>","firstName":"<name>","lastName":"<last>","password":"<StrongPass1!>"}'

# Create API key (extract rawApiKey from response)
curl -s -b /tmp/n8n_cookies.txt -X POST http://localhost:5678/rest/api-keys \
  -H "Content-Type: application/json" \
  -d '{"label":"n8n-local","scopes":["workflow:read","workflow:create","workflow:update","workflow:delete","workflow:execute","workflow:list","execution:read","execution:list","credential:read","credential:list"],"expiresAt":null}'
```

## Step 5: Configure MCP (n8n → Claude Code)
```bash
claude mcp add n8n-mcp \
  -e MCP_MODE=stdio -e LOG_LEVEL=error -e DISABLE_CONSOLE_OUTPUT=true \
  -e N8N_API_URL=http://localhost:5678 \
  -e N8N_API_KEY=<rawApiKey_from_step4> \
  -- npx n8n-mcp
```

## Step 6: Create GitHub repo
```bash
cd ~/Developer/activos && mkdir mac-dev-setup && cd mac-dev-setup
git init && git add . && git commit -m "initial commit: mac-dev-setup"
gh repo create <user>/mac-dev-setup --public --push --source=.
```
If push rejected (email privacy): use `<user>@users.noreply.github.com`

## Step 7: Verification
```bash
echo "macOS: $(sw_vers -productVersion) ($(uname -m))"
echo "Node.js: $(node --version)" && echo "Docker: $(docker --version)"
echo "n8n: $(docker ps --filter name=n8n --format '{{.Status}}')"
echo "Claude Code: $(claude --version)"
echo "~/Developer/: $(ls ~/Developer/ | tr '\n' ' ')"
```

## Common issues
See `references/troubleshooting.md` for Docker PATH, Claude Code PATH, GitHub email privacy, and n8n API key issues.

## Customization
Ask the user: GitHub username, email, whether they want n8n, multiple Macs to sync, additional tools.
