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

## 1. Service overview

*Filled in at Step 1 (authors).*

`<2–4 sentences: what the service does, for whom, and its main parts.>`

**Users:** `<who>`

**Operations** (critical user journeys):

| Operation | What the user does |
|---|---|
| `<Create>` | `<e.g. request a VM and receive it running>` |
| `<Change>` | |
| `<Upgrade>` | |
| `<Delete>` | |

**Diagram:** `<link or a small mermaid diagram: components, request flow, critical dependencies>`

**Critical dependencies:** `<service → their SLO/SLA if known>`

## 2. SLIs and SLOs

*Category, SLI and specification filled in at Step 1; implementation and SLO at Step 2 (authors).*

All SLOs use a **4-week rolling window** unless stated otherwise (rare operations may use a quarter).

**Category** is an operation from §1, or **Availability** for the service while it runs.
Keep **five or fewer** rows.

| Category | SLI | Specification (plain words) | Implementation (how measured) | SLO |
|---|---|---|---|---|
| `<Create>` | Success | `<proportion of valid create requests that reach Ready>` | `<query ÷ query; exclusions>` | `<99%>` |
| `<Create>` | Speed | `<proportion of valid creates Ready within X>` | | `<90% < X; 99% < Y>` |
| `<Upgrade>` | Duration | `<proportion of upgrades done within X>` | | |
| Availability | Uptime | `<proportion of minutes the service is up and reachable>` | | |
| Availability | Response time | `<proportion of requests answered within X>` | | |

**Left out, for now:**

| Category | SLI | Why |
|---|---|---|
| `<Delete>` | `<Success>` | `<e.g. low user pain; revisit at next review>` |

## 3. Rationale

*Filled in at Step 2 (authors).*

`<Where the numbers come from: "rounded down from measured 4-week performance (dates)", or
"ad hoc — no data yet, revisit after 4 weeks". Mention the dependency check and any user feedback.
Be honest: if a target was chosen rather than measured, or has not yet been checked against user
experience, say so.>`

## 4. Error budget

*Filled in at Step 2 (authors).*

| SLO | Budget (window) |
|---|---|
| `<99% create success>` | `<1% of creates ≈ N creates>` |
| `<99.5% uptime>` | `<0.5% ≈ 3 h 22 min in 4 weeks>` |

How we respond when the budget is used up: see the [error budget policy](error-budget-policy.md).

## 5. Clarifications and caveats

*Filled in at Step 2 (authors).*

- `<What is excluded from the SLIs, e.g. quota rejections, load tests, consumer-initiated stops>`
- `<Known gaps in measurement and the plan to fix them>`
- `<Aspirational SLOs, if any. Tracked, but no policy actions attached>`

## 6. Change log

*Updated at every Step 5 review (authors + approver).*

| Version | Date | Change | Approved by |
|---|---|---|---|
| 1.0 | | First version | `<section leader>` |
