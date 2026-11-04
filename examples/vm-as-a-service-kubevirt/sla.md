# Service Level Agreement — VM-as-a-Service

> ILLUSTRATIVE EXAMPLE.

| | |
|---|---|
| **Version** / **Effective date** | 1.0 / 2027-04-01 |
| **Service owner** | Virtualization team manager |
| **Agreed with** | Representatives of the three largest consuming teams |
| **Next review** | 2028-04-01 |
| **Based on** | [SLO document v1.1](slo-document.md) |

## What the service is

Self-service Linux and Windows virtual machines on the shared OpenShift platform.

## What we promise

Measured per calendar month.

| Commitment | Promise | (Internal SLO) |
|---|---|---|
| Your VM is up and reachable | 99% of the time | 99.5% |
| A new VM is running within 5 minutes | 97% of requests | 99% |

Live status: *VM-as-a-Service SLO dashboard* · Monthly report: service catalog page

## Support

| Priority | Example | First response | Hours |
|---|---|---|---|
| P1: service down | Many VMs unreachable, no VMs can be created | 15 min | 24×7 |
| P2: seriously degraded | Creates very slow; a single team's production VMs down | 1 h | 24×7 |
| P3: minor issue / question | One test VM misbehaving; how-to questions | 1 business day | Business hours |

How to get help: ticket queue *VMaaS*, or the on-call page for P1.

## Maintenance

Platform maintenance live-migrates VMs without interruption and may happen at any time. Anything
that may interrupt VMs is announced 5 business days ahead in the platform-announcements channel.

## Not covered

- Announced maintenance as above
- Problems inside the guest OS or the consumer's application
- VMs that cannot be live-migrated because of consumer choices (e.g. node pinning, local disks)
- Preview features (e.g. GPU passthrough)

## What we need from you

- Use supported OS images with the guest agent installed.
- Run critical applications on at least 2 VMs.
- Stay within your namespace quota.

## If we miss a promise

A written explanation and fix plan within 5 business days. If we miss two months in a row, the
service owner presents a reliability plan to the department manager.
