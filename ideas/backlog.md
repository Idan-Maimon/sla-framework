# Ideas Backlog

- **Training session:** a 2-hour workshop for team leads based on Google's *Art of SLOs*, using the KubeVirt example.
- **Department overview dashboard:** every service's SLO status and remaining budget on one page (like the workbook's compliance report, Figure 2-3).
- **Automated monthly SLA report** generated from the same Prometheus data as the dashboards.
- **CI check** for SLO documents: required fields filled in, review date not expired.
- **Advanced topics from the workbook** (later, once basics are in place):
  - critical user journeys measured end-to-end with probes
  - different SLOs per consumer class (e.g. production vs test namespaces)
  - SLOs between internal teams for dependencies
- **Release gating:** CI/CD blocks non-urgent deploys when the error budget is used up.
- **Optional service tiers** if teams ask for default targets (see open question Q9).
