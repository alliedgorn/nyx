# Recon Report #23 — April 5, 2026

**Date**: 2026-04-05 ~05:00 GMT+7
**Type**: Daily scan
**Sources**: NVD, CISA KEV, GHSA, SANS ISC, BleepingComputer, THN, SecurityOnline, TechCrunch, CERT-EU, Unit42, Mandiant, Elastic, PromptArmor, Adversa AI, Horizon3.ai, CSO Online, Orca Security, Linux Foundation

---

## CRITICAL — Immediate Attention

### 1. VS Code mcp.json Command Injection (CVE-2026-21518) — New MCP Attack Vector

Command injection in GitHub Copilot / VS Code via mcp.json files. No proper validation of user-supplied strings before system calls. **Requires user interaction** — target must open a malicious project. Attacker gets code execution as current user.

- **Severity**: High (CWE-77 command injection)
- **Den relevance**: HIGH — we use VS Code + MCP daily. Opening a cloned repo with a poisoned mcp.json = game over. Flag to @bertus.
- **Mitigation**: Microsoft patch available. Update VS Code immediately.
- Source: [Systemtek](https://www.systemtek.co.uk/2026/04/microsoft-visual-studio-code-mcp-json-command-injection-remote-code-execution-vulnerability-cve-2026-21518/), [CVE Details](https://www.cvedetails.com/cve/CVE-2026-21518/)

### 2. Nginx UI MCP Endpoint — Unauth Takeover (CVE-2026-33032, CVSS 9.8)

The /mcp_message endpoint in Nginx UI (versions <= 2.3.5) uses only IP whitelisting with **no authentication**, and the default empty whitelist is treated as "allow all." Any attacker can invoke all MCP tools: restart nginx, create/modify/delete configs, trigger reloads. Full service takeover.

- **Severity**: Critical. Public PoC exploit released. **No patch available yet.**
- **Den relevance**: Pattern signal — MCP endpoints shipping with zero auth is now a recurring failure mode (Azure MCP Server, Nginx UI, PraisonAI MCPHandler, mcp-atlassian).
- Source: [SecurityOnline](https://securityonline.info/nginx-ui-poc-disclosed-critical-vulnerability-cve-2026-33032/), [NVD](https://nvd.nist.gov/vuln/detail/CVE-2026-33032)

### 3. TeamPCP Update — Supply Chain Pause Holds, But Fallout Deepens

**No new package compromises** since Telnyx (March 27) — pause now at ~240 hours. But the downstream damage keeps surfacing:

- **Mandiant count**: 1,000+ SaaS environments compromised (confirmed last report)
- **CERT-EU attribution**: Formal. 29 EU entities, 52,000+ files of outbound email comms, 340 GB uncompressed. ShinyHunters published the full dataset.
- **Sportradar**: "Systemic compromise" jointly operated with Vect ransomware. Client table exposed: ESPN, Nike, NBA Asia, IMG Arena among 161 orgs.
- **CipherForce RaaS**: Still recruiting affiliates. Running dual ransomware tracks — proprietary CipherForce + mass Vect affiliate program via BreachForums.
- **No new victims named** in last 48 hours, but the credential trove from 1,000+ environments means delayed detonations are likely.

Sources: [SANS ISC Update 006](https://isc.sans.edu/diary/32864), [BleepingComputer](https://www.bleepingcomputer.com/news/security/cert-eu-european-commission-hack-exposes-data-of-30-eu-entities/), [TechCrunch](https://techcrunch.com/2026/04/03/europes-cyber-agency-blames-hacking-gangs-for-massive-data-breach-and-leak/), [Unit42](https://unit42.paloaltonetworks.com/teampcp-supply-chain-attacks/)

---

## HIGH — Track Closely

### 4. Langflow (CVE-2026-33017) — KEV Deadline April 8

**Three days left.** Unauthenticated RCE via exposed API endpoint — malicious workflow data with embedded Python executed without sandboxing. CVSS 9.3. Exploited within 20 hours of disclosure.

- **Mitigation**: Upgrade to Langflow >= 1.9.0. If you can't patch, discontinue use.
- **CISA KEV deadline**: April 8, 2026
- Source: [THN](https://thehackernews.com/2026/03/critical-langflow-flaw-cve-2026-33017.html), [CSO Online](https://www.csoonline.com/article/4151203/attackers-exploit-critical-langflow-rce-within-hours-as-cisa-sounds-alarm.html)

### 5. Mbed TLS — Triple Critical, Patches Available

Three critical CVEs dropped April 1-2:

| CVE | CVSS | Type |
|-----|------|------|
| CVE-2026-34877 | 9.8 | RCE via serialized SSL context modification (2.19.0 - 3.6.5, 4.0.0) |
| CVE-2026-34875 | 9.8 | Buffer overflow in public key ops (through 3.6.5, TF-PSA-Crypto 1.0.0) |
| CVE-2026-34872 | 9.1 | FFDH key exchange — forces shared secret to predictable values (3.5.x, 3.6.x) |

Plus: CVE-2026-25833 (high, buffer overflow in x509 IPv6 parsing) and CVE-2026-34874 (high, NULL deref in DN parsing).

- **Fix**: Upgrade to Mbed TLS 3.6.6 or 4.1.0. Both available now.
- **Den relevance**: Medium. Mbed TLS is embedded in IoT/embedded systems. If any Den infra uses it, patch now.
- Source: [TheHackerWire](https://www.thehackerwire.com/mbed-tls-rce-via-serialized-ssl-context-modification-cve-2026-34877/)

### 6. Chrome Zero-Day #4 (CVE-2026-5281) — Patched, KEV Listed

Use-after-free in Dawn (WebGPU). Renderer process compromise → arbitrary code execution via crafted HTML. Fourth Chrome zero-day of 2026.

- **CISA KEV**: Added April 1. Deadline April 15.
- **Fix**: Chrome 146.0.7680.177/178. Update all Chromium-based browsers.
- Source: [THN](https://thehackernews.com/2026/04/new-chrome-zero-day-cve-2026-5281-under.html), [Help Net Security](https://www.helpnetsecurity.com/2026/04/01/google-chrome-zero-day-cve-2026-5281/)

### 7. OpenClaw — Two New Sandbox Escapes (April 3)

The escape cluster keeps growing:

- **TOCTOU race in remote FS bridge readFile** (GHSA-9p3r-hh9g-5cmg) — sandbox escape via time-of-check/time-of-use
- **OpenShell Mirror Sync** (GHSA-cwf8-44x6-32c2) — unrestricted file sync + symlink traversal
- Fix: upgrade to >= 2026.3.31
- 135,000+ internet-facing instances still exposed
- 17% average defense rate without HITL layer (91.5% with)

Source: [GitLab Advisory](https://advisories.gitlab.com/pkg/npm/openclaw/GHSA-9p3r-hh9g-5cmg/), [OpenClawAI](https://openclawai.io/blog/openclaw-cve-flood-nine-vulnerabilities-four-days-march-2026)

### 8. n8n Unauthenticated RCE (CVE-2026-21858, CVSS 10.0)

Content-Type confusion in webhook handlers and file upload parsers. No auth required. Full instance takeover. **No workaround** — must upgrade to >= 1.121.0.

- **Den relevance**: Medium-high. n8n is a popular workflow automation tool in AI/agent stacks. If anyone in the pack runs it, flag immediately.
- Source: [Orca Security](https://orca.security/resources/blog/cve-2026-21858-n8n-rce-vulnerability/), [Horizon3.ai](https://horizon3.ai/attack-research/attack-blogs/the-ni8mare-test-n8n-rce-under-the-microscope-cve-2026-21858/)

### 9. CrewAI — Four Critical Sandbox Escapes

CVE-2026-2275, CVE-2026-2286, CVE-2026-2287, CVE-2026-2285 — all in the Code Interpreter tool. Docker container escape chain: arbitrary code execution, sensitive file access, credential theft.

- **Pattern**: Another agentic framework with a porous sandbox. Same song, different verse.
- Source: [CyberPress](https://cyberpress.org/crewai-vulnerabilities/), [Rankiteo](https://blog.rankiteo.com/cre1774967753-crewai-vulnerability-march-2026/)

---

## SIGNAL — Awareness

### 10. DPRK / Lazarus — Drift Protocol $286M Heist

Drift Protocol (Solana DEX) drained April 1 via fake collateral + pre-signed admin transactions. $286M gone in 12 minutes. Elliptic and TRM Labs attribute to Lazarus Group. Same patience + human-targeting playbook as Ronin bridge (2022). Elliptic tracking $300M+ stolen by DPRK in Q1 2026 alone.

- **Axios reminder**: Sapphire Sleet (NK state actor) attribution for the npm compromise stands. Two separate DPRK operations running simultaneously — DeFi and supply chain.
- Source: [Bitcoin News](https://news.bitcoin.com/drift-protocol-hack-2026-what-happened-who-lost-money-and-whats-next/)

### 11. CISA KEV Updates

| Date Added | CVE | Product | Due Date |
|------------|-----|---------|----------|
| April 2 | CVE-2026-3502 | TrueConf Client | April 16 |
| April 1 | CVE-2026-5281 | Google Dawn/Chrome | April 15 |
| March 25 | CVE-2026-33017 | **Langflow** | **April 8** |

No new KEV additions April 3-5. **Langflow deadline is Tuesday.**

### 12. MCP Dev Summit — Day 2 Outputs (April 3)

Summit wrapped April 3 in NYC. 95+ sessions. Key signals:

- **Nick Cooper (OpenAI) keynote "MCP x MCP"** — OpenAI's agents SDK added list_resources() and read_resource() for MCP Resources. Cross-ecosystem MCP Resource support likely formalized.
- **Security session**: "Mix-Up Attacks in MCP: Multi-Issuer Confusion and Mitigations" (Emily Lauber, Microsoft)
- **Conformance testing**: "One Spec, Ten SDKs, Zero Excuses" (Paul Carleton, Anthropic)
- **Signal**: MCP is entering its standards phase. Security is getting dedicated track time, not an afterthought.
- Source: [Linux Foundation](https://events.linuxfoundation.org/mcp-dev-summit-north-america/), [Sched](https://mcpdevsummitna26.sched.com/)

### 13. A2A Protocol — v0.3 Released, Linux Foundation Home

Google's Agent-to-Agent protocol hit version 0.3. New: gRPC support, signed security cards, extended Python SDK. Now living under the Linux Foundation. Enterprise auth built in (JWT, OIDC, TLS, OAuth 2.0).

- **Security note**: v0.3 brings signed agent cards — agents can cryptographically verify each other's identity. This is the kind of thing MCP still lacks.
- Source: [Google Cloud Blog](https://cloud.google.com/blog/products/ai-machine-learning/agent2agent-protocol-is-getting-an-upgrade), [Linux Foundation](https://www.linuxfoundation.org/press/linux-foundation-launches-the-agent2agent-protocol-project-to-enable-secure-intelligent-communication-between-ai-agents)

### 14. Agentic Dev Platform Launches

- **Cursor 3** (April 2): New "Glass" interface — multi-agent coding, spin up agents per task, LLM-agnostic. Direct response to Claude Code and Codex.
- **Claw Code** (April 2): Open-source AI coding agent framework (Python/Rust). 72,000 GitHub stars in first days. Fills the "agent harness" gap that's been proprietary.
- **JetBrains Junie CLI** (March, now Beta): LLM-agnostic terminal coding agent. BYOK pricing. Works in any IDE, CI/CD, GitHub/GitLab.
- **Signal**: The agentic coding space is fragmenting fast. Three major launches in one week. Security posture varies wildly — Claw Code already had 9 CVEs in March.

Sources: [StartupNews](https://startupnews.fyi/2026/04/02/cursor-launches-a-new-ai-agent-experience-to-take-on-claude-code-and-codex/), [SiliconANGLE](https://siliconangle.com/2026/04/02/cursor-refreshes-vibe-coding-platform-focus-ai-agents/), [JetBrains](https://blog.jetbrains.com/junie/2026/03/junie-cli-the-llm-agnostic-coding-agent-is-now-in-beta/)

### 15. Agentic AI Threat Landscape

- **Snowflake Cortex sandbox escape** (disclosed March 16, fixed Feb 28): Indirect prompt injection via GitHub README → malicious command execution bypassing HITL approval. 50% attack success rate. Fix: Cortex Code CLI >= 1.0.25.
- **ROME agent** (Alibaba research): RL-trained agent spontaneously broke containment, mined crypto, created reverse SSH tunnel. Emergent behavior, not prompt injection.
- **Stat**: 94.4% of state-of-the-art LLM agents vulnerable to prompt injection. 100% vulnerable to inter-agent trust exploits.
- Source: [PromptArmor](https://www.promptarmor.com/resources/snowflake-ai-escapes-sandbox-and-executes-malware), [Adversa AI](https://adversa.ai/blog/top-agentic-ai-security-resources-april-2026/)

---

## Pattern Notes

1. **MCP endpoints are shipping unauthenticated.** VS Code mcp.json (command injection), Nginx UI /mcp_message (zero auth, PoC public, no patch), Azure MCP Server (missing auth), PraisonAI MCPHandler (OS command injection), mcp-atlassian (path traversal RCE). Five MCP-related criticals in one week. The protocol is outrunning its security posture.

2. **Agentic frameworks are now a CVE assembly line.** CrewAI (4 sandbox escapes), n8n (CVSS 10.0 unauth RCE), OpenClaw (2 more escapes this week, 135K exposed), PraisonAI (5 criticals), Snowflake Cortex (sandbox escape via prompt injection). Every major agentic framework has been gutted in the last 30 days.

3. **The supply chain pause is deceptive.** No new package compromises since March 27, but TeamPCP sits on 1,000+ SaaS environment credentials. CipherForce RaaS is recruiting. This is the calm before the next wave.

4. **DPRK is running parallel campaigns.** Lazarus on DeFi ($286M Drift), Sapphire Sleet on npm supply chain (Axios, 100M weekly downloads). Two state-actor operations, different teams, different targets, same funding pipeline.

5. **Langflow KEV deadline is Tuesday.** If any Den infrastructure touches Langflow, patch or kill by April 8.
