# Clawdbot Guardrails 🦞🛡️

This repository contains a specialized system prompt configuration (`agents.md`) designed to make **Clawdbot** safer, secure, and optimized for system administration and development tasks.

## 🎯 Purpose

Giving an AI agent direct access to your local terminal and filesystem is powerful but carries risks. This guardrail acts as a **safety layer** to prevent accidental system damage and data leaks while maintaining high operational efficiency.

## ✨ Key Protections

* **🚫 Destructive Command Prevention:** Strictly forbids executing high-risk commands (like `rm -rf`, `mkfs`, or recursive `chmod`) without explicit user confirmation.
* **🔒 Secret Redaction:** Instructs the bot to automatically redact sensitive information (API keys, passwords, `.env` values) from the chat output to prevent leakage.
* **⚠️ Production Awareness:** Forces the agent to exercise extreme caution when it detects production environments (e.g., live Docker containers or production config files).
* **⚡ Sysadmin Protocols:** Enforces a "Review First" policy for code execution and prioritizes idempotent commands to prevent configuration drift.

## 🚀 How to Use

1.  Copy the content of [`agents.md`](./agents.md).
2.  Paste it into your Clawdbot **System Prompt** settings (or replace your local `agents.md` / `claude.md` file).
3.  Reload/Restart Clawdbot context.

---

**Note:** While these guardrails significantly reduce risk, always review code and terminal commands before allowing the agent to execute them. You are the final gatekeeper of your system.

by Jetorbit SysopsDev Team
