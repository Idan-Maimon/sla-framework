# Example — VM-as-a-Service (KubeVirt)

> **Illustrative.** The numbers and metric names show how the framework is applied. They are not
> real measurements or approved commitments.

Path **A (new service)**, walked through all seven steps:

| Step | What happened |
|---|---|
| 1 · Users | Users = application teams. Key tasks: *create a VM*, *my VM keeps running*, *resize/restart a VM*. Diagram drawn. Dependencies: OpenShift cluster, storage, network. |
| 2 · SLIs | Picked from the [menu](../../guide/3-sli-menu.md): *provisioning success + speed* for "create a VM", *running resource uptime* for "my VM keeps running". 3 SLIs in total. Resize/restart left for later. |
| 3 · SLOs | A probe created a small VM every 10 min, and real usage was measured for 4 weeks before go-live. Results were rounded down to starter SLOs. The dependency check showed storage (99.9%) limits uptime, so uptime SLO = 99.5%. |
| 4 · Agree | Team manager, developers and on-call agreed. On-call asked for the 5-min threshold (not 3 min) because of large image pulls. Error budget policy used as-is. |
| 5 · Watch | SLO dashboard + fast/slow burn alerts. 2-minute check in the weekly meeting. |
| 6 · Review | Month 1: an image-registry outage used 40% of the provisioning budget → the team added an image cache. Month 2: no change. |
| 7 · SLA | After one quarter of meeting the SLOs, a [one-page SLA](sla.md) was published with targets looser than the SLOs. |

Documents:
- [SLO document](slo-document.md)
- Error budget policy: [department default](../../templates/error-budget-policy.md), no changes
- [SLA](sla.md)
