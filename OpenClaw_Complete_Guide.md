# OpenClaw Complete Guide

> AI Agent Framework - Installation to Production

---

## Table of Contents

1. [Prerequisites](#prerequisites)
2. [Installation](#installation)
3. [Configuration](#configuration)
4. [Service Management](#service-management)
5. [Web UI / Dashboard](#web-ui--dashboard)
6. [Model Providers](#model-providers)
7. [Configuration Files](#configuration-files)
8. [Troubleshooting](#troubleshooting)
9. [Logs](#logs)
10. [Uninstallation](#uninstallation)
11. [Common Issues & Solutions](#common-issues--solutions)

---

## Prerequisites

| Requirement | Details |
|-------------|---------|
| OS | Linux (Ubuntu/Debian) or macOS |
| Node.js | v22+ (auto-installed if missing) |
| RAM | 4GB+ minimum |
| LLM Provider | API key (Anthropic/Google/OpenAI) OR local LLM (LM Studio) |

---

## Installation

### One-Line Install

```bash
curl -fsSL https://openclaw.ai/install.sh | bash
```

### What Installer Does

1. Detects OS (Linux/macOS)
2. Installs Node.js if missing
3. Installs build tools (make, g++, cmake, python3)
4. Installs OpenClaw via npm
5. Runs setup wizard

### Installation Wizard Options

| Prompt | Recommended Selection |
|--------|----------------------|
| Security warning | **Yes** |
| Onboarding mode | **QuickStart** |
| Model/auth provider | **vLLM** (local) or **Google** (cloud) |
| Gateway port | **18789** (default) |
| Gateway bind | **Loopback (127.0.0.1)** |
| Gateway auth | **Token** |
| Tailscale exposure | **Off** |
| Gateway token | **(blank - auto-generate)** |
| Configure channels | **No** (skip for now) |
| Configure skills | **No** (skip for now) |
| Enable hooks | **Skip for now** |
| Install Gateway service | **Yes** |
| Gateway runtime | **Node** |
| Hatch bot | **Do this later** |

### Post-Installation: Fix PATH

```bash
# Add npm global bin to PATH
echo 'export PATH="$HOME/.npm-global/bin:$PATH"' >> ~/.bashrc
source ~/.bashrc

# Verify installation
openclaw --version
```

---

## Configuration

### Configure Wizard (Full)

```bash
openclaw configure
```

### Configure Specific Sections

```bash
# Model configuration only
openclaw configure --section model

# Web search configuration
openclaw configure --section web

# Channel configuration (Slack, Discord, etc.)
openclaw configure --section channels
```

### Get/Set Config Values

```bash
# Get a config value
openclaw config get gateway.auth.token
openclaw config get agents.defaults.model.primary

# Set a config value
openclaw config set agents.defaults.model.primary "qwen2.5-coder-3b-instruct"

# Remove a config value
openclaw config unset some.config.key
```

### View All Config

```bash
cat ~/.openclaw/openclaw.json
```

---

## Service Management

### Systemd Commands

```bash
# Check status
systemctl --user status openclaw-gateway

# Start gateway
systemctl --user start openclaw-gateway

# Stop gateway
systemctl --user stop openclaw-gateway

# Restart gateway
systemctl --user restart openclaw-gateway

# Enable on boot
systemctl --user enable openclaw-gateway

# Disable on boot
systemctl --user disable openclaw-gateway

# Reload after config changes
systemctl --user daemon-reload
```

### Manual Gateway Control

```bash
# Start gateway manually
openclaw gateway

# Stop gateway
openclaw gateway stop
```

---

## Web UI / Dashboard

### Open Dashboard

```bash
# Opens browser automatically
openclaw dashboard

# Get URL without opening browser
openclaw dashboard --no-open
```

### Access URLs

```
Web UI: http://127.0.0.1:18789/
Web UI (with token): http://127.0.0.1:18789/#token=YOUR_TOKEN_HERE
Gateway WebSocket: ws://127.0.0.1:18789
```

### Get Gateway Token

```bash
openclaw config get gateway.auth.token
```

### Generate New Token

```bash
openclaw doctor --generate-gateway-token
```

---

## Model Providers

### Cloud Providers

| Provider | Setup Command | Notes |
|----------|---------------|-------|
| Anthropic | `openclaw configure --section model` → Anthropic | Requires API credits |
| Google Gemini | `openclaw configure --section model` → Google | Free tier available |
| OpenAI | `openclaw configure --section model` → OpenAI | Requires API credits |

### Local LLM (LM Studio)

```bash
# During configure, select:
# Provider: vLLM
# Base URL: http://<WINDOWS_IP>:1234/v1
# API Key: lm-studio
# Model: qwen2.5-coder-3b-instruct
```

### LM Studio Requirements

| Setting | Value |
|---------|-------|
| Context Length | 16384 (minimum) |
| Serve on Local Network | Enabled |
| Model | qwen2.5-coder-3b-instruct (recommended) |

### Test LM Studio Connection

```bash
curl http://<WINDOWS_IP>:1234/v1/models
```

---

## Configuration Files

### Main Config File

**Location:** `~/.openclaw/openclaw.json`

```json
{
  "auth": {
    "profiles": {
      "google:default": {
        "provider": "google",
        "mode": "api_key"
      }
    }
  },
  "agents": {
    "defaults": {
      "model": {
        "primary": "google/gemini-2.5-flash"
      },
      "workspace": "/home/user/.openclaw/workspace"
    }
  },
  "gateway": {
    "port": 18789,
    "mode": "local",
    "bind": "loopback",
    "auth": {
      "mode": "token",
      "token": "your-token-here"
    }
  }
}
```

### vLLM (Local LLM) Config Example

```json
{
  "auth": {
    "profiles": {
      "vllm:default": {
        "provider": "vllm",
        "mode": "api_key"
      }
    }
  },
  "agents": {
    "defaults": {
      "model": {
        "primary": "vllm/qwen2.5-coder-3b-instruct"
      }
    }
  }
}
```

### Systemd Service Override (Environment Variables)

**Location:** `~/.config/systemd/user/openclaw-gateway.service.d/override.conf`

```ini
[Service]
Environment="OPENAI_BASE_URL=http://192.168.1.100:1234/v1"
Environment="OPENAI_API_BASE=http://192.168.1.100:1234/v1"
Environment="OPENAI_API_KEY=lm-studio"
```

After creating/editing:
```bash
systemctl --user daemon-reload
systemctl --user restart openclaw-gateway
```

### Workspace Directory

**Location:** `~/.openclaw/workspace/`

Contains:
- Skills
- Custom configurations
- Agent files

### Sessions Directory

**Location:** `~/.openclaw/agents/main/sessions/`

Contains:
- Session history
- Conversation logs

---

## Troubleshooting

### Doctor Command

```bash
# Run diagnostics
openclaw doctor

# Fix common issues
openclaw doctor --fix

# Generate new gateway token
openclaw doctor --generate-gateway-token
```

### Security Audit

```bash
# Deep security scan
openclaw security audit --deep

# Auto-fix security issues
openclaw security audit --fix
```

### Common Checks

```bash
# Check if gateway is running
systemctl --user status openclaw-gateway

# Check OpenClaw version
openclaw --version

# Test LLM connection (for local LLM)
curl http://<LLM_IP>:1234/v1/models

# Check config file syntax
cat ~/.openclaw/openclaw.json | python3 -m json.tool
```

---

## Logs

### View Gateway Logs

```bash
# Recent logs (last 50 lines)
journalctl --user -u openclaw-gateway -n 50

# Follow logs in real-time
journalctl --user -u openclaw-gateway -f

# Logs since today
journalctl --user -u openclaw-gateway --since today

# Logs with timestamps
journalctl --user -u openclaw-gateway -n 100 --no-pager
```

### Log File Location

```bash
# OpenClaw writes logs to:
cat /tmp/openclaw/openclaw-$(date +%Y-%m-%d).log
```

### Debug Mode

Check if debug is enabled:
```bash
cat ~/.openclaw/openclaw.json | grep -i debug
```

---

## Uninstallation

### Complete Removal

```bash
# Step 1: Stop services
openclaw gateway stop
systemctl --user stop openclaw-gateway
systemctl --user disable openclaw-gateway

# Step 2: Remove npm package
npm uninstall -g openclaw

# Step 3: Remove config and data
rm -rf ~/.openclaw
rm -rf ~/.config/systemd/user/openclaw-gateway.service
rm -rf ~/.config/systemd/user/openclaw-gateway.service.d

# Step 4: Reload systemd
systemctl --user daemon-reload

# Step 5: Verify removal
openclaw --version  # Should show: command not found
```

### Keep Config, Remove Package Only

```bash
npm uninstall -g openclaw
```

---

## Common Issues & Solutions

### Issue 1: "Command not found"

**Problem:** `openclaw: command not found`

**Solution:**
```bash
echo 'export PATH="$HOME/.npm-global/bin:$PATH"' >> ~/.bashrc
source ~/.bashrc
```

### Issue 2: Gateway Offline / Disconnected

**Problem:** Web UI shows "Health Offline" or "Disconnected"

**Solution:**
```bash
systemctl --user restart openclaw-gateway
# Use token URL
openclaw dashboard
```

### Issue 3: "Cannot truncate prompt" Error

**Problem:** `Cannot truncate prompt with n_keep (14663) >= n_ctx (4096)`

**Solution:** Increase LM Studio context length to 16384+

### Issue 4: Client Timeout Loop (Local LLM)

**Problem:** Processing keeps restarting, never completes

**Cause:** OpenClaw times out while waiting for slow local LLM

**Solutions:**
1. Use smaller model (3B instead of 7B)
2. Increase GPU offload in LM Studio
3. Use cloud API instead (Gemini/Anthropic)

### Issue 5: "Incorrect API Key" (OpenAI)

**Problem:** `401 Incorrect API key provided`

**Cause:** Using OpenAI provider but pointing to local LLM

**Solution:** Use vLLM provider for local LLMs, not OpenAI

### Issue 6: Rate Limit (429 Too Many Requests)

**Problem:** Gemini API rate limited

**Solutions:**
1. Wait 1-2 minutes
2. Use gemini-2.5-flash-lite
3. Add billing to Google AI Studio

### Issue 7: Config Validation Error

**Problem:** `Unrecognized keys` in config

**Solution:**
```bash
# Restore backup
cp ~/.openclaw/openclaw.json.bak ~/.openclaw/openclaw.json

# Or run doctor
openclaw doctor --fix
```

---

## Quick Reference Card

### Essential Commands

| Task | Command |
|------|---------|
| Install | `curl -fsSL https://openclaw.ai/install.sh \| bash` |
| Configure | `openclaw configure` |
| Start | `systemctl --user start openclaw-gateway` |
| Stop | `systemctl --user stop openclaw-gateway` |
| Restart | `systemctl --user restart openclaw-gateway` |
| Status | `systemctl --user status openclaw-gateway` |
| Dashboard | `openclaw dashboard` |
| Logs | `journalctl --user -u openclaw-gateway -f` |
| Version | `openclaw --version` |
| Help | `openclaw --help` |
| Doctor | `openclaw doctor` |
| Uninstall | `npm uninstall -g openclaw && rm -rf ~/.openclaw` |

### Key File Locations

| File | Location |
|------|----------|
| Main config | `~/.openclaw/openclaw.json` |
| Config backup | `~/.openclaw/openclaw.json.bak` |
| Workspace | `~/.openclaw/workspace/` |
| Sessions | `~/.openclaw/agents/main/sessions/` |
| Systemd service | `~/.config/systemd/user/openclaw-gateway.service` |
| Service override | `~/.config/systemd/user/openclaw-gateway.service.d/override.conf` |
| Logs | `/tmp/openclaw/openclaw-YYYY-MM-DD.log` |

### Chat Commands (in Web UI)

| Command | Action |
|---------|--------|
| `/new` | Start new session |
| `/reset` | Reset conversation |
| `/models` | List available models |
| `/help` | Show help |

---

## Resources

- **Documentation:** https://docs.openclaw.ai
- **Security Guide:** https://docs.openclaw.ai/gateway/security
- **Showcase:** https://openclaw.ai/showcase

---

*Last Updated: February 2026*
