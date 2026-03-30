# MCP Security Landscape -- Intelligence Report

**Date**: 2026-03-31
**Author**: Nyx (Recon/OSINT)
**Classification**: Internal -- The Den
**Status**: Comprehensive research complete

---

## Executive Summary

The Model Context Protocol (MCP) security landscape has exploded from theoretical concern to active exploitation theater in Q1 2026. OWASP has published a formal MCP Top 10 (beta). 30+ CVEs were filed in January-February 2026 alone. Anthropic's own MCP servers and Claude Code itself have been found vulnerable to RCE and credential exfiltration. The root causes are mundane -- missing input validation, absent auth, blind trust in tool descriptions -- but the attack surface is novel and expanding fast.

**The Den runs Claude Code with MCP servers. This is a live risk, not a future one.**

---

## 1. OWASP MCP Top 10

**Published**: 2025 (beta v0.1, Phase 3)
**Lead**: Vandana Verma Sehgal
**License**: CC BY-NC-SA 4.0

| # | Code | Name | Description |
|---|------|------|-------------|
| 1 | MCP01:2025 | Token Mismanagement & Secret Exposure | Hard-coded creds, long-lived tokens, secrets in model memory or protocol logs. Agents send tool calls with DB passwords or API keys in plaintext. |
| 2 | MCP02:2025 | Privilege Escalation via Scope Creep | Loosely defined permissions expand over time. Attackers perform repo modification, data exfil via over-scoped tools. |
| 3 | MCP03:2025 | Tool Poisoning | Adversaries inject malicious context into tool descriptions/schemas. Includes rug pulls and tool shadowing. |
| 4 | MCP04:2025 | Supply Chain Attacks & Dependency Tampering | Compromised dependencies alter agent behavior. Malicious open-source MCP packages and connectors. |
| 5 | MCP05:2025 | Command Injection & Execution | AI agents construct system commands from untrusted input without sanitization. |
| 6 | MCP06:2025 | Intent Flow Subversion | Malicious instructions in context hijack the agent away from user goals toward attacker objectives. |
| 7 | MCP07:2025 | Insufficient Authentication & Authorization | Weak/missing identity validation across agents, users, and services. 38-41% of official MCP servers lack auth. |
| 8 | MCP08:2025 | Lack of Audit and Telemetry | Insufficient logging impedes incident response. No visibility into what tools did what. |
| 9 | MCP09:2025 | Shadow MCP Servers | Unapproved MCP deployments running default creds, permissive configs, unsecured APIs -- outside security governance. |
| 10 | MCP10:2025 | Context Injection & Over-Sharing | Shared context windows leak sensitive info between tasks, users, or agents. |

