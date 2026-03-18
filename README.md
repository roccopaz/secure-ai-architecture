# 🔒 Secure AI Service Architecture

> Enterprise-grade security design for a self-hosted production AI system on Linux VPS

When deploying an always-on AI platform that interacts with real accounts, APIs, and sensitive data, security cannot be an afterthought. I designed and implemented a layered security architecture using the same principles used in enterprise DevSecOps environments.

---

## 🛡️ Security Layers

### Layer 1 — Network Security: Tailscale VPN
The VPS runs **Tailscale**, a zero-config WireGuard-based VPN that creates a private encrypted mesh network.

- All admin access to the VPS is tunneled through Tailscale — **no exposed SSH port on the public internet**
- The server is invisible to external port scanners; only authorized devices on the Tailnet can connect
- WireGuard encryption (ChaCha20-Poly1305) secures all management traffic end-to-end
- Tailscale ACLs (Access Control Lists) restrict which devices can reach which services
- Eliminates brute-force SSH attacks — the port simply doesn't exist to the outside world

### Layer 2 — Service Identity Isolation
Created a dedicated Google account exclusively for the AI system's API access.

- Personal email, contacts, and data are **never** exposed to the AI runtime
- Even if the service account credentials were compromised, the blast radius is fully contained — zero personal data at risk
- Follows the **principle of least privilege** at the identity level

### Layer 3 — Scoped OAuth Permissions
Each Google API authorized independently with only the minimum required scopes:

| API | Scope Granted | Reason |
|-----|--------------|--------|
| Google Drive | Read/Write specific folders | Content pipeline storage |
| Google Sheets | Read/Write | Pipeline logging |
| Google Docs | Read/Write | Content review docs |
| Gmail | ❌ **Excluded** | AI cannot read or send personal email — by design |

### Layer 4 — Encrypted Credential Storage
OAuth tokens stored via encrypted keyring backend on the VPS.

- Tokens never stored in plaintext
- Never committed to version control (`.gitignore` enforced)
- Password-protected keyring file — decrypted only at runtime
- Rotatable without system downtime

### Layer 5 — API Key Segmentation
Third-party API keys (Anthropic Claude, OpenAI) stored as environment variables.

- Isolated from the codebase entirely
- Each key has a single purpose — no shared/combined keys
- Rotatable independently without touching other services

---

## 🗺️ Architecture Overview

```
Internet
    │
    ▼
┌─────────────────────────────┐
│  Tailscale VPN (WireGuard)  │  ← Only authorized devices connect
│  Zero public attack surface │
└────────────┬────────────────┘
             │ Encrypted tunnel
             ▼
┌─────────────────────────────┐
│       Linux VPS             │
│  ┌─────────────────────┐   │
│  │  OpenClaw AI Gateway │   │
│  │  (Node.js runtime)   │   │
│  └──────────┬──────────┘   │
│             │               │
│  ┌──────────▼──────────┐   │
│  │  Service Account     │   │  ← Isolated Google identity
│  │  (scoped OAuth)      │   │  ← No personal data exposure
│  └──────────┬──────────┘   │
│             │               │
│  ┌──────────▼──────────┐   │
│  │  Encrypted Keyring   │   │  ← Tokens at rest, password-protected
│  │  Env Vars (API keys) │   │
│  └─────────────────────┘   │
└─────────────────────────────┘
             │
             ▼
    External APIs
    (Google, Anthropic, OpenAI, Discord)
```

---

## 📚 Security Concepts Demonstrated

| Concept | Implementation |
|---------|---------------|
| **Zero Trust Networking** | Tailscale VPN — no implicit trust, all access authenticated |
| **Principle of Least Privilege** | Per-API OAuth scopes, Gmail excluded |
| **Service Identity Isolation** | Dedicated service account, no personal data exposure |
| **Blast Radius Containment** | Compromise of service account ≠ compromise of personal account |
| **Secrets Hygiene** | No plaintext credentials, no git-committed tokens |
| **Encrypted Storage at Rest** | Password-protected keyring backend |
| **API Key Segmentation** | Environment variables, single-purpose keys |
| **Defense in Depth** | Multiple independent security layers |
| **DevSecOps** | Security designed into the system from day one |

---

## 🔧 Tools & Technologies

- **Tailscale** — Zero-config WireGuard VPN, mesh network, ACL-based access control
- **WireGuard** — Modern VPN protocol (ChaCha20-Poly1305 encryption)
- **OAuth 2.0** — Google Workspace API authentication
- **Linux Keyring** — Encrypted credential storage backend
- **UFW (Uncomplicated Firewall)** — Additional host-based firewall rules
- **Environment Variables** — Runtime secret injection

---

## 🔗 Related Projects

- [Portfolio](https://roccopaz.github.io) — full project breakdown
- [AI Content Pipeline](https://github.com/roccopaz/ai-content-pipeline) — pipeline secured by this architecture

---

*Secure infrastructure design for a self-hosted multi-agent AI system — Texas State University CIS Senior*
