# OpenClaw + NetBox MCP Integration
## Complete Video Guide with Transcript & Steps

---

## VIDEO METADATA

| Field | Value |
|-------|-------|
| **Title** | OpenClaw → NetBox MCP → AI-Powered Network Automation |
| **Alt Titles** | "Build AI Agent for Network Automation - OpenClaw Setup" / "ChatGPT for NetBox? OpenClaw + MCP Tutorial" |
| **Duration** | 20-25 minutes |
| **Series** | AI Powered Automation - Part 4 |

---

## VIDEO TIMELINE

| Section | Time | Duration |
|---------|------|----------|
| Intro & Concepts | 0:00-3:00 | 3 min |
| OpenClaw Installation | 3:00-7:00 | 4 min |
| Chat Integration (Slack/Web UI) | 7:00-10:00 | 3 min |
| NetBox MCP Setup | 10:00-14:00 | 4 min |
| Create Custom Skill | 14:00-18:00 | 4 min |
| End-to-End Testing | 18:00-21:00 | 3 min |
| Wrap Up | 21:00-22:00 | 1 min |

---

# SECTION 1: INTRO & CONCEPTS [0:00-3:00]

## Transcript

**[SCREEN: Recap slide showing AWX + NetBox + GitHub]**

Hey, what's going on everybody - welcome to Part 4 of our AI Powered Automation series.

So quick recap - in the last few videos, we built a complete GitOps pipeline. We've got AWX running jobs, NetBox as our source of truth, GitHub syncing everything, custom execution environments - the whole stack.

Today, we're adding something really exciting on top of all that - an AI layer.

We're going to set up OpenClaw, which is this new AI agent framework, and connect it to our NetBox. By the end of this video, you'll be chatting with an AI that can actually query and update your network inventory.

**[SCREEN: Architecture diagram - Layers]**

But before we dive in, let me quickly explain how OpenClaw actually works. It's built in three layers - and understanding this will make everything else click.

**Layer 1: The LLM**

At the bottom, we have the large language model - in our case, Claude from Anthropic. This is the brain, the thing that understands your questions and figures out what to do.

**Layer 2: Tools (MCP Servers)**

On top of that, we have Tools. These are MCP servers - Model Context Protocol. Think of these as the hands. The NetBox MCP is a tool that knows how to talk to NetBox API. There's tools for ServiceNow, GitHub, Slack - all sorts of things.

The tools themselves don't decide anything. They just provide capabilities.

**Layer 3: Skills**

And then at the top, we have Skills. This is where it gets interesting. Skills are like instructions that tell the AI when and how to use those tools.

So if Tools are the hammer and screwdriver, Skills are the instructions for "build a table" - they know which tools to use, in what order, for what purpose.

**[SCREEN: Simple diagram]**

Let me give you a quick example:

- Tool: NetBox MCP - can query devices, create sites, update inventory
- Skill: "Audit Inventory" - uses the NetBox tool to check for devices missing IP addresses, then reports a summary

The skill combines the tool with logic and context. Makes sense?

Alright, with that foundation - let's start building this thing.

---

# SECTION 2: OPENCLAW INSTALLATION [3:00-7:00]

## Transcript

**[SCREEN: Terminal]**

Okay, let's get OpenClaw installed. I'm going to do this on a fresh Linux machine - you can use Ubuntu, Debian, whatever you've got.

First, let me make sure we have the prerequisites.

## Steps to Follow

```bash
# Check Python version (need 3.10+)
python3 --version

# Install required packages
sudo apt update
sudo apt install -y git curl python3-pip python3-venv
```

## Transcript (continued)

Good, Python 3.11 - we're good to go.

Now let's clone the OpenClaw repository.

## Steps to Follow

```bash
# Clone OpenClaw repo
git clone https://github.com/anthropics/openclaw.git

# Navigate into folder
cd openclaw

# Check what's inside
ls -la
```

## Transcript (continued)

**[SCREEN: Show folder contents]**

So you can see the structure here - we've got the main source code, configuration templates, and most importantly this install script.

Let's run the installer.

## Steps to Follow

```bash
# Run installer
./scripts/install.sh
```

## Transcript (continued)

**[SCREEN: Installer running]**

The installer is going to ask us a few questions. First one - provider. We're going with Anthropic since we want to use Claude.

**[SCREEN: Provider selection]**

Now it's asking for the API key. You'll need to grab this from console.anthropic.com. Let me show you real quick.

