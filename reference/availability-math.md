# Availability Math (reference)

## Allowed downtime per target

| SLO | Per 4 weeks | Per 30 days | Per quarter (91 d) | Per year |
|---|---|---|---|---|
| 99%    | 6 h 43 m  | 7 h 12 m  | 21 h 50 m | 3 d 15 h 36 m |
| 99.5%  | 3 h 22 m  | 3 h 36 m  | 10 h 55 m | 1 d 19 h 48 m |
| 99.9%  | 40 m 19 s | 43 m 12 s | 2 h 11 m  | 8 h 45 m |
| 99.95% | 20 m 10 s | 21 m 36 s | 1 h 5 m   | 4 h 22 m |
| 99.99% | 4 m 2 s   | 4 m 19 s  | 13 m 6 s  | 52 m 34 s |

Calculator: <https://uptime.is> · Google's table: <https://sre.google/sre-book/availability-table/>

> **Reality check:** detecting, paging, acknowledging and diagnosing an incident takes about 30
> minutes. A 99.99% monthly target (4 minutes) needs fully automatic detection and repair.

## Dependencies

If your service **can't work without** A and B, it can be at most as reliable as both together:

```
best possible = A × B × (your own components)
```

Example: OpenShift 99.9% × storage 99.9% × network 99.95% ≈ **99.75%**, before counting any of
your own failures.

**Redundancy:** two copies can raise this in theory (`1 − (1−A)(1−B)`), but the workbook warns the
math is *"deceptive"* unless the copies truly share nothing: no common control plane, network,
storage or release process.

Workbook rule: a critical dependency must be **at least as reliable** as the task that depends on
it. If it isn't, add defences (caching, retries, graceful degradation) or choose a different
component.

Further reading: [The Calculus of Service Availability (ACM Queue)](https://queue.acm.org/detail.cfm?id=3096459)
