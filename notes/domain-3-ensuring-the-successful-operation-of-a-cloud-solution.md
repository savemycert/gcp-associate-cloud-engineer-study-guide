# Domain 3: Ensuring the successful operation of a cloud solution (30%)

Tied for the largest domain on the ACE exam. It covers day-2 operations on compute, storage, databases, and networks, plus Cloud Monitoring and Cloud Logging. Questions usually describe a symptom or request and ask for the tool, command, or setting that fixes it.

## Managing compute resources

- **Access:**
  - `gcloud compute ssh` handles the key for you.
  - `instances list --filter=status=RUNNING` and `describe` show what's running.
  - **No external IP** → add `--tunnel-through-iap`. It needs:
    - Ingress on TCP 22 from `35.235.240.0/20`.
    - `roles/iap.tunnelResourceAccessor`.
    - No bastion.
  - Windows: `reset-windows-password`, then RDP.
- **Snapshot vs image:**
  - **Snapshot** = disk backup.
    - Incremental, and safe while the disk is in use.
    - Restore with `disks create --source-snapshot`.
    - Automate with a **snapshot schedule** resource policy (with retention) attached through `disks add-resource-policies`.
  - **Custom image** = golden build for new VMs. **Image families** always point to the newest image.
- **GKE inventory:** `clusters get-credentials`, then `kubectl get nodes|pods --all-namespaces|services`. Use `kubectl describe pod` for events on `Pending` or `ImagePullBackOff`.
- **Artifact Registry:**
  - Nodes pull as the **node service account**, which needs `roles/artifactregistry.reader`.
  - Most common failure: the repository is in **another project** and the grant was never added there.
  - Also check the image path and that access scopes weren't narrowed.
- **Node pools** (Standard only):
  - Every node in a pool shares one configuration. You can't change the machine type in place, so add a new pool and migrate.
  - Deleting a pool drains its nodes.
  - The **cluster autoscaler** (`--enable-autoscaling --min-nodes --max-nodes`) reacts to **pending Pods**, not CPU. Pods without requests never trigger it.
- **Kubernetes objects:**
  - **Deployment**: interchangeable replicas. `set image` rolls out, `rollout undo` reverts.
  - **StatefulSet**: stable names (`db-0`), a volume claim per replica, ordered start and stop.
  - **Service** types: `ClusterIP` (internal), `NodePort`, `LoadBalancer` (external passthrough LB). Ingress or Gateway → Application LB.
- **Autoscalers:**

| Scaler | Changes | Driven by |
|---|---|---|
| Horizontal Pod Autoscaler | Replica count (`kubectl autoscale`) | CPU, memory, custom or external metrics |
| Vertical Pod Autoscaler | Pod CPU and memory requests (Off, Initial, Auto) | Observed usage |
| Cluster autoscaler | Nodes in a pool | Pending Pods, idle nodes |

- VPA `Auto` evicts Pods. Never let HPA scale on a CPU or memory signal VPA is also tuning.
- **Autopilot:** you manage no node pools. Billing follows Pod **requests**, missing requests get defaults, and out-of-range requests are adjusted.
- **Cloud Run:**
  - Every deploy creates an immutable **revision** that takes all traffic by default.
  - Canary: deploy with `--no-traffic`, then `services update-traffic --to-revisions=v1=95,v2=5`, and finish with `--to-latest`.
  - Rollback = move traffic back. `--tag` gives a revision its own test URL.
  - Functions split traffic the same way. GKE splits with two Deployments or weighted Gateway routes.
  - Scaling knobs:
    - `--min-instances`: avoid cold starts.
    - `--max-instances`: protect the backend.
    - `--concurrency`: requests per instance.
- **Accelerators:**
  - A Compute Engine GPU (`--accelerator`) requires `--maintenance-policy=TERMINATE`. A- and G-series machine types include GPUs.
  - GPU quota is zonal.
  - GKE: GPU node pools, with Pods requesting `nvidia.com/gpu`.
  - TPUs: `compute tpus tpu-vm create`, or through GKE.
- **Other surfaces:**
  - **Agent Runtime** hosts agents with no infrastructure.
  - **Workbench** notebooks bill until stopped.
  - **BigQuery Studio** notebooks.
  - **Cloud Workstations** gives standardized dev environments with idle shutdown.

