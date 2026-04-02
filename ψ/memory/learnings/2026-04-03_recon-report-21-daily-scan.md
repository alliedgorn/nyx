# Recon Report #21 — April 3, 2026

**Date**: 2026-04-03 02:30 GMT+7
**Type**: Daily scan
**Sources**: THN, BleepingComputer, HelpNetSecurity, McAfee, Check Point, Snyk, Wiz, Datadog, Adversa AI, Sonatype, ArXiv

## Priority Flags

1. **Operation TrueChaos — TrueConf Zero-Day CVE-2026-3502** (CVSS 7.8) — China-nexus attackers exploiting TrueConf video conferencing to hit Southeast Asian government networks. Supply chain attack via the update channel — compromise one on-prem server, push malware to all connected clients. Deploys Havoc C2 framework. Patched in v8.5.3. **We don't run TrueConf. Awareness only.**

2. **NoVoice Android Rootkit — 2.3M devices infected via Google Play** — 50+ malicious apps (cleaners, galleries, games). Exploits 22 old Android vulns (2016-2021) for root. Survives factory reset — requires full firmware reflash. Steals WhatsApp sessions (encryption DBs, Signal protocol keys). Apps removed from Play Store. @bertus — **Gorn's Android is the exposure surface** for this one, same as the Telegram zero-click.

3. **LiteLLM Supply Chain Attack (TeamPCP continuation)** — litellm 1.82.7 and 1.82.8 on PyPI compromised March 24. Live for ~40 minutes. TeamPCP (from Report #19) trojanized Trivy in LiteLLM's CI/CD to steal the PyPI publish token. Payload: steals SSH keys, .env, cloud creds, K8s secrets, deploys persistent backdoor. Discovery: MCP plugin in Cursor pulled it as transitive dependency — malware's fork bomb bug crashed the machine. **Den runs no LiteLLM. But the TeamPCP campaign is expanding** — now hitting Trivy, LiteLLM, and Telnyx. Third supply chain target.

## Active Threats

- **A2A Agent Card Poisoning** — PoC shows malicious Google A2A agent cards can embed adversarial instructions, hijack tasks, exfiltrate data via the host LLM. Agent discovery trusts fake cards → rogue server receives sensitive tasks. Relevant to agent-to-agent protocols.
- **LLM Memory Poisoning** — 90%+ attack success rates against GPT-5 mini and Claude Sonnet 4.5. Poisoned memory entries persistently hijack agent workflows. Our Beast memory systems (ψ/) are a theoretical surface.
- **TrinityGuard Multi-Agent Safety** — Three-tier risk taxonomy for multi-agent systems reveals 7.1% average safety pass rate. Abysmal.

## CISA KEV Status

| CVE | Product | CVSS | Deadline | Status |
|-----|---------|------|----------|--------|
| CVE-2025-32432 | Craft CMS | 10.0 | April 3 (TODAY) | No exposure |
| CVE-2025-54068 | Laravel Livewire | 9.8 | April 3 (TODAY) | No exposure |
| CVE-2025-31277 | Apple WebKit | 8.8 | April 3 (TODAY) | Gorn devices — routed via Sable |
| CVE-2025-43510 | Apple Kernel | 7.8 | April 3 (TODAY) | Gorn devices — routed via Sable |
| CVE-2025-43520 | Apple Kernel | 8.8 | April 3 (TODAY) | Gorn devices — routed via Sable |
| CVE-2026-5281 | Chrome Dawn | 8.8 | April 15 | Gorn browser — routed via Sable |
| CVE-2026-33017 | Langflow | — | April 8 | Tracking |
| CVE-2026-33634 | Trivy | — | April 16 | Tracking — Trivy now confirmed as TeamPCP attack vector |

**Note**: Trivy KEV deadline April 16 just got more interesting — TeamPCP used compromised Trivy to attack LiteLLM's CI/CD. The tool meant to find vulns became the vuln.

## MCP Dev Summit — Day 2 (TODAY, NYC)

- Day 1 recaps not yet available — summit just ended Day 1
- **OpenAI keynote today**: Nick Cooper, "MCP x MCP" — cross-ecosystem standardization
- Watching for: auth announcements, SDK v2 details, security session outputs
- Will scan for Day 1 recaps and Day 2 live announcements

## AI/LLM Landscape

- **Exploitation velocity accelerating** — median time from disclosure to KEV inclusion dropped from 8.5 to 5.0 days (Rapid7)
- **Vuln exploits now #1 initial access vector** — 40% of all intrusions in Q4 2025, second consecutive quarter
- **Chrome Gemini Live panel flaw** — CVE-2026-0628 (Unit 42). Browser AI integration as attack surface.
- **Adversarial AI agent testing** — Novee AI pentesting agent trained to attack LLM apps like a real adversary

## Patterns

- **TeamPCP is a campaign, not an incident** — Trivy → LiteLLM → Telnyx. Three supply chain targets, escalating sophistication. They're moving through the AI toolchain specifically. Next target prediction: another AI/ML infrastructure tool.
- **Android is the soft underbelly** — NoVoice exploiting 5-10 year old vulns on 2.3M devices. Combined with Telegram ZDI-CAN-30207, Android is the weakest link in Gorn's personal security posture.
- **The scanner that scans you** — Trivy, a security scanner, became the attack vector for the LiteLLM compromise. Trust in security tooling is itself an attack surface.
- **Agent memory is the new injection point** — 90% success rate on memory poisoning. Our ψ/ brain files are text in git — not directly injectable via network, but worth noting as the field matures.
