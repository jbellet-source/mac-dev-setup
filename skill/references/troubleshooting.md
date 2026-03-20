# Troubleshooting Guide

## Docker CLI not found after install
Docker Desktop installs the CLI inside the app bundle, not in the system PATH.
```bash
export PATH="/Applications/Docker.app/Contents/Resources/bin:$PATH"
```

## Claude Code not found after npm install
npm global packages install to `~/.npm-global/bin/` which may not be in PATH.
```bash
export PATH="$HOME/.npm-global/bin:$PATH"
```

## GitHub push rejected — email privacy
```bash
git config user.email "<username>@users.noreply.github.com"
git commit --amend --reset-author --no-edit && git push
```

## n8n API key not visible in UI
Create via REST API instead (see SKILL.md Step 4).

## n8n password requirements
Requires: 1 uppercase, 1 number, 1 special character.

## Docker Desktop not starting
Check disk space (`df -h /`), kill existing instances, or reset Docker.

## brew says app already exists
Just open the app: `open /Applications/Docker.app`