## Steps to Follow

```
# Get Anthropic API Key:
1. Go to: console.anthropic.com
2. Sign in or create account
3. Go to API Keys section
4. Create new key
5. Copy the key (starts with sk-ant-)
```

## Transcript (continued)

**[SCREEN: Paste API key - hide actual key]**

I'll paste my key here... and we're good.

Now it's asking about model selection. I'm going with Claude Sonnet for this demo - good balance of speed and capability. You can use Opus if you want the most powerful one, but it's more expensive.

## Steps to Follow

```
# Model options:
- claude-3-opus-20240229    (Most powerful, expensive)
- claude-3-sonnet-20240229  (Balanced - recommended)
- claude-3-haiku-20240229   (Fastest, cheapest)
```

## Transcript (continued)

**[SCREEN: More configuration options]**

For the other settings, I'm going to keep most defaults for now. We can always change these later in the config file.

And... installation complete!

Let me verify it's working.

## Steps to Follow

```bash
# Check OpenClaw installed
openclaw --version

# Or if using Python module
python -m openclaw --version
```

## Transcript (continued)

Perfect - OpenClaw is installed. But right now it's just the base framework. We need to connect it to a chat interface and add our NetBox tool.

---

# SECTION 3: CHAT INTEGRATION [7:00-10:00]

## Transcript

**[SCREEN: Config file location]**

So OpenClaw needs a way for us to talk to it, right? It supports a bunch of platforms - Slack, Discord, Telegram, Microsoft Teams - but it also has a built-in Web UI.

For this demo, I'm going to show you both the Web UI for quick testing, and then Slack for the real setup.

Let's start with the Web UI since it's the fastest way to test.

## Steps to Follow

```bash
# Start OpenClaw with Web UI
openclaw gateway --web-ui

# Or
python -m openclaw gateway --web-ui
```

## Transcript (continued)

**[SCREEN: Terminal showing startup]**

You can see it's starting up... and there we go - it says Web UI available at localhost:3000.

Let me open that in the browser.

**[SCREEN: Browser - OpenClaw Web UI]**

And here's our OpenClaw dashboard. Pretty clean interface. We can see the overview, sessions, and there's a chat interface right here.

Let me just do a quick test.

**[SCREEN: Type in chat]**

"Hi, are you working?"

**[SCREEN: Response appearing]**

And we got a response! So the base installation is working. But right now, it doesn't have any tools - it can't actually DO anything useful yet.

Now let me quickly show you the Slack setup, because that's probably what you'll want for real use.

## Steps to Follow

```
# Slack Setup Steps:

1. Go to: api.slack.com/apps
2. Click "Create New App"
3. Choose "From scratch"
4. Name: "OpenClaw" (or whatever)
5. Select your workspace

6. Under "OAuth & Permissions":
   - Add Bot Token Scopes:
     - chat:write
     - channels:read
     - channels:history
     - app_mentions:read
   - Install to Workspace
   - Copy "Bot User OAuth Token" (starts with xoxb-)

7. Under "Socket Mode":
   - Enable Socket Mode
   - Create App-Level Token (name: "openclaw")
   - Copy this token (starts with xapp-)

8. Under "Event Subscriptions":
   - Enable Events
   - Subscribe to bot events:
     - app_mention
     - message.channels
```

## Transcript (continued)

**[SCREEN: Show Slack app creation briefly]**

I'm not going to go through every click here, but basically you create a Slack app, get two tokens - the bot token and app token - and add them to OpenClaw config.

## Steps to Follow

```bash
# Edit OpenClaw config
nano ~/.config/openclaw/config.json
```

```json
{
  "provider": "anthropic",
  "model": "claude-3-sonnet-20240229",
  "chat": {
    "platform": "slack",
    "slack": {
      "bot_token": "xoxb-your-bot-token",
      "app_token": "xapp-your-app-token"
    }
  }
}
```

## Transcript (continued)

Once you save that and restart OpenClaw, you can mention @OpenClaw in any Slack channel and it'll respond.

But for the rest of this demo, I'll use the Web UI to keep things simple.

---

# SECTION 4: NETBOX MCP SETUP [10:00-14:00]

## Transcript

**[SCREEN: Back to terminal]**

Alright, now for the fun part - adding NetBox as a tool. This is where MCP comes in.

MCP stands for Model Context Protocol. It's basically a standard way for AI agents to connect to external tools and services.

There's an official NetBox MCP package that we can install. Let me show you.

