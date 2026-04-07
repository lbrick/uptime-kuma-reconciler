# Cerebrum

> OpenWolf's learning memory. Updated automatically as the AI learns from interactions.
> Do not edit manually unless correcting an error.
> Last updated: 2026-04-07

## User Preferences

- Concise responses; doesn't need lengthy explanations.
- Works in a fork (`git@github.com:lbrick/uptime-kuma-reconciler.git`) — changes stay on `main` until ready to contribute upstream.
- Wants GHCR images published under `ghcr.io/lbrick/uptime-kuma-reconciler`.

## Key Learnings

- **Project:** uptime-kuma-reconciler — a Kubernetes reconciler (single Python file) that auto-discovers `Ingress`, `IngressRoute` (Traefik), and `HTTPRoute` (Gateway API) resources and manages Uptime Kuma monitors via the `uptime-kuma-api` WebSocket client library.
- **Helm chart mirrors reconciler.py**: `charts/uptime-kuma-reconciler/files/reconciler.py` is a copy of the root `reconciler.py` mounted via ConfigMap. Both must be kept in sync whenever the script is changed.
- **Uptime Kuma ≥ 1.23.x enforces `conditions NOT NULL`**: All `api.add_monitor()` and `api.edit_monitor()` calls must include `conditions=[]` or Uptime Kuma's SQLite layer will reject the INSERT/UPDATE.
- **`ensure_group()` must be wrapped in try/except at call sites**: It is called unguarded inside `reconcile_resource()` and `reconcile_static_monitors()`. If it raises (e.g., the conditions constraint above), the exception propagates into the broad CRD-discovery `except` handlers in `full_reconcile()`, causing it to be mislogged as "IngressRoute/HTTPRoute CRD not available" with the SQL error as the message — masking the real failure.
- **MANAGED_TAG sentinel**: The reconciler only touches monitors tagged `managed-by-reconciler` (blue, `#2563eb`). Manually-created monitors are never modified.
- **No existing CI/CD before this session** — no `.github/workflows/` existed; added `docker-publish.yaml` this session.
- **GitHub actor**: `lbrick` (GitHub username for the fork owner).

## Do-Not-Repeat

- **[2026-04-07]** Do not call `api.add_monitor()` or `api.edit_monitor()` without `conditions=[]`. Uptime Kuma will reject it with `SQLITE_CONSTRAINT: NOT NULL constraint failed: monitor.conditions`. This affects group creation in `ensure_group()` too.
- **[2026-04-07]** Do not leave `ensure_group()` calls unguarded. Wrap them in try/except at the call site so failures log with the correct context and don't silently abort an entire CRD resource list.
- **[2026-04-07]** After editing `reconciler.py`, always sync the change to `charts/uptime-kuma-reconciler/files/reconciler.py`.

## Decision Log

- **[2026-04-07]** GitHub Action tags images with short SHA and `:latest` (on default branch). No semantic versioning yet — project is in active refinement. Will revisit when ready to contribute upstream.
- **[2026-04-07]** `conditions=[]` chosen over `conditions="[]"` — the `uptime-kuma-api` library serializes Python lists to JSON arrays internally; passing a Python list is the correct form.
