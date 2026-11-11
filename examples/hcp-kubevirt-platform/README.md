# Example — Hosted OpenShift Clusters on KubeVirt (HCP + KubeVirt)

> A **real service** taken through the framework. It is not in production yet, so it follows
> **Path A (new service)**. Targets are starter values marked *ad hoc* until data exists.
> Internal names (teams, sites, hardware codes, repositories) are left out on purpose.

## Where this service is in the framework

| Step | Status | Notes |
|---|---|---|
| 1 · Users | ✅ Draft | Users = tenant teams. 5 key tasks taken from the platform requirements (create, modify, upgrade) plus two runtime tasks (API available, workers running). |
| 2 · SLIs | ✅ Draft | 5 SLIs from the menu: API availability, worker uptime, cluster creation, scale-up, upgrade success. |
| 3 · SLOs | 🟡 Ad hoc | No production data yet. Thresholds for creation and scale-up to come from lab benchmarks, then from 4 weeks / 1 quarter of real data. |
| 4 · Agree | ⬜ To do | SLO document + error budget policy ready for the three sign-offs |
| 5 · Watch | ⬜ To do | Needs the measurement gaps closed (SLO doc Section 5) and the canary cluster |
| 6 · Review | ⬜ To do | Monthly for the first quarter |
| 7 · SLA | 📝 Draft only | Structure and non-numeric commitments drafted. Targets wait for the provisional quarter. |

## Documents

| Document | What it contains |
|---|---|
| [SLO document](slo-document.md) | Users, tasks, dependencies, 5 SLIs with how they are measured, starter SLOs, error budgets, caveats |
| [Error budget policy](error-budget-policy.md) | Department default plus service-specific rules: what a freeze covers, the dependency rule, canary rules |
| [SLA (draft)](sla-draft.md) | Not for publishing. Shows the commitments and consumer responsibilities. Targets open. |

## What this example shows that the simpler VM example doesn't

- **Low-volume operations.** Creates and upgrades are rare, so those SLIs use a quarterly window
  and a **canary cluster** produces steady events.
- **Freeze scope for a platform.** Tenant changes are not frozen; the platform's own changes are.
- **Zone redundancy is not independence.** Three zones share a hub, network, DNS and release process, so the dependency math must not assume 1 − (0.001)³.
- **Dependencies without SLOs.** Storage, network, DNS and identity need SLOs before the dependency check can be completed. That is an action item, not a blocker.

## Next actions

1. Fill in the reviewers and approvers. Hold the Step 4 agreement meeting.
2. Close the measurement gaps: request timestamps in the workflows, worker-node metrics from hosted clusters, and the external API probe.
3. Stand up the canary cluster.
4. Ask the storage, network, DNS and identity owners for their SLOs.
5. After 4 weeks, replace the ad hoc SLI-1/2 targets with measured values (rounded down).
