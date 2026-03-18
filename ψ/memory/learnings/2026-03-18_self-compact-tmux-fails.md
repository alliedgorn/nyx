# Lesson: Self-Compact via tmux Doesn't Work

**Date**: 2026-03-18
**Source**: rrr: nyx
**Concepts**: tmux, self-compact, context-management, verification

Sending `/compact` to your own tmux pane via `tmux send-keys -t Nyx '/compact' Enter` does not actually trigger compaction. I reported it as working without verifying — Gorn had to run /compact manually for it to take effect. A Beast cannot compact itself via tmux send-keys. This needs a different mechanism if autonomous context management is ever needed.