📖 Full lesson: [Managing Compute Engine, GKE, and Cloud Run: Day-2 Operations](https://www.savemycert.com/revision/gcp-associate-cloud-engineer/gcp-managing-compute-gke-instances-autoscaling/?utm_source=github&utm_medium=readme&utm_campaign=gcp-associate-cloud-engineer-study-guide)

## Managing storage and data solutions

- **Securing buckets:**
  - **Uniform bucket-level access** makes IAM the only control. ACLs are legacy.
  - **Public access prevention** blocks `allUsers`.
  - **Signed URL**: expiring access to one object for someone without a Google identity. Use it instead of a public bucket.
  - **Versioning** keeps overwritten or deleted data as noncurrent versions (`ls --all-versions`).
  - Compliance holds → a retention policy or Object Lock.
- **Lifecycle rules** (`buckets update --lifecycle-file`):
  - Actions: `SetStorageClass` (colder only), `Delete`, `AbortIncompleteMultipartUpload`.
  - Conditions: `age`, `createdBefore`, `matchesStorageClass`, plus `isLive`, `numNewerVersions`, and `daysSinceNoncurrentTime` for versions.
  - Trap: moving frequently read data cold raises cost through retrieval fees and the 30/90/365-day minimums. Unknown patterns → **Autoclass**.
- **Query tool per service:**

| Service | Tool |
|---|---|
| Cloud SQL | `gcloud sql connect` or the Cloud SQL Auth Proxy |
| AlloyDB | `psql` via the AlloyDB Auth Proxy, or AlloyDB Studio |
| BigQuery | `bq query --use_legacy_sql=false`; `--dry_run` shows bytes scanned for free |
| Spanner | `gcloud spanner databases execute-sql` or Spanner Studio |
| Bigtable | `cbt` or client libraries (no native SQL) |
| Firestore | Client libraries or the console query builder |

- **Costs:**
  - Planning → pricing calculator. Existing spend → billing reports or the BigQuery export.
  - Cloud Storage = GiB per class + operations + retrieval + early deletes.
  - BigQuery = storage (lower rate after 90 days unmodified) + compute, either **on-demand** per TiB scanned or **capacity** slots. Partitioning and clustering reduce scanned bytes.
  - Cloud SQL and AlloyDB bill for provisioned resources. Bigtable bills per node.
- **Backups:**
  - Cloud SQL: automated daily plus on-demand (`gcloud sql backups create`).
  - **PITR** needs automated backups + transaction logs, and restores to a **new instance** at a chosen timestamp.
  - AlloyDB: continuous backup, restored to a new cluster. Spanner: backups to a new database.
  - Firestore: backups, PITR, exports. Bigtable: table backups, no general PITR.
  - Backup = saved state. PITR = chosen moment. Export = portable copy.
- **Jobs:**
  - Dataflow: `gcloud dataflow jobs list|describe`. **Drain** finishes in-flight data, **cancel** can drop it.
  - BigQuery: `bq ls -j`, `bq show -j JOB_ID` for errors, and `INFORMATION_SCHEMA.JOBS` across many jobs.
- **Database Center:**
  - Fleet-wide database inventory with availability, security, performance, and cost findings.
  - A single slow query → **Query Insights** instead.
- **CMEK:**
  - Create a Cloud KMS key ring and key.
  - Grant the service's **service agent** `roles/cloudkms.cryptoKeyEncrypterDecrypter`.
  - Attach the key: a bucket default key, or at creation for Cloud SQL, BigQuery, and Spanner.
  - The locations must be compatible.
  - Disabling the key or removing the grant → data unreadable. Destroyed versions → permanent loss. Rotation is safe.

📖 Full lesson: [Managing Cloud Storage and Databases: Lifecycle, Backups, and CMEK](https://www.savemycert.com/revision/gcp-associate-cloud-engineer/gcp-managing-cloud-storage-databases-cmek/?utm_source=github&utm_medium=readme&utm_campaign=gcp-associate-cloud-engineer-study-guide)

## Managing networking resources

- **Subnet full:**
  - `subnets expand-ip-range SUBNET --region=R --prefix-length=21`. In place, no downtime.
  - The new range can't overlap other subnets, peers, or on-premises ranges.
  - **No shrinking**: to go smaller, create a new subnet and migrate.
  - Auto mode subnets top out at `/16` until the network is converted to custom mode.
- **Static IPs** (`gcloud compute addresses create`):
  - External: regional, or `--global` for global LBs.
  - Internal: `--subnet`, optionally with a fixed `--addresses`.
  - **Promote** an in-use ephemeral IP by passing it to `--addresses`. Nothing is interrupted.
  - An unattached reserved external IP costs more, so release unused ones.
- **Static routes** (`routes create`):
  - Next hop: internet gateway, an instance (`--can-ip-forward`), an internal IP, an internal passthrough LB, or a VPN tunnel. `--tags` scopes a route to tagged VMs.
  - The most specific prefix wins, then the lowest priority number.
  - **Subnet routes can't be overridden.**
- **Cloud DNS:**
  - **Public zone**: delegate at the registrar to the zone's name servers.
  - **Private zone** (`--visibility=private --networks=VPC`): answers only those VPCs.
  - Record sets: name, type, TTL, data.
  - Also: forwarding zones (to on-premises), peering zones, split horizon.
- **Cloud NAT:**
  - Outbound-only internet for resources with no external IP, configured on a regional **Cloud Router**.
  - **Manual** NAT IPs give partners fixed addresses to allowlist. Auto-allocated ones can change.
  - VMs with their own external IP skip NAT.
  - Port exhaustion → more NAT IPs or more minimum ports per VM.
  - **Private Google Access** reaches Google APIs without NAT.
- **Firewall rules:**
  - Stateful, lower number wins, default priority 1000.
  - Prefer service-account targets (tags only need `setTags`).
  - Carve exceptions with a narrower, higher-priority deny.
  - `--enable-logging` records matches. The implied rules never log.
- **Cloud NGFW:**
  - Order: hierarchical (organization, then folder) first. Then, by default, VPC rules, then global, then regional network policies.
  - `goto_next` passes the decision down. A hierarchical deny can't be undone below.
  - Tiers: Essentials, Standard (FQDN, geolocation, threat intelligence), Enterprise (intrusion prevention with TLS inspection).

📖 Full lesson: [Managing VPC Networking: Subnets, Static IPs, Cloud DNS, and Cloud NAT](https://www.savemycert.com/revision/gcp-associate-cloud-engineer/gcp-managing-vpc-networking-dns-nat/?utm_source=github&utm_medium=readme&utm_campaign=gcp-associate-cloud-engineer-study-guide)

## Monitoring and logging

- **Metrics vs logs:**
  - Numbers over time → **Cloud Monitoring** (dashboards, alerts, uptime checks, PromQL).
  - Discrete events → **Cloud Logging** (buckets, Logs Explorer, Log Analytics SQL).
  - Threshold alert → Monitoring. "Who did it" → audit logs.
- **Alerting policies:**
  - A condition (metric, threshold, duration, aligner) + **notification channels** (email, SMS, chat, PagerDuty, webhooks, Pub/Sub) + optional documentation.
  - **Metric absence** catches silent sources. **Forecast** conditions warn early. **Uptime checks** probe endpoints.
  - A **metrics scope** covers many projects.
- **Custom vs log-based metrics:**
  - Custom metrics (`custom.googleapis.com/`, API or OpenTelemetry) when logs lack the number.
  - Log-based **counter** or **distribution** metrics need no code, but only count logs that arrive after the metric is created.
- **Audit logs:**

| Type | Default |
|---|---|
| Admin Activity (config changes) | Always on, free, can't be disabled |
| Data Access (reads, user data) | Off except BigQuery; enable per service; charged |
| System Event (Google actions) | Always on, free |
| Policy Denied (blocked by policy) | On, charged |

- **VPC Flow Logs**: per subnet, sampled. **Firewall logging**: per rule.
- **Buckets and routing:**
  - `_Required` (Admin Activity, System Event, Access Transparency) has fixed retention.
  - `_Default` has adjustable retention.
  - Custom buckets can be regional. Upgrade any bucket to **Log Analytics** for SQL.
- **Sinks** = filter + destination:
  - Log bucket.
  - **BigQuery** for analysis.
  - **Cloud Storage** for archive.
  - **Pub/Sub** for external or on-premises systems.
- Grant the sink's **writer identity** access to the destination, or delivery fails silently.
- **Aggregated sinks** (`--include-children`) centralize logs. **Exclusions** cut cost.
- **Reading logs:**
  - Logs Explorer filters, for example `severity>=ERROR`.
  - Audit fields `protoPayload.authenticationInfo.principalEmail` (who) and `protoPayload.methodName` (what).
  - `gcloud logging read`.
- **Ops Agent:**
  - Adds memory, swap, disk space, and app logs and metrics.
  - Deploy at scale with agent policies.
  - The VM's account needs Logs Writer and Monitoring Metric Writer.
- **Managed Service for Prometheus:** keep PromQL, Google stores the data. On GKE use managed collection with `PodMonitoring`.
- **Symptom → tool:**
  - Cross-service latency → Cloud Trace.
  - Code CPU or heap → Cloud Profiler.
  - Slow SQL → Query Insights.
  - Google-side incident → Personalized Service Health.
  - Idle resources, rightsizing, IAM trimming → Active Assist.
  - Application-level view → Cloud Hub.
  - AI-guided investigation → Gemini Cloud Assist.

📖 Full lesson: [Cloud Monitoring and Logging: Alerts, Log Sinks, and Audit Logs](https://www.savemycert.com/revision/gcp-associate-cloud-engineer/gcp-cloud-monitoring-logging-alerts/?utm_source=github&utm_medium=readme&utm_campaign=gcp-associate-cloud-engineer-study-guide)

[← Back to the study guide](../README.md)
