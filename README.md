# 🦞 OpenClaw Complete Guide

> **AI Agent Framework - From Installation to Production**
>
> Transform any LLM into a powerful automation agent that actually takes action on your system.

[![OpenClaw](https://img.shields.io/badge/OpenClaw-v2026.3.1-red)](https://openclaw.ai)
[![License](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)
[![Node](https://img.shields.io/badge/Node.js-22%2B-green)](https://nodejs.org)

---

<details>
<summary><strong>📋 Prerequisites</strong></summary>

### System Requirements

| Requirement | Minimum | Recommended |
|-------------|---------|-------------|
| **OS** | Ubuntu 22.04 / macOS 12+ | Ubuntu 24.04 LTS |
| **Node.js** | v22+ | v22.22+ |
| **RAM** | 4GB | 8GB+ |
| **Storage** | 2GB free | 5GB+ free |

### LLM Provider (Choose One)

| Provider | Cost | Best For |
|----------|------|----------|
| **OpenRouter** | Free tier available | Beginners, testing |
| **Anthropic Claude** | Paid | Production, complex tasks |
| **Google Gemini** | Free tier available | General use |
| **LM Studio** | Free (local) | Privacy, offline use |

### Verify Prerequisites

```bash
# Check OS
hostnamectl

# Check Node.js (must be v22+)
node -v

# Check npm
npm -v
```

</details>

---

<details>
<summary><strong>🚀 Installation</strong></summary>

### One-Line Install

```bash
curl -fsSL https://openclaw.ai/install.sh | bash
```

### What the Installer Does

1. ✅ Detects your OS (Linux/macOS/Windows)
2. ✅ Verifies Node.js v22+ is installed
3. ✅ Installs OpenClaw npm package globally
4. ✅ Launches interactive setup wizard

### Setup Wizard Selections

| Prompt | Recommended Selection |
|--------|----------------------|
| Security warning | **Yes** (acknowledge) |
| Onboarding mode | **QuickStart** |
| Model/auth provider | **OpenRouter** |
| API Key | Paste your key |
| Default model | `openrouter/z-ai/glm-4.5-air:free` |
| Configure channels | **Skip for now** |
| Configure skills | **No** |
| Enable hooks | **Skip for now** |
| Hatch bot | **Do this later** |

### Post-Installation: Fix PATH

```bash
# Add npm global bin to PATH
echo 'export PATH="$HOME/.npm-global/bin:$PATH"' >> ~/.bashrc
source ~/.bashrc

# Verify installation
openclaw --version
```

### Health Check

```bash
# Run diagnostics
openclaw doctor

# Auto-fix issues
openclaw doctor --fix

# Start gateway service
openclaw daemon start
```

</details>

---

<details>
<summary><strong>⚙️ Configuration</strong></summary>

### Config File Location

```
~/.openclaw/openclaw.json
```

### View Current Config

```bash
# View full config
cat ~/.openclaw/openclaw.json

# View specific value
openclaw config get agents.defaults.model.primary
openclaw config get gateway.auth.token
```

### Set Config Values

```bash
# Set primary model
openclaw config set agents.defaults.model.primary "openrouter/anthropic/claude-sonnet-4"

# Remove a config value
openclaw config unset some.config.key
```

### Run Configuration Wizard

```bash
# Full configuration
openclaw configure

# Specific sections
openclaw configure --section model
openclaw configure --section web
openclaw configure --section channels
```

### Example Config Structure

```json
{
  "auth": {
    "profiles": {
      "openrouter:default": {
        "provider": "openrouter",
        "mode": "api_key"
      }
    }
  },
  "agents": {
    "defaults": {
      "model": {
        "primary": "openrouter/z-ai/glm-4.5-air:free"
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
  },
  "env": {
    "PATH": "/home/user/.local/bin:/home/user/.npm-global/bin:/usr/local/bin:/usr/bin:/bin",
    "MCPORTER_CONFIG": "/home/user/netbox-mcp-server/config/mcporter.json"
  }
}
```

</details>

---

<details>
<summary><strong>🔧 Service Management</strong></summary>

### Systemd Commands

```bash
# Check status
systemctl --user status openclaw-gateway

# Start gateway
systemctl --user start openclaw-gateway

# Stop gateway
systemctl --user stop openclaw-gateway

# Restart gateway (use after config changes)
systemctl --user restart openclaw-gateway

# Enable on boot
systemctl --user enable openclaw-gateway

# Disable on boot
systemctl --user disable openclaw-gateway

# Reload systemd after service file changes
systemctl --user daemon-reload
```

### OpenClaw Daemon Commands

```bash
# Start daemon
openclaw daemon start

# Stop daemon
openclaw daemon stop

# Restart daemon
openclaw daemon restart
```

</details>

---

<details>
<summary><strong>🖥️ Web UI / Dashboard</strong></summary>

### Access Dashboard

```bash
# Get URL without opening browser
openclaw dashboard --no-open

# Open in default browser
openclaw dashboard
```

### Default URLs

| Service | URL |
|---------|-----|
| Web UI | `http://127.0.0.1:18789/` |
| Web UI (with token) | `http://127.0.0.1:18789/#token=YOUR_TOKEN` |
| WebSocket | `ws://127.0.0.1:18789` |

### Get/Generate Gateway Token

```bash
# Get current token
openclaw config get gateway.auth.token

# Generate new token
openclaw doctor --generate-gateway-token
```

### Dashboard Tabs

| Tab | Purpose |
|-----|---------|
| **Chat** | Direct conversation with AI agent |
| **Overview** | Gateway health, connection status |
| **Agents** | Model config, workspace settings |
| **Skills** | View/manage available skills |
| **Config** | Edit configuration |
| **Debug** | Real-time request logs |

### Chat Commands

| Command | Action |
|---------|--------|
| `/new` | Start new session |
| `/clear` | Clear current session context |
| `/reset` | Reset conversation |
| `/models` | List available models |
| `/help` | Show help |

</details>

---

<details>
<summary><strong>🤖 Model Providers</strong></summary>

### OpenRouter (Recommended for Beginners)

**Why OpenRouter?**
- One API key for all models (Claude, GPT-4, Llama, etc.)
- Free models available
- Easy to switch between models

**Setup:**
1. Go to [openrouter.ai](https://openrouter.ai)
2. Sign up with Google/GitHub
3. Go to **Keys** → **Create Key**
4. Copy key (starts with `sk-or-v1-...`)

**Free Models:**
- `openrouter/z-ai/glm-4.5-air:free`
- `openrouter/meta-llama/llama-3.1-8b-instruct:free`
- `openrouter/google/gemma-2-9b-it:free`
- `openrouter/mistralai/mistral-7b-instruct:free`

**Paid Models (Better for Skills):**
- `openrouter/anthropic/claude-sonnet-4`
- `openrouter/openai/gpt-4o`

### Local LLM (LM Studio)

```bash
# During configure, select:
# Provider: vLLM
# Base URL: http://<YOUR_IP>:1234/v1
# API Key: lm-studio
# Model: qwen2.5-coder-7b-instruct
```

**LM Studio Settings:**

| Setting | Value |
|---------|-------|
| Context Length | 16384+ |
| Serve on Local Network | ✅ Enabled |
| GPU Offload | Maximum possible |

**Test Connection:**
```bash
curl http://<YOUR_IP>:1234/v1/models
```

</details>

---

<details>
<summary><strong>🔌 MCP Integration (Model Context Protocol)</strong></summary>

### What is MCP?

MCP (Model Context Protocol) is a standard protocol that lets AI models call external tools directly. Think of it as a plugin system for AI.

### Architecture

```
You → OpenClaw → mcporter (bridge) → MCP Server → External API
```

### Step 1: Install UV Package Manager

```bash
# Install UV
curl -LsSf https://astral.sh/uv/install.sh | sh

# Add to current session
source $HOME/.local/bin/env

# Verify
uv --version
```

### Step 2: Install mcporter

```bash
# Install globally
npm install -g mcporter

# Verify
mcporter --version
```

### Step 3: Clone MCP Server (Example: NetBox)

```bash
cd ~
git clone https://github.com/netboxlabs/netbox-mcp-server.git
cd netbox-mcp-server
```

### Step 4: Configure MCP Server

```bash
# Create credentials file
cat > .env << 'EOF'
NETBOX_URL=http://192.168.1.120:8000
NETBOX_TOKEN=your-netbox-api-token
EOF
```

### Step 5: Install Dependencies & Test

```bash
# Install Python dependencies
uv sync

# Test server starts (Ctrl+C to stop)
uv run netbox-mcp-server
```

**Expected output:**
```
🦞 FastMCP 2.14.2
🖥  Server: NetBox
INFO: Starting MCP server 'NetBox' with transport 'stdio'
```

### Step 6: Create mcporter Config

```bash
# Create config directory
mkdir -p ~/netbox-mcp-server/config

# Create config file
cat > ~/netbox-mcp-server/config/mcporter.json << 'EOF'
{
  "mcpServers": {
    "netbox": {
      "command": "/home/user/.local/bin/uv",
      "args": [
        "--directory",
        "/home/user/netbox-mcp-server",
        "run",
        "netbox-mcp-server"
      ]
    }
  },
  "imports": []
}
EOF
```

### Step 7: Verify mcporter Configuration

```bash
# Set config path
export MCPORTER_CONFIG=~/netbox-mcp-server/config/mcporter.json

# List servers (should show "netbox")
mcporter list

# View available tools
mcporter netbox --tools

# Test query
mcporter call netbox.netbox_get_objects object_type="dcim.device" filters="{}"
```

**Expected mcporter list output:**
```
mcporter 0.7.3 — Listing 1 server(s)
- netbox (4 tools, 1.4s)
✔ Listed 1 server (1 healthy).
```

### Step 8: Update OpenClaw Config

```bash
# Backup config
cp ~/.openclaw/openclaw.json ~/.openclaw/openclaw.json.bak

# Add env section with PATH and MCPORTER_CONFIG
python3 << 'EOF'
import json

with open('/home/user/.openclaw/openclaw.json', 'r') as f:
    config = json.load(f)

config['env'] = {
    "PATH": "/home/user/.local/bin:/home/user/.npm-global/bin:/usr/local/bin:/usr/bin:/bin",
    "MCPORTER_CONFIG": "/home/user/netbox-mcp-server/config/mcporter.json"
}

with open('/home/user/.openclaw/openclaw.json', 'w') as f:
    json.dump(config, f, indent=2)

print("✅ env section added!")
EOF

# Verify
cat ~/.openclaw/openclaw.json | grep -A 4 '"env"'
```

### Step 9: Restart Gateway

```bash
systemctl --user restart openclaw-gateway
systemctl --user status openclaw-gateway
```

### Step 10: Test in Dashboard

```bash
openclaw dashboard --no-open
```

Open URL in browser → Chat tab → Type: `List all devices in NetBox`

</details>

---

<details>
<summary><strong>📚 Skills</strong></summary>

### What Are Skills?

Skills are markdown files (SKILL.md) that teach OpenClaw how to perform specific tasks. They're like instruction manuals for your AI.

### Skills Directory

```
~/.openclaw/workspace/skills/
```

### View Available Skills

```bash
# List all skills
openclaw skills list

# Search for specific skill
openclaw skills list | grep -i netbox
```

### Create a Custom Skill

```bash
# Create skill directory
mkdir -p ~/.openclaw/workspace/skills/netbox

# Create skill file
cat > ~/.openclaw/workspace/skills/netbox/SKILL.md << 'EOF'
---
name: netbox
description: Query NetBox network infrastructure. Use when user asks about devices, IPs, sites, VLANs, or network inventory.
---

# NetBox API Access

Query NetBox at http://192.168.1.120:8000 using curl:

```bash
curl -s -H "Authorization: Token YOUR_API_TOKEN" "http://192.168.1.120:8000/api/dcim/devices/"
```

## Common Endpoints

| Endpoint | Description |
|----------|-------------|
| `/api/dcim/devices/` | All devices |
| `/api/dcim/devices/?role=router` | Routers only |
| `/api/dcim/devices/?role=switch` | Switches only |
| `/api/ipam/ip-addresses/` | All IP addresses |
| `/api/dcim/sites/` | All sites |
| `/api/ipam/vlans/` | All VLANs |
| `/api/dcim/interfaces/` | All interfaces |

## Example Queries

- All devices: `/api/dcim/devices/`
- Filter by site: `/api/dcim/devices/?site=main-dc`
- Filter by status: `/api/dcim/devices/?status=active`
EOF
```

### Reload Skills

```bash
# Restart gateway to load new skills
systemctl --user restart openclaw-gateway

# Verify skill is loaded
openclaw skills list | grep -i netbox
```

### Skill File Structure

```yaml
---
name: skill-name
description: When to use this skill (triggers AI to use it)
---

# Instructions

Step-by-step instructions for the AI to follow.

## Section 1

Details...

## Section 2

More details...
```

### Skills vs MCP Comparison

| Aspect | Skills | MCP |
|--------|--------|-----|
| **What it is** | Markdown instructions | Tool protocol |
| **How AI uses it** | Reads & executes commands | Calls tools directly |
| **Setup complexity** | Easy (just .md file) | Medium (needs server) |
| **Reliability** | Depends on AI model | More consistent |
| **Best for** | Quick custom integrations | Production workflows |

</details>

---

<details>
<summary><strong>🔍 Troubleshooting</strong></summary>

### Diagnostic Commands

```bash
# Run full diagnostics
openclaw doctor

# Auto-fix issues
openclaw doctor --fix

# Security audit
openclaw security audit --deep

# Auto-fix security issues
openclaw security audit --fix
```

### Common Issues & Solutions

#### Issue: `openclaw: command not found`

**Cause:** npm global bin not in PATH

**Fix:**
```bash
echo 'export PATH="$HOME/.npm-global/bin:$PATH"' >> ~/.bashrc
source ~/.bashrc
```

#### Issue: `uv: command not found`

**Cause:** UV not in PATH

**Fix:**
```bash
source $HOME/.local/bin/env
```

#### Issue: `Unknown MCP server 'netbox'`

**Cause:** MCPORTER_CONFIG not set in OpenClaw

**Fix:**
```bash
# Add to ~/.openclaw/openclaw.json env section:
"MCPORTER_CONFIG": "/home/user/netbox-mcp-server/config/mcporter.json"

# Restart gateway
systemctl --user restart openclaw-gateway
```

#### Issue: `spawn uv ENOENT`

**Cause:** OpenClaw can't find UV binary

**Fix:**
```bash
# Add to ~/.openclaw/openclaw.json env section:
"PATH": "/home/user/.local/bin:/home/user/.npm-global/bin:/usr/local/bin:/usr/bin:/bin"

# Restart gateway
systemctl --user restart openclaw-gateway
```

#### Issue: `device signature expired` in Dashboard

**Cause:** Stale browser session

**Fix:**
```bash
# Clear browser localStorage for localhost:18789
# Then get fresh URL:
openclaw dashboard --no-open
```

#### Issue: Model doesn't follow skills

**Cause:** Free models may not follow instructions well

**Fix:**
- Be explicit in prompts: `"Run: curl -s -H 'Authorization: Token XXX' http://..."`
- Use better model: `openrouter/anthropic/claude-sonnet-4`

#### Issue: Gateway not starting

**Fix:**
```bash
# Check status
systemctl --user status openclaw-gateway

# View logs
journalctl --user -u openclaw-gateway -n 50

# Restart
systemctl --user restart openclaw-gateway
```

</details>

---

<details>
<summary><strong>📜 Logs</strong></summary>

### View Gateway Logs

```bash
# Last 50 lines
journalctl --user -u openclaw-gateway -n 50

# Follow in real-time
journalctl --user -u openclaw-gateway -f

# Since today
journalctl --user -u openclaw-gateway --since today

# Since specific time
journalctl --user -u openclaw-gateway --since "1 hour ago"

# With timestamps (no pager)
journalctl --user -u openclaw-gateway -n 100 --no-pager
```

### Debug in Dashboard

1. Open Dashboard
2. Click **Debug** in sidebar (under Settings)
3. View real-time request/response logs

</details>

---

<details>
<summary><strong>🗑️ Uninstallation</strong></summary>

### Complete Removal

```bash
# Step 1: Stop services
openclaw daemon stop
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

### Remove MCP Server (Optional)

```bash
rm -rf ~/netbox-mcp-server
npm uninstall -g mcporter
```

</details>

---

## 📋 Quick Reference

<details>
<summary><strong>Essential Commands</strong></summary>

| Task | Command |
|------|---------|
| Install | `curl -fsSL https://openclaw.ai/install.sh \| bash` |
| Version | `openclaw --version` |
| Doctor | `openclaw doctor` |
| Doctor Fix | `openclaw doctor --fix` |
| Configure | `openclaw configure` |
| Dashboard | `openclaw dashboard --no-open` |
| Start | `systemctl --user start openclaw-gateway` |
| Stop | `systemctl --user stop openclaw-gateway` |
| Restart | `systemctl --user restart openclaw-gateway` |
| Status | `systemctl --user status openclaw-gateway` |
| Logs | `journalctl --user -u openclaw-gateway -f` |
| Skills | `openclaw skills list` |

</details>

<details>
<summary><strong>File Locations</strong></summary>

| File | Location |
|------|----------|
| Main config | `~/.openclaw/openclaw.json` |
| Config backup | `~/.openclaw/openclaw.json.bak` |
| Workspace | `~/.openclaw/workspace/` |
| Skills | `~/.openclaw/workspace/skills/` |
| Sessions | `~/.openclaw/agents/main/sessions/` |
| Systemd service | `~/.config/systemd/user/openclaw-gateway.service` |

</details>

<details>
<summary><strong>MCP Commands</strong></summary>

| Task | Command |
|------|---------|
| Install UV | `curl -LsSf https://astral.sh/uv/install.sh \| sh` |
| Install mcporter | `npm install -g mcporter` |
| List MCP servers | `mcporter list` |
| View tools | `mcporter netbox --tools` |
| Test query | `mcporter call netbox.netbox_get_objects object_type="dcim.device" filters="{}"` |

</details>

---

## 🔗 Resources

| Resource | Link |
|----------|------|
| OpenClaw Website | [openclaw.ai](https://openclaw.ai) |
| Documentation | [docs.openclaw.ai](https://docs.openclaw.ai) |
| Security Guide | [docs.openclaw.ai/gateway/security](https://docs.openclaw.ai/gateway/security) |
| OpenRouter | [openrouter.ai](https://openrouter.ai) |
| MCP Registry | [github.com/modelcontextprotocol/servers](https://github.com/modelcontextprotocol/servers) |
| NetBox MCP | [github.com/netboxlabs/netbox-mcp-server](https://github.com/netboxlabs/netbox-mcp-server) |

---

<p align="center">
  <strong>Made with 🦞 by the OpenClaw Community</strong><br>
  <em>Last Updated: March 2026</em>
</p>
