# anatomy.md

> Auto-maintained by OpenWolf. Last scanned: 2026-04-07T07:36:22.341Z
> Files: 24 tracked | Anatomy hits: 0 | Misses: 0

## ./

- `.dockerignore` — Docker ignore rules (~19 tok)
- `.gitignore` — Git ignore rules (~64 tok)
- `CLAUDE.md` — OpenWolf (~57 tok)
- `Dockerfile` — Docker container definition (~54 tok)
- `LICENSE` — Project license (~284 tok)
- `README.md` — Project documentation (~1773 tok)
- `reconciler.py` — signal_handler, connect_kuma, get_managed_monitors, ensure_tag + 7 more (~4114 tok)
- `requirements.txt` — Python dependencies (~14 tok)

## .claude/

- `settings.json` (~441 tok)

## .claude/rules/

- `openwolf.md` (~313 tok)

## .github/workflows/

- `docker-publish.yaml` — CI: Build and Push Docker Image (~304 tok)

## charts/uptime-kuma-reconciler/

- `Chart.yaml` (~128 tok)
- `values.yaml` — Uptime Kuma connection settings (~533 tok)

## charts/uptime-kuma-reconciler/files/

- `reconciler.py` — signal_handler, connect_kuma, get_managed_monitors, ensure_tag + 7 more (~4114 tok)
- `requirements.txt` — Python dependencies (~14 tok)

## charts/uptime-kuma-reconciler/templates/

- `_helpers.tpl` (~496 tok)
- `clusterrole.yaml` — K8s ClusterRole: {{ (~287 tok)
- `configmap.yaml` — K8s ConfigMap: {{ (~212 tok)
- `deployment.yaml` — K8s Deployment: {{ (~785 tok)
- `secret.yaml` — K8s Secret: {{ (~116 tok)
- `serviceaccount.yaml` — K8s ServiceAccount: {{ (~111 tok)
- `static-monitors.yaml` — K8s ConfigMap: {{ (~110 tok)

## examples/

- `ingress-with-annotations.yaml` — Example Ingress resource with Uptime Kuma reconciler annotations (~580 tok)
- `static-monitors.yaml` — Example static monitors configuration (~454 tok)
