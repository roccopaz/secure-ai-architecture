# Secure AI Service Architecture | Hermes Agent VPS

This repository describes access and credential controls currently used on the Linux VPS running Hermes Agent for **MODTECH Shipment Tracking & Alerts**. The same environment can support future Hermes Agent workflows. It is an account of the current setup, not a formal security assessment.

## Current controls

| Area | Current setup | Purpose |
| --- | --- | --- |
| Administrative access | Tailscale-only access to the VPS | Limit the path used to manage the server |
| API authorization | Limited OAuth permissions | Give connected services only the permissions selected for their tasks |
| Token storage | Encrypted token storage | Protect stored authorization tokens |
| API keys | Keys stored in environment variables | Keep key values out of public project files |

These controls were confirmed by the project owner on September 24, 2026. This public repository does not contain private configuration, credentials, or an independent test of every control.

## Current use

Hermes Agent and OpenAI Codex generated the Python implementation of MODTECH's shipment tracker. Rocco Paz defined the user story and five acceptance criteria, directed the work, and tested its initial behavior. The tracker checks UPS, FedEx, and USPS shipment status through Shippo every 60 minutes, stores shipment and order context, and sends Telegram alerts when a status changes.

The Systems Analysis & Design report documents three successful initial functional tests: preventing a repeat alert when status was unchanged, retaining order context with a delivered status, and registering a shipment from a label image with a user-specified package name. Those tests cover tracker behavior; they do not establish the security of the VPS or connected services.

The workflow supports human fulfillment decisions. It does not automatically update Shopify fulfillment, message customers, or make purchasing decisions.

## Architecture at a glance

```text
Administrator -- Tailscale --> Linux VPS running Hermes Agent
                                  |-- limited OAuth permissions and encrypted tokens
                                  |-- API keys supplied through environment variables
                                  |-- shipment tracker --> Shippo status checks
                                  `-- change alerts --> Telegram --> human review
```

The public documentation describes the boundaries of the current system. It does not include network rules, OAuth scope values, tokens, keys, customer information, or private shipment records.

[Rocco Paz GitHub profile](https://github.com/roccopaz) · [MODTECH shipment project on LinkedIn](https://www.linkedin.com/in/roccopaz/)
