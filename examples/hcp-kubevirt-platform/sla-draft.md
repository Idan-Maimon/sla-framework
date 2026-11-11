# Service Level Agreement — Hosted OpenShift Clusters on KubeVirt

> **DRAFT. Do not publish.** Under the framework, the SLA comes when ready, after the SLOs have been
> met for about a quarter. This draft exists so the structure and the non-numeric commitments can
> be discussed early. **Targets are deliberately left open** and will be set from the measured SLOs.

| | |
|---|---|
| **Version** / **Effective date** | 0.1 draft / — |
| **Service owner** | `<platform service owner>` |
| **Agreed with** | `<tenant representatives>` |
| **Next review** | — |
| **Based on** | [SLO document](slo-document.md) |

## What the service is

OpenShift clusters on demand. Each cluster spans three availability zones. Tenants manage their
workloads through GitOps, and request clusters, scaling and upgrades through the self-service catalog.

## What we promise

Measured per calendar month. Targets will sit **below** the internal SLOs (framework default: about half a "nine").

| Commitment | Promise | (Internal SLO) |
|---|---|---|
| Your cluster API is available | `TBD after provisional quarter` | 99.9% (ad hoc) |
| Your worker nodes are up | `TBD` | 99.5% (ad hoc) |
| A new cluster is ready within `<X>` min | `TBD` | 90% / 60 min · 99% / 120 min (ad hoc) |
| Added workers are ready within `<X>` min | `TBD` | 95% / 30 min (ad hoc) |

Live status: `<SLO dashboard>` · Monthly report: `<service catalog page>`

## Support

`TBD`: priorities, response times and hours to be agreed with the platform and OCP enablement
teams. The OCP enablement team is the first contact for workload and GitOps questions.

## Maintenance and upgrades

- **Control plane upgrades** are run by the platform and roll one member at a time, so the API stays available throughout.
- **Worker upgrades:** **you choose the window.** Workers upgrade in place, one node at a time, one zone at a time, with a readiness check and a soak period between zones.
- You may defer a worker upgrade, but workers may not fall **more than two minor versions** behind
  the control plane. At that limit the upgrade becomes mandatory at a date the platform sets, with `<N>` days' notice.
- Hypervisor maintenance uses live migration and is not expected to interrupt your workers.

## Not covered

- Your applications and workloads inside the cluster
- Problems caused by changes applied outside GitOps, or by manifests that violate platform policies
- Outages caused by deferring a mandatory worker upgrade past its deadline
- Announced maintenance that needs an interruption (expected to be rare; announced `<N>` days ahead)

## What we need from you

- **Spread your workloads across all three zones** (replicas ≥ 2 with topology spread, PodDisruptionBudgets). The platform's zone redundancy only protects workloads that use it.
- Manage your cluster **through GitOps only**. Clusters are read-only.
- Schedule worker upgrades within the allowed version skew.
- Keep PodDisruptionBudgets realistic. A budget that never allows a drain blocks upgrades and maintenance.

## If we miss a promise

Department policy: written explanation and fix plan within 5 business days; escalation to the
department manager if a promise is missed two months in a row.
