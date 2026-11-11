# 2 — The Steps

Based on the Google SRE Workbook chapter [Implementing SLOs](https://sre.google/workbook/implementing-slos/).
Read [the basics](1-the-basics.md) first if SLI / SLO / error budget are new to you.

> **Golden rule from the workbook:** *your first SLO doesn't have to be right.* Get something
> measured, agree on it, and improve it. Don't let perfect be the enemy of good.

## How it works

**The process is filling in one document.** The authors create the
[SLO document](../templates/slo-document.md) and fill it in (Steps 1–2), the approver approves it
(Step 3), and once the service is running, on-call watches it and the authors and approver keep it
right (Steps 4–5).

| Role | Who | Does |
|---|---|---|
| **Authors** | **Architect** + **service owner** (team lead or engineer) | Own the document and fill in every section, including which rows go in the SLI table |
| **Reviewers** | **Dev lead** + **on-call lead** | Confirm the targets and the error budget policy before approval |
| **Approver** | **Section leader** | Approves the document and the error budget policy; settles disagreements |
| **On-call** | Whoever runs the service and is on call | Runs the dashboard and alerts |

| Step | What | Owner |
|---|---|---|
| **1. Create the SLO document** | Service overview · SLIs: what to measure | Authors |
| **2. Build and measure for SLO targets** | SLI implementation and SLO targets · Rationale · Error budget · Clarifications and caveats · error budget policy | Authors |
| **3. Send for approval** | Reviewers confirm, approver approves | Approver |
| **4. Watch** | Dashboard and alerts | On-call |
| **5. Review and improve** | Update the document: new version, new revisit date | Authors + approver |

Steps 4 and 5 are a loop: watch, review, update the document, watch again.

---

## Step 1 — Create the SLO document

**Fills in:** the service overview, and the SLIs (category, SLI, specification) · **Owner:** authors

**Why:** SLOs exist to keep *users* happy. If you don't know who they are and what they do,
you'll measure the wrong things.

**Do:**
1. Copy the [SLO document template](../templates/slo-document.md) into the service's repository.
2. Pick **one service**. If your product has several parts, start with the most important one and add the others later.
3. Write down **who the users are**: people, teams or systems whose happiness you're optimising.
4. List the **requirements**: the business operations the service provides, each with an ID.
   Typically **R1 Provision**, **R2 Modify**, **R3 Upgrade** and **R4 Decommission**. Then ask users
   which ones matter most to them.
5. Draw a **simple diagram**: main components, how requests flow, and the critical dependencies.
6. Decide **what to measure**. Each row of the SLI table has a **category**: either an
   **requirement**, or **Availability** for the service while it runs. Each category raises two
   questions, and each question is one candidate SLI:

   | Category | Question 1 | Question 2 |
   |---|---|---|
   | A requirement (R1 Provision, R2 Modify, R3 Upgrade, R4 Decommission) | **Did it work?** | **How long did it take?** |
   | Availability | **Uptime:** is it up? | **Response time:** is it fast? |

   For a VM service:

   | Category | SLI | Good ÷ total |
   |---|---|---|
   | R1 Provision | Success | VM creates that reached Running ÷ valid creates |
   | R1 Provision | Speed | VM creates Running within 5 min ÷ valid creates |
   | R2 Modify | Success | changes applied ÷ valid changes |
   | R2 Modify | Speed | changes applied within 2 min ÷ valid changes |
   | R3 Upgrade | Success | upgrades with no manual step ÷ all upgrades |
   | R3 Upgrade | Duration | upgrades done within 1.5 h ÷ all upgrades |
   | R4 Decommission | Success | deletes completed and freed ÷ all deletes |
   | Availability | Uptime | minutes VMs are reachable ÷ minutes they should run |
   | Availability | Response time | disk I/O faster than 10 ms ÷ all I/O |

   The [SLI menu](3-sli-menu.md) has ready-made wording for each type.
7. **Choose five or fewer rows.** Keep the ones where users hurt most when they fail, and that
   happen often enough to measure. Give each chosen row an ID: **S1**, **S2**… List the ones you
   left out, with a reason, under *Left out, for now*.
   > VM service: *S1 Provision success · S2 Provision speed · S3 Availability uptime*. Decommission rarely hurts anyone → later.
8. Write each chosen SLI's **specification** in plain words: *"proportion of VM create requests
   that reach Running within 5 min"*. For "how long" SLIs, consider **two thresholds**, one for
   typical requests and one for the slow tail (e.g. 90% < 2 min **and** 99% < 5 min).

**Tip:** ask 2–3 users *"tell me about the last time this service let you down"*. Their answers
show you which requirements matter and how much delay or failure they will tolerate.

**Not measured by SLOs:** security, cost, how easy the service is to change, and similar
qualities. They are checked by pipeline checks and architecture reviews, not by counting good
and bad events. See the [cloud architecture principles](https://github.com/Idan-Maimon/cloud-architecture-principles).

**Done when:** the service overview is complete, and the SLI table has five or fewer rows (S1…)
with a specification each, plus the left-out candidates with a reason.

---

## Step 2 — Build and measure for SLO targets

**Fills in:** SLI implementation and SLO targets · Rationale · Error budget · Clarifications and caveats ·
the [error budget policy](../templates/error-budget-policy.md) · **Owner:** authors

**Why:** a target turns a measurement into a decision ("are we OK or not?"), and a target is only
as good as the measurement behind it.

**Do:**
1. **Build the measurement** for each SLI and write its **implementation**: *"from metric X in
   Prometheus, ÷ metric Y, excluding quota rejections"*.
   - **Use data you already have.** The workbook says: if logs exist today but probes would take
     weeks to build, use the logs. You can improve the measurement later.
   - **Rare requirements** (upgrade, decommission) produce few events, so one failure swings the
     percentage. Use a longer window (a quarter) or a canary that exercises them regularly.
2. **Measure 4 weeks** of each SLI.
   - *New service:* run the service (or a probe against it) for 4 weeks first. If you must set a
     target before you have data, write *"ad hoc — no data yet"* in the rationale.
3. **Round down** to a sensible number. That's your **starter SLO**.
   Workbook example: measured 97.123% → SLO **97%**; measured p90 latency 432 ms → **90% of requests < 450 ms**.
4. Use a **4-week rolling window** (the workbook's recommendation: it always contains the same number of weekends).
5. Calculate the **error budget**: `100% − SLO`, as allowed failures or minutes in the window.
6. **Check against your dependencies.** You can't be more reliable than something you can't work
   without. If the storage you depend on promises 99.9%, your SLO can't be 99.95% without extra
   engineering. See [availability math](../reference/availability-math.md).
7. Look back at the 4 weeks: **did the bad days match real incidents?** If not, adjust the SLI.
8. **Write the rationale, honestly.** If a target was chosen rather than measured, or nobody has
   checked it matches user experience yet, say so. Google's example does; it tells future readers
   where better evidence is needed.
9. Write the **clarifications and caveats**: what the measurement misses and what is excluded.
10. Fill in the **error budget policy**. The template already contains the department defaults;
    only the blanks need filling.

**Caution:** current performance is a starting point, not a limit. If users need better than
today, record it as an **aspirational SLO** (Step 5) rather than setting a target you'll miss every day.

**Done when:** every row has a working query/probe, a target, a window, an error budget and a
rationale; the clarifications and caveats and the error budget policy are filled in.

---

## Step 3 — Send for approval

**Owner:** approver (section leader)

**Why:** an SLO only works if the people who build and run the service accept it *and* accept
what happens when it's missed. Without this step, the SLO is just another report.

**Do:**
1. The authors send the SLO document and the error budget policy to the reviewers and the approver.
2. The **reviewers** confirm, in one short meeting with the authors:

   | Reviewer | Confirms that… |
   |---|---|
   | **Dev lead** | When the budget is gone, the team will pause risky work and fix reliability. |
   | **On-call lead** | The targets can be met without heroics, constant firefighting or burnout. |

3. The **approver** approves both documents and sets the revisit date.
4. Store both documents **in git, next to the service code**, and link them from the service catalog.

**If the reviewers don't agree:** that's useful. It means a target is wrong. The workbook says to
iterate: the SLO is too strict (on-call objects), too costly (developers object), or too loose (the
authors think users would suffer first). The authors adjust and resend. If it can't be settled, the approver decides.

**Done when:** both documents are approved, with names, an approval date and a revisit date.

---

## Step 4 — Watch: dashboard and alerts

**Owner:** on-call

**Why:** you need to know you're burning budget *before* it's gone.

**Do:**
1. Build an **SLO dashboard** per service: each SLI vs its target, error budget remaining, and the trend over 4 weeks.
2. Set up **error budget alerts**, not "CPU > 80%" alerts:
   - **Page** when the budget is burning fast (e.g. 2% of it gone in 1 hour)
   - **Ticket** when it's burning slowly but steadily (e.g. 10% gone in 3 days)

   Details: [Alerting on SLOs](https://sre.google/workbook/alerting-on-slos/). Tools such as Pyrra or
   Sloth generate these rules for you (see [open questions](../ideas/open-questions.md)).
3. In the **weekly team meeting**, spend 2 minutes on the dashboard: how much budget is left, and what used it?
4. In **post-incident reviews**, record how much budget each incident used. That makes incidents
   comparable: *"this outage used 30% of our budget."*

**Done when:** dashboard and alerts are live and the on-call team knows what they mean.

---

## Step 5 — Review and improve

**Owner:** authors + approver

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
   changed, the reviewers confirm and the approver approves the new version.

**Done when:** the review is recorded in the document's change log.

---

## When ready — publish the SLA

**Owner:** authors, approved by the approver

**When:** after the SLOs have been met for **about a quarter** (3 monthly reviews) and the targets are stable.

**Do:**
1. Fill in the one-page **[SLA template](../templates/sla.md)**.
2. Set each SLA target **looser than the SLO**. Default: about half a "nine" (SLO 99.5% → SLA 99%).
3. Add what consumers need to know beyond the targets: support hours and response times, planned
   maintenance, exclusions, what consumers must do themselves, and what happens on a breach.
4. Agree it with representatives of the main consumers. The approver approves it, and it is
   published in the service catalog.

> The workbook notes that an SLA is a *business* agreement: it applies when users are so unhappy
> you owe them something. Internally that means an explanation, a fix plan and escalation,
> not money (see [open questions](../ideas/open-questions.md)).

**Done when:** the SLA is published and the monthly SLA report is automated from the same metrics as the SLO dashboard.
