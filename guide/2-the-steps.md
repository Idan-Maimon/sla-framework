# 2 — The Steps

Based on the Google SRE Workbook chapter [Implementing SLOs](https://sre.google/workbook/implementing-slos/).
Read [the basics](1-the-basics.md) first if SLI / SLO / error budget are new to you.

> **Golden rule from the workbook:** *your first SLO doesn't have to be right.* Get something
> measured, agree on it, and improve it. Don't let perfect be the enemy of good.

## How it works

**The process is filling in one document.** The [SLO document](../templates/slo-document.md) has
five sections. Steps 1–3 fill them in, Step 4 approves them, and Steps 5–6 keep them right once the
service is running.

| Role | Who | Does |
|---|---|---|
| **Authors** | **Architect** + **service owner** (team lead or engineer) | Own the document and fill in every section, including which rows go in the SLI table |
| **Reviewers** | **Dev lead** + **on-call lead** | Confirm the targets and the error budget policy before approval (Step 4) |
| **Approver** | **Section leader** | Approves the document and the error budget policy; settles disagreements |

| Step | Fills in | Who |
|---|---|---|
| **1. Overview** | §1 Service overview | Authors |
| **2. SLIs** | §2 SLIs: categories, specification, implementation | Authors |
| **3. SLOs** | §2 targets · §3 Rationale · §4 Error budget | Authors |
| **4. Approve** | §5 Caveats · error budget policy → approval | Authors → reviewers → **section leader** |
| **5. Watch** | Dashboard and alerts (outside the document) | Service owner's team |
| **6. Review** | Updates §2–§4: new version, new revisit date | Authors → reviewers → **section leader** |
| **7. SLA** | The separate [SLA](../templates/sla.md), once the SLOs are stable | Authors → **section leader** |

Steps 5 and 6 are a loop: watch, review, update the document, watch again.

## Pick your path

| Path | Your service is… | Steps | Typical effort |
|---|---|---|---|
| **A · New** | Not in production yet | 1 → 2 during design · add measurement before go-live · 3 after the first 4 weeks of data · 4 → 6 · 7 after ~1 quarter | Spread over the launch |
| **B · Running, no SLOs** | In production with monitoring, no formal targets | 1 → 6 in one go · 7 after ~1 quarter | 2–4 weeks part-time |
| **C · Running, SLO without teeth** | Has an SLO or SLA, but nobody acts on misses | 4 (approve + policy) → 5 → 6, re-checking SLIs in 6 | 1–2 weeks |

---

## Step 1 — Overview: who uses it, and for what?

**Fills in:** §1 Service overview · **Who:** authors

**Why:** SLOs exist to keep *users* happy. If you don't know who they are and what they do,
you'll measure the wrong things.

**Do:**
1. Pick **one service**. If your product has several parts, start with the most important one and add the others later.
2. Write down **who the users are**: people, teams or systems whose happiness you're optimising.
3. List the **operations** the service provides: typically *create*, *change*, *upgrade* and
   *delete*. Then ask users which ones matter most to them (the workbook calls these *critical user journeys*).
4. Draw a **simple diagram**: main components, how requests flow, and the critical dependencies.

**Tip:** ask 2–3 users *"tell me about the last time this service let you down"*. Their answers
show you which operations matter and how much delay or failure they will tolerate.

**Done when:** §1 lists the users, the operations, the diagram and the critical dependencies.

---

## Step 2 — SLIs: what do we measure?

**Fills in:** §2 SLIs (categories, specification, implementation) · **Who:** authors

**Why:** an SLI is how you know, with numbers, whether users are getting what they need.

**Do:**
1. Each row of the SLI table has a **category**: either an **operation** from Step 1, or
   **Availability** for the service while it runs. Each category raises two questions, and each
   question is one candidate SLI:

   | Category | Question 1 | Question 2 |
   |---|---|---|
   | An operation (Create, Change, Upgrade, Delete) | **Did it work?** | **How long did it take?** |
   | Availability | **Uptime:** is it up? | **Response time:** is it fast? |

   For a VM service:

   | Category | SLI | Good ÷ total |
   |---|---|---|
   | Create | Success | creates that reached Running ÷ valid creates |
   | Create | Speed | creates Running within 5 min ÷ valid creates |
   | Change | Success | changes applied ÷ valid changes |
   | Change | Speed | changes applied within 2 min ÷ valid changes |
   | Upgrade | Success | upgrades with no manual step ÷ all upgrades |
   | Upgrade | Duration | upgrades done within 1.5 h ÷ all upgrades |
   | Delete | Success | deletes completed and freed ÷ all deletes |
   | Availability | Uptime | minutes VMs are reachable ÷ minutes they should run |
   | Availability | Response time | disk I/O faster than 10 ms ÷ all I/O |

   The [SLI menu](3-sli-menu.md) has ready-made wording for each type.
2. **Choose five or fewer rows.** Keep the ones where users hurt most when they fail, and that
   happen often enough to measure. List the ones you left out, with a reason, in §2.
   > VM service: *Create success · Create speed · Availability uptime*. Delete rarely hurts anyone → later.
3. Write each SLI in two parts:
   - **Specification**, in plain words: *"proportion of VM create requests that reach Running within 5 min"*
   - **Implementation**, how you measure it: *"from metric X in Prometheus, ÷ metric Y, excluding quota rejections"*
4. **Use data you already have.** The workbook says: if logs exist today but probes would take
   weeks to build, use the logs. You can improve the measurement later.
5. For "how long" SLIs, consider **two thresholds**, one for typical requests and one for the slow tail
   (e.g. 90% < 2 min **and** 99% < 5 min).
6. **Rare operations** (upgrades, deletes) produce few events, so one failure swings the percentage.
   Use a longer window (a quarter) or a canary that runs the operation regularly.

**Not measured by SLOs:** security, cost, how easy the service is to change, and similar
qualities. They are checked by pipeline checks and architecture reviews, not by counting good
and bad events. See the [cloud architecture principles](https://github.com/Idan-Maimon/cloud-architecture-principles).

**Done when:** every chosen row has a specification and a working query/probe, and the left-out
candidates are listed with a reason.

---

## Step 3 — SLOs: set starter targets

**Fills in:** §2 SLO column · §3 Rationale · §4 Error budget · **Who:** authors

**Why:** a target turns a measurement into a decision ("are we OK or not?").

**Do:**
1. **Measure the last 4 weeks** of each SLI.
   - *Path A (new):* run the service (or a probe against it) for 4 weeks first. If you must set a
     target before you have data, write *"ad hoc — no data yet"* in the rationale.
2. **Round down** to a sensible number. That's your **starter SLO**.
   Workbook example: measured 97.123% → SLO **97%**; measured p90 latency 432 ms → **90% of requests < 450 ms**.
3. Use a **4-week rolling window** (the workbook's recommendation: it always contains the same number of weekends).
4. Calculate the **error budget**: `100% − SLO`, as allowed failures or minutes in 4 weeks.
5. **Check against your dependencies.** You can't be more reliable than something you can't work
   without. If the storage you depend on promises 99.9%, your SLO can't be 99.95% without extra
   engineering. See [availability math](../reference/availability-math.md).
6. Look back at the 4 weeks: **did the bad days match real incidents?** If not, adjust the SLI.
7. **Be honest in the rationale.** If a target was chosen rather than measured, or nobody has
   checked it matches user experience yet, say so. Google's example does; it tells future readers
   where better evidence is needed.

**Caution:** current performance is a starting point, not a limit. If users need better than
today, record it as an **aspirational SLO** (Step 6) rather than setting a target you'll miss every day.

**Done when:** each row has a target, a window, an error budget and a written rationale.

---

## Step 4 — Approve

**Fills in:** §5 Clarifications and caveats · the [error budget policy](../templates/error-budget-policy.md) ·
**Who:** authors, then reviewers, then the section leader

**Why:** an SLO only works if the people who build and run the service accept it *and* accept
what happens when it's missed. Without this step, the SLO is just another report.

**Do:**
1. The authors complete **§5** (what the measurement misses, what is excluded) and fill in the
   **error budget policy**. The policy template already contains the department defaults; only the blanks need filling.
2. The **reviewers** confirm, in one short meeting with the authors:

   | Reviewer | Confirms that… |
   |---|---|
   | **Dev lead** | When the budget is gone, the team will pause risky work and fix reliability. |
   | **On-call lead** | The targets can be met without heroics, constant firefighting or burnout. |

3. The **section leader** approves the SLO document and the error budget policy.
4. Store both documents **in git, next to the service code**, and link them from the service catalog.

**If the reviewers don't agree:** that's useful. It means a target is wrong. The workbook says to
iterate: the SLO is too strict (on-call objects), too costly (developers object), or too loose (the
authors think users would suffer first). Adjust and try again. If it can't be settled, the section leader decides.

**Done when:** both documents are approved, with names, an approval date and a revisit date.

---

## Step 5 — Watch: dashboard and alerts

**Who:** the service owner's team

**Why:** you need to know you're burning budget *before* it's gone.

**Do:**
1. Build an **SLO dashboard** per service: each SLI vs its target, error budget remaining, and the trend over 4 weeks.
2. Set up **error budget alerts**, not "CPU > 80%" alerts:
   - **Page** when the budget is burning fast (e.g. 2% of it gone in 1 hour)
   - **Ticket** when it's burning slowly but steadily (e.g. 10% gone in 3 days)

   Details: [Alerting on SLOs](https://sre.google/workbook/alerting-on-slos/). Tools such as Pyrra or
   Sloth generate these rules for you (see [open questions](../ideas/open-questions.md)).
3. In your **weekly team meeting**, spend 2 minutes on the dashboard: how much budget is left, and what used it?
4. In **post-incident reviews**, record how much budget each incident used. That makes incidents
   comparable: *"this outage used 30% of our budget."*

**Done when:** dashboard and alerts are live and the on-call team knows what they mean.

---

## Step 6 — Review and improve

**Updates:** §2–§4, version and revisit date · **Who:** authors, then reviewers, then the section leader

**Why:** your first SLO is a guess backed by data. Reviews make it right.

**When:** on the document's **revisit date**: **monthly** for the first quarter, then **quarterly**.

**Do:**
1. **Compare the SLO with reality.** Count incidents, tickets and user complaints. Did the bad
   periods show up as budget drops? Did budget drops match real user pain?
2. **Decide using the table below** (simplified from the workbook's decision matrix):

   | SLO | Users | Do this |
   |---|---|---|
   | Met | Happy | Fine. Consider moving faster or spending effort elsewhere. |
   | Met | Unhappy | SLO too loose or measuring the wrong thing → **tighten** it or **fix the SLI** |
   | Missed | Happy | SLO too strict → **loosen** it |
   | Missed | Unhappy | Real problem → follow the **error budget policy**, fix reliability |

   If running the service takes a lot of manual work (toil), fix that as well. See the full matrix in [reference](../reference/decision-matrix.md).
3. **Improve the measurement** if it misses real problems: measure closer to the user (load balancer
   or probe instead of server logs), or add a row the table is missing.
4. **Aspirational SLO:** if users need more than you can deliver today, track the better target
   alongside the real one, with no policy actions attached, until you can meet it.
5. Update the SLO document: new version, new revisit date, a line in the change log. If targets
   changed, the reviewers confirm and the section leader approves again.

**Done when:** the review is recorded in the document's change log.

---

## Step 7 — Publish the SLA (when ready)

**Who:** authors, then the section leader

**Why:** consumers need a clear promise they can plan around. But promise only what you've proven.

**When:** after the SLO has been met for **about a quarter** (3 monthly reviews) and the targets are stable.

**Do:**
1. Fill in the one-page **[SLA template](../templates/sla.md)**.
2. Set each SLA target **looser than the SLO**. Default: about half a "nine" (SLO 99.5% → SLA 99%).
3. Add what consumers need to know beyond the targets: support hours and response times, planned
   maintenance, exclusions, what consumers must do themselves, and what happens on a breach.
4. Agree it with representatives of the main consumers. The section leader approves it, and it is
   published in the service catalog.

> The workbook notes that an SLA is a *business* agreement: it applies when users are so unhappy
> you owe them something. Internally that means an explanation, a fix plan and escalation,
> not money (see [open questions](../ideas/open-questions.md)).

**Done when:** the SLA is published and the monthly SLA report is automated from the same metrics as the SLO dashboard.
