# homelab-cert-manager

[cert-manager](https://cert-manager.io) deployment for the homelab k3s cluster, managed via ArgoCD. CRDs live in the separate `homelab-cert-manager-crds` repo.

Issues a single Let's Encrypt wildcard certificate for `*.morrisons.site` via DNS-01 validation against Cloudflare (DNS host for the `morrisons.site` zone; registration stays at Hover). The Cloudflare API token is stored in OpenBao at `homelab/cert-manager` → `CLOUDFLARE_API_TOKEN` and pulled in via External Secrets Operator (`manifests/secret-store.yaml`, `manifests/external-secret.yaml`) — never stored in git.

The resulting certificate is installed as Traefik's cluster-wide default (`manifests/tlsstore.yaml`, in `kube-system` alongside k3s's built-in Traefik), so every existing `Ingress` gets HTTPS automatically — no per-service changes needed.

`manifests/certificate.yaml` currently points at the `letsencrypt-staging` issuer for initial validation (staging certs aren't browser-trusted, but avoid burning Let's Encrypt's production rate limits while testing). Once a staging cert issues successfully, switch `issuerRef.name` to `letsencrypt-prod` and it'll re-issue on next sync.

**Requires, set up out-of-band in OpenBao** (not in this repo):
- KV secret at `homelab/cert-manager` → `CLOUDFLARE_API_TOKEN`, scoped to `Zone:DNS:Edit` on the `morrisons.site` zone only
- A Kubernetes auth role named `cert-manager`, bound to the `cert-manager` ServiceAccount (created by the Helm chart itself) in the `cert-manager` namespace, with a policy granting read on `kv/data/homelab/cert-manager`

---

[Homelab Docs](https://github.com/mattjmorrison/homelab/blob/main/docs/INDEX.md)
