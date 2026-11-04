# 1 — The Basics (5-minute read)

Four words, explained with one running example: a service that gives teams **virtual machines**.

## SLI — what we measure

A **Service Level Indicator** is a number that tells us how well the service is doing *from the
user's point of view*. We always write it the same way:

```
SLI = good events ÷ total events
```

> *"Of all VM create requests, how many produced a running VM within 5 minutes?"*
> 9,950 good out of 10,000 total → **SLI = 99.5%**

Because it's always a percentage between 0% (nothing works) and 100% (nothing is broken), every
SLI looks the same, and the same dashboards and alerts work for all of them.

## SLO — the target

A **Service Level Objective** is the target for an SLI over a time window.

> *"99% of VM create requests produce a running VM within 5 minutes, over a rolling 4 weeks."*

The SLO is the line between *"users are happy"* and *"users start complaining"*.
**100% is the wrong target**: it costs a fortune, users can't tell the difference, and you could
never change anything again, because change is the biggest cause of outages.

## Error budget — the failure we're allowed

```
error budget = 100% − SLO
```

> SLO 99% → the budget is 1%. With 10,000 creates in 4 weeks, **100 may fail or be slow**.
> One bad upgrade that broke 30 creates used **30% of the budget**.

The error budget turns reliability into a simple rule everyone can follow:

| Budget left? | What we do |
|---|---|
| ✅ Yes | Ship features, upgrade, experiment. Unused budget is wasted. |
| ❌ No | Pause risky changes and fix reliability until the budget recovers. |

The budget decides, not opinions. That rule is written in the **error budget policy**.

## SLA — the promise to consumers

A **Service Level Agreement** is what we *promise* consumers, plus what happens if we break the promise.

> *"VM provisioning: 97% within 5 minutes per month. P1 support response: 15 minutes, 24×7."*

- The SLA is always **looser than the SLO**, so we notice and react before breaking the promise.
- It comes **last**: first prove the SLO works, then promise something.

## How they fit together

```
 SLI  ── measured against ──▶  SLO  ── looser version promised as ──▶  SLA
(what)                       (target)                                (promise)
                                │
                                └── 100% − SLO = error budget ──▶ error budget policy (what we do)
```

## Three roles

The workbook names three parties who must agree. In our organisation:

| Workbook role | Here | Their question |
|---|---|---|
| Product owner | **Service owner** (team manager) | "Is this target good enough for our users?" |
| Developers | **The team building the service** | "Will we really pause features when the budget runs out?" |
| Operators / SRE | **Whoever runs it and is on call** (often the same team) | "Can we meet this without heroics?" |

An **architect** reviews the SLO document for technical accuracy. Disagreements go to the
**department manager**.
