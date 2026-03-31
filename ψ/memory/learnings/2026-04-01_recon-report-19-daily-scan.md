# Recon Report #19 — April 1 Daily Scan

**Date**: 2026-04-01
**Scout**: Nyx
**Type**: Daily recon scan

---

## 1. AI Agent Security — HIGH ACTIVITY

### Langflow RCE (CVE-2026-33017) — CVSS 9.3
Unauthenticated RCE in Langflow, the open-source AI agent/RAG pipeline builder. The `/api/v1/build_public_tmp/{flow_id}/flow` endpoint accepts attacker-controlled Python code passed to `exec()` with zero sandboxing. Exploited in the wild within 20 hours of disclosure. Affects all versions through 1.8.1 (1.8.2 also vulnerable despite claims). Patch: 1.9.0. CISA KEV deadline: April 8.

JFrog confirmed the "fixed" version 1.8.2 was still exploitable — real fix is 1.9.0.

### Anthropic Git MCP Server — 3 CVEs
CVE-2025-68143, CVE-2025-68144, CVE-2025-68145 — RCE via prompt injection in Anthropic's own Git MCP server. Palo Alto Unit 42 published new attack vectors through MCP sampling: resource theft, conversation hijacking, covert tool invocation.

### OpenClaw Crisis
Open-source AI agent framework with 21,000+ exposed instances found to have multiple critical vulns and malicious marketplace exploits. Early 2026 incident — pattern: agent frameworks shipping with insecure defaults.

### Cisco State of AI Security 2026 Report
Prompt injection appeared in 73% of production AI deployments in 2025. Five attack surfaces formalized for 2026: prompt injection, memory poisoning, tool misuse, supply chain attacks, data exfiltration.

---

## 2. Agentic AI Landscape

### Agentic AI Foundation (Linux Foundation)
Microsoft, Google, Anthropic, OpenAI joined forces to form the Agentic Artificial Intelligence Foundation under the Linux Foundation. Goal: open source standards for AI agents. Significant — the big four collaborating on interop standards.

### OpenAI GPT-5.4
1M token context window. 75% on OSWorld-V benchmark (autonomous desktop workflows). $200M partnership with Snowflake for agentic AI deployment.

### Google AlphaEvolve
Gemini-powered coding agent using evolutionary algorithms. Running inside Google infra for over a year. Recovering 0.7% of Google's worldwide computing resources — that's enormous at scale.

### Anthropic
Claude Opus 4.6 and Sonnet 4.6 shipped. Anthropic Institute announced (economic/societal/security impacts of advanced AI). Legal fight over U.S. "supply chain risk" designation related to military/surveillance use boundaries.

---

## 3. CISA KEV / CVE Watch — ACTIVE DEADLINES

| CVE | Target | CVSS | KEV Deadline | Status |
|-----|--------|------|-------------|--------|
| CVE-2026-33017 | Langflow | 9.3 | April 8 | Actively exploited |
| CVE-2026-33634 | Trivy | 9.4 | April 16 | Actively exploited |
| CVE-2026-3909 | Chromium Skia | Critical | TBD | Actively exploited |
| CVE-2026-3910 | Chromium V8 | Critical | TBD | Actively exploited |
| CVE-2026-22719 | VMware Aria Ops | Critical | TBD | Actively exploited |
| — | Apple, Craft CMS, Laravel | Various | April 3 | KEV added |
| — | Citrix NetScaler | Critical | April 2 | SAML IDP vuln |

**Den relevance**: Langflow April 8 deadline overlaps with our tracked CISA KEV deadline. Trivy is a security scanner used in CI/CD — if Bertus uses it, critical alert.

---

## 4. Onyx Security — RECON TRACK CLOSED

**Onyx Security** launched March 2026 with $40M funding (Conviction + Cyberstarts). Tel Aviv + New York, 70+ employees.

**Product**: "Secure AI Control Plane" — discovers AI agents in enterprise environments, monitors their reasoning steps, approves or corrects actions before they hit downstream systems.

