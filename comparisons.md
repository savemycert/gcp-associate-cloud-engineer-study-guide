# ACE Commonly Confused Services and Concepts

Side-by-side comparisons of the ACE services and ideas that exam questions most often set against each other. Each table ends with the one thing to remember.

- [Domain 1: Setting up a cloud solution environment](#domain-1-setting-up-a-cloud-solution-environment)
- [Domain 2: Planning and implementing a cloud solution](#domain-2-planning-and-implementing-a-cloud-solution)
- [Domain 3: Ensuring the successful operation of a cloud solution](#domain-3-ensuring-the-successful-operation-of-a-cloud-solution)
- [Domain 4: Configuring access and security](#domain-4-configuring-access-and-security)

## Domain 1: Setting up a cloud solution environment

### IAM vs organization policy

| | IAM | Organization policy |
|---|---|---|
| Question it answers | Who can do what on which resource | What may be done with resources at all |
| Building block | Role bindings (role + principals) | Constraints (boolean or list) |
| Binds Owners | No, Owners hold broad grants | Yes, constraints apply to every identity |
| Inheritance | Additive; a lower level cannot remove an inherited grant | Inherited, but a lower level can override unless the parent blocks it |
| Admin role | Owner or a resource admin role with `setIamPolicy` | Organization Policy Administrator at the organization |
| Exam cue | "grant access", "least privilege" | "prevent anyone", "only allow these regions", "block external IPs" |

**Remember:** IAM decides who gets in. An organization policy limits what anyone can do, Owners included.

### Syncing users into Cloud Identity vs Workforce Identity Federation

| | Sync or provision into Cloud Identity | Workforce Identity Federation |
|---|---|---|
| Google Accounts created | Yes, one per user | No |
| Tools | GCDS (one-way LDAP sync), provisioning APIs, SAML SSO | Workforce identity pool + SAML or OIDC provider |
| Where the account lifecycle lives | The source directory plus Cloud Identity | Only the external IdP |
| Works with Google Workspace apps | Yes | Not the goal; Google Cloud access only |
| Exam cue | "users also need Workspace", "keep directory in sync" | "without creating Google Accounts", "IdP stays the only source" |

**Remember:** sync copies people into Google. Federation lets Google trust your IdP, so no Google accounts are created.

### Billing Account Administrator vs Billing Account User vs Billing Account Viewer

| | Administrator | User | Viewer |
|---|---|---|---|
| Link projects to the account | Yes | Yes | No |
| See costs and transactions | Yes | No | Yes |
| Change payment settings, budgets, exports | Yes | No | No |
| Manage billing IAM | Yes | No | No |
| Receives budget emails by default | Yes | Yes | No |
| Exam cue | "manage the payment method", "set up export" | "let this team link projects" | "finance needs to see spend" |

**Remember:** User can only link projects, Viewer can only look, and Administrator can do everything. Linking also needs Project Billing Manager on the project.

### Budget alerts vs disabling billing

| | Budget with alerts | Disabling billing on a project |
|---|---|---|
| Effect on resources | None, everything keeps running | Paid services stop; data can eventually be lost |
| Stops spending | No | Yes |
| Triggered by | Actual or forecasted spend crossing thresholds | A person, or automation reacting to a budget's Pub/Sub message |
| Reversible | Nothing to reverse | Relinking restores billing, but stopped workloads and lost data may not come back |
| Exam cue | "notify before overspending" | "hard cap", "stop all spend immediately" |

**Remember:** a budget only warns. The only way to cap spend is automation that disables billing, and that takes the workload down.

### Standard vs detailed usage cost export

| | Standard usage cost | Detailed usage cost |
|---|---|---|
| Granularity | Service, SKU, project, labels | Everything in standard plus individual resources |
| Typical use | Chargeback by project or label, SKU trends | Attributing cost to a specific VM or disk |
| Retroactive | No | No |
| Configured | Per billing account, into a BigQuery dataset | Per billing account, into a BigQuery dataset |
| Exam cue | "cost per team or label" | "which individual resource is costing us" |

**Remember:** neither export fills in history from before you turned it on, so enable both when you create the billing account.

## Domain 2: Planning and implementing a cloud solution

### Compute Engine vs GKE vs Cloud Run vs Cloud Run functions

| | Compute Engine | GKE | Cloud Run | Cloud Run functions |
|---|---|---|---|---|
| What you ship | VM instance | Containers in Pods | Container image | Source code with an entry point |
| You manage | OS, patching, scaling policy | Workloads (Autopilot) or node pools too (Standard) | Nothing below the container | Nothing below the code |
| Scales to zero | No | No | Yes | Yes |
| State | Persistent and local disks | PersistentVolumes, StatefulSets | Stateless, external state | Stateless, external state |
| Exam cue | "lift and shift", "custom kernel", "licensing" | "Kubernetes", "many microservices", "StatefulSet" | "stateless HTTP container", "unpredictable traffic" | "run code on each upload or message" |

**Remember:** the more of the stack you need to control, the further left you go. The less you want to operate, the further right.

### Cloud SQL vs Spanner vs Firestore vs Bigtable vs BigQuery

| | Cloud SQL | Spanner | Firestore | Bigtable | BigQuery |
|---|---|---|---|---|---|
| Data model | Relational | Relational | Documents | Wide-column NoSQL | Columnar analytics tables |
| Scale | Vertical, regional | Horizontal, regional to global | Automatic | Horizontal, very high throughput | Serverless, very large datasets |
| Transactions | Full, single primary | Strong, global | Strong | Single-row only | Analytical, not OLTP |
| Wrong when | Global writes needed | Small app Cloud SQL handles | Complex joins, heavy analytics | Small data, multi-row transactions | Serving single-row app lookups |
| Exam cue | "existing MySQL/PostgreSQL app" | "globally consistent transactions" | "mobile offline sync" | "IoT time series at scale" | "SQL analytics over petabytes" |

**Remember:** every scenario includes one requirement that rules out all but one database. Find that requirement first.

### Standard vs Nearline vs Coldline vs Archive

| | Standard | Nearline | Coldline | Archive |
|---|---|---|---|---|
| Access pattern | Frequent | About monthly | About quarterly | Under once a year |
| Minimum storage duration | None | 30 days | 90 days | 365 days |
| Retrieval fee | None | Yes | Higher | Highest |
| First-byte latency | Milliseconds | Milliseconds | Milliseconds | Milliseconds |
| Exam cue | "serving website assets" | "monthly backups" | "disaster recovery copies" | "compliance retention for years" |

**Remember:** colder classes are no slower to read. They only cost less to store and more to read, and deleting early still bills the minimum.

### Shared VPC vs VPC Network Peering

| | Shared VPC | VPC Network Peering |
|---|---|---|
| What it does | Many projects use one network | Two separate networks exchange traffic |
| Administration | Central, in the host project | Each side runs its own network |
| Across organizations | No | Yes |
| Transitive | Not applicable, it is one network | No |
| Overlapping ranges | Not applicable | Not allowed |
| Exam cue | "central network team, app teams in own projects" | "connect our VPC to a partner's VPC" |

**Remember:** Shared VPC shares one network across projects. Peering connects two networks that stay separate.

### Application Load Balancer vs proxy Network Load Balancer vs passthrough Network Load Balancer

| | Application LB | Proxy Network LB | Passthrough Network LB |
|---|---|---|---|
| Layer | 7 (HTTP/HTTPS) | 4 (TCP, optional TLS) | 4 (TCP, UDP, other IP protocols) |
| Terminates connections | Yes | Yes | No |
| Preserves client source IP | No | No | Yes |
| Scope | Global or regional | Global or regional | Regional |
| Exam cue | "URL path routing", "CDN", "Cloud Armor" | "global TCP service, not HTTP" | "UDP", "must see client IP" |

**Remember:** HTTP goes to an Application LB. UDP or client-IP preservation needs passthrough. Global anycast needs Premium Tier.

### Terraform vs Config Connector vs Helm

| | Terraform | Config Connector | Helm |
|---|---|---|---|
| Manages | Any Google Cloud resource | Google Cloud resources as Kubernetes objects | Kubernetes application manifests |
| Interface | HCL, CLI, CI pipelines | YAML with kubectl or GitOps | Charts, values, releases |
| Drift | Found at the next plan, fixed on apply | Continuously reconciled | Not its job; release history and rollback |
| Exam cue | "provision networks, projects, clusters" | "Kubernetes-native team", "self-healing infrastructure" | "package an app for several GKE environments" |

**Remember:** Terraform builds the platform, Config Connector lets Kubernetes teams manage cloud resources, and Helm ships applications onto the cluster.

## Domain 3: Ensuring the successful operation of a cloud solution

### Snapshot vs custom image

| | Snapshot | Custom image |
|---|---|---|
| Purpose | Back up and restore one disk | Launch new, identical VMs |
| Storage model | Incremental after the first full copy | Full bootable image |
| Automation | Snapshot schedules with retention | Image families that point to the latest image |
| Typical source | An attached disk, even while in use | A prepared boot disk, snapshot, or another image |
| Exam cue | "daily backups", "restore the disk" | "golden image", "instance template for a MIG" |

**Remember:** use snapshots to recover data and images to build new machines.

### Horizontal Pod Autoscaler vs Vertical Pod Autoscaler vs cluster autoscaler

| | HPA | VPA | Cluster autoscaler |
|---|---|---|---|
| Changes | Number of Pod replicas | CPU and memory requests per Pod | Number of nodes in a pool |
| Triggered by | CPU, memory, custom or external metrics | Observed historical usage | Unschedulable Pods, underused nodes |
| Disruption | None to existing Pods | Auto mode evicts Pods to resize them | Drains nodes it removes |
| On Autopilot | Works | Works | Handled by Google |
| Exam cue | "traffic varies, add replicas" | "requests were guessed wrong" | "Pods stuck Pending for capacity" |

**Remember:** HPA adds Pods, VPA resizes Pods, and the cluster autoscaler adds nodes. Keep HPA off any CPU or memory signal that VPA is also tuning.

### Cloud SQL backups vs point-in-time recovery

| | Automated or on-demand backup | Point-in-time recovery |
|---|---|---|
| Restores to | The moment the backup was taken | Any timestamp within the retained log window |
| Requirements | Backups enabled | Automated backups plus transaction logging |
| Restore target | Same or a new instance | A new instance |
| Exam cue | "restore last night's state" | "undo a DELETE run ten minutes ago" |

**Remember:** a backup restores a saved state. PITR restores to a moment you choose, and it must be enabled before the incident.

### Signed URL vs IAM binding

| | Signed URL | IAM binding |
|---|---|---|
| Who it serves | Anyone holding the URL, no Google identity needed | A specific principal: user, group, or service account |
| Scope | One object | Bucket, project, or higher |
| Lifetime | Expires after the set duration | Lasts until removed |
| Exam cue | "partner or customer needs temporary access to a file" | "team needs ongoing read access" |

**Remember:** use a signed URL to share one object temporarily with someone outside your org. Never make a bucket public to share one file.

### Admin Activity vs Data Access audit logs

| | Admin Activity | Data Access |
|---|---|---|
| Records | Changes to configuration and metadata | Reads of configuration, reads and writes of user data |
| Default | Always on, cannot be disabled | Off for most services (on for BigQuery) |
| Charge | None | Charged |
| Stored in | `_Required` bucket | `_Default` bucket |
| Exam cue | "who deleted the VM", "who changed IAM" | "who read the objects", "who queried the table" |

**Remember:** Admin Activity logs are always there. Data Access logs exist only if someone enabled them before the event.

### Cloud Trace vs Cloud Profiler vs Query Insights

| | Cloud Trace | Cloud Profiler | Query Insights |
|---|---|---|---|
| Looks at | A request's path across services | Function-level CPU and memory use in running code | Query performance inside a database instance |
| Output | Spans showing where latency accrues | Flame graphs | Slow queries, plans, index advice |
| Where | Distributed applications | Production application code | Cloud SQL and AlloyDB |
| Exam cue | "which service in the chain is slow" | "why does this service burn CPU" | "which SQL statement is slow" |

**Remember:** Trace follows a request across services, Profiler looks inside the code, and Query Insights looks inside the database.

## Domain 4: Configuring access and security

### Basic vs predefined vs custom roles

| | Basic | Predefined | Custom |
|---|---|---|---|
| Defined by | Google (legacy) | Google, per service | You |
| Breadth | Every service in the project | One job on one service | Exactly the permissions you list |
| Kept current as services change | Yes | Yes | No, you maintain it |
| Where it can be created | Built in | Built in | Project or organization, never a folder |
| Exam cue | "Owner", "Editor", "Viewer" (usually wrong) | "least privilege", "service-specific access" | "only start and stop VMs", "no predefined role matches" |

**Remember:** default to predefined roles. Use custom only when no predefined role fits, and avoid basic roles in production.

### Project-level vs organization-level custom role

| | Project-level custom role | Organization-level custom role |
|---|---|---|
| Usable in | Only the project that defines it | Any project in the organization |
| Created with | `gcloud iam roles create ... --project` | `gcloud iam roles create ... --organization` |
| Fits | A one-off need in a single project | The same custom role needed across many projects |
| Exam cue | "only in this project" | "reuse across all projects", "avoid duplicating the role" |

**Remember:** a custom role can't be defined on a folder. For reuse across projects, define it at the organization.

### serviceAccountUser vs serviceAccountTokenCreator

| | `roles/iam.serviceAccountUser` | `roles/iam.serviceAccountTokenCreator` |
|---|---|---|
| Lets the holder | Attach the account to resources and run workloads as it | Mint short-lived tokens and sign as the account |
| Typical holder | People or pipelines deploying VMs or Cloud Run services | Engineers or automation impersonating the account |
| Used with | `--service-account` on create or deploy | `--impersonate-service-account`, `auth print-access-token` |
| Risk if granted project-wide | Can attach every service account in the project | Can impersonate every service account in the project |
| Exam cue | "deploy a VM that runs as this account" | "temporarily act as the account without a key" |

**Remember:** User is for attaching the account, TokenCreator is for impersonating it. Grant both on the individual account, never project-wide.

### Service account key vs impersonation

| | Service account key | Impersonation |
|---|---|---|
| Credential | JSON private key file | Short-lived token from the Service Account Credentials API |
| Lifetime | Until deleted | One hour by default |
| Who shows in audit logs | Only the service account | The human or caller who impersonated |
| Revoking access | Delete the key everywhere it was used | Remove the TokenCreator grant or group membership |
| Exam cue | "third-party tool only accepts a key file" | "engineers occasionally need the account's powers" |

**Remember:** a key is a long-lived secret someone can steal. Impersonation gives the same access briefly and records who used it.

### Workload Identity Federation for GKE vs Workload Identity Federation vs Workforce Identity Federation

| | Workload Identity Federation for GKE | Workload Identity Federation | Workforce Identity Federation |
|---|---|---|---|
| Identity type | Software in GKE Pods | Software outside Google Cloud | Human users |
| Trusts | Kubernetes service account tokens | External OIDC or SAML tokens | External IdP sign-ins |
| Setup | Cluster pool, `workloadIdentityUser` binding, KSA annotation | Workload identity pool, provider, attribute conditions | Workforce pool at the organization, provider, attribute mappings |
| Exam cue | "GKE app needs Cloud Storage without keys" | "CI/CD in another platform deploys to Cloud Run" | "employees sign in with our IdP, no Google Accounts" |

**Remember:** GKE Pods use the GKE feature, outside software uses Workload Identity Federation, and people use Workforce Identity Federation.

[← Back to the study guide](README.md)
