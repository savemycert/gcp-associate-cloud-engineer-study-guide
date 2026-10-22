# Domain 2: Planning and implementing a cloud solution (30%)

Tied for the largest domain on the ACE exam. It covers choosing and deploying compute, storage and databases, networking, and infrastructure-as-code tooling. Most questions describe a workload and ask for the one service, setting, or command that fits.

## Planning and implementing compute resources

- **Picking compute:** first ask whether the workload is containerized, then decide who should manage the infrastructure.

| Signal in the question                                       | Choose                                           |
| ------------------------------------------------------------ | ------------------------------------------------ |
| Lift and shift, custom kernel or OS, machine-bound licensing | Compute Engine                                   |
| Kubernetes, many cooperating containers, StatefulSets        | GKE                                              |
| Stateless HTTP container, scale to zero, spiky traffic       | Cloud Run                                        |
| Small handler for an upload, message, or webhook             | Cloud Run functions                              |
| Hosting an AI agent with no infrastructure                   | Agent Runtime (Gemini Enterprise Agent Platform) |

- **Availability policy:**
  - `--maintenance-policy=MIGRATE` (the usual default) live-migrates the VM during host maintenance. `TERMINATE` stops it instead, which many GPU VMs need.
  - `--restart-on-failure` controls automatic restart after a crash.
  - Change either later with `instances set-scheduling`.
- **SSH:**
  - Project metadata keys reach every VM. `block-project-ssh-keys=TRUE` shuts them out of one VM.
  - **OS Login** (`enable-oslogin=TRUE`) is preferred: access comes from `roles/compute.osLogin` or `osAdminLogin` (sudo), revocation is an IAM change, and metadata keys stop working on that VM.
- **VM Manager** (`enable-osconfig=TRUE`): patch jobs, OS inventory, and OS policies across the fleet. Cue: SSH access → OS Login. Patching or configuration drift → VM Manager.
- **Block storage:**
  - **Zonal PD**: the default. `pd-balanced` general, `pd-ssd` databases, `pd-standard` HDD, `pd-extreme` provisioned IOPS.
  - **Regional PD**: synchronous copy in two zones. Force-attach to a standby VM after a zone failure.
  - **Hyperdisk**: performance is set separately from size. The newest machine series require it.
  - **Local SSD**: fastest, but data is lost on stop. Use it for scratch data only.
- **Managed instance groups:**
  - Built from an **instance template**, which cannot be edited. To change the fleet, create a new template and roll it out.
  - Autoscale on CPU, LB capacity, or Monitoring metrics (`set-autoscaling --min-num-replicas --max-num-replicas --target-cpu-utilization`).
  - **Regional MIG + autohealing** is the high-availability pattern for VMs.
- **Cost:**
  - **Spot VMs** (`--provisioning-model=SPOT`): cheap, can be preempted any time, no SLA. Batch and CI only.
  - **Custom machine types**: exact vCPU and memory.
- **GKE:**
  - **Autopilot** (recommended, always regional, `create-auto`): Google runs the nodes, billing follows Pod requests.
  - **Standard**: you run node pools. Choose it only when you need node-level control.
  - **Regional** clusters keep the control plane up through a zone loss. **Private** clusters give nodes internal IPs only, with egress through Cloud NAT.
  - Trap: on a regional Standard cluster, `--num-nodes` counts **per zone**.
- **kubectl:** install it and `gke-gcloud-auth-plugin`, then run `get-credentials` first. That's the usual fix for "kubectl can't reach the cluster."
- **Deploying to GKE:**
  - Push the image to Artifact Registry, `kubectl create deployment`, then `kubectl expose`.
  - Service types: `LoadBalancer` = external passthrough LB, `ClusterIP` = internal. Ingress or Gateway = Application LB.
  - `ImagePullBackOff` → a bad image path or missing registry read access.
- **Serverless and events:**
  - `gcloud run deploy`. Without `--allow-unauthenticated`, callers need `roles/run.invoker`.
  - **Pub/Sub** (push subscription or `--trigger-topic`) for messages.
  - **Cloud Storage notifications** for uploads.
  - **Eventarc** for routing any Google event (including audit-log events) to Cloud Run as CloudEvents.
- **GPU vs TPU:** GPUs for general acceleration and most ML. TPUs for very large tensor and model-training jobs.