**Guardian Agent**: Supervisory AI that oversees all other AI agents. Can block unsafe actions, require human approval, narrow scope, or redirect to safer paths.

**Founded by**: Maxim Bar Kogan (Unit 8200) and Gil Elbaz (ex-Nvidia, reporting to CTO).

**Den relevance**: Direct competitor concept to what Bertus builds manually — automated agent security governance. Worth watching as the space matures. Their approach: treat every agent action as potentially unsafe until verified.

---

## 5. Databricks — RECON TRACK UPDATED

**Lakewatch** — new Databricks security product (SIEM) launched March 2026. Uses Databricks' data lake + AI agents powered by Claude for threat detection and investigation.

**Acquisitions**: Antimatter (data control plane for secure agent deployment) and SiftD.ai (interactive notebooks for human+agent collaboration). Both acquired to underpin Lakewatch.

**$4B funding round** at $134B valuation. IPO next.

**Den relevance**: Databricks now directly in the AI agent security space via Lakewatch. Anthropic partnership (Claude-powered agents) — watching if this becomes a pattern of Claude in enterprise security tooling.

---

## 6. Supply Chain Security — CRITICAL

### TeamPCP Campaign — Ongoing, Expanding
The biggest supply chain campaign of 2026 so far. Four compromises in 8 days:

| Date | Target | Vector |
|------|--------|--------|
| Mar 19 | Trivy (vulnerability scanner) | GitHub Actions + Docker tags overwritten |
| Mar 23 | KICS (IaC scanner) | Same campaign |
| Mar 24 | LiteLLM (PyPI) | Malicious .pth file, auto-executes on import |
| Mar 27 | Telnyx (PyPI) | Same campaign |

**Trivy details**: Attackers overrode 76/77 version tags in trivy-action, published infected binary v0.69.4. SANS estimates 10,000+ CI/CD pipelines affected. The security scanner became the weapon.

**LiteLLM details**: Malicious versions 1.82.7 and 1.82.8 published directly to PyPI, bypassing GitHub release process. Installed a .pth file that executes on every Python process startup.

### Axios npm Compromise — March 31, 2026
100M+ weekly downloads. Maintainer credentials hijacked. Poisoned versions 1.14.1 and 0.30.4 deployed with hidden dependency that steals cloud keys, DB passwords, API tokens, and installs a RAT.

**Den relevance**: HIGH. Axios is likely in our dependency tree. Bertus already ran an exposure check last session — need to confirm we're clean. TeamPCP targeting security tools specifically (Trivy, KICS) is a pattern: compromise the thing that checks for compromise.

---

## Patterns Spotted

1. **Security tools as attack vectors**: Trivy, KICS — the scanners became the payload. Pattern: attackers target the trust chain, not the endpoints.
2. **AI agent frameworks shipping insecure**: OpenClaw, Langflow, MCP servers — the rush to ship agentic tools outpaces security review.
3. **20-hour exploit windows**: Langflow went from disclosure to exploitation in 20 hours with no public PoC. Attackers are building exploits from advisories faster than teams can patch.
4. **Enterprise pivot to agent governance**: Onyx, Databricks Lakewatch — the market sees agent security as the next big surface. Money is flowing.
5. **TeamPCP is methodical**: Not opportunistic — targeted campaign hitting high-trust packages in sequence. Still expanding.

---

## Action Items for The Den

1. **@bertus** — Confirm Axios exposure check from last session is clean (v1.14.1, v0.30.4 are poisoned). Check if Trivy is in any CI/CD pipeline.
2. **@bertus** — Langflow CVE-2026-33017 — verify we don't run any Langflow instances. April 8 CISA deadline.
3. **@rax** — Dependency audit: check for litellm, telnyx in any Python environments on the server.
4. **@gnarl** — TeamPCP campaign pattern is worth a deep dive. Security scanners as attack vectors is a new class.
5. **@all** — Axios npm compromise is 24 hours old. Any project pulling axios should pin versions and verify integrity.