## Steps to Follow

```bash
# Install NetBox MCP package
pip install netbox-mcp

# Or using uv (faster)
uv pip install netbox-mcp

# Verify installation
pip show netbox-mcp
```

## Transcript (continued)

Package installed. Now we need to tell OpenClaw to use it.

Let's edit the config file.

## Steps to Follow

```bash
# Open config file
nano ~/.config/openclaw/config.json
```

## Transcript (continued)

**[SCREEN: Config file open]**

So in the config file, there's a section called mcpServers. This is where we register all our tools.

I'm going to add NetBox here.

## Steps to Follow

```json
{
  "provider": "anthropic",
  "model": "claude-3-sonnet-20240229",
  "mcpServers": {
    "netbox": {
      "command": "uvx",
      "args": ["netbox-mcp"],
      "env": {
        "NETBOX_URL": "http://your-netbox-ip:8000",
        "NETBOX_TOKEN": "your-netbox-api-token"
      }
    }
  }
}
```

## Transcript (continued)

**[SCREEN: Editing config]**

Let me break this down:

- `command` and `args` - this tells OpenClaw how to run the MCP server
- `NETBOX_URL` - your NetBox instance URL, make sure to include the port
- `NETBOX_TOKEN` - your API token from NetBox

Let me grab my NetBox API token real quick.

## Steps to Follow

```
# Get NetBox API Token:
1. Log into NetBox
2. Click your username (top right)
3. Go to "API Tokens"
4. Create new token
5. Copy the token
```

## Transcript (continued)

**[SCREEN: NetBox UI showing token]**

I'll paste in my NetBox URL and token... save the file.

Now let's restart OpenClaw to pick up the new config.

## Steps to Follow

```bash
# Stop OpenClaw if running (Ctrl+C)

# Start again
openclaw gateway --web-ui
```

## Transcript (continued)

**[SCREEN: Startup logs]**

Watch the startup logs here... and there we go - you can see it says "Loaded MCP server: netbox". That means it's connected.

Let's test it!

**[SCREEN: Web UI chat]**

I'll type: "List all devices from NetBox"

**[SCREEN: Response with devices]**

And look at that - it's actually querying NetBox and showing me the devices! We've got our routers and switches from the lab.

Let me try something else: "What sites are configured in NetBox?"

**[SCREEN: Sites response]**

Beautiful - it shows the sites. The NetBox MCP tool is working.

Now let me try a write operation: "Create a new site called 'Test-Lab' in NetBox"

**[SCREEN: Create site response]**

And it created it! Let me verify in NetBox...

**[SCREEN: NetBox UI showing new site]**

There it is - Test-Lab site, created by our AI agent. This is pretty powerful stuff, right?

---

# SECTION 5: CREATE CUSTOM SKILL [14:00-18:00]

## Transcript

**[SCREEN: Terminal]**

Okay, so we've got the tool working. The AI can query and update NetBox. But right now, we're just asking simple questions.

What if we want the AI to perform a specific workflow? That's where Skills come in.

Let me show you how to create a custom skill from scratch.

## Steps to Follow

```bash
# Navigate to skills folder
cd ~/.config/openclaw/skills

# Or create if doesn't exist
mkdir -p ~/.config/openclaw/skills
cd ~/.config/openclaw/skills

# Check existing skills
ls -la
```

## Transcript (continued)

**[SCREEN: Skills folder]**

So skills are just YAML files that define what the AI should do. Let me create a simple one.

I'm going to create an "inventory audit" skill - something that checks NetBox for devices with missing information.

## Steps to Follow

```bash
# Create new skill file
nano inventory_audit.yaml
```

## Transcript (continued)

**[SCREEN: Empty file]**

Now let me write the skill definition.

## Steps to Follow

```yaml
# inventory_audit.yaml
name: inventory_audit
description: "Audit NetBox inventory for missing or incomplete device information"
version: "1.0"

trigger:
  - "audit inventory"
  - "check netbox devices"
  - "inventory health check"

tools:
  - netbox

instructions: |
  When the user asks to audit inventory, perform these steps:
  
  1. Query all devices from NetBox
  2. For each device, check for:
     - Missing primary IP address
     - Missing site assignment
     - Missing device role
     - Missing platform
  3. Compile a summary report showing:
     - Total devices checked
     - Devices with issues (list each one)
     - Devices that are complete
  4. Provide recommendations for fixing issues
  
  Be thorough but present the information clearly.
  Use tables or lists to make it readable.

examples:
  - user: "audit my inventory"
    assistant: "I'll check all devices in NetBox for completeness..."
```

