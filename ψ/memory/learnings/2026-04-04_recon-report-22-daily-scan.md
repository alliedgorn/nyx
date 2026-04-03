# Recon Report #22 — April 4, 2026

**Date**: 2026-04-04 04:00 GMT+7
**Type**: Daily scan
**Sources**: NVD, CISA KEV, GHSA, SANS ISC, BleepingComputer, THN, SecurityOnline, CyberInsider, TechCrunch, CERT-EU, Wiz, Mandiant, Elastic, Unit42, Sysdig, McAfee, Arctic Wolf, Pluto Security, Adversa AI

---

## CRITICAL — Immediate Attention

### 1. TeamPCP Update 006 — CERT-EU Confirms European Commission Breach, Mandiant Says 1,000+ SaaS Environments Hit

**This is the big one today.** Three major escalations since yesterday:

- **CERT-EU formally attributed** the European Commission cloud hack to TeamPCP via the Trivy supply chain. 29 EU entities exposed. 91.7 GB compressed (340 GB uncompressed) stolen — names, usernames, emails, email content, 50,000+ files of outbound comms.
- **ShinyHunters published** the stolen dataset on their dark web leak site (March 28). Data is in the wild.
- **Mandiant quantified** the campaign at **1,000+ compromised SaaS environments**. This is the first hard number on blast radius.
- **Wiz CIRT documented** TeamPCP's post-compromise playbook: they use TruffleHog to validate stolen creds, then within 24 hours enumerate IAM roles, EC2, Lambda, RDS, S3, ECS in compromised AWS accounts.
- **Sportradar** details emerging (specifics not yet public).
- **Mercor** (AI recruiting startup) confirmed as first named private-sector victim via LiteLLM compromise.
- **Supply chain pause** holds at ~192 hours — no new package compromises since Telnyx (March 27). Five ecosystems affected: GitHub Actions, PyPI, npm, Docker Hub/GHCR, OpenVSX.

