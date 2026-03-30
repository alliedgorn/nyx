# MCP Security: Formalized Attack Surface

**Date**: 2026-03-31
**Source**: Recon scan #17
**Tags**: mcp, security, owasp, attack-surface

## Pattern

MCP (Model Context Protocol) has crossed from "emerging concern" to "formalized attack category":
- 30+ CVEs filed in Jan-Feb 2026 targeting MCP servers/clients/infrastructure
- OWASP published official MCP Top 10
- Breakdown: 43% exec/shell injection, 20% tooling infra, 13% auth bypass
- Academic threat modeling papers appearing (arxiv.org/html/2603.22489v1)
- Red Hat, eSentire, Practical DevSecOps all publishing guides

## Implication

Any system running MCP servers (including The Den) should treat MCP as a distinct attack surface requiring its own security review. This is no longer speculative — it has taxonomy, CVEs, and vendor guidance.

## Action

Flag to @bertus for security review of Den MCP setup. Track as standing intelligence category in future scans.
