# Recon Supplement — CISA KEV Deep Dive, Telegram Zero-Click, MCP Dev Summit

**Date**: 2026-04-02 05:50 GMT+7
**Type**: Supplemental scan — follow-up on Report #20 pending tracks
**Sources**: CISA KEV, SecurityAffairs, SecurityOnline, SecureReading, Heise, CyberKendra, Dev.to, Linux Foundation, Yahoo Finance

## 1. CISA KEV — April 3 Deadline (TOMORROW)

### Craft CMS CVE-2025-32432 (CVSS 10.0)
- **Type**: Code injection (CWE-94) — unauthenticated RCE
- **Mechanism**: Malicious `return URL` parameters in `actions/assets/generate-transform` endpoint, saved to PHP session files then executed
- **Attack chain**: Chained with CVE-2024-58136 (Yii framework input validation) for full server compromise + data theft
- **Exploited by**: Unknown threat actors as zero-day since February 2025 (Orange Cyberdefense SensePost)
- **Affected**: Craft CMS 3.x, 4.x, 5.x
- **Patched**: 3.9.15, 4.14.15, 5.6.17 + Yii 2.0.52
- **Den exposure**: None — we don't run Craft CMS. **Awareness only.**

### Laravel Livewire CVE-2025-54068 (CVSS 9.8)
- **Type**: Code injection via unsafe hydration of component property updates
- **Mechanism**: Unauthenticated RCE through how Livewire deserializes component state during updates
- **Attribution**: Linked to Iran-nexus APT group **MuddyWater** — targeting diplomatic, energy, and finance sectors
- **Affected**: Livewire 3.0.0-beta.1 through 3.6.3
- **Patched**: Livewire 3.6.4 (no workarounds available)
- **Disclosure**: July 17, 2025
- **PoC**: Public (Synacktiv write-up: "remote command execution through unmarshaling")
- **Den exposure**: None — we don't run Laravel Livewire. **Awareness only.**

### Apple (3 CVEs also due April 3)
- CVE-2025-31277 (WebKit, 8.8), CVE-2025-43510 (Kernel, 7.8), CVE-2025-43520 (Kernel, 8.8)
- Gorn's devices are the only Apple surface. Already routed through Sable per Bertus.

**Assessment**: No direct Den exposure on any April 3 KEV items. All tracked for pattern awareness.

## 2. Telegram Zero-Click — ZDI-CAN-30207

### Key Facts
- **Discoverer**: Michael DePlante, Trend Micro Zero Day Initiative
- **Reported to Telegram**: March 26, 2026
- **CVSS**: Originally 9.8, lowered to 7.0 after Telegram described server-side mitigations
- **Affected platforms**: Android and Linux confirmed. iOS/desktop unclear.
- **Attack vector**: Corrupted sticker files. Preview-generation pipeline triggers code execution. Zero interaction required — no clicks, no opens, no message acceptance.
- **Impact**: Arbitrary code execution, private comms access, surveillance, credential theft

### The Dispute
- **Telegram denies the vulnerability exists**. Claims all stickers are validated/scanned server-side before distribution, making malicious stickers impossible.
- **ZDI maintains the finding is valid**. Full disclosure deadline: **July 24-26, 2026**.
- Telegram took to X to publicly deny. Unusual aggressive posture for a disclosure.

### Mitigations (until resolved)
- Disable automatic media downloads in Telegram
- Restrict messaging to known contacts
- Disable unknown bot interactions
- Monitor endpoints for anomalous Telegram behavior

### Den Relevance
- **Gorn uses Telegram** (our notification channel). Direct exposure.
- Android/Linux affected — matches Gorn's likely device profile.
- No patch available. Server-side mitigations are Telegram's claim, not independently verified.
- **Recommendation**: Flag to Gorn via Sable. Consider disabling auto-download for stickers until resolved.

## 3. MCP Dev Summit — Day 1 (TODAY, April 2, NYC)

### What We Know
- **95+ sessions**, 50+ speakers from Anthropic, Google, OpenAI, Datadog, Hugging Face, Microsoft
- Under the **Agentic AI Foundation** (Linux Foundation)

### Key Sessions to Watch

**Auth (6 dedicated sessions — the hot topic)**
- Aaron Parecki (OAuth 2.1 spec author) + Paul Carleton (Anthropic) on cross-app access and SSO for AI agents
- Auth is "the dominant unsolved problem in the MCP ecosystem"

**Protocol Evolution**
- "MCP at 18 Months: Protocols, Patterns, What We Didn't See Coming" — Shaun Smith (Hugging Face)
- "One Spec, Ten SDKs, Zero Excuses: Conformance Testing MCP" — Paul Carleton (Anthropic)

**Security**
- "Mix-Up Attacks in MCP: Multi-Issuer Confusion and Mitigations" — Emily Lauber (Microsoft)
- Directly relevant to our MCP standing intel track and OpenClaw findings

**SDK**
- "Path to V2 for MCP SDKs" — Max Isbey (Anthropic). First public statement on Python SDK v2. May include `mcp.server.auth` changes.

**Cross-Ecosystem**
- "MCP x MCP" keynote — Nick Cooper (OpenAI), April 3. OpenAI and Anthropic SDKs both implementing `list_resources()` / `read_resource()`. Standardization signals.

**Production**
- "When MCP Isn't Enough: Product Decisions Behind Scalable Agent Systems" — Cansu Berkem (Datadog)

### Day 1 outputs not yet available — summit just starting. Will scan for recaps tomorrow.

## Patterns

- **MuddyWater using Laravel Livewire** — Iran APT exploiting web frameworks, not just network appliances. The attack surface is shifting to app-layer.
- **Telegram's denial posture** is unusual and concerning. A CVSS 9.8 finding from ZDI is not a false alarm. The dispute itself is the signal — Telegram may be buying time to patch quietly.
- **MCP auth is the bottleneck** — 6 sessions dedicated to auth at the summit confirms what OpenClaw's sandbox escapes already showed: the protocol shipped faster than its security model.
- **Supply chain + agentic AI convergence** — Claude Code source leak (Report #20) + MCP conformance testing + OpenClaw CVEs = the agent toolchain is the new supply chain.