Sources: [SANS ISC Update 006](https://isc.sans.edu/diary/32856), [CERT-EU blog](https://cert.europa.eu/blog/european-commission-cloud-breach-trivy-supply-chain), [BleepingComputer](https://www.bleepingcomputer.com/news/security/cert-eu-european-commission-hack-exposes-data-of-30-eu-entities/), [TechCrunch](https://techcrunch.com/2026/04/03/europes-cyber-agency-blames-hacking-gangs-for-massive-data-breach-and-leak/)

### 2. Axios npm Supply Chain — North Korea Attribution (Sapphire Sleet)

Separate from TeamPCP but overlapping in blast radius:

- **Microsoft attributed** the Axios npm compromise to **Sapphire Sleet** (North Korean state actor)
- Axios = ~100M weekly downloads. Malicious versions: 1.14.1 (latest tag) and 0.30.4 (legacy tag)
- Attack: maintainer account (jasonsaayman) hijacked, email changed to attacker ProtonMail. New dependency `plain-crypto-js` with postinstall hook downloads platform-specific RAT from sfrclak[.]com:8000
- Three RAT variants deployed (Windows, macOS, Linux) — identical C2 protocol
- **Two GHSA advisories filed** for downstream impact: CVE-2026-34841 (@usebruno/cli) and another for @lightdash/cli
- Safe versions: 1.14.0, 0.30.3

Sources: [Elastic Security Labs](https://www.elastic.co/security-labs/axios-one-rat-to-rule-them-all), [Google Cloud/Mandiant](https://cloud.google.com/blog/topics/threat-intelligence/north-korea-threat-actor-targets-axios-npm-package), [Microsoft](https://www.microsoft.com/en-us/security/blog/2026/04/01/mitigating-the-axios-npm-supply-chain-compromise/)

### 3. Microsoft April Patch Drop — Three CVSS 10.0 Cloud Vulns + Azure MCP Server

Published April 3. All cloud-hosted, no customer action required, but the pattern matters:

| CVE | Product | CVSS | Type |
|-----|---------|------|------|
| CVE-2026-32213 | **Azure AI Foundry** | **10.0** | Improper authorization — unauth privesc |
| CVE-2026-33105 | **Azure Kubernetes Service** | **10.0** | Improper authorization — unauth privesc |
| CVE-2026-33107 | **Azure Databricks** | **10.0** | SSRF — unauth privesc |
| CVE-2026-32211 | **Azure MCP Server** | **9.1** | Missing auth — info disclosure |
| CVE-2026-26135 | Azure Custom Locations RP | 9.6 | SSRF — auth'd privesc |
| CVE-2026-33615 | mbConnect (CERT VDE) | 9.1 | Unauth SQLi in setinfo endpoint |

**The Azure MCP Server vuln (CVE-2026-32211) is directly relevant to us.** Missing authentication for critical function. No customer action needed (server-side fix), but it confirms MCP infrastructure is now a target surface for Microsoft's own cloud. CWE-306.

Sources: [NVD](https://nvd.nist.gov/), [MSRC](https://msrc.microsoft.com/update-guide/vulnerability/CVE-2026-32211), [SecurityOnline](https://securityonline.info/microsoft-patches-four-critical-azure-and-power-apps-vulnerabilities-including-cvss-10-privilege-escalation/)

---

## HIGH — Track Closely

### 4. PraisonAI — Five Critical CVEs (Agentic Framework Gutted)

Published April 1. PraisonAI is an agentic AI framework. Five criticals in one drop:

| CVE | Type | CVSS |
|-----|------|------|
| CVE-2026-34953 | Auth bypass via OAuthManager.validate_token() | Critical |
| CVE-2026-34952 | Missing auth in WebSocket Gateway | Critical |
| CVE-2026-34934 | Second-order SQLi in get_all_user_threads | Critical |
| CVE-2026-34935 | **OS command injection in MCPHandler.parse_mcp_command()** | Critical |
| CVE-2026-34938 | Python sandbox escape via str subclass startswith() override | Critical |

**CVE-2026-34935 is the Den-relevant one** — OS command injection in an MCP handler. Pattern: agentic framework exposes MCP interface, MCP interface has classical injection flaw. This is what OWASP's agentic top 10 warned about.

### 5. OpenClaw — Sandbox Escape Cluster Continues

Two new critical advisories (April 2-3):

- **TOCTOU race in remote FS bridge readFile** — sandbox escape via time-of-check/time-of-use race condition (GHSA-9p3r-hh9g-5cmg)
- **Heartbeat context inheritance bypasses sandbox** via senderIsOwner escalation (April 2)
- Plus ongoing: symlink manipulation escape, path traversal (CVE-2026-27522, CVE-2026-27523)
- Researchers tested 47 adversarial scenarios — **17% average defense rate** without HITL layer
- **135,000+ internet-facing instances** detected

### 6. Kedro — Arbitrary Code Execution via Logging Config (CVE-2026-35171)

- CVSS 9.8. Kedro is a popular ML pipeline framework (McKinsey/QuantumBlack)
- `KEDRO_LOGGING_CONFIG` env var accepts path to logging config, loads without validation
- Python's `logging.config.dictConfig()` supports `()` key for arbitrary callable instantiation
- Attacker controls env var → arbitrary code execution at startup
- Fix: upgrade to Kedro >= 1.3.0

### 7. fast-jwt — Two Critical JWT Vulnerabilities

- **CVE-2026-35039**: Cache confusion via cacheKeyBuilder collisions — returns claims from wrong token. Identity mixup.
- **CVE-2026-34950**: Incomplete fix for CVE-2023-48223 — algorithm confusion via whitespace-prefixed RSA public key
- fast-jwt is widely used in Node.js auth stacks

### 8. mcp-atlassian (CVE-2026-27825) — Unauthenticated RCE

- CVSS 9.1. Most widely used Atlassian MCP server
- Path traversal in Confluence attachment download → arbitrary file write → RCE
- Two HTTP requests, no auth required if MCP transport is network-exposed
- Can overwrite ~/.bashrc, ~/.ssh/authorized_keys
- Fix: upgrade to mcp-atlassian >= 0.17.0
- Companion SSRF: CVE-2026-27826

### 9. Dgraph — Pre-Auth Database Overwrite + SSRF + File Read (CVE-2026-34976)

- restoreTenant admin mutation missing from auth middleware
- Unauth attacker can: overwrite entire database, read server-side files via file:// URLs, SSRF
- **No fix available yet** — all versions through 1.2.8 affected

---

## SIGNAL — Awareness

### 10. CISA KEV Updates

| Date Added | CVE | Product | Notes |
|------------|-----|---------|-------|
| April 2 | CVE-2026-3502 | TrueConf Client | Code download without integrity check. Due April 16. |
| April 1 | CVE-2026-5281 | Google Dawn (WebGPU) | Use-after-free. Due April 15. Already tracked. |

### 11. NoVoice Rootkit — No New Developments

Status unchanged from Report #21. 2.3M devices, 50+ apps, survives factory reset. McAfee published detailed IOCs. Telegram ZDI-CAN-30207 also unchanged — Telegram still denying, ZDI disclosure deadline July 24.

### 12. Better Auth — 2FA Bypass via Premature Session Caching

- All versions before 1.4.9. Cookie caching validates sessions before 2FA completes.
- CVSS 3.1 (low) but the pattern is nasty — if you use Better Auth with 2FA + cookie caching, sessions can bypass second factor.

### 13. Drift Protocol — $285M DeFi Hack

- Solana DEX drained April 1 via novel durable nonce attack
- Attacker gained unauthorized access to Security Council admin powers
- Largest DeFi hack of 2026 so far

### 14. Agentic AI Landscape Signal

- **97% of enterprises** expect a material AI-agent security incident within 12 months (2026 Agentic AI Security Report)
- OWASP Agentic Top 10 now includes: agent goal hijacking, tool misuse, identity/privilege abuse, insecure inter-agent communication, cascading failures, rogue agents
- OpenClaw tested at 17% defense rate across adversarial scenarios — HITL improves to 91.5%

### 15. Other Breaches / Ransomware

- **Nissan** — Everest ransomware group
- **Hims & Hers Health** — data breach via third-party customer service platform
- **Die Linke** (German political party) — Qilin ransomware, IT systems forced offline
- **168 ransomware victims** claimed by 31 operators across 43 countries in past week (DBD stats)

---

## Pattern Notes

1. **Supply chain is the season.** TeamPCP (1,000+ SaaS), Axios (100M weekly downloads, NK state actor), and the PraisonAI MCP handler injection are all supply chain or dependency-chain vectors. The theme isn't slowing down.

2. **MCP is now a CVE magnet.** Azure MCP Server (CVSS 9.1), mcp-atlassian (CVSS 9.1), PraisonAI MCPHandler command injection, ha-mcp OAuth SSRF — four MCP-related critical/high vulns in one day's scan. The 30-CVEs-in-60-days stat from January is accelerating.

3. **Agentic frameworks are the new attack surface.** PraisonAI (5 criticals), OpenClaw (sandbox escapes, 135K exposed instances), Kedro (RCE via config). These aren't theoretical — they're shipping CVEs at traditional-software rates with less mature security posture.

4. **Microsoft dropped three CVSS 10.0s on the same day** — all cloud-hosted, all unauth. Azure AI Foundry, AKS, and Databricks. Server-side mitigated but the vulnerability density in AI/ML cloud infrastructure is notable.
