# Clawdbot Guardrails 🦞🛡️

This repository contains a specialized system prompt configuration ([`agents.md`](./agents.md) / [`claude.md`](./claude.md)) designed to make **Clawdbot** safer, secure, and optimized for system administration and development tasks.

## ⚡ Quick Start

Install the guardrails in your Clawdbot workspace:

```bash
# Copy to your Clawdbot workspace (default: ~/clawd)
cp agents.md ~/clawd/AGENTS.md
cp claude.md ~/clawd/CLAUDE.md

# Restart the Gateway to apply changes
clawdbot gateway restart

# Test that guardrails are active
clawdbot message send --message "What guardrails do you have?"
```

**[→ See full installation guide](#how-to-use)**

## Purpose

Giving an AI agent direct access to your local terminal and filesystem is powerful but carries risks. This guardrail acts as a **safety layer** to prevent accidental system damage and data leaks while maintaining high operational efficiency.

![Clawdbot Guardrails Overview](ss.png)

## Consent Model

All operations are classified into tiers with appropriate consent requirements:

| Tier | Consent | Examples |
|------|---------|----------|
| **DENYLIST** | Double consent (explain → yes → "YES") | `rm`, `dd`, `mkfs`, `DROP TABLE`, `git push --force`, `docker rm` |
| **DENYLIST** | **ABSOLUTELY FORBIDDEN** | **ANY payments, purchases, subscriptions, domain buys, crypto trades** |
| **RESTRICTED** | Single consent | `sudo`, system dirs, package installs, e-commerce sites, billing forms |
| **SENSITIVE DIRS** | Single consent for reading | `~/.ssh/*`, `~/.aws/*`, `~/.gnupg/*`, `.env` files |
| **SAFE** | No consent | `ls`, `cat`, `grep`, `git status`, `docker ps` |
| **FORBIDDEN** | Never execute | `/boot`, `/System/Library`, kernel modules, firmware |

## Key Protections

* **💳 Financial Transaction Block:** **ABSOLUTELY FORBIDS** any purchases, payments, or subscriptions. Prevents autonomous spending (e.g., $2,997 mastermind signups, $4,200 domain purchases).
* **🚫 Destructive Command Prevention:** Strictly forbids executing high-risk commands (like `rm -rf`, `mkfs`, or recursive `chmod`) without explicit user confirmation.
* **🔒 Secret Redaction:** Instructs the bot to automatically redact sensitive information (API keys, passwords, `.env` values) from the chat output to prevent leakage.
* **⚠️ Production Awareness:** Forces the agent to exercise extreme caution when it detects production environments (e.g., live Docker containers or production config files).
* **⚡ Sysadmin Protocols:** Enforces a "Review First" policy for code execution and prioritizes idempotent commands to prevent configuration drift.

### Double Consent Protocol
Destructive operations require a 4-step confirmation flow:
1. **Explain** - State command, consequences, affected resources
2. **First consent** - "Do you understand the consequences? (yes/no)"
3. **Second consent** - "Type 'YES' (all caps) to confirm"
4. **Execute** - Only after exact "YES" response

### Sensitive Directory Protection
Reading from credential directories (`~/.ssh`, `~/.aws`, `~/.gnupg`, `~/.kube`, `.env` files) requires explicit user approval. Private key contents are NEVER output.

### 💳 Financial & E-Commerce Restrictions (NEW)
**ABSOLUTE PROHIBITION** - The agent is **FORBIDDEN** from:

- Payment processing (Stripe, PayPal, banking APIs)
- Subscription/membership signups (SaaS, courses, masterminds)
- Domain purchases (GoDaddy, Namecheap, registrars)
- E-commerce checkout flows (Amazon, online shopping)
- Crypto operations (wallet transfers, DeFi, NFTs)
- Investment trades (stocks, crypto exchanges)
- Donations/tipping (Patreon, Ko-fi)
- Payment method management (adding cards, billing info)

**Mandatory behavior:** REFUSE → EXPLAIN → REDIRECT. Even if explicitly instructed, the agent must refuse and state: *"I cannot make purchases or process payments. Financial transactions require your direct action."*

> **Inspired by real incidents** where AI agents autonomously made thousands in purchases based on YouTube content analysis.

### Secret Redaction
Automatically detects and redacts:
- API keys (`sk-*`, `pk_*`, `api_*`, `key_*`)
- AWS credentials (`AKIA*`, `AWS*`)
- Private keys (`-----BEGIN * PRIVATE KEY-----`)
- Connection strings with passwords

### Production Environment Detection
Auto-escalates caution when detecting:
- Files: `docker-compose.prod.yml`, `.env.production`
- Directories: `/var/www`, `/srv`, `/opt/*`
- Environment: `NODE_ENV=production`, `RAILS_ENV=production`
- Database names containing `prod`, `production`, `live`

### Audit Trail
All write operations are logged with:
```
[TIMESTAMP] [LEVEL] Action: <description>
├── Command: <exact command>
├── Target: <file/resource path>
├── Impact: <what changed>
└── Rollback: <how to undo>
```

### Tool Capability Tiers
| Tier | Access Level | Operations |
|------|--------------|------------|
| **Tier 1** | Unrestricted | Read-only: logs, status, git read ops |
| **Tier 2** | Single consent | Config edits, file creation, local dev servers |
| **Tier 3** | Double consent | Service management, system packages, production deploys |
| **Tier 4** | Forbidden | Kernel modules, firmware, BIOS/UEFI |

## How to Use

There are two ways to apply these guardrails to your Clawdbot installation:

### Method 1: Via Workspace Files (Recommended)

Place the guardrail files in your Clawdbot workspace directory:

1. Copy [`agents.md`](./agents.md) to `~/clawd/AGENTS.md` (or your configured workspace)
2. Copy [`claude.md`](./claude.md) to `~/clawd/CLAUDE.md`
3. Restart your Clawdbot Gateway: `clawdbot gateway restart`

**Notes:**
- Workspace defaults to `~/clawd` but can be customized in `~/.clawdbot/clawdbot.json` via `agents.defaults.workspace`
- `AGENTS.md` is automatically injected into the agent's system prompt
- `CLAUDE.md` is used by Claude Code / CLI integrations

### Method 2: Via Configuration File

1. Copy the content of [`agents.md`](./agents.md) or [`claude.md`](./claude.md)
2. Edit `~/.clawdbot/clawdbot.json` (or run `clawdbot configure`)
3. Set the system prompt via `agents.list[].systemPrompt` or workspace bootstrap files
4. Restart the Gateway: `clawdbot gateway restart`

### Verify Installation

After applying the guardrails, test them:

```bash
# Test that the agent acknowledges the guardrails
clawdbot message send --message "What guardrails do you have?"

# Or send a message via your connected channel (WhatsApp/Telegram/etc.)
# Try: "What are your safety protocols?"
```

The agent should respond with details about:
- DENYLIST operations requiring double consent
- RESTRICTED operations requiring single consent
- SENSITIVE DIRECTORIES protection
- Audit trail requirements
- Emergency stop commands

## Emergency Stop

Type `STOP`, `CANCEL`, or `ABORT` to immediately:
- Halt running processes
- Terminate multi-step chains
- Cancel pending commands

---

**Note:** While these guardrails significantly reduce risk, always review code and terminal commands before allowing the agent to execute them. You are the final gatekeeper of your system.

by Jetorbit SysopsDev Team
