# 3 — SLI Menu

Pick from here instead of inventing your own. The first three types come from the SRE Workbook
(Table 2-1). *Provisioning* and *running resource* are added for platform services like ours.

Every SLI is written as **good events ÷ total events**.

## Which type is my service?

| Type | The user… | Examples |
|---|---|---|
| **Request-driven** | sends a request and expects a response | APIs, portals, registries, DNS |
| **Provisioning** | asks for something to be created or changed and waits for it | create VM / cluster / namespace / volume |
| **Running resource** | expects something they got to keep working | a running VM, a cluster, a database |
| **Pipeline** | expects data to be processed and delivered somewhere | CI/CD, backups, ETL, log/metric pipelines |
| **Storage** | expects stored data to be there when they read it | block, file and object storage |

Most services are a mix. A VM service, for example, is *provisioning* + *running resource*.

## The menu

| Type | SLI | Good ÷ total |
|---|---|---|
| Request-driven | **Availability** | successful requests ÷ all requests (5xx and timeouts = bad) |
| Request-driven | **Latency** | requests faster than threshold ÷ all requests |
| Request-driven | **Quality** | responses served without degradation ÷ all responses |
| Provisioning | **Success** | requests that reach *Ready* ÷ valid requests (exclude quota denials and invalid input) |
| Provisioning | **Speed** | requests ready within threshold ÷ valid requests |
| Running resource | **Uptime** | minutes the resource is up and reachable ÷ minutes it should be running |
| Running resource | **Interruptions** | resources not interrupted unplanned ÷ all resources |
| Pipeline | **Freshness** | data reads where data is newer than threshold ÷ all reads |
| Pipeline | **Correctness** | records with correct output ÷ all records |
| Pipeline | **Coverage** | jobs/records processed successfully in time ÷ all |
| Storage | **Durability** | records written that can be read back ÷ records written |
| Storage | **Latency** | I/O operations faster than threshold ÷ all operations |

Support response times (P1/P2…) are not SLIs from telemetry. They come from the ticketing tool and belong in the **SLA** (Step 7).

## Where to measure

From the workbook, in order of how close each is to the user:

| Source | Good | Watch out |
|---|---|---|
| **Server / application metrics** | Already there, cheap | Misses failures in front of the server |
| **Load balancer / ingress** | Closer to users, already there | Misses DNS and network issues |
| **Synthetic probes** (a script doing the user task every few minutes) | Tests the real journey; works with no traffic | A sample, not real users; needs maintenance |
| **Client-side** | The truest user view | Hardest to build |

**Start with what you already have.** For low-traffic services, add a probe; otherwise a single
failure swings the percentage too much.
