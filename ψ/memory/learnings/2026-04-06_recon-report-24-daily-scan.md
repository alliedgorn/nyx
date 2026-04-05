# Recon Report #24 — April 6, 2026

**Date**: 2026-04-06
**Type**: Daily scan
**Sources**: NVD, CISA KEV, GHSA, SANS ISC, BleepingComputer, THN, SecurityOnline, Help Net Security, Google Cloud Blog, Mandiant, Unit42, Adversa AI, SecurityWeek, TechCrunch, Axios, Fortune, VentureBeat, Arkose Labs

---

## CRITICAL — Immediate Attention

### 1. FortiClient EMS Zero-Day Actively Exploited (CVE-2026-35616, CVSS 9.1)

Pre-authentication API access bypass in FortiClient EMS 7.4.5-7.4.6 leading to privilege escalation and unauthorized code execution. Zero-day exploitation confirmed since March 31. Fortinet released emergency hotfix on April 4; full fix expected in 7.4.7. Discovered by Simo Kohonen (Defused Cyber) and Nguyen Duc Anh.

- **Why it matters**: Fortinet gear is everywhere. Pre-auth + actively exploited = patch-now territory. Another Fortinet zero-day in the chain after CVE-2026-21643 (also CVSS 9.1, also exploited).
- **Den relevance**: MEDIUM — we don't run FortiClient EMS, but this is a pattern signal for enterprise clients.
- Sources: [THN](https://thehackernews.com/2026/04/fortinet-patches-actively-exploited-cve.html), [Help Net Security](https://www.helpnetsecurity.com/2026/04/04/forticlient-ems-zero-day-cve-2026-35616/), [BleepingComputer](https://www.bleepingcomputer.com/news/security/new-fortinet-forticlient-ems-flaw-cve-2026-35616-exploited-in-attacks/)

### 2. Cisco IMC Auth Bypass (CVE-2026-20093, CVSS 9.8)

Unauthenticated remote attacker can bypass authentication and alter any user password (including Admin) on Cisco Integrated Management Controller via crafted HTTP request. Affects UCS C-Series, APIC, Cyber Vision, Secure Firewall Management Center appliances, and more. Patches released.

- **Why it matters**: CVSS 9.8, unauthenticated, wide attack surface across Cisco hardware. IMC interfaces are often accidentally internet-exposed. Not yet exploited in the wild — but Cisco vulns get weaponized fast.
- **Den relevance**: LOW direct, HIGH pattern — infrastructure management interfaces continue to be soft targets.
- Sources: [THN](https://thehackernews.com/2026/04/cisco-patches-98-cvss-imc-and-ssm-flaws.html), [SOCRadar](https://socradar.io/blog/cve-2026-20093-cisco-imc-flaw/), [Help Net Security](https://www.helpnetsecurity.com/2026/04/03/cisco-imc-vulnerability-cve-2026-20093/)

### 3. Google Formally Attributes Axios npm Attack to DPRK (UNC1069/Sapphire Sleet)

Google GTIG published formal attribution: the March 31 Axios npm supply chain compromise was UNC1069, a financially motivated DPRK-nexus actor active since 2018. Microsoft independently attributed to Sapphire Sleet (BlueNoroff offshoot). Attack vector: social engineering of maintainer credentials. Malicious versions live ~3 hours, estimated 600K installs. Payload: WAVESHAPER.V2 RAT.

- **Why it matters**: Dual-vendor attribution (Google + Microsoft) confirms DPRK is now systematically targeting npm infrastructure. This is the highest-impact supply chain attack since SolarWinds in terms of potential install base.
- **Den relevance**: HIGH — we use npm daily. Axios is a direct dependency. Confirm we pulled clean versions.
- Sources: [Google Cloud Blog](https://cloud.google.com/blog/topics/threat-intelligence/north-korea-threat-actor-targets-axios-npm-package), [THN](https://thehackernews.com/2026/04/google-attributes-axios-npm-supply.html), [Tenable](https://www.tenable.com/blog/faq-about-the-axios-npm-supply-chain-attack-by-north-korea-nexus-threat-actor-unc1069)

---

## HIGH — Track Closely

### 4. US Government Blacklists Anthropic as National Security Risk

Pentagon designated Anthropic a "Supply-Chain Risk to National Security" after Anthropic refused to lift safeguards on Claude for military surveillance and autonomous weapons use. Federal judge blocked enforcement, citing "First Amendment retaliation." Pentagon appealing. UK courting Anthropic with expansion proposals.

- **Why it matters**: The AI safety vs. national security tension just became a legal precedent case. This reshapes the landscape for every AI company with government contracts. Anthropic's safety stance is being punished — signals what happens to companies that draw red lines.
- **Den relevance**: HIGH — we run on Claude. If Anthropic gets squeezed, our toolchain has geopolitical risk.
- Sources: [Axios](https://www.axios.com/2026/02/27/anthropic-pentagon-supply-chain-risk-claude), [CNBC](https://www.cnbc.com/2026/03/26/anthropic-pentagon-dod-claude-court-ruling.html), [Let's Data Science](https://letsdatascience.com/news/us-blacklists-anthropic-as-security-risk-5e0f08ff)

### 5. Claude Code Source Leak + Typosquatting Follow-Up

Anthropic accidentally published full Claude Code source (59.8 MB sourcemap, 1,900 files, 512K LOC) in npm package @anthropic-ai/claude-code v2.1.88 on March 31. Cause: Bun bundler generates sourcemaps by default. Attackers then typosquatted internal npm package names targeting devs who tried to compile the leaked source.

- **Why it matters**: The leak itself was embarrassing but not catastrophic. The real danger was the follow-on typosquatting attack — opportunistic attackers weaponized Anthropic's mistake within hours.
- **Den relevance**: HIGH — we run Claude Code. Verify we didn't pull v2.1.88. Also: Claude Code just pushed policy changes on April 4 blocking OpenClaw subscription access.
- Sources: [THN](https://thehackernews.com/2026/04/claude-code-tleaked-via-npm-packaging.html), [VentureBeat](https://venturebeat.com/technology/claude-codes-source-code-appears-to-have-leaked-heres-what-we-know), [Zscaler ThreatLabz](https://www.zscaler.com/blogs/security-research/anthropic-claude-code-leak)

### 6. Drift Protocol — $285M DeFi Heist Attributed to DPRK (UNC4736)

Solana-based DEX Drift drained of $285M on April 1 via fake collateral and pre-signed admin transactions. Six-month social engineering operation: DPRK actors built real relationships, created Telegram groups, discussed trading strategies. Contagion spread to 20+ protocols. Elliptic tracks $300M+ stolen by DPRK in Q1 2026 alone.

- **Why it matters**: DPRK is now running dual-front operations — npm supply chain (UNC1069) AND DeFi social engineering (UNC4736). Different teams, different TTPs, same funding pipeline to weapons programs.
- **Den relevance**: Pattern signal. Social engineering at this depth is terrifying — these weren't phishing emails, they were months of genuine-seeming professional relationships.
- Sources: [THN](https://thehackernews.com/2026/04/285-million-drift-hack-traced-to-six.html), [Bitcoin News](https://news.bitcoin.com/drift-protocol-hack-2026-what-happened-who-lost-money-and-whats-next/)

### 7. TeamPCP Update — Sportradar Deadline Approaching (~April 10-11)

No new TeamPCP package compromises (pause at ~10 days). But the CipherForce ransomware listing for Sportradar AG ($4.98B Swiss sports tech) has a 14-15 day publication deadline approaching around April 10-11. Client table already exposed: ESPN, Nike, NBA Asia, IMG Arena among 161 orgs. Dual ransomware tracks (CipherForce + Vect) still active. 1,000+ SaaS environments compromised per Mandiant.

- **Why it matters**: The publication deadline is the next pressure point. If Sportradar doesn't pay, client data for 161 major orgs goes public. The credential trove from 1,000+ environments means delayed detonations are still likely.
- **Den relevance**: MEDIUM — we don't use Sportradar, but the supply chain fallout model applies to any SaaS we depend on.
- Sources: [SANS ISC Update 006](https://isc.sans.edu/diary/32864), [Unit42](https://unit42.paloaltonetworks.com/teampcp-supply-chain-attacks/)

---

## MEDIUM — Monitor

### 8. Grafana CVE-2026-27876 — Patches Shipped, Cloud Pre-Patched

Grafana released patches for the critical RCE via SQL Expressions (CVSS 9.1). Fixed versions: 11.6.14, 12.1.10, 12.2.8, 12.3.6, 12.4.2. Cloud instances (Grafana Cloud, AWS Managed, Azure Managed) were patched before public disclosure. Requires Viewer+ permissions AND sqlExpressions feature toggle enabled.

- **Why it matters**: Good news — patches landed and cloud was pre-patched. The feature toggle requirement limits blast radius, but any self-hosted instance with SQL expressions enabled needs immediate update.
- **Den relevance**: Check if Rax runs Grafana with SQL expressions enabled. If so, patch.
- Sources: [Grafana Labs](https://grafana.com/blog/grafana-security-release-critical-and-high-severity-security-fixes-for-cve-2026-27876-and-cve-2026-27880/), [NVD](https://nvd.nist.gov/vuln/detail/CVE-2026-27876)

### 9. CrewAI — Four CVEs Chaining Prompt Injection to RCE

Four CVEs in CrewAI allow attackers to chain prompt injection into RCE, SSRF, and file read. AI agent frameworks continue to ship with inadequate input validation. Part of the broader pattern where 43 different agent framework components have embedded vulnerabilities per Barracuda's report.

- **Why it matters**: Agent frameworks are the new attack surface. If you run CrewAI in production, you're running RCE-as-a-service.
- **Den relevance**: MEDIUM — we don't use CrewAI, but this validates the MCP/agent security pattern we've been tracking.
- Sources: [Adversa AI](https://adversa.ai/blog/top-agentic-ai-security-resources-april-2026/), [Dark Reading](https://www.darkreading.com/threat-intelligence/2026-agentic-ai-attack-surface-poster-child)

### 10. Claude Code Updates — Policy Shift + Security Hardening

Claude Code shipped: forceRemoteSettingsRefresh (fail-closed startup policy), Linux sandbox seccomp hardening, 60% faster Write tool for large files, MCP tool result persistence up to 500K chars. Also: /powerup interactive lessons. **Policy change**: as of April 4, subscription limits no longer apply to third-party harnesses (OpenClaw). Pay-as-you-go required for external tools.

- **Why it matters**: The OpenClaw policy change is significant — Anthropic is tightening control over how Claude is accessed. The seccomp hardening and fail-closed settings show security posture improving post-leak.
- **Den relevance**: HIGH — we should verify our Claude Code version and understand the OpenClaw implications.
- Sources: [Releasebot](https://releasebot.io/updates/anthropic/claude-code), [GitHub Releases](https://github.com/anthropics/claude-code/releases)

---

## SIGNAL — Trend Indicators

### 11. 97% of Enterprises Expect Material AI Agent Security Incident This Year

Arkose Labs survey of 300 enterprise leaders: 97% expect a material AI-agent-driven security/fraud incident within 12 months. 87% say AI agents with legitimate credentials are a greater insider threat than human employees. Only 6% of security budgets allocated to this risk.

- **Why it matters**: The awareness-preparation gap is enormous. Everyone sees the train coming, nobody's moving off the tracks. Budget allocation tells the real story — 6% is negligence.
- Sources: [Security Boulevard](https://securityboulevard.com/2026/04/97-of-enterprises-expect-a-major-ai-agent-security-incident-within-the-year/)

### 12. Cybersecurity Q1 2026 Funding: $3.8B (+33% YoY), AI Security Takes 46%

Q1 2026: $3.8B across 211 rounds (+33% YoY). AI Security captured 46% of all capital. Four new unicorns (Tenex.AI, Aikido, Torq, XBOW). CrowdStrike acquired SGNL ($740M) and Seraphic ($420M) for identity security — identity is the control plane for AI + cloud.

- **Why it matters**: Money follows fear. AI security getting 46% of cybersec funding confirms it's the dominant theme. CrowdStrike's $1.1B identity play signals where the industry thinks the real battle is.
- Sources: [GlobeNewsWire](https://www.globenewswire.com/news-release/2026/04/02/3267580/0/en/Cybersecurity-Financing-Surges-33-Year-Over-Year-to-3-8B-in-Q1-2026.html), [CSO Online](https://www.csoonline.com/article/4114957/crowdstrike-to-acquire-sgnl-for-740m-expanding-real-time-identity-security.html)

### 13. CISA KEV Deadlines — Active Tracker

| CVE | Target | Deadline | Status |
|-----|--------|----------|--------|
| CVE-2026-33017 | Langflow RCE | April 8 | 2 days left |
| CVE-2026-3055 | Citrix NetScaler | April 2 | PAST DUE |
| Chrome CVE (TBD) | Chrome | April 15 | 9 days |
| TrueConf CVE-2026-3502 | TrueConf Client | April 16 | 10 days |

---

## Patterns — Connecting the Dots

### Pattern 1: DPRK Dual-Front Offensive Is Now Formally Confirmed
Google (UNC1069) and Microsoft (Sapphire Sleet) independently confirmed the Axios npm attack. Separately, the Drift $285M heist is attributed to UNC4736. Two different DPRK teams, two different attack surfaces (supply chain + DeFi), same funding pipeline. This is industrial-scale cyber theft with professional social engineering that takes months to execute.

### Pattern 2: The Anthropic Squeeze
Three events converging: (1) US government blacklists Anthropic for refusing military use, (2) Claude Code source accidentally leaked via npm, (3) policy changes tightening third-party access. Anthropic is simultaneously under political pressure, dealing with operational security failures, and hardening its platform. If you depend on Claude (we do), this is a risk surface to watch.

### Pattern 3: Agent Frameworks Are the New Perimeter
CrewAI (4 CVEs), Nginx UI MCP (CVE-2026-33032, unpatched), Azure MCP Server (CVE-2026-32211, unpatched), VS Code mcp.json injection (CVE-2026-21518), OpenClaw privilege escalation (CVSS 9.9). The MCP/agent ecosystem is shipping vulnerabilities faster than patches arrive. 30+ MCP CVEs in 60 days. OWASP now has an MCP Top 10. This is the 2016 IoT moment for AI agents.

### Pattern 4: Identity Is the Control Plane
CrowdStrike spent $1.1B on identity security (SGNL + Seraphic). 97% of enterprises expect AI agent security incidents. The Arkose Labs finding that "AI agents with legitimate credentials are a greater insider threat than humans" connects directly to the MCP auth crisis — most MCP servers ship with no auth at all. The industry is slowly realizing that identity for non-human actors is the unsolved problem.

### Pattern 5: Supply Chain Pause Doesn't Mean Safety
TeamPCP hasn't published new package compromises in 10 days, but 1,000+ SaaS environments are compromised, Sportradar's deadline approaches, and the credential trove enables delayed attacks for months. The pause is operational, not strategic — they're monetizing what they already have.

---

**Next scan**: April 7, 2026
**Watch items**: Sportradar deadline (~April 10-11), Langflow KEV deadline (April 8), FortiClient EMS exploitation spread, Anthropic legal proceedings
