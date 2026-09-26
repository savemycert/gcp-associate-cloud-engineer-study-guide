# ACE Study Guide: Google Cloud Associate Cloud Engineer

A free, open study guide for the **Google Cloud Associate Cloud Engineer (ACE)** exam: revision notes for every domain, side-by-side comparisons of commonly confused services, a glossary, 20 worked sample questions, and the official syllabus as a checklist with a full free lesson for every topic.

Maintained by [SaveMyCert](https://www.savemycert.com/?utm_source=github&utm_medium=readme&utm_campaign=gcp-associate-cloud-engineer-study-guide), where you can read every lesson free, [practice with explained questions](https://www.savemycert.com/practice/gcp-associate-cloud-engineer/?utm_source=github&utm_medium=readme&utm_campaign=gcp-associate-cloud-engineer-study-guide) and [take timed mock exams](https://www.savemycert.com/mocks/gcp-associate-cloud-engineer/?utm_source=github&utm_medium=readme&utm_campaign=gcp-associate-cloud-engineer-study-guide).

## Contents

- [Exam at a glance](#exam-at-a-glance)
- [Exam domains](#exam-domains)
- [What is in this repo](#what-is-in-this-repo)
- [Syllabus checklist](#syllabus-checklist)
  - [Domain 1: Setting up a cloud solution environment](#domain-1-setting-up-a-cloud-solution-environment)
  - [Domain 2: Planning and implementing a cloud solution](#domain-2-planning-and-implementing-a-cloud-solution)
  - [Domain 3: Ensuring the successful operation of a cloud solution](#domain-3-ensuring-the-successful-operation-of-a-cloud-solution)
  - [Domain 4: Configuring access and security](#domain-4-configuring-access-and-security)
- [How to study for ACE](#how-to-study-for-ace)
- [Sample questions](sample-questions.md)
- [Free resources](#free-resources)

## Exam at a glance

| | |
|---|---|
| Exam code | ACE |
| Level | Associate |
| Questions | 50–60 |
| Time limit | 120 min |
| Passing score | Pass / fail |
| Format | Multiple choice & multiple select |
| Exam fee | $125 |
| Valid for | 3 years |

Exam details change. Always confirm them in the official [Google Cloud Associate Cloud Engineer exam guide](https://cloud.google.com/learn/certification/cloud-engineer) from Google Cloud.

## Exam domains

| # | Domain | Weight | Topics |
|---|---|---|---|
| 1 | [Setting up a cloud solution environment](#domain-1-setting-up-a-cloud-solution-environment) | 20% | 2 |
| 2 | [Planning and implementing a cloud solution](#domain-2-planning-and-implementing-a-cloud-solution) | 30% | 4 |
| 3 | [Ensuring the successful operation of a cloud solution](#domain-3-ensuring-the-successful-operation-of-a-cloud-solution) | 30% | 4 |
| 4 | [Configuring access and security](#domain-4-configuring-access-and-security) | 20% | 2 |

That is 4 domains and 12 topics. Spend your time in proportion to the weights: the heaviest domain decides more of your score than the lightest.

Google publishes these weights as approximate figures (for example "~20%").

## What is in this repo

| File | What it gives you |
|---|---|
| [Domain 1 notes](notes/domain-1-setting-up-a-cloud-solution-environment.md) | Setting up a cloud solution environment: condensed revision notes per topic |
| [Domain 2 notes](notes/domain-2-planning-and-implementing-a-cloud-solution.md) | Planning and implementing a cloud solution: condensed revision notes per topic |
| [Domain 3 notes](notes/domain-3-ensuring-the-successful-operation-of-a-cloud-solution.md) | Ensuring the successful operation of a cloud solution: condensed revision notes per topic |
| [Domain 4 notes](notes/domain-4-configuring-access-and-security.md) | Configuring access and security: condensed revision notes per topic |
| [Commonly confused services](comparisons.md) | Side-by-side tables of the services questions set against each other |
| [Glossary](glossary.md) | Every in-scope term and service in one sentence |
| [Sample questions](sample-questions.md) | 20 worked questions with answers and reasoning |
| [Exam-day guide](exam-day-guide.md) | Booking, testing options, scoring, results and retakes |

## Syllabus checklist

Tick each topic off once you can explain it without notes. The "Must know" facts are the ones questions turn on. Each lesson link goes to the complete, free lesson.

### Domain 1: Setting up a cloud solution environment

**Weight: 20%.** Setting up cloud projects, accounts, and billing on Google Cloud. Official (approximate) weighting ~20%.

📝 Revision notes: [Domain 1: Setting up a cloud solution environment](notes/domain-1-setting-up-a-cloud-solution-environment.md)

- [ ] **Setting up cloud projects and accounts**
  <br>Creating a resource hierarchy; applying organizational policies; granting IAM roles within a project; managing users and groups in Cloud Identity (manual and automated); enabling APIs; provisioning Google Cloud Observability; assessing quotas and requesting increases; setting up standalone organizations; setting up cloud networking; verifying product availability across regions and zones; configuring Cloud Asset Inventory and using Gemini Cloud Assist to analyze resources; configuring Workforce Identity Federation.
  - 📖 Lesson: [Setting Up Google Cloud Projects, Resource Hierarchy, and IAM](https://www.savemycert.com/revision/gcp-associate-cloud-engineer/gcp-projects-resource-hierarchy-iam-setup/?utm_source=github&utm_medium=readme&utm_campaign=gcp-associate-cloud-engineer-study-guide)
  - Must know: The hierarchy is organization to folders to projects to resources; IAM and organization policies attach at any level and inherit downward
  - Must know: IAM inheritance is additive — a lower level can never revoke a role granted above it
- [ ] **Managing billing configuration**
  <br>Creating one or more billing accounts; linking projects to a billing account; establishing billing budgets and alerts; setting up billing exports.
  - 📖 Lesson: [Google Cloud Billing Accounts, Budgets, Alerts, and BigQuery Export](https://www.savemycert.com/revision/gcp-associate-cloud-engineer/gcp-billing-accounts-budgets-alerts/?utm_source=github&utm_medium=readme&utm_campaign=gcp-associate-cloud-engineer-study-guide)
  - Must know: One billing account funds many projects; each project links to exactly one billing account
  - Must know: Linking a project requires Billing Account User on the account plus Project Billing Manager (or Owner) on the project — neither alone suffices

### Domain 2: Planning and implementing a cloud solution

**Weight: 30%.** Planning and deploying compute, storage/data, networking, and resources via tooling. Official (approximate) weighting ~30%.

📝 Revision notes: [Domain 2: Planning and implementing a cloud solution](notes/domain-2-planning-and-implementing-a-cloud-solution.md)

- [ ] **Planning and implementing compute resources**
  <br>Selecting compute for a workload (Compute Engine, GKE, Cloud Run, Cloud Run functions, Agent Runtime on Gemini Enterprise Agent Platform); launching instances (availability policy, SSH keys); choosing Compute Engine storage (zonal/regional Persistent Disk, Hyperdisk); autoscaled managed instance groups with instance templates; OS Login; VM Manager; Spot VMs and custom machine types; kubectl; deploying GKE clusters (Autopilot, regional, private); deploying containerized apps to GKE; serverless deployments and event processing (Pub/Sub, Cloud Storage notifications, Eventarc); choosing GPUs vs TPUs.
  - 📖 Lesson: [Choosing and Deploying Compute: Compute Engine, GKE, and Cloud Run](https://www.savemycert.com/revision/gcp-associate-cloud-engineer/gcp-compute-engine-gke-cloud-run-selection/?utm_source=github&utm_medium=readme&utm_campaign=gcp-associate-cloud-engineer-study-guide)
  - Must know: Pick compute by management burden and control: Compute Engine for VMs and lift-and-shift, GKE for orchestrated containers, Cloud Run for stateless containers with scale-to-zero, Cloud Run functions for single-purpose event handlers.
  - Must know: The availability policy (MIGRATE vs TERMINATE plus automatic restart) controls maintenance behavior; OS Login replaces metadata SSH keys with IAM-governed access, and VM Manager patches, inventories, and enforces policy across the fleet.
- [ ] **Planning and implementing storage and data solutions**
  <br>Choosing and deploying data products (Cloud SQL, BigQuery, Firestore, Spanner, Bigtable, AlloyDB, Dataflow, Pub/Sub, Managed Service for Apache Kafka, Memorystore); choosing and deploying storage products (Cloud Storage, Filestore, NetApp Volumes, Managed Lustre) and Cloud Storage classes (Standard, Nearline, Coldline, Archive); loading data (CLI upload, from Cloud Storage, Storage Transfer Service); maintaining multi-region redundancy.
  - 📖 Lesson: [Choosing Storage and Databases: Cloud SQL, BigQuery, Spanner, and More](https://www.savemycert.com/revision/gcp-associate-cloud-engineer/gcp-storage-data-cloud-sql-bigquery-spanner/?utm_source=github&utm_medium=readme&utm_campaign=gcp-associate-cloud-engineer-study-guide)
  - Must know: Select databases by data model, scale, and consistency: Cloud SQL for regional relational, Spanner for globally consistent relational scale, Firestore for documents with sync, Bigtable for massive-throughput wide-column data, BigQuery for analytics.
  - Must know: AlloyDB is the PostgreSQL-compatible step up from Cloud SQL for demanding transactional-plus-analytical workloads; Memorystore is the managed cache and session store, not a system of record.
- [ ] **Planning and implementing networking resources**
  <br>Creating a VPC with subnets (custom mode, Shared VPC, VPC Network Peering); creating and applying VPC firewall rules and Cloud NGFW policies with ingress/egress attributes; using Tags (secure Tags) and service accounts in NGFW policy rules; establishing connectivity (Cloud VPN, VPC Network Peering, Cloud Interconnect); choosing and deploying load balancers; differentiating Network Service Tiers.
  - 📖 Lesson: [VPC Networks, Firewall Rules, and Load Balancing on Google Cloud](https://www.savemycert.com/revision/gcp-associate-cloud-engineer/gcp-vpc-subnets-firewall-load-balancing/?utm_source=github&utm_medium=readme&utm_campaign=gcp-associate-cloud-engineer-study-guide)
  - Must know: VPC networks are global; subnets are regional — and custom mode is the production choice because auto mode ranges overlap and block peering.
  - Must know: Shared VPC shares one network across projects in a single organization; VPC Network Peering connects two independent networks, is non-transitive, and forbids overlapping ranges.
- [ ] **Planning and implementing resources using tooling**
  <br>Infrastructure as Code tooling (Fabric FAST, Config Connector, Terraform, Helm); AI-assisted planning and implementation (Gemini CLI, Google Antigravity, Gemini Cloud Assist, Application Design Center).
  - 📖 Lesson: [Terraform, Config Connector, and AI-Assisted Tooling on Google Cloud](https://www.savemycert.com/revision/gcp-associate-cloud-engineer/gcp-iac-terraform-deployment-tooling/?utm_source=github&utm_medium=readme&utm_campaign=gcp-associate-cloud-engineer-study-guide)
  - Must know: Declarative IaC describes desired end state and safely converges to it; imperative gcloud scripts list steps and are not safely repeatable.
  - Must know: The Terraform loop is init (download providers, configure backend), plan (preview the diff), apply (execute), destroy (tear down) — and plan output showing replace means destroy-and-recreate.

### Domain 3: Ensuring the successful operation of a cloud solution

**Weight: 30%.** Managing compute, storage/data, and networking resources day-to-day, plus monitoring and logging. Official (approximate) weighting ~30%.

📝 Revision notes: [Domain 3: Ensuring the successful operation of a cloud solution](notes/domain-3-ensuring-the-successful-operation-of-a-cloud-solution.md)

- [ ] **Managing compute resources**
  <br>Remotely connecting to Compute Engine; viewing running instances; snapshots and images (create/view/delete/schedule); viewing GKE inventory (nodes, Pods, Services); configuring GKE access to Artifact Registry; working with node pools (add/edit/remove, autoscaling); working with Kubernetes resources (Pods, Services, StatefulSets); horizontal/vertical Pod autoscaling; GKE Autopilot Pod resource requests; deploying new Cloud Run versions; traffic splitting (Cloud Run, functions, GKE); Cloud Run autoscaling; attaching GPUs/TPUs; deploying an agent to Agent Runtime; managing notebooks (Workbench, BigQuery); managing developer environments (Cloud Workstations).
  - 📖 Lesson: [Managing Compute Engine, GKE, and Cloud Run: Day-2 Operations](https://www.savemycert.com/revision/gcp-associate-cloud-engineer/gcp-managing-compute-gke-instances-autoscaling/?utm_source=github&utm_medium=readme&utm_campaign=gcp-associate-cloud-engineer-study-guide)
  - Must know: Use gcloud compute ssh with --tunnel-through-iap for instances without external IPs; IAP needs a firewall rule allowing 35.235.240.0/20 on port 22 and the roles/iap.tunnelResourceAccessor role.
  - Must know: Snapshots are incremental disk backups you can automate with snapshot schedules; custom images are golden templates for launching identical instances.
- [ ] **Managing storage and data solutions**
  <br>Managing and securing objects in Cloud Storage buckets; object lifecycle management policies; executing queries against data instances (Cloud SQL, BigQuery, Bigtable, Spanner, Firestore, AlloyDB); estimating storage costs; backing up and restoring database instances; reviewing job status (Dataflow, BigQuery); using Database Center to manage the database fleet; configuring customer-managed encryption keys (CMEK).
  - 📖 Lesson: [Managing Cloud Storage and Databases: Lifecycle, Backups, and CMEK](https://www.savemycert.com/revision/gcp-associate-cloud-engineer/gcp-managing-cloud-storage-databases-cmek/?utm_source=github&utm_medium=readme&utm_campaign=gcp-associate-cloud-engineer-study-guide)
  - Must know: Prefer uniform bucket-level access so IAM alone governs Cloud Storage; fine-grained ACLs are legacy and only for per-object permission schemes.
  - Must know: Signed URLs grant time-limited access to a single object without Google accounts or IAM changes — the answer for external, temporary sharing.
- [ ] **Managing networking resources**
  <br>Resizing a subnet's IPv4 range; reserving static external or internal IP addresses; adding custom static routes in a VPC; using Cloud DNS and Cloud NAT; managing VPC firewall rules and Cloud NGFW policies.
  - 📖 Lesson: [Managing VPC Networking: Subnets, Static IPs, Cloud DNS, and Cloud NAT](https://www.savemycert.com/revision/gcp-associate-cloud-engineer/gcp-managing-vpc-networking-dns-nat/?utm_source=github&utm_medium=readme&utm_campaign=gcp-associate-cloud-engineer-study-guide)
  - Must know: Subnet ranges can only expand — gcloud compute networks subnets expand-ip-range decreases the prefix length in place with no downtime, and auto mode subnets cap at /16
  - Must know: Static external IPs survive instance restarts and can be promoted from an in-use ephemeral address; internal static IPs come from the subnet range and stay VPC-only
- [ ] **Monitoring and logging**
  <br>Creating Cloud Monitoring alerts on resource metrics; creating and ingesting custom metrics; configuring audit logs (VPC Flow Logs, audit logs, firewall logs); exporting logs to external systems; configuring log buckets, log analytics, and log routers; viewing and filtering logs in Cloud Logging; using diagnostic tools (Cloud Trace, Cloud Profiler, Query Insights); Personalized Service Health; configuring Ops Agent; deploying Managed Service for Prometheus; using Gemini Cloud Assist for Monitoring; Active Assist; Cloud Hub.
  - 📖 Lesson: [Cloud Monitoring and Logging: Alerts, Log Sinks, and Audit Logs](https://www.savemycert.com/revision/gcp-associate-cloud-engineer/gcp-cloud-monitoring-logging-alerts/?utm_source=github&utm_medium=readme&utm_campaign=gcp-associate-cloud-engineer-study-guide)
  - Must know: Cloud Monitoring handles numeric time series and alerting; Cloud Logging handles discrete log entries, storage, and routing — log-based metrics bridge logs into alertable metrics
  - Must know: An alerting policy is conditions plus notification channels: metric, threshold, and a duration window to suppress blips; metric-absence conditions catch heartbeats going silent

### Domain 4: Configuring access and security

**Weight: 20%.** Managing IAM policies and roles, and service accounts, on Google Cloud. Official (approximate) weighting ~20%.

📝 Revision notes: [Domain 4: Configuring access and security](notes/domain-4-configuring-access-and-security.md)

- [ ] **Managing IAM**
  <br>Viewing and creating IAM policies; attaching roles and understanding policy inheritance in the Organization hierarchy; managing the various role types and defining custom IAM roles.
  - 📖 Lesson: [Managing Cloud IAM: Roles, Policies, and Inheritance](https://www.savemycert.com/revision/gcp-associate-cloud-engineer/gcp-iam-roles-policies-management/?utm_source=github&utm_medium=readme&utm_campaign=gcp-associate-cloud-engineer-study-guide)
  - Must know: An IAM policy is a set of role bindings on a resource; each binding ties one role to principals, optionally with a condition
  - Must know: Permissions are never granted directly — they travel only inside basic, predefined, or custom roles
- [ ] **Managing service accounts**
  <br>Creating service accounts (including Google-managed); using service accounts in IAM policies with minimum permissions; assigning service accounts to resources; managing a service account’s IAM permissions; service account impersonation; creating and managing short-lived credentials; using a service account with a GKE application; provisioning Workload Identity Federation.
  - 📖 Lesson: [Service Accounts, Impersonation, and Workload Identity Federation](https://www.savemycert.com/revision/gcp-associate-cloud-engineer/gcp-service-accounts-workload-identity/?utm_source=github&utm_medium=readme&utm_campaign=gcp-associate-cloud-engineer-study-guide)
  - Must know: Service accounts come in three kinds: Google-managed service agents (hands off), auto-created default service accounts (historically over-privileged — avoid for workloads), and user-managed accounts you create per workload
  - Must know: Grant service accounts minimum roles exactly like users — one account per workload, resource-level bindings where possible, never Editor for convenience

## How to study for ACE

1. **Read the lesson for each topic** in the checklist above, starting with the heaviest domain. Every lesson is free on the [ACE revision notes](https://www.savemycert.com/revision/gcp-associate-cloud-engineer/?utm_source=github&utm_medium=readme&utm_campaign=gcp-associate-cloud-engineer-study-guide).
2. **Practice straight after reading.** Answer [ACE practice questions](https://www.savemycert.com/practice/gcp-associate-cloud-engineer/?utm_source=github&utm_medium=readme&utm_campaign=gcp-associate-cloud-engineer-study-guide) on the topic you just read. Each option comes with an explanation of why it is right or wrong.
3. **Review what you got wrong**, re-read that lesson section, and tick the topic off only when you get its questions right.
4. **Take a full-length [ACE mock exam](https://www.savemycert.com/mocks/gcp-associate-cloud-engineer/?utm_source=github&utm_medium=readme&utm_campaign=gcp-associate-cloud-engineer-study-guide)** under the real time limit. Aim to pass mocks comfortably before you book.
5. **On the last day**, skim the [ACE cheat sheet](https://www.savemycert.com/cheat-sheet/gcp-associate-cloud-engineer/?utm_source=github&utm_medium=readme&utm_campaign=gcp-associate-cloud-engineer-study-guide) instead of starting anything new.

## Sample questions

[sample-questions.md](sample-questions.md) has 20 worked ACE questions with the answer, why each option is right or wrong, and the reasoning steps.

## Free resources

- [Google Cloud Associate Cloud Engineer exam guide](https://cloud.google.com/learn/certification/cloud-engineer): the official source (Google Cloud)
- [ACE certification overview](https://www.savemycert.com/certifications/gcp-associate-cloud-engineer/?utm_source=github&utm_medium=readme&utm_campaign=gcp-associate-cloud-engineer-study-guide)
- [ACE revision notes](https://www.savemycert.com/revision/gcp-associate-cloud-engineer/?utm_source=github&utm_medium=readme&utm_campaign=gcp-associate-cloud-engineer-study-guide): every lesson, free to read
- [ACE practice questions](https://www.savemycert.com/practice/gcp-associate-cloud-engineer/?utm_source=github&utm_medium=readme&utm_campaign=gcp-associate-cloud-engineer-study-guide): with an explanation on every option
- [ACE mock exams](https://www.savemycert.com/mocks/gcp-associate-cloud-engineer/?utm_source=github&utm_medium=readme&utm_campaign=gcp-associate-cloud-engineer-study-guide): full-length and timed
- [ACE cheat sheet](https://www.savemycert.com/cheat-sheet/gcp-associate-cloud-engineer/?utm_source=github&utm_medium=readme&utm_campaign=gcp-associate-cloud-engineer-study-guide): the key facts on one page
- [All certification study guides](https://github.com/savemycert/certification-study-guides)

## Contributing

Spotted an error or an out-of-date fact? [Open an issue](../../issues) with the topic and a link to the official source. See [CONTRIBUTING.md](CONTRIBUTING.md).

## License and disclaimer

This guide is licensed under [CC BY 4.0](LICENSE). You can reuse and adapt it, including commercially, as long as you credit **SaveMyCert** with a link to https://www.savemycert.com/.

This is an independent study resource. It is not affiliated with or endorsed by Google Cloud. Google Cloud Associate Cloud Engineer and ACE are trademarks of their respective owner. Exam domains and weights are taken from the official exam guide linked above.
