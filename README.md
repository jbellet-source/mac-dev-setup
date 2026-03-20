# mac-dev-setup

Setup automatizado para entorno de desarrollo en macOS con Docker, n8n, Claude Code y GitHub.

## Qué incluye
- **CLAUDE.md** — instrucciones para que Claude Code configure el Mac automáticamente
- Estructura de carpetas estandarizada (`~/Developer/`)
- n8n self-hosted via Docker con MCP para Claude Code
- Sincronización entre múltiples Macs via GitHub

## Uso
1. Clona este repo: `gh repo clone <tu-usuario>/mac-dev-setup ~/Developer/activos/mac-dev-setup`
2. Instala Claude Code: `npm install -g @anthropic-ai/claude-code`
3. Desde la carpeta del repo: `claude`
4. Dile: "ejecuta el setup completo del CLAUDE.md"

## Autor
[@jbellet-source](https://github.com/jbellet-source)