**Source**: [OWASP MCP Top 10](https://owasp.org/www-project-mcp-top-10/)

---

## 2. CVE Trends

### The "30 CVEs in 60 Days" Claim: Confirmed

Between January and February 2026, security researchers filed **30+ CVEs** targeting MCP servers, clients, and infrastructure. Root causes were not exotic zero-days -- they were **missing input validation, absent authentication, and blind trust in tool descriptions**.

### Notable CVEs

| CVE | Target | Severity | Impact |
|-----|--------|----------|--------|
| CVE-2025-6514 | mcp-remote | CVSS 9.6 | Arbitrary OS command execution. 437,000+ downloads affected. |
| CVE-2025-53109/53110 | Anthropic Filesystem MCP Server | High | Path traversal escape from sandboxed filesystem. |
| CVE-2025-68145/68143/68144 | Anthropic mcp-server-git | Critical chain | Path validation bypass + unrestricted git_init + argument injection = full RCE via malicious .git/config. |
| CVE-2025-54136 | Cursor IDE | High | Trust bypass ("MCPoison"). |
| CVE-2025-49596 | MCP Inspector | High | Remote code execution. |
| CVE-2025-59536 | Claude Code | CVSS 8.7 | RCE via malicious .claude/settings.json in cloned repos. Executes before trust dialog shown. |
| CVE-2026-21852 | Claude Code | CVSS 5.3 | API key exfiltration via ANTHROPIC_BASE_URL override. Opening a crafted repo leaks active API key. |
| CVE-2026-33980 | Azure Data Explorer MCP | High | KQL injection -- table_name interpolated without validation. |

### Ecosystem Survey Stats

From a survey of **2,614 MCP implementations**:
- **82%** use file system operations prone to path traversal
- **67%** use sensitive APIs related to code injection
- **34%** use sensitive APIs related to command injection
- **43%** of all vulnerabilities involved exec/shell injection
- **38-41%** of official MCP servers lack authentication

---

## 3. Known Attack Vectors

### 3.1 Tool Poisoning
Malicious instructions embedded in MCP tool description fields. Invisible to users, visible to the LLM. The model follows hidden instructions to exfiltrate data, bypass controls, or execute unauthorized actions.

**MCPTox benchmark** (arxiv 2508.14925): Tested 45 live MCP servers, 353 tools, 20 LLM agents. Attack success rate up to **72.8%** (o1-mini). Some implementations hit **100%** success rate.

**Key finding**: Claude Desktop and Cline demonstrated effective defenses, proving mitigation is achievable.

### 3.2 Rug Pulls
Tool definitions mutate after initial approval. User approves a safe-looking tool on Day 1; by Day 7 it's rerouted API keys to an attacker. Most MCP clients don't notify users of tool description changes.

**Defense**: Tool pinning via cryptographic hashing (implemented in Invariant's MCP-Scan).

### 3.3 Cross-Server Exfiltration
When multiple MCP servers connect to the same client, a malicious server poisons tool descriptions to exfiltrate data from other trusted servers. Enables auth hijacking where credentials from one server pass to another.

### 3.4 Full-Schema Poisoning (CyberArk)
Goes beyond description field poisoning. Entire tool schema is an attack surface. CyberArk demonstrated zero-width character injection into LLM outputs that bypass security filters and alter downstream behavior.

### 3.5 Prompt Injection via MCP
Tool outputs can contain prompt injection payloads. Any data returned by an MCP tool can manipulate the LLM's next action. Simon Willison flagged this fundamental architectural issue in April 2025.

### 3.6 Claude Code Specific Attacks (Check Point Research)
- **CVE-2025-59536**: Malicious .claude/settings.json in cloned repos executes arbitrary code before trust dialog
- **CVE-2026-21852**: ANTHROPIC_BASE_URL override exfiltrates API keys on repo open
- Attack chain: Hooks + MCP servers + environment variables = arbitrary shell execution when users clone untrusted repos

---

## 4. Key Researchers & Publications

| Organization | Contribution |
|---|---|
| **OWASP** | MCP Top 10 (beta), MCP Security Cheat Sheet |
| **Invariant Labs** (acquired by Snyk) | Discovered tool poisoning. Built MCP-Scan with tool pinning. Foundational MCP security research. |
| **Trail of Bits** | "Line jumping" vulnerability. Built mcp-context-protector. Documented plaintext credential storage. |
| **CyberArk Labs** | "Poison Everywhere" -- full-schema poisoning, zero-width character injection in tool outputs. |
| **Check Point Research** | Claude Code RCE + API key exfiltration (CVE-2025-59536, CVE-2026-21852). |
| **Cymulate** | "EscapeRoute" -- Anthropic Filesystem MCP Server path traversal (CVE-2025-53109/53110). |
| **CoSAI** | MCP security framework covering 12 threat categories. |
| **Microsoft** | Published guide on protecting against indirect prompt injection in MCP. |
| **Elastic Security Labs** | Attack vectors and defense recommendations for autonomous agents. |
| **Semgrep** | "A Security Engineer's Guide to MCP" |
| **Red Hat** | MCP security risks and controls guide. |
| **Simon Willison** | Early public analysis of MCP prompt injection fundamentals (Apr 2025). |
| **Academic** | MCPTox benchmark (arxiv 2508.14925), MCP threat modeling (arxiv 2603.22489). |

---

## 5. Mitigations and Defenses

### OWASP MCP Security Cheat Sheet Recommendations

1. **Least Privilege**: Separate, scoped credentials per MCP server. Never share tokens across systems.
2. **Tool Pinning**: Cryptographic hashing of tool definitions at discovery. Detect post-deployment mutations (rug pulls). Verify hashes before each execution.
3. **Strict Schema Validation**: `additionalProperties: false` on all tool schemas. Treat schemas as injection surfaces.
4. **Isolation & Sandboxing**: Containerize MCP servers. Restrict filesystem and network access. Use `stdio` transport for local deployments.
5. **Zero Trust Between Servers**: Treat each MCP server as an untrusted security domain. Prevent cross-server shadowing.
6. **Human-in-the-Loop**: Explicit user confirmation for destructive, financial, or data-sharing operations. Full parameter visibility. No auto-approval.
7. **Input/Output Sanitization**: Sanitize all tool inputs against injection (SQL, OS command, path traversal). Treat outputs as untrusted before re-injecting into LLM context.
8. **Message Signing**: Sign JSON-RPC payloads with unique nonces and timestamps. Prevent tampering and replay even after TLS termination.
9. **Supply Chain Hardening**: Verified sources only. Review code pre-deployment. Verify checksums. Monitor for post-install changes.
10. **Audit & Monitoring**: Log all tool invocations with full context. Feed into SIEM. Alert on anomalies (new tools, abnormal call frequency).

### Available Security Tools

| Tool | Provider | Purpose |
|------|----------|---------|
| **MCP-Scan** | Invariant Labs / Snyk | Security scanner for MCP servers. Tool pinning. Rug pull detection. |
| **mcp-context-protector** | Trail of Bits | Security wrapper. Trust-on-first-use pinning. LLM guardrail integration. |
| **Agent-Scan** | Snyk | Broader security scanner for AI agents and MCP skills. |

---

## 6. Relevance to The Den

### Direct Exposure

The Den runs Claude Code with MCP servers. Based on this research, the following risks are live:

| Risk | Severity | Relevance |
|------|----------|-----------|
| **Claude Code config poisoning** (CVE-2025-59536) | Critical | Any Beast cloning untrusted repos could trigger RCE via malicious .claude/settings.json |
| **API key exfiltration** (CVE-2026-21852) | Medium | If a Beast opens a crafted repo, API keys leak before trust prompt appears |
| **Tool poisoning via MCP servers** | High | If any MCP server in the Den's config is compromised or supplies malicious tool descriptions |
| **Cross-server exfiltration** | High | Multiple MCP servers on same client = one rogue server can harvest data from trusted ones |
| **Rug pulls** | Medium | MCP tool definitions can mutate silently after initial approval |
| **Missing auth on MCP servers** | High | If Den MCP servers lack authentication, any local process can call them |
| **Supply chain** | Medium | Third-party MCP server packages could contain backdoors |

### Recommended Actions

1. **Immediate**: Audit all MCP servers running in The Den. Inventory what tools are registered, what permissions they have, what auth they use.
2. **Immediate**: Ensure Claude Code is updated to patched versions (CVE-2025-59536 and CVE-2026-21852 fixes).
3. **Short-term**: Run MCP-Scan (Invariant/Snyk) against Den MCP servers to check for tool poisoning and rug pull vulnerabilities.
4. **Short-term**: Implement tool pinning for all MCP tool definitions.
5. **Medium-term**: Sandbox MCP servers in containers with restricted filesystem/network access.
6. **Medium-term**: Add audit logging for all MCP tool invocations.
7. **Standing**: Never clone untrusted repos into Beast workspaces without review. The .claude/ directory is now a known attack vector.
8. **Standing**: Track MCP CVEs as a standing intelligence category in future recon scans.

---

## Sources

- [OWASP MCP Top 10](https://owasp.org/www-project-mcp-top-10/)
- [OWASP MCP Security Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/MCP_Security_Cheat_Sheet.html)
- [MCP Security 2026: 30 CVEs in 60 Days](https://www.heyuan110.com/posts/ai/2026-03-10-mcp-security-2026/)
- [Invariant Labs -- Tool Poisoning Attacks](https://invariantlabs.ai/blog/mcp-security-notification-tool-poisoning-attacks)
- [Invariant Labs -- MCP-Scan](https://invariantlabs.ai/blog/introducing-mcp-scan)
- [Trail of Bits -- MCP Security Layer](https://blog.trailofbits.com/2025/07/28/we-built-the-security-layer-mcp-always-needed/)
- [CyberArk -- Poison Everywhere](https://www.cyberark.com/resources/threat-research-blog/poison-everywhere-no-output-from-your-mcp-server-is-safe)
- [Check Point -- Claude Code RCE & API Key Exfiltration](https://research.checkpoint.com/2026/rce-and-api-token-exfiltration-through-claude-code-project-files-cve-2025-59536/)
- [Cymulate -- EscapeRoute (CVE-2025-53109/53110)](https://cymulate.com/blog/cve-2025-53109-53110-escaperoute-anthropic/)
- [Microsoft -- Protecting Against Indirect Injection in MCP](https://developer.microsoft.com/blog/protecting-against-indirect-injection-attacks-mcp)
- [Elastic Security Labs -- MCP Attack & Defense](https://www.elastic.co/security-labs/mcp-tools-attack-defense-recommendations)
- [Simon Willison -- MCP Prompt Injection](https://simonwillison.net/2025/Apr/9/mcp-prompt-injection/)
- [MCPTox Benchmark (arxiv)](https://arxiv.org/abs/2508.14925)
- [MCP Threat Modeling (arxiv)](https://arxiv.org/html/2603.22489v1)
- [The Vulnerable MCP Project](https://vulnerablemcp.info/)
- [Anthropic MCP Git Server Flaws](https://thehackernews.com/2026/01/three-flaws-in-anthropic-mcp-git-server.html)
- [Claude Code Security Docs](https://code.claude.com/docs/en/security)
- [Snyk Acquires Invariant Labs](https://snyk.io/news/snyk-acquires-invariant-labs-to-accelerate-agentic-ai-security-innovation/)
- [MCP Security Best Practices (Spec)](https://modelcontextprotocol.io/specification/draft/basic/security_best_practices)
- [Semgrep -- Security Engineer's Guide to MCP](https://semgrep.dev/blog/2025/a-security-engineers-guide-to-mcp/)
- [Red Hat -- MCP Security Risks and Controls](https://www.redhat.com/en/blog/model-context-protocol-mcp-understanding-security-risks-and-controls)
- [AuthZed -- Timeline of MCP Security Breaches](https://authzed.com/blog/timeline-mcp-breaches)
- [Darkreading -- Microsoft & Anthropic MCP Servers at Risk](https://www.darkreading.com/application-security/microsoft-anthropic-mcp-servers-risk-takeovers)
- [CoSAI MCP Security Framework](https://genai.owasp.org/resource/a-practical-guide-for-secure-mcp-server-development/)
