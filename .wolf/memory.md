# Memory

> Chronological action log. Hooks and AI append to this file automatically.
> Old sessions are consolidated by the daemon weekly.

## 2026-04-07

| Time  | Description | File(s) | Outcome | ~tokens |
|-------|-------------|---------|---------|---------|
| 07:15 | Fixed NOT NULL constraint on monitor.conditions — added `conditions=[]` to all add_monitor/edit_monitor calls | reconciler.py, charts/.../files/reconciler.py | Fixed; both copies synced | ~500 |
| 07:15 | Wrapped ensure_group() in try/except at all call sites to prevent mislogged "CRD not available" errors | reconciler.py, charts/.../files/reconciler.py | Fixed | ~200 |
| 07:15 | Created GitHub Actions workflow to build and push Docker image to ghcr.io/lbrick/uptime-kuma-reconciler | .github/workflows/docker-publish.yaml | Created | ~150 |
| 07:30 | Updated OpenWolf cerebrum, buglog, and memory with session learnings | .wolf/cerebrum.md, .wolf/buglog.json, .wolf/memory.md | Done | ~300 |
