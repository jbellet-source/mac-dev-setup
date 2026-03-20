# Mac Dev Setup — CLAUDE.md
# Este archivo instruye a Claude Code para configurar un Mac de desarrollo completo.
# Personaliza con tu usuario de GitHub y email

## Contexto
- 2 Macs: Mac mini + MacBook Pro, ambos Apple Silicon, mismo usuario macOS
- Objetivo: entorno de desarrollo sincronizado con Docker, n8n, Claude Code, GitHub
- Estructura de proyectos: ~/Developer/{activos,archivados,sandbox,config}

## Herramientas requeridas
1. **Homebrew** — gestor de paquetes macOS
2. **Node.js + npm** — runtime para Claude Code y herramientas JS
3. **Git** — control de versiones
4. **GitHub CLI (gh)** — operaciones GitHub desde terminal
5. **Docker Desktop** — contenedores (n8n y futuros servicios)
6. **Claude Code CLI** — `npm install -g @anthropic-ai/claude-code`
7. **Python 3** — para scripts y agentes (organizer_agent, etc.)

## Estructura de carpetas
```
~/Developer/
├── activos/          # Proyectos en desarrollo activo
│   ├── mac-dev-setup/    # Este repo — setup y configuración
│   └── [otros repos]/
├── archivados/       # Proyectos terminados o pausados
├── sandbox/          # Experimentos y pruebas
└── config/           # Configuraciones compartidas
```

## n8n (self-hosted via Docker)
```bash
# Levantar n8n
docker run -d --name n8n -p 5678:5678 -v n8n_data:/home/node/.n8n docker.n8n.io/n8nio/n8n

# Verificar que corre
docker ps | grep n8n

# Acceder: http://localhost:5678
```

## MCP de n8n para Claude Code
```bash
# Conectar n8n como MCP (ya configurado en Mac mini)
claude mcp add n8n-mcp \
  -e MCP_MODE=stdio \
  -e LOG_LEVEL=error \
  -e DISABLE_CONSOLE_OUTPUT=true \
  -e N8N_API_URL=http://localhost:5678 \
  -e N8N_API_KEY=<ver ~/.claude.json para la key actual> \
  -- npx n8n-mcp
```

## Git — configuración global
```bash
git config --global user.name "Julien Bellet"
git config --global user.email "<tu-email>"
git config --global init.defaultBranch main
```

## Dispatch (control remoto desde móvil)
- Instalar desde: https://dispatch.app
- Permite ejecutar comandos en el Mac desde el teléfono

## Checklist de setup
- [ ] Homebrew instalado
- [ ] Node.js + npm instalados
- [ ] Git configurado
- [ ] GitHub CLI autenticado (`gh auth login`)
- [ ] Docker Desktop instalado y corriendo
- [ ] Claude Code CLI instalado (`claude --version`)
- [ ] Estructura ~/Developer/ creada
- [ ] n8n corriendo en Docker (localhost:5678)
- [ ] MCP de n8n configurado en Claude Code
- [ ] Dispatch instalado
- [ ] Repos sincronizados con GitHub

## Para sesiones futuras
Al iniciar Claude Code en este directorio, decir:
> "Lee CLAUDE.md y dime qué está hecho y qué falta"

Claude Code detectará automáticamente el estado del entorno.
