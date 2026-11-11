# SLO Document — `<Service name>`

> Based on [SRE Workbook Appendix A — Example SLO Document](https://sre.google/workbook/slo-document/).
> Keep it short. Replace everything in `<…>`. Store in git next to the service code.
> How to fill it in: [the steps](../guide/2-the-steps.md).

| | |
|---|---|
| **Status** | Draft / Approved |
| **Version** | 1.0 |
| **Authors** | `<architect>` · `<service owner>` |
| **Reviewers** | `<dev lead>` · `<on-call lead>` |
| **Approver** | `<section leader>` |
| **Approval date** | |
| **Revisit date** | `<monthly for the first quarter, then quarterly>` |

## Service overview

*Filled in at Step 1 (authors).*

`<2–4 sentences: what the service does, for whom, and its main parts.>`

**Users:** `<who>`

**Requirements:**

| ID | Requirement | What the user does |
|---|---|---|
| R1 | Provision | `<e.g. request a VM and receive it running>` |
| R2 | Modify | |
| R3 | Upgrade | |
| R4 | Decommission | |

**Diagram:** `<link or a small mermaid diagram: components, request flow, critical dependencies>`

**Critical dependencies:**

| Dependency | Needed for | Owner | Has an SLO? |
|---|---|---|---|
| `<service>` | `<R1, S2…>` | `<team>` | `<yes: 99.9% / not yet>` |

## SLIs and SLOs

*Category, SLI and specification filled in at Step 1; implementation and SLO at Step 2 (authors).*

All SLOs use a **4-week rolling window** unless stated otherwise (rare requirements may use a quarter).

**Category** is a requirement (R1…), or **Availability** for the service while it runs.
Keep **five or fewer** rows.

| ID | Category | SLI | Specification (plain words) | Implementation (how measured) | SLO |
|---|---|---|---|---|---|
| S1 | `<R1 Provision>` | Success | `<proportion of valid provision requests that reach Ready>` | `<query ÷ query; exclusions>` | `<99%>` |
| S2 | `<R1 Provision>` | Speed | `<proportion of valid provisions Ready within X>` | | `<90% < X; 99% < Y>` |
| S3 | `<R3 Upgrade>` | Success | `<proportion of upgrades completed with no manual step>` | | |
| S4 | Availability | Uptime | `<proportion of minutes the service is up and reachable>` | | |
| S5 | Availability | Response time | `<proportion of requests answered within X>` | | |

**Left out, for now:**

| Category | SLI | Why |
|---|---|---|
| `<R4 Decommission>` | `<Success>` | `<e.g. low user pain; revisit at next review>` |

## Rationale

*Filled in at Step 2 (authors).*

`<Per S-row, where the numbers come from: "rounded down from measured 4-week performance (dates)",
or "ad hoc — no data yet, revisit after 4 weeks". Mention the dependency check and any user feedback.
Be honest: if a target was chosen rather than measured, or has not yet been checked against user
experience, say so.>`

## Error budget

*Filled in at Step 2 (authors).*

| ID | SLO | Budget (window) |
|---|---|---|
| `<S1>` | `<99% provision success>` | `<1% of provisions ≈ N>` |
| `<S4>` | `<99.5% uptime>` | `<0.5% ≈ 3 h 22 min in 4 weeks>` |

How we respond when the budget is used up: see the [error budget policy](error-budget-policy.md).

## Clarifications and caveats

*Filled in at Step 2 (authors).*

- `<What is excluded from the SLIs, e.g. quota rejections, load tests, consumer-initiated stops>`
- `<Known gaps in measurement and the plan to fix them>`
- `<Aspirational SLOs, if any. Tracked, but no policy actions attached>`

## Change log

*Updated at every Step 5 review (authors + approver).*

| Version | Date | Change | Approved by |
|---|---|---|---|
| 1.0 | | First version | `<section leader>` |
