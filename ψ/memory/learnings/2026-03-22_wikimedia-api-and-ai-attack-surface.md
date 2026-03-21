# Wikimedia Commons API & AI Orchestration Attack Surface

**Date**: 2026-03-22
**Source**: Recon Report #7 research + avatar resolution

## Wikimedia Commons API

When stock sites and web scraping fail, Wikimedia Commons API works without auth:
- Search: `commons.wikimedia.org/w/api.php?action=query&list=search&srnamespace=6&srsearch=<query>`
- Image info: `action=query&titles=File:<name>&prop=imageinfo&iiprop=url|size|mime&iiurlwidth=400`
- Thumbnails at predictable URLs via `thumburl` in response
- All CC-licensed, free to use

## AI Orchestration = New Attack Surface

Pattern emerging across March 2026 CVEs:
- CVE-2026-26030 (Semantic Kernel) — code generation flaw in orchestration
- CVE-2026-27577 (n8n) — sandbox escape, CVSS 9.4
- CVE-2026-33017 (Langflow) — unauthenticated RCE, exploited in 20h
- CVE-2026-26118 (Azure MCP) — SSRF via managed identity tokens
- Trivy CanisterWorm — security scanner compromised via supply chain

The layer between LLMs and tools (orchestrators, workflow engines, MCP servers) is where attackers are focusing. These tools hold elevated permissions and mediate tool execution — compromise them and you own the agent.
