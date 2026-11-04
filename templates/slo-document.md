# SLO Document — `<Service name>`

> Based on [SRE Workbook Appendix A — Example SLO Document](https://sre.google/workbook/slo-document/).
> Keep it short. Replace everything in `<…>`. Store in git next to the service code.

| | |
|---|---|
| **Status** | Draft / Approved |
| **Version** | 1.0 |
| **Authors** | `<who wrote it>` |
| **Reviewers** | `<architect + engineers who checked technical accuracy>` |
| **Approvers** | `<service owner>` · `<dev lead>` · `<ops/on-call lead>` |
| **Approval date** | |
| **Next review** | `<monthly for the first quarter, then quarterly>` |
| **Path** | A (new) / B (running, no SLO) / C (SLO without teeth) |

## 1. Service overview

`<2–4 sentences: what the service does, for whom, and its main parts.>`

**Users:** `<who>`
**Key tasks (critical user journeys):**
1. `<task>`
2. `<task>`
3. `<task>`

**Diagram:** `<link or a small mermaid diagram: components, request flow, critical dependencies>`

**Critical dependencies:** `<service → their SLO/SLA if known>`

## 2. SLIs and SLOs

All SLOs use a **4-week rolling window**.

| Task | SLI type | Specification (plain words) | Implementation (how measured) | SLO |
|---|---|---|---|---|
| `<task 1>` | Availability | `<proportion of … that …>` | `<metric/query ÷ metric/query; exclusions>` | `<99%>` |
| `<task 1>` | Latency | `<proportion of … faster than X>` | | `<90% < X; 99% < Y>` |
| `<task 2>` | | | | |

## 3. Rationale

`<Where the numbers come from: "rounded down from measured 4-week performance (dates)", or
"ad hoc — no data yet, revisit after 4 weeks". Mention the dependency check and any user feedback.>`

## 4. Error budget

| SLO | Budget (4 weeks) |
|---|---|
| `<99% availability>` | `<1% of requests ≈ N requests>` or `<6 h 43 min>` |

How we respond when the budget is used up: see the [error budget policy](error-budget-policy.md).

## 5. Clarifications and caveats

- `<What is excluded from the SLIs, e.g. quota rejections, load tests, consumer-initiated stops>`
- `<Known gaps in measurement and the plan to fix them>`
- `<Aspirational SLOs, if any. Tracked, but no policy actions attached>`

## 6. Change log

| Version | Date | Change | Approved by |
|---|---|---|---|
| 1.0 | | First version | |