📖 Full lesson: [Choosing and Deploying Compute: Compute Engine, GKE, and Cloud Run](https://www.savemycert.com/revision/gcp-associate-cloud-engineer/gcp-compute-engine-gke-cloud-run-selection/?utm_source=github&utm_medium=readme&utm_campaign=gcp-associate-cloud-engineer-study-guide)

## Planning and implementing storage and data solutions

- **Database selection:** ask about the data model, then scale and location, then consistency.

| Need                                                             | Choose                  |
| ---------------------------------------------------------------- | ----------------------- |
| Relational, one region, existing MySQL/PostgreSQL/SQL Server app | Cloud SQL               |
| Demanding PostgreSQL, mixed transactional and analytical queries | AlloyDB                 |
| Relational, horizontal scale, strong consistency across regions  | Spanner                 |
| Documents with mobile/web sync and offline clients               | Firestore (Native mode) |
| Very high-throughput wide-column: IoT, time series               | Bigtable                |
| SQL analytics over huge tables                                   | BigQuery                |
| Cache or session store                                           | Memorystore             |

- **Cloud SQL:**
  - `--availability-type=REGIONAL` adds a standby in another zone. It survives a **zone** failure only.
  - A cross-region read replica, **promoted** by an operator, is the regional DR plan.
- **Spanner:** the instance **config** sets regional or multi-region. Capacity in processing units or nodes scales online. It's overkill for small apps.
- **Firestore:** the location is permanent. Multi-region (for example `nam5`) for top availability, regional to cost less.
- **Bigtable:**
  - Add nodes for scale and clusters for replication.
  - Single-row transactions only. Not suited to small data.
- **Memorystore:** Redis `STANDARD_HA` has a failover replica. `BASIC` has none.
- **Data in motion:**
  - Pub/Sub ingests and decouples.
  - Dataflow (Apache Beam) transforms.
  - Managed Service for Apache Kafka only when Kafka compatibility is required.
  - Canonical pipeline: Pub/Sub → Dataflow → BigQuery.
- **Cloud Storage classes:** same durability, APIs, and millisecond reads for all four. Colder means cheaper storage and pricier reads.

| Class    | Access pattern    | Minimum storage duration |
| -------- | ----------------- | ------------------------ |
| Standard | Frequent          | None                     |
| Nearline | About monthly     | 30 days                  |
| Coldline | About quarterly   | 90 days                  |
| Archive  | Under once a year | 365 days                 |

- Deleting early still bills the full minimum.
- **Bucket location** is permanent: region, dual-region (optional turbo replication), or multi-region. **Autoclass** manages classes automatically.
- **File storage:**
  - **Filestore**: NFS.
  - **NetApp Volumes**: NFS **and SMB**.
  - **Managed Lustre**: HPC and AI training throughput.
  - If nothing needs a mount, use Cloud Storage.
- **Loading data:**
  - `gcloud storage cp` or `rsync` for ad hoc and scripted uploads (limited by the client machine).
  - **Storage Transfer Service** for large, scheduled, cross-cloud, or on-premises (agent) transfers.
  - Stage in a bucket, then `bq load` or `gcloud sql import sql`. The Cloud SQL service account needs bucket read access first.
- **Redundancy:**
  - Chosen at creation: bucket location, Spanner config, Firestore location, BigQuery `US`/`EU`.
  - Operator-driven: Cloud SQL replica promotion, Bigtable multi-region clusters (asynchronous).

📖 Full lesson: [Choosing Storage and Databases: Cloud SQL, BigQuery, Spanner, and More](https://www.savemycert.com/revision/gcp-associate-cloud-engineer/gcp-storage-data-cloud-sql-bigquery-spanner/?utm_source=github&utm_medium=readme&utm_campaign=gcp-associate-cloud-engineer-study-guide)

## Planning and implementing networking resources

- **VPC is global, subnets are regional.** Cross-region VM traffic on one VPC uses internal IPs with no gateway.
- **Custom mode for production:**
  - Every auto mode network uses the same ranges, so auto mode VPCs collide with each other and can't peer.
  - You can convert auto → custom only.
  - Subnets grow but never shrink. Reserve **secondary ranges** for GKE.
- **Shared VPC vs peering:**
  - **Shared VPC**: a host project's network is used by service projects. One organization, central control.
  - **Peering**: links two independent VPCs, even across organizations. **Not transitive**, **no overlapping ranges**, and both sides configure it.
- **Firewall rules:**
  - Direction, allow/deny, priority 0–65535 (**lower wins**), source or destination, targets, protocols and ports.
  - **Stateful**. Implied rules deny all ingress and allow all egress at 65535.
  - Targets: all instances, a network tag, or a service account.
  - Unexpected block → a higher-priority deny. Turn on Firewall Rules Logging to confirm.
- **Cloud NGFW policies:**
  - **Hierarchical** (organization or folder) policies are evaluated first and can't be overridden by projects.
  - **Global** and **regional network** policies attach to one VPC.
  - **Secure Tags** need IAM to create and bind. Network tags can be added by anyone who can edit the VM.
  - Service-account rules are hard to bypass.
- **Hybrid connectivity:**
  - **HA VPN**: IPsec over the internet, BGP via Cloud Router, 99.99% SLA with the recommended tunnels. Quick and encrypted.
  - **Dedicated Interconnect**: private links at a colocation facility, highest bandwidth. Not encrypted by default.
  - **Partner Interconnect**: through a provider, 50 Mbps to 50 Gbps, when you can't reach a colocation facility.
  - **Peering** is VPC to VPC only, never on-premises.
- **Load balancers:**
  - Layer: HTTP(S) → Application LB. TCP/UDP → Network LB.
  - Clients: external or internal. Scope: global or regional.
  - Worldwide HTTPS with CDN and Cloud Armor → global external Application LB.
  - UDP or client IP must be preserved → **passthrough** Network LB (regional).
  - Global TCP/TLS that isn't HTTP → **proxy** Network LB.
  - Build order: health check → backend service → routing → forwarding rule.
- **Network Service Tiers:**
  - **Premium** (default): Google backbone to the nearest edge. Required for global LBs.
  - **Standard**: exits to the internet near the region. Cheaper, regional only.
  - Can be set per resource.

📖 Full lesson: [VPC Networks, Firewall Rules, and Load Balancing on Google Cloud](https://www.savemycert.com/revision/gcp-associate-cloud-engineer/gcp-vpc-subnets-firewall-load-balancing/?utm_source=github&utm_medium=readme&utm_campaign=gcp-associate-cloud-engineer-study-guide)

## Planning and implementing resources using tooling

- **Declarative vs imperative:** a gcloud script lists steps and can break or duplicate things on a rerun. Declarative IaC states the end result and applies only the difference. Keep gcloud and the console for exploring and emergency fixes.
- **Terraform:**
  - The Google provider (`google` for GA, `google-beta` for previews) turns HCL into API calls. References between resources set the build order.
  - Preinstalled in Cloud Shell. Reuse Google's modules and blueprints.
  - Workflow:
    - `init`: providers, modules, backend.
    - `plan`: preview only.
    - `apply`: execute.
    - `destroy`: tear down.
  - **replace** in a plan means destroy and recreate, which is dangerous for data.
  - Changes made by hand appear as **drift** in the next plan. `terraform import` adopts existing resources.
  - **State**: a `gcs` backend with locking and Object Versioning, restricted by IAM because state can hold secrets.
- **Config Connector:**
  - A GKE add-on that manages cloud resources as Kubernetes objects through kubectl or GitOps.
  - Continuously reconciles, so manual changes get reverted.
  - For Kubernetes-native teams. Terraform still builds the platform underneath.
- **Helm:** packages **applications** as charts. `install`, `upgrade`, and `rollback` manage releases. Charts can live in Artifact Registry. Helm never creates cloud infrastructure.
- **Fabric FAST:** Google's staged Terraform framework for a whole-organization landing zone (bootstrap, hierarchy, networking and security, project factory).
- **AI-assisted tools:**
  - **Gemini Cloud Assist**: in the console.
  - **Gemini CLI**: open-source terminal agent.
  - **Google Antigravity**: agentic development platform.
  - **Application Design Center**: visual design that outputs IaC.
  - Generated code still gets planned and reviewed.

📖 Full lesson: [Terraform, Config Connector, and AI-Assisted Tooling on Google Cloud](https://www.savemycert.com/revision/gcp-associate-cloud-engineer/gcp-iac-terraform-deployment-tooling/?utm_source=github&utm_medium=readme&utm_campaign=gcp-associate-cloud-engineer-study-guide)

[← Back to the study guide](../README.md)
