# Example — VM-as-a-Service (KubeVirt)

> **Illustrative.** The numbers and metric names show how the framework is applied. They are not
> real measurements or approved commitments.

A new service, walked through all five steps:

| Step | What happened |
|---|---|
| 1 · Create the SLO document | The architect and the team tech lead (authors) wrote the service overview: users = application teams; requirements R1 Provision, R2 Modify, R3 Upgrade, R4 Decommission; diagram; critical dependencies (OpenShift cluster, storage, network, image registry). They chose 3 SLIs: S1 Provision success, S2 Provision speed, S3 Availability uptime. Modify, Upgrade, Decommission and disk response time were left out, with reasons. |
| 2 · Build and measure | A probe created a small VM every 10 min, and real usage was measured for 4 weeks before go-live. Results were rounded down to starter SLOs. The dependency check showed storage (99.9%) limits uptime, so S3 = 99.5%. Error budget policy: department default, unchanged. |
| 3 · Send for approval | The dev lead and on-call lead reviewed. On-call asked for the 5-min S2 threshold (not 3 min) because of large image pulls. The section leader approved. |
| 4 · Watch | On-call set up the SLO dashboard and fast/slow burn alerts. 2-minute check in the weekly meeting. |
| 5 · Review and improve | Month 1: an image-registry outage used 40% of the S2 budget → the team added an image cache. Month 2: no change. |
| When ready · SLA | After one quarter of meeting the SLOs, a [one-page SLA](sla.md) was published with targets looser than the SLOs. |

Documents:
- [SLO document](slo-document.md)
- Error budget policy: [department default](../../templates/error-budget-policy.md), no changes
- [SLA](sla.md)
