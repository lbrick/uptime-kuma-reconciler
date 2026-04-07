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
| 08:10 | Fixed Helm deployment.yaml — gated ConfigMap /app mount on not useCustomImage; was overwriting fixed script in image | charts/.../deployment.yaml | Fixed | ~150 |
| 08:21 | Fixed monkey-patch approach for conditions — library rejects conditions kwarg; patch _build_monitor_data output instead | reconciler.py, charts/.../files/reconciler.py | Fixed | ~250 |
| 08:24 | Fixed HTTPRoute URL scheme — use http:// by default, https:// only when parentRef sectionName contains 'https' | reconciler.py, charts/.../files/reconciler.py | Fixed | ~100 |
| 08:24 | Simplified monitor name — build_monitor_key() now returns just metadata.name (e.g. 'sonarr') not full namespace/Kind/name | reconciler.py, charts/.../files/reconciler.py | Fixed | ~50 |
| 08:29 | Fixed MonitorType enum vs string comparison — added _type_str() helper; fixed ensure_group() group detection and all needs_update type checks | reconciler.py, charts/.../files/reconciler.py | Fixed | ~200 |
| 08:35 | Updated OpenWolf with all session learnings | .wolf/cerebrum.md, .wolf/buglog.json, .wolf/memory.md | Done | ~400 |
| 08:40 | Added monitorNameIncludeNamespace feature — Helm value + env var + build_monitor_key() logic | values.yaml, configmap.yaml, reconciler.py (both copies) | Done | ~150 |
| 08:43 | Fixed duplicate group creation — added group_cache dict passed through full_reconcile to all ensure_group call sites | reconciler.py (both copies) | Fixed | ~200 |
| 08:43 | Removed static/ prefix from static monitor names — key = name directly | reconciler.py (both copies) | Fixed | ~50 |
| 08:50 | Updated OpenWolf with bug-005 and new cerebrum entries | .wolf/ | Done | ~200 |
