# SLO Document — Hosted OpenShift Clusters on KubeVirt (HCP + KubeVirt)

> Built with this framework. **Path A (new service).** No production data exists yet, so every
> target is a **starter value marked "ad hoc"**. They are replaced with measured values after the
> first 4 weeks of data (Step 3). Internal names are deliberately left out.

| | |
|---|---|
| **Status** | Draft |
| **Version** | 0.1 |
| **Authors** | Platform architect |
| **Reviewers** | `<platform tech lead>` · `<SRE>` |
| **Approvers** | `<platform service owner>` · `<dev lead>` · `<on-call lead>` |
| **Approval date** | — |
| **Next review** | 4 weeks after measurement goes live, then monthly for the first quarter |
| **Path** | A: new service, not yet in production |

## 1. Service overview

The platform gives tenants **OpenShift clusters on demand**. Each tenant cluster is a HyperShift
**hosted cluster (HC)**: its control plane runs as pods on a shared management hub (Customer MCE),
and its workers are **KubeVirt VMs** drawn from a pre-built warm pool. Every tenant cluster spans
**three availability zones**, with one NodePool per zone, each on a separate KubeVirt (KV) cluster.
Tenants have read-only access and change their clusters through GitOps or the self-service catalog.

**Users:** tenant teams that run their workloads on hosted clusters.
*(The platform team and the OCP enablement team operate and support the service. They are not the users whose happiness these SLOs measure.)*

**Key tasks (critical user journeys)**, taken from the platform requirements:

| # | Task | Requirement |
|---|---|---|
| CUJ-1 | **Use my cluster.** The cluster API answers (deploy, GitOps sync, `oc get`). | runtime |
| CUJ-2 | **My workers keep running.** Nodes stay up, so my workloads keep running. | runtime |
| CUJ-3 | **Get a new cluster.** Request it and receive a working cluster. | R1 Create |
| CUJ-4 | **Scale my cluster.** Add workers to a NodePool and get them Ready. | R2 Modify |
| CUJ-5 | **Upgrade my cluster.** Control plane and workers move to a new version without breaking my workloads. | R3 Upgrade |
| — | Delete my cluster | R4 Delete: low user pain, not covered yet |

```mermaid
flowchart LR
    T[Tenant] -->|GitOps / catalog| GIT[Git + ArgoCD]
    T -->|API| HCP[Hosted control plane<br/>3× API · 3× etcd<br/>on Customer MCE]
    GIT --> TMP[Temporal workflows]
    TMP --> MCE[Customer MCE<br/>stretched over 3 zones]
    MCE --- HCP
    HCP --> NP1[NodePool zone A<br/>VMs on KV cluster A]
    HCP --> NP2[NodePool zone B<br/>VMs on KV cluster B]
    HCP --> NP3[NodePool zone C<br/>VMs on KV cluster C]
    NP1 & NP2 & NP3 --> INFRA[Storage · DC network · DNS]
    SEED[Seed cluster per zone<br/>hosts KV control planes] -.-> NP1 & NP2 & NP3
    INV[Inventory Controller<br/>+ warm pool] -.->|not in request path| MCE
```

**Critical dependencies**

| Dependency | Needed for | Owner | Has an SLO? |
|---|---|---|---|
| Customer MCE (hub) | CUJ-1 (hosts the control planes), CUJ-3/4/5 | Platform team | Not yet (internal) |
| KV clusters (one per zone) | CUJ-2, CUJ-4 | Platform team | Not yet (internal) |
| Seed cluster (one per zone) | KV control planes. Running VMs continue if it fails, but no scaling or upgrade in that zone. | Platform team | Not yet (internal) |
| Shared storage | CUJ-2 (live migration), etcd | Storage team | **Needed** |
| DC network | All | Network team | **Needed** |
| DNS | CUJ-1 (API endpoints), provisioning (Redfish) | Network/DNS team | **Needed** |
| Identity provider | CUJ-1 login (soft: existing tokens keep working) | IAM team | **Needed** |
| Git + ArgoCD, Temporal | CUJ-3/4/5 (changes only, not runtime) | Platform team | Not yet (internal) |
| Warm pool / Inventory Controller | CUJ-3/4 speed. By design it is never in the request path. | Platform team | Not yet (internal) |
| OCP release registry | CUJ-5 | External (Red Hat) | Public |

## 2. SLIs and SLOs

Window: **4-week rolling** for SLI-1 and SLI-2. **Rolling quarter** for SLI-3 to SLI-5, because
creates, scales and upgrades are low-volume events and a 4-week ratio would swing on a single failure.

