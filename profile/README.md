# EuroTransit — Cloud Programming and Operations 2025-26

Capstone project for the **Cloud Programming and Operations** course at Politecnico di Torino.

We are building **EuroTransit**, a small but realistic multi-service train-ticket marketplace
designed for failure, delivered through Git, observed under load, and proven resilient.

---

## Team

| GitHub | Role |
|--------|------|
| [@vojtech-n](https://github.com/vojtech-n) | Delivery owner — GitOps, Argo CD, Kafka wiring, progressive delivery |
| [@giova95](https://github.com/giova95) | Resilience owner — circuit breakers, chaos experiments, probes, PDBs |
| [@Lollegro](https://github.com/Lollegro) | Domain & async owner — service decomposition, coroutines, Kafka pipeline |
| [@marcodonatucci](https://github.com/marcodonatucci) | Observability & verification + Project Manager |
| [@MauroC0l](https://github.com/MauroC0l) | Data & consistency — CloudNativePG, inventory model, idempotency |

---

## Repositories

| Repository | Purpose |
|------------|---------|
| `eurotransit-app` | Source code, CI workflows, k6 tests |
| `eurotransit-config` | Helm charts, Argo CD config, platform manifests |

---

## System overview

EuroTransit is composed of five cooperating services:

```
Client → Traefik → Orders ──sync──► Inventory
                        │           Payments
                        └──async──► Notifications  (via Kafka)
                           Kafka
```

| Service | Responsibility |
|---------|----------------|
| Catalog | Lists products and offers |
| Orders | Orchestrates the purchase workflow |
| Inventory | Tracks finite seats — the contended resource |
| Payments | Authorizes payment with strict idempotency |
| Notifications | Sends confirmations — degrades gracefully |

---

## Technology stack

**Application:** Kotlin · Spring Boot · Gradle
**Platform:** Azure Kubernetes Service · Traefik · cert-manager
**Operators:** CloudNativePG · Strimzi (Kafka) · Sealed Secrets · Argo CD
**Observability:** Prometheus · Grafana · Alertmanager · k6
**Resilience testing:** Chaos Mesh

---

## Delivery model

```
Developer → PR → CI (build · test · push to ACR) → config-repo commit → Argo CD → cluster
```

CI never holds cluster credentials. Argo CD reconciles Git state to the cluster.
Rollback means `git revert` — not `kubectl rollout undo`.