# 🔒 Secure AI Service Architecture

> Service identity isolation and credential security design for production AI infrastructure

When deploying AI systems that interact with real accounts and APIs, I applied enterprise DevSecOps principles to keep personal credentials completely separate from the AI runtime.

---

## 🛡️ What I Implemented

### 1. Dedicated Service Account
Created a separate Google account exclusively for the AI system's API access. Personal email and data are **never** exposed to the AI runtime — even if credentials were compromised, the blast radius is fully contained.

### 2. Scoped OAuth Permissions (Least Privilege)
Each Google API authorized independently with only the minimum required scopes:
- ✅ Google Drive API — file read/write for content pipeline
- ✅ Google Sheets API — logging and tracking
- ✅ Google Docs API — document generation
- ❌ Gmail — **intentionally excluded** — AI cannot read or send personal email

### 3. Encrypted Credential Storage
OAuth tokens stored via encrypted keyring backend (file-based) on the VPS. Tokens are:
- Never stored in plaintext
- Never committed to version control
- Rotatable without system downtime

### 4. API Key Segmentation
Anthropic (Claude) and OpenAI API keys stored as environment variables — isolated from the codebase and independently rotatable.

---

## 📚 Security Concepts Demonstrated

| Concept | Implementation |
|---------|---------------|
| **Principle of Least Privilege** | Per-API OAuth scopes, Gmail excluded |
| **Service Identity Isolation** | Dedicated Google account for AI runtime |
| **Blast Radius Containment** | Personal account never exposed |
| **Secrets Hygiene** | No plaintext creds, no git commits of tokens |
| **Encrypted Storage** | Keyring file backend on VPS |
| **API Key Segmentation** | Environment variables, not hardcoded |
| **DevSecOps** | Security designed in from day one |

---

## 🔗 Related

- [Portfolio](https://roccopaz.github.io) — full breakdown with context
- [OpenClaw VPS AI System](https://roccopaz.github.io) — the platform this secures

---

*Applied security architecture for a self-hosted multi-agent AI system — Texas State University CIS*
