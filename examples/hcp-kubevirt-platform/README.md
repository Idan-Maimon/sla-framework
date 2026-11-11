# Example — Hosted OpenShift Clusters on KubeVirt (HCP + KubeVirt)

> A **real service** taken through the framework. It is a **new service**, not in production yet.
> Targets are starter values marked *ad hoc* until data exists.
> Internal names (teams, sites, hardware codes, repositories) are left out on purpose.

## Where this service is in the framework

| Step | Status | Notes |
|---|---|---|
| 1 · Create the SLO document | ✅ Draft | Users = tenant teams. Requirements R1 Provision, R2 Modify, R3 Upgrade, R4 Decommission, plus Availability. 5 SLIs: S1 API uptime, S2 worker uptime, S3 provision speed, S4 scale-up speed, S5 upgrade success. Upgrade duration, decommission, API response time and live-migration transparency left out, with reasons. |
| 2 · Build and measure | 🟡 Ad hoc | No production data yet. Thresholds for provisioning and scale-up to come from lab benchmarks, then from 4 weeks / 1 quarter of real data. |
| 3 · Send for approval | ⬜ To do | SLO document + error budget policy ready for the reviewers and the section leader |
| 4 · Watch | ⬜ To do | Needs the measurement gaps closed (see the SLO document's caveats) and the canary cluster |
| 5 · Review and improve | ⬜ To do | Monthly for the first quarter |
| When ready · SLA | 📝 Draft only | Structure and non-numeric commitments drafted. Targets wait for the provisional quarter. |

## Documents

| Document | What it contains |
|---|---|
| [SLO document](slo-document.md) | Users, requirements, dependencies, 5 SLIs with how they are measured, starter SLOs, error budgets, caveats |
| [Error budget policy](error-budget-policy.md) | Department default plus service-specific rules: what a freeze covers, the dependency rule, canary rules |
| [SLA (draft)](sla-draft.md) | Not for publishing. Shows the commitments and consumer responsibilities. Targets open. |

## What this example shows that the simpler VM example doesn't

- **Low-volume requirements.** Provisions and upgrades are rare, so those SLIs use a quarterly window
  and a **canary cluster** produces steady events.
- **Freeze scope for a platform.** Tenant changes are not frozen; the platform's own changes are.
- **Zone redundancy is not independence.** Three zones share a hub, network, DNS and release process, so the dependency math must not assume 1 − (0.001)³.
- **Dependencies without SLOs.** Storage, network, DNS and identity need SLOs before the dependency check can be completed. That is an action item, not a blocker.

## Next actions

1. Fill in the authors, reviewers and approver. Send for approval (Step 3).
2. Close the measurement gaps: request timestamps in the workflows, worker-node metrics from hosted clusters, and the external API probe.
3. Stand up the canary cluster.
4. Ask the storage, network, DNS and identity owners for their SLOs.
5. After 4 weeks, replace the ad hoc S1/S2 targets with measured values (rounded down).
