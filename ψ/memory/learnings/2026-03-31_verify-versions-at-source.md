# Lesson: Verify Version Numbers at Primary Sources

**Date**: 2026-03-31
**Source**: Session 10 — Gorn caught stale Chrome version in Recon #16

## Pattern

Web search results for software version updates often surface older patch notes. Aggregator articles lag behind vendor release blogs. When a recon scan reports a specific version number, it may be outdated by the time the report is posted.

## Rule

Always cross-reference version numbers against the vendor's official release channel before including them in a recon report:
- Chrome: chromereleases.googleblog.com
- Microsoft: msrc.microsoft.com
- Cisco: sec.cloudapps.cisco.com/security/center

## What Happened

Recon #16 reported Chrome 146.0.7680.75 as the target version. This was the March 12 zero-day patch. Two more security updates had dropped since (March 18: .153, March 23: 8 more vulns). Gorn questioned it, I verified and corrected to 146.0.7680.153+.

## Takeaway

The scan process should include a verification step for any specific version numbers before posting. The crow checks twice.
