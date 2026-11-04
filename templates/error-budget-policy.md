# Error Budget Policy — `<Service name>`

> Based on [SRE Workbook Appendix B — Example Error Budget Policy](https://sre.google/workbook/error-budget-policy/).
> **The text below is the department default.** Fill in the `<…>` blanks. Change a rule only if
> you have a reason, and write the reason down.

| | |
|---|---|
| **Authors** | |
| **Reviewers** | |
| **Approvers** | `<service owner>` · `<dev lead>` · `<ops/on-call lead>` |
| **Approval date** / **Next review** | |
| **Applies to** | SLOs in `<link to SLO document>` |

## Service overview

`<One or two sentences. How often do changes and releases happen?>`

## Goals

- Protect users from repeated SLO misses.
- Give the team a fair, data-based way to balance reliability work with feature work.

## Non-goals

This policy is **not a punishment** for missing SLOs. Stopping change is undesirable. The policy
gives the team permission to focus on reliability when the data says it's needed.

## When the SLO is met

Releases and changes continue as normal.

## When the error budget is used up (SLO missed over the last 4 weeks)

- **Freeze** all changes and releases except urgent (P1) fixes and security patches, until the service is back within its SLO.
- The team **must** work on reliability if:
  - a bug or a procedural mistake in our own service used up the budget, or
  - a post-incident review shows a dependency we could make less critical.
- The team **may** continue feature work if:
  - the cause was a company-wide outage (e.g. data-center network), or
  - the cause was another team's service *and that team has frozen its own changes*, or
  - the budget was used by traffic outside the SLO's scope (load tests, security scans).

## Big incidents

- An incident that uses **more than 20% of the budget** in 4 weeks gets a post-incident review with at least one high-priority action item.
- The **same kind of problem** using more than 20% of the budget over a quarter becomes a top-priority item in the next quarter's plan.

## Escalation

If people disagree on the budget calculation or on the actions above, the **department manager**
decides.

## Background (for readers new to error budgets)

An error budget is `100% − SLO`. With a 99.9% SLO and 1,000,000 requests in 4 weeks, up to 1,000
can fail before the SLO is missed. While budget remains, the team can take risks. When it's gone,
the team reduces risk until it recovers.
