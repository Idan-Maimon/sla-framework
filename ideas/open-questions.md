# Open Questions

| # | Question | Recommendation | Decider |
|---|---|---|---|
| Q1 | What happens when an SLA is missed? (internal, no money changes hands) | Written explanation + fix plan in 5 business days; escalation to department manager after 2 missed months | Department manager |
| Q2 | Tooling for SLO dashboards and burn-rate alerts | **Pyrra** (operator that watches its own CRD and generates `PrometheusRule`s, plus a budget UI) or **Sloth**. Decide after a 2-week spike on the pilot service. | Architect + ops |
| Q3 | Where are SLO documents, policies and SLAs kept? | In git next to the service code; linked from the service catalog consumers already use | Department manager |
| Q4 | Which services must adopt first? | Services that other teams depend on. Pilot: VM-as-a-Service + cluster provisioning. | Department manager |
| Q5 | Do any external customers consume our services? | If yes, legal reviews SLA wording before publishing | Department manager |
| Q6 | Should SLAs use a calendar month or a rolling window? | SLO: 4-week rolling (workbook). SLA: calendar month (easier to report). | Architect |
| Q7 | Shared platforms (e.g. OpenShift fleet) that many services depend on | The platform team publishes its SLO once; other services use it in their dependency check | Architect |
| Q8 | Does the existing OpenShift SLA work (benchmarks + Grafana) feed in? | Yes. Lab benchmarks are the starting data for new platform services (Path A, Step 3). | Architect |
| Q9 | Do we need service tiers (default targets per criticality)? | Not in v0.2: starter SLOs come from data. Revisit after the pilot if teams want defaults. | Architect |
