## What does this PR do?

<!--
One sentence. Be specific: "Adds circuit breaker on Orders → Payments" not "Improves resilience".
-->

## Type of change

- [ ] Feature / new capability
- [ ] Bug fix
- [ ] Refactor (no behaviour change)
- [ ] Configuration / infrastructure
- [ ] Documentation
- [ ] CI / tooling

## Linked issue or task

<!--
Reference any related issue, ADR, or capstone DoD item.
e.g. "Closes #12" or "Implements DoD item: Pillar C — circuit breakers"
-->

## How to verify

<!--
Steps a reviewer can follow to confirm this works.
For infrastructure changes: what to check in Argo CD or kubectl.
For code changes: which test to run or endpoint to hit.
-->

```bash
# example
kubectl get application -n argocd eurotransit
# expected: Synced / Healthy
```

## Checklist

### Everyone
- [ ] PR title is descriptive (not "fix" or "update")
- [ ] Branch is up to date with `main`
- [ ] No `.env*`, `*.key`, or `*.pem` files committed
- [ ] No plaintext `kind: Secret` manifests (must be `SealedSecret`)

### Code changes (application repo)
- [ ] Tests pass locally (`just test`)
- [ ] Liveness probe does NOT check any downstream dependency
- [ ] Every Kafka consumer handler is idempotent
- [ ] No `kubectl`, `helm upgrade`, or cluster credentials in CI workflows

### Infrastructure changes (configuration repo)
- [ ] `helm lint deploy/charts/eurotransit/` passes
- [ ] `helm template ... | kubectl apply --dry-run=client -f -` passes
- [ ] Image tags use `{{ .Values.<service>.image.tag }}`, not literals
- [ ] `resources:` section present on every container
- [ ] `selfHeal` and `prune` in Argo CD Application are still `true`

### Agent-generated content
- [ ] I reviewed every line — I can explain and defend what is here
- [ ] If the agent made a mistake I caught and corrected, I added it to `docs/agent-log.md`

## Notes for reviewer

<!--
Anything the reviewer should pay special attention to, known limitations,
or follow-up items that are out of scope for this PR.
-->