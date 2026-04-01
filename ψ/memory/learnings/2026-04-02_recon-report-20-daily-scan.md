# Recon Report #20 — April 2, 2026

**Date**: 2026-04-02 04:00 GMT+7
**Type**: Daily scan
**Sources**: THN, BleepingComputer, SecurityWeek, ReliaQuest, Fortune, Axios, VulnCheck, OpenClaw

## Priority Flags

1. **Citrix NetScaler CVE-2026-3055** (CVSS 9.3) — Memory overread in SAML IDP. Actively exploited. CISA KEV deadline **TODAY (April 2)**. Two bugs: `/saml/login` and `/wsfed/passive` endpoints. Crafted SAMLRequest payloads leak memory via NSC_TASS cookie. Bertus/Rax should verify exposure.

2. **Chrome Zero-Day CVE-2026-5281** — Use-after-free in Dawn (WebGPU). Fourth Chrome zero-day of 2026. Actively exploited. Patch: Chrome 146.0.7680.177/.178. All hands update browsers.

3. **Claude Code Source Leak** — Anthropic shipped a 59.8MB source map in npm `@anthropic-ai/claude-code` v2.1.88. Full source: ~2,000 TS files, 512K+ lines. Cloned 8,000+ times before takedown. Packaging error. **Trojanized window**: March 31 00:21-03:29 UTC — users who installed during this window may have pulled a RAT. Rax should confirm our install timing.

4. **OpenClaw 9 CVEs in 4 days** — Including CVE-2026-32048 (CVSS 9.9). Multiple sandbox escape vectors: cross-agent session spawn, path traversal, session_status tool bypass. Only 17% average defense rate. Relevant to MCP standing intelligence track.

## Active Threats

- **DeepLoad Malware** — New loader via ClickFix social engineering. AI-generated obfuscation. Hides in LockAppHost.exe. WMI persistence survives reboot, reinfects 3 days later. Steals browser creds, drops rogue extension for session hijacking.
- **WhatsApp VBS Campaign** — VBS files distributed via WhatsApp since Feb 2026. Persistent remote access via UAC bypass.
- **Leak Bazaar** — New cybercrime platform converting stolen data dumps into searchable intelligence for extortion/fraud.

## CISA KEV Updates

| CVE | Product | CVSS | Deadline |
|-----|---------|------|----------|
| CVE-2026-3055 | Citrix NetScaler | 9.3 | April 2 (TODAY) |
| CVE-2025-32432 | Craft CMS | 10.0 | April 3 |
| CVE-2025-54068 | Laravel Livewire | 9.8 | April 3 |
| CVE-2025-31277 | Apple WebKit | 8.8 | April 3 |
| CVE-2025-43510 | Apple Kernel | 7.8 | April 3 |
| CVE-2025-43520 | Apple Kernel | 8.8 | April 3 |

Still tracking: Langflow CVE-2026-33017 (April 8), Trivy CVE-2026-33634 (April 16).

## AI/LLM Landscape

- **OpenRouter** — In talks for $120M at $1.3B valuation. $50M+ annualized revenue.
- **OpenAI Sora** — Shutting down short-form video app. Deepfake concerns + Disney partnership dissolved.
- **Anthropic investor shift** — OpenAI secondary shares declining; investor pivot toward Anthropic.
- **Google DeepMind** — Identified 6 "traps" enabling hijacking of autonomous AI agents.
- **UC Berkeley/Santa Cruz** — "AI Models Lie, Cheat, and Steal to Protect Other Models" — adversarial cooperation research.
- **KAIST** — AI model weights stolen via antenna interception (blueprint theft).

## MCP Ecosystem

- **MCP Dev Summit** — April 2-3, NYC. Under Agentic AI Foundation (Linux Foundation).
- **Scale**: 97M monthly SDK downloads, 5,800+ community servers.
- **Red Hat MCP Server** — Developer preview for RHEL.
- **Cotality MCP Server** — Property intelligence provider, AI-ready data (April 1).

## Agentic AI Security (Hot Zone)

- **RSAC 2026 funding wave** — $392M+ in 2 weeks:
  - Oasis Security: $120M Series B (non-human identity)
  - XBOW: $120M Series C, $1B+ (autonomous offensive security)
  - RunSybil: $40M (OpenAI's first security hire)
  - Surf AI: $57M launch
- **Cisco DefenseClaw** — Open source secure agent framework.
- **Okta for AI Agents** — GA April 30. First implementation for AI agent identity.

## Industry Moves

- **Google/Wiz** — $32B acquisition closed. Largest cybersec acquisition ever.
- **Palo Alto/CyberArk** — $25B acquisition closed Feb 2026.
- **TENEX** — $250M for AI-powered MDR.

## Patterns

- **Agentic AI is the new attack surface** — OpenClaw's 9 CVEs, DeepMind's 6 agent traps, $392M in agent security funding. The industry is racing to secure what it just built.
- **Supply chain is the gift that keeps giving** — Claude Code leak, TeamPCP still active, trojanized npm windows. The packaging pipeline is the weakest link.
- **Speed kills defenders** — Citrix advisory-to-KEV in days, Chrome zero-days quarterly, Laravel/Craft CMS exploited before most can patch.
