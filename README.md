# homelab-cert-manager

[cert-manager](https://cert-manager.io) Helm chart install for the homelab k3s cluster, managed via ArgoCD.

CRDs live in the separate `homelab-cert-manager-crds` repo. Actual configuration (issuers, certificate, secrets) lives in `homelab-cert-manager-config` — kept as a separate Application from this one after discovering that using the same git source both as a Helm `valueFiles` reference and a raw manifests path caused stale renders.

---

[Homelab Docs](https://github.com/mattjmorrison/homelab/blob/main/docs/INDEX.md)