## Transcript (continued)

**[SCREEN: Skill file written]**

Let me walk through what we just wrote:

- `name` - unique identifier for the skill
- `description` - what it does, helps the AI understand when to use it
- `trigger` - phrases that activate this skill
- `tools` - which MCP tools this skill needs (netbox in our case)
- `instructions` - the actual workflow, step by step
- `examples` - helps the AI understand the expected behavior

Save that... and let's restart OpenClaw to load the new skill.

## Steps to Follow

```bash
# Restart OpenClaw
# Ctrl+C to stop, then:
openclaw gateway --web-ui
```

## Transcript (continued)

**[SCREEN: Startup logs]**

And in the logs... there - "Loaded skill: inventory_audit". 

Let's test it out.

**[SCREEN: Web UI]**

I'll type: "audit my inventory"

**[SCREEN: AI processing]**

Watch this - it's thinking, it knows to use the inventory_audit skill, it's querying NetBox...

**[SCREEN: Audit results]**

And here's our audit report! Look at this:
- Total devices: 4
- Devices with issues: 2 (Router-2 missing primary IP, Switch-1 missing platform)
- Complete devices: 2

And it's giving recommendations on how to fix each issue. That's exactly what we defined in the skill.

This is the power of skills - you define the workflow once, and the AI executes it whenever you ask.

---

# SECTION 6: END-TO-END TESTING [18:00-21:00]

## Transcript

**[SCREEN: Web UI]**

Alright, let's do a complete end-to-end test to make sure everything is working together.

I'm going to simulate a real workflow - like something you'd actually do in your day-to-day.

**Test 1: Basic Query**

"Show me all routers in NetBox"

**[SCREEN: Response]**

Got our routers listed.

**Test 2: Create Something**

"Add a new device called 'Test-Switch-01', device type should be Cisco Catalyst, site is Test-Lab"

**[SCREEN: Response]**

Created! Let me verify in NetBox...

**[SCREEN: NetBox showing new device]**

There it is.

**Test 3: Run Our Custom Skill**

"Audit my inventory"

**[SCREEN: Audit running]**

And it's running our audit skill again - now it should include the new device we just created...

**[SCREEN: Updated audit]**

And look - it found our Test-Switch-01 is missing primary IP and platform. The skill is working perfectly.

**Test 4: Fix Based on Recommendation**

"Update Test-Switch-01 with primary IP 192.168.1.100 and platform IOS"

**[SCREEN: Update response]**

Done. Now let's run the audit one more time...

"Audit inventory"

**[SCREEN: Clean audit]**

And now Test-Switch-01 shows as complete. We just used AI to identify and fix inventory issues.

## Quick Summary Table

| Test | Command | Result |
|------|---------|--------|
| Basic Query | "Show all routers" | ✅ Listed devices |
| Create | "Add new device..." | ✅ Device created |
| Custom Skill | "Audit inventory" | ✅ Report generated |
| Update | "Update device with IP..." | ✅ Device updated |

---

# SECTION 7: WRAP UP [21:00-22:00]

## Transcript

**[SCREEN: Architecture diagram recap]**

Alright, let's wrap this up.

So today we built an AI agent from scratch using OpenClaw. We connected it to NetBox using MCP, and we created a custom skill to audit our inventory.

Think about what we just did:
- We can chat naturally with an AI about our network
- It can query and update NetBox automatically
- We can define custom workflows as skills
- All of this runs locally or in your own infrastructure

This is the AI layer on top of our GitOps pipeline. In the previous videos we had AWX running playbooks, NetBox as source of truth, GitHub syncing everything. Now we have an AI that can interact with all of that using natural language.