| Task | SLI type | Specification (plain words) | Implementation (how measured) | Starter SLO (ad hoc) |
|---|---|---|---|---|
| CUJ-1 Use my cluster | Request-driven **availability** | Proportion of tenant API requests to the hosted cluster API that succeed | `apiserver_request_total` from each HCP's kube-apiserver (scraped on the Customer MCE): non-5xx ÷ all, excluding 429 throttling caused by the tenant. **Plus** a blackbox probe of `/readyz` on every HC API endpoint every 30 s, from outside the hub, so that DNS and network failures are counted too. | **99.9%** |
| CUJ-2 Workers keep running | Running resource **uptime** | Proportion of worker-node-minutes in which a node that should be Ready is Ready | `kube_node_status_condition{condition="Ready",status="true"}` per HC, summed over node-minutes ÷ expected node-minutes. **Excluded:** a node drained by a NodePool roll the tenant scheduled (upgrade window, `maxUnavailable: 1`), and nodes being removed by a scale-down. | **99.5%** |
| CUJ-3 Get a new cluster | **Provisioning** success + speed | Proportion of valid create requests where the HC is Available with all three NodePools at the requested size within the threshold | Request time from the catalog/Temporal create workflow; done time from `HostedCluster` condition `Available=True` plus all NodePools `ReadyReplicas == Replicas`. Excluded: quota or capacity rejection given to the tenant up front, and invalid specs. | **90% within 60 min · 99% within 120 min** |
| CUJ-4 Scale my cluster | **Provisioning** success + speed | Proportion of NodePool scale-up requests where the new nodes are Ready within the threshold | `update-nodepool` workflow start → NodePool `ReadyReplicas` reaching the new target. Each request counts once. Excluded: requests rejected up front by the pre-flight capacity check. | **95% within 30 min** |
| CUJ-5 Upgrade my cluster | **Correctness** (operation success) | Proportion of upgrade runs (control plane, or one zone of a worker roll) that complete without manual intervention and without breaching SLI-1 during the run | Upgrade workflow outcome + HostedCluster/NodePool version conditions. A run counts as bad if it needed a human step, ended in the DLQ, or SLI-1 burned more than 2% of its budget during the run. | **95%** |

Five SLIs is the workbook's maximum. API latency, deletion and live-migration transparency are
deliberately left for later reviews (Section 5).

### Canary cluster

Tenant creates, scales and upgrades are rare, so the platform runs **one canary hosted cluster**
of its own. Each day it scales a NodePool up and down, and each week it is recreated. It runs every
upgrade first. This gives SLI-3 to SLI-5 a steady flow of events and catches breakage before
tenants do. Canary events are reported separately and also included in the SLIs.

## 3. Rationale

- **All targets are ad hoc. No production data exists yet.** Replace them with measured values,
  rounded down (Step 3), after 4 weeks of data (SLI-1/2) or one quarter (SLI-3/4/5). Lab benchmarks
  from the OpenShift benchmarking work, which serve quality goal SC2 (Predictability), are the
  first evidence for the SLI-3/4 thresholds.
- **SLI-1 at 99.9%.** Each control plane runs 3 API servers and 3 etcd members across 3 zones
  (2-of-3 quorum), on a hub stretched across those zones. The design is meant to survive a full zone loss.
- **SLI-2 at 99.5%, lower than SLI-1.** A single worker sits in one zone, on one KV cluster, and
  depends on storage and network. Hypervisor maintenance uses live migration and should not count
  against it. A failed KV cluster or storage outage in a zone does count.
- **SLI-3/4 thresholds** assume the warm pool is not empty. Because the pool removes VM build
  time from the request path, an empty pool is a real user-facing failure and is counted.
- **Dependency check: incomplete.** Storage, network, DNS and identity do not publish SLOs yet.
  Assuming 99.9% each for the hard runtime dependencies, one zone's worker ceiling is about
  0.999 × 0.999 × (KV + own components), which is below 99.8%. That supports 99.5% for SLI-2.
  **Caution (workbook):** three zones do not make SLI-1 = 1 − (0.001)³. All zones share the hub,
  the network core, DNS, Git and the release process, so zone redundancy is not independent.

## 4. Error budget

| SLO | Window | Budget |
|---|---|---|
| 99.9% API availability | 4 weeks | 0.1% of requests (≈ 40 min of full outage) |
| 99.5% worker uptime | 4 weeks | 0.5% of node-minutes (≈ 3 h 22 min per node) |
| 90% creates within 60 min | Quarter | 10% of creates |
| 99% creates within 120 min | Quarter | 1% of creates |
| 95% scale-ups within 30 min | Quarter | 5% of scale-ups |
| 95% upgrades without manual intervention | Quarter | 5% of upgrade runs |

Response to budget exhaustion: [error budget policy](error-budget-policy.md).

## 5. Clarifications and caveats

- **Excluded from all SLIs:** canary-only test failures *if* they come from deliberate fault
  injection, load tests, requests the tenant caused (invalid manifests, quota), and workloads
  inside tenant clusters (the tenant's own apps).
- **Planned worker reboots** inside a tenant-chosen upgrade window are excluded from SLI-2. Rolls
  forced by the version-skew deadline (workers may trail the control plane by at most two minors)
  are **not** excluded, because the platform decides their timing.
- **Seed cluster failure** stops scale and upgrade in one zone but not running workers. It shows up
  in SLI-4/5, not SLI-2. That is intended.
- **Known measurement gaps:**
  - SLI-3/4 need the request timestamp recorded by the create/scale workflow.
  - SLI-2 needs worker-node metrics from every hosted cluster in central monitoring.
  - The "SLI-1 budget burned during an upgrade" condition in SLI-5 needs a recording rule that joins the upgrade run to the SLI-1 burn.
- **Candidates for later reviews:**
  - API latency (e.g. 99% of non-watch requests < 1 s)
  - deletion completion (R4)
  - live-migration transparency
  - per-tenant SLO tracking: the workbook's "bucketing" idea, i.e. how many tenants are inside SLO

## 6. Change log

| Version | Date | Change | Approved by |
|---|---|---|---|
| 0.1 | 2026-11-08 | First draft from the framework (Path A) | — |
