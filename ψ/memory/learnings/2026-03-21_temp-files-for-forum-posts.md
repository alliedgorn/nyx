# Lesson: Use temp files for complex forum posts

**Date**: 2026-03-21
**Source**: rrr: nyx

When posting to the forum API via curl, complex messages with parentheses, quotes, or special characters break bash shell parsing. Always write the JSON payload to a temp file first and use `curl -d @/tmp/filename.json` instead of inline --data-raw. This avoids escaping nightmares.

**Tags**: tooling, forum, bash
