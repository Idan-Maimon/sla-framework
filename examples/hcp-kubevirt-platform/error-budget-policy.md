# Error Budget Policy — Hosted OpenShift Clusters on KubeVirt

> Starts from the [department default](../../templates/error-budget-policy.md). This page lists
> only the **service-specific additions and changes**, each with its reason. Everything else in
> the default applies unchanged.

| | |
|---|---|
| **Authors** | Platform architect · `<platform service owner>` |
| **Reviewers** | `<dev lead>` · `<on-call lead>` |
| **Approver** | `<section leader>` |
| **Approval date** / **Revisit date** | — / with the SLO document |
| **Applies to** | SLOs in the [SLO document](slo-document.md) |

## Service overview

Hosted OpenShift clusters for tenants. Control planes run on a shared hub, and workers are KubeVirt
VMs across three zones. The platform team changes the platform often: hub, KV and seed cluster
upgrades, workflow and controller releases, and inventory changes. Tenants change their own
clusters through GitOps.

## What "freeze" means for this service

The default policy freezes "changes and releases". For this platform:

| Frozen when the budget is gone | **Not** frozen |
|---|---|
| Platform releases: workflow and controller code, catalog changes | P1 fixes and security patches (default) |
| Hub (MCE), KV cluster and seed cluster upgrades | **Tenant-initiated changes**: creating, scaling and upgrading their own clusters through the catalog. The freeze protects tenants and does not block them. |
| Changes to warm-pool or inventory settings, unless the change is the fix | Worker rolls forced by the **version-skew deadline**, treated like security patches. Missing the deadline would push clusters out of support. |
| New tenant onboarding waves (single tenants still allowed) | Capacity additions (new KV cluster, more servers). They add capacity and do not change how the platform behaves. |

**Reason:** most of the platform's risk comes from its own changes. Tenant changes go through the
same automation the SLOs measure, so blocking them would hurt users without making anything safer.

## Who caused it: the dependency rule

The platform team owns most of the stack: hub, KV clusters, seed clusters, workflows and the
Inventory Controller. Budget burned by **any of these is the team's own** and the default "must work
on reliability" rule applies.

The "another team caused it" exception applies only to **storage, DC network, DNS and the identity
provider**, and only when that team has frozen its own changes (default rule). The platform team
then still:
- adds the incident to that team's dependency review, and
- checks whether the dependency can be made less critical (default rule).

## Canary cluster

- Failures seen **only** on the canary cluster that came from **deliberate fault injection** do not consume budget.
- A failure on the canary that would have hit tenants **does** count. That is what the canary is for.
- A failed upgrade on the canary **blocks rolling that upgrade to tenants** until it passes, whatever the budget level.

## Big incidents (addition)

In addition to the default 20% rule:
- **Zone loss.** Any event that takes down a whole zone (KV cluster, seed cluster or storage) gets
  a post-incident review, even if the budget impact was small. Surviving zone loss is the core design promise.
- **Empty warm pool.** If a pool is empty when a tenant request arrives, the incident is reviewed
  even if no SLO was missed. It is the early warning for S3/S4.

## Escalation

Platform service owner → department manager (default).
