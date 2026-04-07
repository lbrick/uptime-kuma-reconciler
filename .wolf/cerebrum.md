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
- **Helm chart mirrors reconciler.py**: `charts/uptime-kuma-reconciler/files/reconciler.py` is a copy of the root `reconciler.py` mounted via ConfigMap. Both must be kept in sync whenever the script is changed. Use `cp reconciler.py charts/uptime-kuma-reconciler/files/reconciler.py` after every edit.
- **Uptime Kuma ≥ 1.23.x enforces `conditions NOT NULL`**: Fixed via monkey-patch of `UptimeKumaApi._build_monitor_data` at module load — pops `conditions` from kwargs (library rejects it as unknown), injects `conditions: []` into the returned dict instead.
- **`ensure_group()` must be wrapped in try/except at call sites**: Exceptions from it propagate into the CRD-discovery `except` handlers in `full_reconcile()` and get mislogged as "IngressRoute/HTTPRoute CRD not available". Both call sites in `reconcile_resource()` and `reconcile_static_monitors()` now catch and log correctly.
- **MonitorType enum vs API string mismatch**: `api.get_monitors()` returns `type` as a plain string (e.g. `"group"`, `"http"`). Comparing directly against `MonitorType.GROUP` (enum) fails silently in Python unless the enum inherits from `str`. Fixed with `_type_str()` helper (`t.value if hasattr(t, "value") else t`) applied to both sides of every type comparison. This affected: `ensure_group()` group detection (caused group recreation every reconcile) and `needs_update` type checks.
- **Group monitors are NOT tagged**: `ensure_group()` creates groups without `managed-by-reconciler` tag. Groups are found by scanning ALL monitors (`api.get_monitors()`), not just managed ones. Do not expect groups to appear in `get_managed_monitors()`.
- **Group cache is required**: `ensure_group()` takes a `group_cache` dict (created once per reconcile in `full_reconcile()`). Without it, multiple monitors in the same group cause duplicate group creation because the WebSocket API doesn't reflect newly created monitors instantly within the same reconcile cycle. Cache is keyed by group name → monitor ID.
- **Static monitor names have no prefix**: `key = name` (the user-supplied name verbatim). The old `static/` prefix has been removed. No tracking prefix is used — users are responsible for avoiding name collisions with Kubernetes resource names.
- **HTTPRoute scheme detection**: HTTPRoute has no native HTTP/HTTPS distinction — it's determined by the Gateway listener. Heuristic: check `spec.parentRefs[].sectionName`; if any contains `"https"` → use `https://`, else `http://`.
- **Monitor name = Kubernetes resource name only by default**: `build_monitor_key()` returns just `metadata.name` (e.g. `sonarr`). Set `MONITOR_NAME_INCLUDE_NAMESPACE=true` (via `monitorNameIncludeNamespace: true` in Helm values) to get `namespace/name` format (e.g. `entertainment/sonarr`). Changing this renames all managed monitors — old-named ones are orphaned and recreated.
- **useCustomImage=true + ConfigMap mount conflict**: When `useCustomImage: true`, the Helm deployment was still mounting the ConfigMap at `/app`, overwriting the image's script. Fixed in `deployment.yaml` — both the volumeMount and the volume are now conditional on `not .Values.image.useCustomImage`.
- **MANAGED_TAG sentinel**: The reconciler only touches monitors tagged `managed-by-reconciler` (blue, `#2563eb`). Manually-created monitors are never modified.
- **GitHub CI**: `.github/workflows/docker-publish.yaml` builds and pushes to `ghcr.io/lbrick/uptime-kuma-reconciler` on all branch pushes. Tags: short SHA always, `:latest` on `main` only.

## Do-Not-Repeat

- **[2026-04-07]** Do NOT pass `conditions=[]` as a kwarg to `api.add_monitor()` or `api.edit_monitor()`. The installed `uptime-kuma-api` library rejects it. Use the monkey-patch at module load instead.
- **[2026-04-07]** Do not compare `m.get("type")` directly against `MonitorType.*` — the API returns plain strings, enums don't compare equal to strings by default. Always use `_type_str()` on both sides.
- **[2026-04-07]** Do not leave `ensure_group()` calls unguarded. Wrap in try/except at call sites.
- **[2026-04-07]** After editing `reconciler.py`, always sync to `charts/uptime-kuma-reconciler/files/reconciler.py`.
- **[2026-04-07]** Do not call `ensure_group()` without passing the `group_cache` dict — duplicate groups will be created within the same reconcile because the API doesn't reflect new monitors instantly.
- **[2026-04-07]** Do not assume the Helm chart's ConfigMap won't interfere with a custom image — the `/app` mount must be gated on `not .Values.image.useCustomImage` in both volumeMounts and volumes.

## Decision Log

- **[2026-04-07]** GitHub Action tags images with short SHA and `:latest` (on default branch). No semantic versioning yet — project is in active refinement. Will revisit when ready to contribute upstream.
- **[2026-04-07]** `conditions=[]` chosen over `conditions="[]"` — the `uptime-kuma-api` library serializes Python lists to JSON arrays internally; passing a Python list is the correct form.
