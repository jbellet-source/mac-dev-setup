# Setup MacBook Pro — Instrucciones para Claude Code

## Contexto
Este setup replica el entorno del Mac mini en el MacBook Pro.
GitHub: <tu-usuario> | Email: <tu-email>

## Pre-requisitos (verificar primero)
```bash
# Ejecuta esto en Terminal para ver qué tienes:
sw_vers && uname -m && node --version && npm --version && git --version && brew --version && docker --version && claude --version && gh auth status
```

## Pasos (solo lo que falte)

### 1. Homebrew (si no está)
```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

### 2. Node.js + npm (si no está)
```bash
brew install node
```

### 3. Git configuración
```bash
git config --global user.name "Julien Bellet"
git config --global user.email "jbellet-source@users.noreply.github.com"
git config --global init.defaultBranch main
```

### 4. GitHub CLI
```bash
brew install gh
gh auth login
```

### 5. Docker Desktop
```bash
brew install --cask docker
# Abrir Docker Desktop y esperar a que arranque
open /Applications/Docker.app
```

### 6. Claude Code CLI
```bash
npm install -g @anthropic-ai/claude-code
# Añadir al PATH si es necesario:
echo 'export PATH="$HOME/.npm-global/bin:$PATH"' >> ~/.zshrc
echo 'export PATH="/Applications/Docker.app/Contents/Resources/bin:$PATH"' >> ~/.zshrc
source ~/.zshrc
claude  # autenticar con cuenta Anthropic
```

### 7. Estructura de carpetas + clonar repo
```bash
mkdir -p ~/Developer/{activos,archivados,sandbox,config}
cd ~/Developer/activos
gh repo clone <tu-usuario>/mac-dev-setup
```

### 8. n8n en Docker
```bash
docker run -d --name n8n -p 5678:5678 -v n8n_data:/home/node/.n8n docker.n8n.io/n8nio/n8n
# Crear cuenta en http://localhost:5678
# Luego crear API key en Settings → API
```

### 9. MCP de n8n
```bash
claude mcp add n8n-mcp \
  -e MCP_MODE=stdio \
  -e LOG_LEVEL=error \
  -e DISABLE_CONSOLE_OUTPUT=true \
  -e N8N_API_URL=http://localhost:5678 \
  -e N8N_API_KEY=<tu-nueva-api-key-del-macbook> \
  -- npx n8n-mcp
```

### 10. Verificación final
```bash
docker ps | grep n8n && claude --version && gh auth status && echo "✅ Setup completo"
```

## Credenciales n8n MacBook Pro
- URL: http://localhost:5678
- Email: <tu-email>
- Password: <tu-contraseña>
