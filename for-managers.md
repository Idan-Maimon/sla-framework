# For Managers — 3-minute summary

## What this is

A light, standard way for every team to define and manage the reliability of its services. It is
based on Google's SRE Workbook chapter [Implementing SLOs](https://sre.google/workbook/implementing-slos/),
which is free, widely used and tool-independent.

## Why

- Today each team describes "how good" its service is differently, or not at all.
- Arguments about outages and priorities are based on opinion.
- With an **error budget**, data decides when a team works on features and when it works on reliability.

## What each team produces

| Document | Size | When |
|---|---|---|
| **SLO document**: what we measure and our targets | 1–2 pages | Within the first weeks |
| **Error budget policy**: what we do when targets are missed | A filled-in template | Same time |
| **SLA**: the promise to consumers | 1 page | After ~1 quarter of meeting the SLOs |

Effort: about **2–4 weeks part-time** for a running service; spread over the launch for a new one.

## What we need from you

| Decision | Recommendation |
|---|---|
| Make the framework the standard for services other teams depend on | Yes. Start with a pilot of 1–2 services, then roll out. |
| Back the error budget policy: teams **pause feature work** when the budget is used up | Yes. Without this, SLOs are just reports. |
| Be the **escalation point** when teams disagree | Yes (the workbook gives this role to the CTO) |
| Consequences when an SLA is missed | Written explanation + fix plan; escalation after 2 missed months |
| Choose tooling for SLO dashboards and alerts | Open-source on our existing Prometheus/Grafana. See [open questions](ideas/open-questions.md). |

## How you'll see progress

A monthly overview of every service: SLO met or missed, error budget left, trend. In quarterly
planning, error budget data shows which reliability work matters most.

## Rollout

| When | What |
|---|---|
| Month 1–2 | Pilot: 1–2 services (e.g. VM-as-a-Service, cluster provisioning) |
| Month 3 | Adjust the framework from pilot lessons |
| Month 4–6 | All services that other teams depend on |
| Ongoing | Monthly reviews, then quarterly. First SLAs published after a quarter of stable SLOs. |
