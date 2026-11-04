# SLO Decision Matrix (reference)

The full version of the table in Step 6, from SRE Workbook Table 2-5. Use it in reviews.
*Toil* means repetitive manual work needed to keep the service running.

| SLO | Toil | Users | Action |
|---|---|---|---|
| Met | Low | Happy | Either move faster (relax release processes), or spend engineering time on services that need it more |
| Met | Low | Unhappy | Tighten the SLO |
| Met | High | Happy | If alerts are false positives, make them less sensitive. Otherwise loosen the SLO temporarily (or offload toil) and fix the product / automate recovery |
| Met | High | Unhappy | Tighten the SLO |
| Missed | Low | Happy | Loosen the SLO |
| Missed | Low | Unhappy | Make alerting more sensitive |
| Missed | High | Happy | Loosen the SLO |
| Missed | High | Unhappy | Offload toil, and fix the product / automate recovery |

## How to judge "users happy?"

Start cheap (from the workbook):
- Count support tickets and complaints per week, and check whether they line up with error budget drops.
- Count outages found by people rather than by alerts.
- Ask the service owner to include reliability in their regular conversations with consumers.
