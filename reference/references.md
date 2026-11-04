# References

## Core: what this framework is based on

| Source | Use |
|---|---|
| **SRE Workbook ch. 2 — Implementing SLOs** · <https://sre.google/workbook/implementing-slos/> | The method: Steps 1–6 follow this chapter |
| SRE Workbook App. A — Example SLO Document · <https://sre.google/workbook/slo-document/> | Basis of `templates/slo-document.md` |
| SRE Workbook App. B — Example Error Budget Policy · <https://sre.google/workbook/error-budget-policy/> | Basis of `templates/error-budget-policy.md` |
| SRE Workbook ch. 5 — Alerting on SLOs · <https://sre.google/workbook/alerting-on-slos/> | Step 5: burn-rate alerts |
| SRE Book ch. 4 — Service Level Objectives · <https://sre.google/sre-book/service-level-objectives/> | SLI/SLO/SLA definitions; SLA looser than SLO (Step 7) |
| SRE Book ch. 3 — Embracing Risk · <https://sre.google/sre-book/embracing-risk/> | Why 100% is the wrong target; error budgets |

## Training material

- **The Art of SLOs** (Google workshop with slides and exercises, good for onboarding team leads): <https://sre.google/resources/practices-and-processes/art-of-slos/>
- Alex Hidalgo, *Implementing Service Level Objectives* (O'Reilly, 2020): <https://www.oreilly.com/library/view/implementing-service-level/9781492076803/>

## Enterprise alignment (optional reading)

The framework is SRE-based but compatible with these:
- ITIL 4 Service Level Management practice: <https://www.axelos.com/resource-hub/practice/service-level-management-itil-4-practice-guide>
- ISO/IEC 20000-1:2018 (service management): <https://www.iso.org/standard/70636.html>
- ISO/IEC 19086-1:2016 (cloud SLA framework): <https://www.iso.org/standard/67545.html>

## Tooling (Prometheus-based)

- Pyrra (SLO operator + UI): <https://github.com/pyrra-dev/pyrra>
- Sloth (SLO rule generator): <https://sloth.dev/>
- OpenSLO (vendor-neutral SLO file format; not a CRD): <https://github.com/OpenSLO/OpenSLO>

## Other

- The Calculus of Service Availability (ACM Queue): <https://queue.acm.org/detail.cfm?id=3096459>
- Uptime calculator: <https://uptime.is/>
- Cloud SLA examples (wording, exclusions): [Google Cloud](https://cloud.google.com/terms/sla) · [AWS](https://aws.amazon.com/legal/service-level-agreements/) · [Red Hat support SLAs](https://access.redhat.com/support/offerings/production/sla)