**[SCREEN: What's next slide]**

In the next video, I'm thinking we could expand this - maybe add more MCP tools like ServiceNow, or build more advanced skills that actually trigger AWX job templates. Let me know in the comments what you'd like to see.

If you found this useful, please like the video and subscribe. The OpenClaw repo link and all the config files will be in the pinned comment.

Thanks for watching, and I'll see you in the next one. Take care!

---

# QUICK REFERENCE

## Installation Commands

```bash
# Prerequisites
sudo apt update
sudo apt install -y git curl python3-pip python3-venv

# Clone OpenClaw
git clone https://github.com/anthropics/openclaw.git
cd openclaw

# Install
./scripts/install.sh

# Install NetBox MCP
pip install netbox-mcp
```

## Config File Location

```
~/.config/openclaw/config.json
```

## Config File Template

```json
{
  "provider": "anthropic",
  "model": "claude-3-sonnet-20240229",
  "api_key": "sk-ant-your-key-here",
  "mcpServers": {
    "netbox": {
      "command": "uvx",
      "args": ["netbox-mcp"],
      "env": {
        "NETBOX_URL": "http://your-netbox:8000",
        "NETBOX_TOKEN": "your-token"
      }
    }
  }
}
```

## Skill File Template

```yaml
name: skill_name
description: "What this skill does"
version: "1.0"

trigger:
  - "phrase that activates"
  - "another phrase"

tools:
  - netbox

instructions: |
  Step by step instructions for the AI...

examples:
  - user: "example input"
    assistant: "example response"
```

## Common Commands

```bash
# Start with Web UI
openclaw gateway --web-ui

# Start with Slack
openclaw gateway --slack

# Check logs
tail -f ~/.config/openclaw/logs/openclaw.log

# List installed MCP tools
openclaw tools list
```

## Troubleshooting

| Issue | Cause | Fix |
|-------|-------|-----|
| MCP not loading | Package missing | `pip install netbox-mcp` |
| Connection refused | Wrong URL | Include http:// and port |
| 403 error | Bad token | Check NetBox token permissions |
| Skill not triggering | Not loaded | Restart OpenClaw |
| Slow responses | Model size | Try claude-3-haiku |

---

# PINNED COMMENT

```
📌 RESOURCES & CONFIG FILES

🔗 Links:
• OpenClaw: https://github.com/anthropics/openclaw
• NetBox MCP: https://pypi.org/project/netbox-mcp
• Anthropic Console: https://console.anthropic.com

📄 Config File (~/.config/openclaw/config.json):
{
  "provider": "anthropic",
  "model": "claude-3-sonnet-20240229",
  "mcpServers": {
    "netbox": {
      "command": "uvx",
      "args": ["netbox-mcp"],
      "env": {
        "NETBOX_URL": "http://your-netbox:8000",
        "NETBOX_TOKEN": "your-token"
      }
    }
  }
}

📝 Skill Template (inventory_audit.yaml):
name: inventory_audit
description: "Audit NetBox inventory"
trigger:
  - "audit inventory"
tools:
  - netbox
instructions: |
  1. Query all devices
  2. Check for missing info
  3. Report summary

⌨️ Commands:
• Start: openclaw gateway --web-ui
• Install NetBox MCP: pip install netbox-mcp

Drop a comment if you have questions! 👇
```

---

# THUMBNAIL PROMPTS

## Prompt 1: "OPENCLAW + NETBOX"

```
Clean whiteboard style YouTube thumbnail, white background, hand-drawn marker aesthetic. No human faces.

Title: "OPENCLAW + NETBOX" in bold black marker
Subtitle: "AI Network Agent" in blue

Center visual - Three layer stack:
- Top: Brain icon labeled "Skills"
- Middle: Wrench icon labeled "MCP Tools"
- Bottom: Chip icon labeled "LLM (Claude)"
Arrow pointing to NetBox logo

Right side - Chat bubble:
"Audit my inventory" → Results list

Corner badge: "PART 4" in red

Style: Whiteboard marker sketch, black/blue/red ink, 1280x720
```

## Prompt 2: "AI AGENT FOR NETBOX"

```
Clean whiteboard style YouTube thumbnail, white background. No human faces.

Title: "AI AGENT" in bold black
Subtitle: "For Network Automation" in yellow highlight

Center visual - Flow diagram:
[Chat bubble] → [OpenClaw logo] → [NetBox logo]
With bidirectional arrows

Below: Three skill icons
- "Query" magnifying glass
- "Create" plus sign
- "Audit" checklist

Bottom text: "Natural Language → Network Actions"

Style: Hand-drawn whiteboard, marker pen look, 1280x720
```

---

# ARROW STYLE TITLES

**Option A:**
```
OpenClaw → NetBox MCP → AI-Powered Inventory Management
```

**Option B:**
```
Chat → AI Agent → NetBox: Build Network Automation Brain
```

**Option C:**
```
LLM + MCP Tools + Skills = Intelligent Network Agent
```

**Option D:**
```
Natural Language → Network Actions: OpenClaw + NetBox Setup
```

---

*Ready for Recording!*
