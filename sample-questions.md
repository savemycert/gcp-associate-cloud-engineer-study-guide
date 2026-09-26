# ACE Sample Questions with Answers

20 worked practice questions for the **Google Cloud Associate Cloud Engineer (ACE)** exam. Try each one before you open the answer.

For many more, with the same explanation on every option, use the [ACE practice questions](https://www.savemycert.com/practice/gcp-associate-cloud-engineer/?utm_source=github&utm_medium=readme&utm_campaign=gcp-associate-cloud-engineer-study-guide) on SaveMyCert.

## Question 1

*Setting up a cloud solution environment*

**A new cloud administrator is documenting how resources are organized in the company's Google Cloud environment. Which option correctly describes the Google Cloud resource hierarchy from the top level down?** Choose one.

- **a.** Projects, then organization, then folders, then resources
- **b.** Organization, then projects, then folders, then resources
- **c.** Folders, then organization, then projects, then resources
- **d.** Organization, then folders, then projects, then resources

<details>
<summary>Show answer and explanation</summary>

**Answer: d**

- **a** ❌ Projects are a middle layer that hold resources; they cannot contain the organization node, which is always the top of the hierarchy.
- **b** ❌ Folders sit between the organization and projects, not beneath projects. A project can never contain a folder.
- **c** ❌ The organization node is always the root of the hierarchy. Folders can only exist beneath an organization, never above it.
- **d** ✅ Correct. The organization node is the root, folders provide optional grouping beneath it, projects sit inside folders (or directly under the organization), and individual resources such as VMs and buckets belong to projects.

**The concept.** The Google Cloud resource hierarchy defines how resources are grouped and how IAM policies and organization policies flow downward. Its layers, from top to bottom, are the organization node, folders, projects, and finally the service resources themselves.

**Why this is correct.** Option (a) is the only ordering that matches Resource Manager's model: the organization is the root, folders provide optional intermediate grouping (for departments, teams, or environments), every resource must belong to exactly one project, and projects attach to a folder or directly to the organization. Options (b) and (d) invert the folder/project relationship — projects can never contain folders. Option (c) places folders above the organization, which is impossible because the organization node is always the root of the hierarchy.

**How to reason it out**

1. Remember the hierarchy top-down: organization, folders, projects, resources.
2. Note that folders are optional and can be nested, but projects and the organization node are not optional in an enterprise setup.
3. Recall that IAM allow policies set at a higher level are inherited by everything beneath that level, which is why the ordering matters on the exam.

> **Exam tip:** The hierarchy is organization, folders, projects, resources — and policies inherit downward through it.

</details>

📖 Learn this topic: [Setting Up Google Cloud Projects, Resource Hierarchy, and IAM](https://www.savemycert.com/revision/gcp-associate-cloud-engineer/gcp-projects-resource-hierarchy-iam-setup/?utm_source=github&utm_medium=readme&utm_campaign=gcp-associate-cloud-engineer-study-guide)

---

## Question 2

*Setting up a cloud solution environment*

**Your data engineering team of 15 people needs the BigQuery Data Editor role on all 12 projects inside the "analytics" folder. Team membership changes monthly. Following Google-recommended practices, what is the MOST appropriate way to grant this access?** Choose one.

- **a.** Grant the BigQuery Data Editor role to the group at the organization level
- **b.** Grant the BigQuery Data Editor role to a Google group containing the team, on the "analytics" folder
- **c.** Create a single shared user account for the team and grant it the role on the folder
- **d.** Grant the BigQuery Data Editor role to each engineer individually on each of the 12 projects

<details>
<summary>Show answer and explanation</summary>

**Answer: b**

- **a** ❌ An organization-level grant is inherited by every project in the company, not just the analytics folder — it violates least privilege by over-scoping the access.
- **b** ✅ Correct. Granting the role to a group at the folder level means all 12 projects inherit the binding, and joiners/leavers are handled by editing group membership instead of touching IAM policies.
- **c** ❌ Shared accounts destroy audit accountability and violate Google's identity best practices; every person should authenticate with their own identity.
- **d** ❌ This creates 180 individual bindings that must be updated every time the team changes, which is error-prone and against Google's recommendation to grant roles to groups.

**The concept.** Two Google-recommended practices combine here: grant IAM roles to Google groups rather than individual users, and grant roles at the highest level of the hierarchy where they legitimately apply so inheritance does the fan-out for you.

**Why this is correct.** A single binding of BigQuery Data Editor to the team's group on the "analytics" folder is inherited by all 12 projects beneath it, and monthly membership churn becomes a group-membership edit rather than an IAM change. Individual per-project grants (b) multiply the maintenance burden and risk drift. An organization-level grant (c) leaks the role into every unrelated project, violating least privilege. A shared account (d) removes individual accountability from Cloud Audit Logs and is explicitly discouraged.

**How to reason it out**

1. Create or reuse a Google group in Cloud Identity that represents the data engineering team.
2. Add a single IAM binding on the "analytics" folder granting roles/bigquery.dataEditor to that group.
3. Manage joiners and leavers through group membership; the IAM policy never needs to change.

> **Exam tip:** Grant roles to groups at the folder level and let inheritance and group membership do the ongoing work.

</details>

📖 Learn this topic: [Setting Up Google Cloud Projects, Resource Hierarchy, and IAM](https://www.savemycert.com/revision/gcp-associate-cloud-engineer/gcp-projects-resource-hierarchy-iam-setup/?utm_source=github&utm_medium=readme&utm_campaign=gcp-associate-cloud-engineer-study-guide)

---

## Question 3

*Setting up a cloud solution environment*

**A security auditor was granted the Viewer role at the organization level. The owners of one sensitive project want to prevent the auditor from viewing that specific project while leaving access to all other projects intact. What should you tell them?** Choose one.

- **a.** Remove the auditor's binding from the sensitive project's IAM policy
- **b.** Inherited roles cannot be revoked at a lower level; the organization-level grant itself must be changed or rescoped
- **c.** Add a deny entry for the auditor in the project's allow policy
- **d.** Create an organization policy constraint on the project that blocks the auditor's access

<details>
<summary>Show answer and explanation</summary>

**Answer: b**

- **a** ❌ The binding does not exist on the project — it exists at the organization level. There is nothing on the project's own policy to remove, and inherited access remains.
- **b** ✅ Correct. IAM allow policies are additive: the effective policy is the union of the resource's policy and everything inherited. A child resource cannot subtract an ancestor's grant, so the fix must happen where the grant was made.
- **c** ❌ Allow policies have no deny entries. Denials require a separate mechanism, and the standard ACE-level answer is that allow policies are additive and allow-only.
- **d** ❌ Organization policy constraints restrict what configurations are allowed (for example, blocking external IPs); they do not remove a specific principal's IAM access.

**The concept.** IAM allow policies in Google Cloud are additive and allow-only. A resource's effective policy is the union of the policy set directly on it and the policies inherited from its ancestors — a lower level can add access but never take inherited access away.

**Why this is correct.** Because the Viewer grant lives at the organization level, it flows down to every project, including the sensitive one. Option (b) is right: the only clean fixes are to change the grant at its source — for example, remove the org-level binding and instead grant Viewer on the folders or projects the auditor actually needs. Option (a) fails because there is no project-level binding to remove. Option (c) confuses organization policy (configuration guardrails) with IAM (who can do what). Option (d) invents deny entries inside allow policies, which do not exist.

**How to reason it out**

1. Identify where the binding was made — here, on the organization node.
2. Recall that inheritance is additive: child policies cannot subtract ancestor grants.
3. Rescope the grant: remove the org-level Viewer binding and re-grant it on the specific folders or projects that should be visible.

> **Exam tip:** You cannot revoke an inherited role at a lower level — fix the grant where it was made.

</details>

📖 Learn this topic: [Setting Up Google Cloud Projects, Resource Hierarchy, and IAM](https://www.savemycert.com/revision/gcp-associate-cloud-engineer/gcp-projects-resource-hierarchy-iam-setup/?utm_source=github&utm_medium=readme&utm_campaign=gcp-associate-cloud-engineer-study-guide)

---

## Question 4

*Setting up a cloud solution environment*

**A new administrator is learning how Cloud Billing accounts relate to projects. Which statement correctly describes the relationship?** Choose one.

- **a.** A project can split its charges across several billing accounts simultaneously
- **b.** Billing accounts are created inside a project and deleted along with it
- **c.** Each project must have its own dedicated billing account
- **d.** One billing account can pay for many projects, but each project is linked to at most one billing account at a time

<details>
<summary>Show answer and explanation</summary>

**Answer: d**

- **a** ❌ A project's usage always charges to the single billing account it is linked to; cost splitting is done through reporting and exports, not multiple links.
- **b** ❌ A billing account is an independent resource associated with the organization and a payments profile — it exists outside any project and survives project deletion.
- **c** ❌ Billing accounts are designed to be shared — most organizations fund many projects from a small number of billing accounts.
- **d** ✅ Correct. Billing accounts and projects have a one-to-many relationship: a billing account funds any number of projects, while a project charges to exactly one billing account (or none, if billing is disabled).

**The concept.** A Cloud Billing account is a standalone resource that defines who pays (via its payments profile). Projects link to a billing account to run paid services: one billing account to many projects, each project to at most one billing account at a time.

**Why this is correct.** Option (a) states the one-to-many model exactly. Option (b) inverts common practice — dedicated per-project billing accounts would multiply administrative overhead for no benefit. Option (c) is impossible: charges flow to the single linked billing account, and any cost allocation across teams is a reporting exercise (labels, BigQuery export), not a linking feature. Option (d) misplaces the billing account in the hierarchy: it is not a project resource, and unlinking or deleting a project does not touch the billing account itself.

**How to reason it out**

1. Remember the cardinality: billing account to projects is one-to-many; project to billing account is at most one.
2. Treat the billing account as an organization-level resource with its own IAM policy and payments profile.
3. Use labels and billing export for cost breakdowns rather than expecting multiple billing links per project.

> **Exam tip:** One billing account funds many projects; each project links to at most one billing account.

</details>

📖 Learn this topic: [Google Cloud Billing Accounts, Budgets, Alerts, and BigQuery Export](https://www.savemycert.com/revision/gcp-associate-cloud-engineer/gcp-billing-accounts-budgets-alerts/?utm_source=github&utm_medium=readme&utm_campaign=gcp-associate-cloud-engineer-study-guide)

---

## Question 5

*Setting up a cloud solution environment*

**Your compliance team requires that no Compute Engine VM anywhere in the company can be created with an external IP address, regardless of which IAM roles the creator holds. What is the MOST appropriate mechanism?** Choose one.

- **a.** Remove the Compute Admin role from all users and grant a custom role without the external IP permission
- **b.** Configure a firewall rule in every VPC that blocks inbound traffic from the internet
- **c.** Apply the compute.vmExternalIpAccess organization policy constraint at the organization level
- **d.** Set a budget alert so administrators are notified when VMs with external IPs are created

<details>
<summary>Show answer and explanation</summary>

**Answer: c**

- **a** ❌ IAM controls who can act, not what configurations are valid. Role surgery is fragile, hard to maintain across every current and future project, and there is no single permission that maps cleanly to "assign external IP".
- **b** ❌ Firewall rules filter traffic to VMs; they do not prevent an external IP from being assigned in the first place, which is what the requirement demands.
- **c** ✅ Correct. Organization policy constraints are configuration guardrails that apply no matter what IAM permissions a user has, and setting the constraint at the organization node covers every project.
- **d** ❌ Budgets monitor spend, not network configuration, and alerts are reactive notifications — they cannot prevent anything.

**The concept.** Organization policies and IAM answer different questions. IAM decides who can perform actions; organization policy constraints decide what resource configurations are allowed at all, and they bind everyone — even project owners — throughout the subtree where they are set.

**Why this is correct.** The requirement is a configuration guardrail that must hold "regardless of IAM roles," which is the textbook use case for an organization policy constraint. Applying compute.vmExternalIpAccess (set to deny all) at the organization node enforces it everywhere, including future projects. Option (b) tries to encode a configuration rule in IAM, which cannot express it reliably and must be repeated for every principal. Option (c) restricts traffic after the fact but leaves the external IP assignable. Option (d) is monitoring, not prevention.

**How to reason it out**

1. Recognize "must hold regardless of who or which role" as an organization policy requirement, not an IAM one.
2. Choose the matching constraint — compute.vmExternalIpAccess — and configure it to deny all external IPs.
3. Set the constraint at the organization node so every current and future folder and project inherits it.

> **Exam tip:** Use organization policy constraints for what can be configured; use IAM for who can act.

</details>

📖 Learn this topic: [Setting Up Google Cloud Projects, Resource Hierarchy, and IAM](https://www.savemycert.com/revision/gcp-associate-cloud-engineer/gcp-projects-resource-hierarchy-iam-setup/?utm_source=github&utm_medium=readme&utm_campaign=gcp-associate-cloud-engineer-study-guide)

---

## Question 6

*Planning and implementing a cloud solution*

**A startup is deploying a stateless, containerized REST API. Traffic is bursty and unpredictable, and the service is sometimes idle for hours at a time. The team wants a fully managed platform that scales to zero when idle and requires the LEAST operational overhead. Which compute option should they choose?** Choose one.

- **a.** A managed instance group of Compute Engine VMs
- **b.** Cloud Run
- **c.** A GKE Standard cluster
- **d.** A single Compute Engine VM with the container installed

<details>
<summary>Show answer and explanation</summary>

**Answer: b**

- **a** ❌ A managed instance group can autoscale, but it cannot scale to zero for a serving workload behind a load balancer without downtime, and the team must maintain images, patching, and instance templates.
- **b** ✅ Cloud Run runs stateless containers on a fully managed platform, autoscales with request traffic, and scales to zero when idle, so the team pays nothing during idle hours and manages no infrastructure.
- **c** ❌ GKE Standard requires the team to provision and manage node pools, and the cluster's nodes keep billing even when the API is idle, so it neither scales to zero nor minimizes operations.
- **d** ❌ A single VM bills continuously whether or not requests arrive, provides no autoscaling, and puts OS patching and availability entirely on the team.

**The concept.** Compute selection on Google Cloud maps workload characteristics to the platform that manages the most for you: Cloud Run for stateless request-driven containers, GKE for orchestrated container fleets you control, and Compute Engine when you need the VM itself.

**Why this is correct.** The workload is stateless, containerized, and request-driven with long idle periods - exactly the Cloud Run profile. Cloud Run bills per use, scales from zero to many instances automatically, and removes all node and OS management. GKE and Compute Engine both leave always-on infrastructure and operational tasks with the team, which contradicts the least-operational-overhead and scale-to-zero requirements.

**How to reason it out**

1. Identify the workload shape: stateless container, HTTP-driven, bursty traffic, long idle periods.
2. Match scale-to-zero plus fully managed requirements to serverless container platforms, which on Google Cloud means Cloud Run.
3. Eliminate GKE and Compute Engine options because both keep billable infrastructure running while idle and require node or VM management.

> **Exam tip:** Stateless container plus bursty traffic plus minimal ops equals Cloud Run - it is the only listed option that scales to zero.

</details>

📖 Learn this topic: [Choosing and Deploying Compute: Compute Engine, GKE, and Cloud Run](https://www.savemycert.com/revision/gcp-associate-cloud-engineer/gcp-compute-engine-gke-cloud-run-selection/?utm_source=github&utm_medium=readme&utm_campaign=gcp-associate-cloud-engineer-study-guide)

---

## Question 7

*Planning and implementing a cloud solution*

**A company is migrating a legacy application that depends on a specific Linux kernel module, requires full root access to the operating system, and cannot be containerized without a major rewrite. Which Google Cloud compute option should the company use?** Choose one.

- **a.** GKE Autopilot
- **b.** Cloud Run
- **c.** Cloud Run functions
- **d.** Compute Engine

<details>
<summary>Show answer and explanation</summary>

**Answer: d**

- **a** ❌ GKE Autopilot requires containerized workloads and blocks privileged access to nodes; Google manages the node OS, so custom kernel modules cannot be installed.
- **b** ❌ Cloud Run runs containers on a managed runtime; you cannot load custom kernel modules or control the host operating system, and the app is not containerized.
- **c** ❌ Cloud Run functions execute event-driven code snippets in a managed runtime with no OS-level access, which cannot host a full legacy application with kernel dependencies.
- **d** ✅ Compute Engine provides full virtual machines where you control the OS image, kernel modules, and root access, which is exactly what a non-containerizable legacy application needs.

**The concept.** When a workload needs OS-level control - custom kernels, kernel modules, licensed OS images, or root access - infrastructure-as-a-service (Compute Engine) is the right layer; container and serverless platforms deliberately abstract the OS away.

**Why this is correct.** Only Compute Engine gives the team its own VM with a chosen OS image, root access, and the ability to load kernel modules. Cloud Run and Cloud Run functions run managed runtimes with no host access, and GKE Autopilot manages the node OS for you and requires containers, so none of them can satisfy the kernel-module dependency.

**How to reason it out**

1. List the hard requirements: custom kernel module, root OS access, no containerization.
2. Recognize that every serverless and Autopilot option abstracts away the operating system, failing those requirements.
3. Choose Compute Engine, the IaaS layer where you own the guest OS end to end.

> **Exam tip:** Kernel or OS-level requirements always point to Compute Engine - managed container platforms never expose the host OS.

</details>

📖 Learn this topic: [Choosing and Deploying Compute: Compute Engine, GKE, and Cloud Run](https://www.savemycert.com/revision/gcp-associate-cloud-engineer/gcp-compute-engine-gke-cloud-run-selection/?utm_source=github&utm_medium=readme&utm_campaign=gcp-associate-cloud-engineer-study-guide)

---

## Question 8

*Planning and implementing a cloud solution*

**A regional e-commerce web application needs a fully managed relational database for orders and inventory. The team uses MySQL, requires ACID transactions, and wants automated backups and patching without managing database servers. Which service should they choose?** Choose one.

- **a.** Bigtable
- **b.** Memorystore for Redis
- **c.** Cloud SQL for MySQL
- **d.** BigQuery

<details>
<summary>Show answer and explanation</summary>

**Answer: c**

- **a** ❌ Bigtable is a wide-column NoSQL store with no SQL joins or multi-row ACID transactions in the relational sense - wrong model for an orders-and-inventory schema built on MySQL.
- **b** ❌ Memorystore is an in-memory cache; it is not a durable system of record and offers no relational schema or transactional guarantees for order data.
- **c** ✅ Cloud SQL is Google Cloud's managed relational database for MySQL, PostgreSQL, and SQL Server - it provides ACID transactions, automated backups, patching, and high availability for regional applications.
- **d** ❌ BigQuery is an analytics warehouse optimized for scanning large datasets, not for the high-frequency, low-latency transactional reads and writes an order system performs.

**The concept.** Cloud SQL is the default choice for regional, MySQL- or PostgreSQL-compatible OLTP workloads: managed relational databases with transactions, backups, replicas, and maintenance handled by Google.

**Why this is correct.** Every signal in the scenario - relational schema, MySQL compatibility, ACID transactions, regional scope, managed operations - maps to Cloud SQL. Bigtable and BigQuery serve NoSQL time-series and analytics needs respectively, and Memorystore is a cache layer, not a system of record.

**How to reason it out**

1. Classify the workload: relational OLTP with an existing MySQL engine dependency.
2. Check the scope: a single-region application, so global distribution (Spanner) is not required.
3. Choose Cloud SQL for MySQL and enable automated backups and high availability as needed.

> **Exam tip:** Regional relational OLTP with MySQL or PostgreSQL compatibility is Cloud SQL territory.

</details>

📖 Learn this topic: [Choosing Storage and Databases: Cloud SQL, BigQuery, Spanner, and More](https://www.savemycert.com/revision/gcp-associate-cloud-engineer/gcp-storage-data-cloud-sql-bigquery-spanner/?utm_source=github&utm_medium=readme&utm_campaign=gcp-associate-cloud-engineer-study-guide)

---

## Question 9

*Planning and implementing a cloud solution*

**Your organization is designing a new VPC network for a production workload that will run in three specific regions. The network team must have full control over subnet IP ranges because the on-premises network already uses 10.128.0.0/9 and address overlap must be avoided. What should you do?** Choose one.

- **a.** Create a custom mode VPC and manually define subnets with non-overlapping CIDR ranges in the three required regions.
- **b.** Create three separate auto mode VPCs, one for each region.
- **c.** Create an auto mode VPC so that subnets are provisioned automatically in every region.
- **d.** Use the default network that Google Cloud creates in every new project.

<details>
<summary>Show answer and explanation</summary>

**Answer: a**

- **a** ✅ Custom mode gives you complete control over which regions get subnets and which CIDR ranges they use, so you can avoid the 10.128.0.0/9 on-premises space entirely.
- **b** ❌ VPC networks are global resources, and each auto mode VPC would still create subnets in every region from the overlapping 10.128.0.0/9 range.
- **c** ❌ Auto mode VPCs allocate their subnet ranges from 10.128.0.0/9, which directly overlaps the on-premises address space and would break hybrid routing.
- **d** ❌ The default network is an auto mode VPC, so its subnets also come from 10.128.0.0/9 and would overlap the on-premises ranges.

**The concept.** Google Cloud VPCs come in two subnet creation modes: auto mode, which automatically creates one subnet per region from the predefined 10.128.0.0/9 block, and custom mode, which creates no subnets until you define them yourself.

**Why this is correct.** Because the on-premises network already occupies 10.128.0.0/9, any auto mode network (including the default network) would produce overlapping routes that make hybrid connectivity unreliable. A custom mode VPC starts empty, letting the network team place subnets only in the three required regions and assign CIDR ranges that do not collide with on-premises space. Google's own guidance is to use custom mode for production networks precisely because of this control.

**How to reason it out**

1. Identify the address space already in use on-premises (here, 10.128.0.0/9).
2. Create a custom mode VPC, which starts with no subnets.
3. Define one subnet per required region with a CIDR range outside the reserved on-premises space.
4. Document the IP plan so future subnets and peered networks also avoid overlap.

> **Exam tip:** Use custom mode VPCs for production; auto mode's fixed 10.128.0.0/9 ranges frequently collide with on-premises networks.

</details>

📖 Learn this topic: [VPC Networks, Firewall Rules, and Load Balancing on Google Cloud](https://www.savemycert.com/revision/gcp-associate-cloud-engineer/gcp-vpc-subnets-firewall-load-balancing/?utm_source=github&utm_medium=readme&utm_campaign=gcp-associate-cloud-engineer-study-guide)

---

## Question 10

*Planning and implementing a cloud solution*

**A new team member clones a repository containing Terraform configuration for Google Cloud and immediately runs terraform plan. The command fails with an error stating that required provider plugins are not installed. What should they run first?** Choose one.

- **a.** terraform apply -auto-approve
- **b.** terraform init
- **c.** gcloud auth application-default login
- **d.** terraform validate

<details>
<summary>Show answer and explanation</summary>

**Answer: b**

- **a** ❌ apply depends on the same initialized providers and backend as plan, so it fails with the identical error until init has been run.
- **b** ✅ terraform init initializes the working directory: it downloads the required providers (such as the google provider) and configures the backend, and it must run before plan or apply in a fresh checkout.
- **c** ❌ Setting up Application Default Credentials addresses authentication, not the missing provider plugins that init is responsible for installing.
- **d** ❌ validate checks configuration syntax and internal consistency, but it also requires an initialized directory with providers installed.

**The concept.** terraform init prepares a working directory for use: it installs the provider plugins declared in the configuration (for example hashicorp/google), initializes any modules, and configures the state backend.

**Why this is correct.** The error explicitly says provider plugins are missing, and provider installation is the job of terraform init. In a freshly cloned repository nothing has been downloaded yet, so init is always the first command. Once init completes, plan can build its execution graph against the installed google provider. Credentials commands solve a different class of failure (permission or authentication errors), and neither validate nor apply can run in an uninitialized directory.

**How to reason it out**

1. Clone the repository and change into the Terraform configuration directory.
2. Run terraform init to download providers, initialize modules, and configure the backend.
3. Run terraform plan to preview the proposed changes.
4. Run terraform apply once the plan output has been reviewed.

> **Exam tip:** terraform init is always the first command in a fresh checkout - it installs providers and wires up the backend.

</details>

📖 Learn this topic: [Terraform, Config Connector, and AI-Assisted Tooling on Google Cloud](https://www.savemycert.com/revision/gcp-associate-cloud-engineer/gcp-iac-terraform-deployment-tooling/?utm_source=github&utm_medium=readme&utm_campaign=gcp-associate-cloud-engineer-study-guide)

---

## Question 11

*Planning and implementing a cloud solution*

**A media company runs a nightly batch rendering job on Compute Engine. The job checkpoints its progress to Cloud Storage every few minutes and can restart from the last checkpoint if interrupted. Which option is the MOST cost-effective way to run this job?** Choose one.

- **a.** Run the job on Spot VMs
- **b.** Run the job on standard VMs and rely on sustained use discounts
- **c.** Purchase a three-year committed use discount for standard VMs
- **d.** Run the job on sole-tenant nodes

<details>
<summary>Show answer and explanation</summary>

**Answer: a**

- **a** ✅ Spot VMs offer the deepest Compute Engine discounts (typically 60-91 percent off on-demand) in exchange for possible preemption, which this checkpointed, restartable job tolerates by design.
- **b** ❌ Sustained use discounts are modest and apply to VMs running a large portion of the month; a nightly job neither runs long enough to maximize them nor comes close to Spot pricing.
- **c** ❌ Committed use discounts require a long-term spend commitment and suit steady, predictable 24/7 usage - paying for committed capacity that sits idle most of the day costs more than Spot for an interruptible nightly batch.
- **d** ❌ Sole-tenant nodes dedicate an entire physical server to you for compliance or licensing needs and carry a price premium - the opposite of a cost optimization.

**The concept.** Spot VMs are Compute Engine capacity sold at a steep discount because Google can preempt them at any time; they are the standard cost lever for fault-tolerant, interruptible workloads like batch processing.

**Why this is correct.** The job's checkpoint-and-restart design means preemption costs only a few minutes of rework, so the workload matches the Spot model exactly and captures the largest possible discount. Sustained use discounts are small and usage-proportional, committed use requires paying for capacity around the clock, and sole-tenant nodes increase cost rather than reduce it.

**How to reason it out**

1. Confirm the workload is fault tolerant: it checkpoints and can resume after interruption.
2. Map fault-tolerant batch work to Spot VMs, the discount model built for preemptible capacity.
3. Reject discount models designed for steady always-on usage (sustained use, committed use) and premium isolation options (sole-tenant).

> **Exam tip:** If a workload can survive preemption, Spot VMs are almost always the most cost-effective Compute Engine choice.

</details>

📖 Learn this topic: [Choosing and Deploying Compute: Compute Engine, GKE, and Cloud Run](https://www.savemycert.com/revision/gcp-associate-cloud-engineer/gcp-compute-engine-gke-cloud-run-selection/?utm_source=github&utm_medium=readme&utm_campaign=gcp-associate-cloud-engineer-study-guide)

---

## Question 12

*Ensuring the successful operation of a cloud solution*

**A Compute Engine instance in your project was created without an external IP address for security reasons. You need to open an SSH session to it from your laptop using the gcloud CLI, without adding a bastion host or assigning a public IP. What should you do?** Choose one.

- **a.** Assign an ephemeral external IP to the instance, connect, and then remove the IP afterwards.
- **b.** Create a Cloud NAT gateway in the VM's region so SSH traffic can reach the instance.
- **c.** Run gcloud compute ssh with the --tunnel-through-iap flag to connect through Identity-Aware Proxy TCP forwarding.
- **d.** Open firewall port 22 to 0.0.0.0/0 so the instance's internal IP becomes reachable from the internet.

<details>
<summary>Show answer and explanation</summary>

**Answer: c**

- **a** ❌ Temporarily exposing the VM with a public IP defeats the security posture the team chose and adds manual toil; IAP tunneling avoids the external IP entirely.
- **b** ❌ Cloud NAT only provides outbound internet access for instances without external IPs; it never accepts inbound connections such as SSH from your laptop.
- **c** ✅ IAP TCP forwarding tunnels SSH traffic through Google's IAP infrastructure to the instance's internal IP, so no external IP or bastion host is needed.
- **d** ❌ A firewall rule cannot make an RFC 1918 internal IP routable from the internet, and opening SSH to 0.0.0.0/0 is a security anti-pattern.

**The concept.** Identity-Aware Proxy (IAP) TCP forwarding lets you SSH or RDP into Compute Engine instances that have no external IP by tunneling the connection through Google's IAP service, authenticated by IAM.

**Why this is correct.** gcloud compute ssh INSTANCE --tunnel-through-iap wraps the SSH session in an IAP tunnel that terminates on the instance's internal IP. Access is controlled by the roles/iap.tunnelResourceAccessor IAM role and a firewall rule allowing IAP's source range, so the VM stays completely private. An ephemeral external IP re-exposes the VM, Cloud NAT is outbound-only by design, and no firewall rule can make a private internal address internet-routable.

**How to reason it out**

1. Grant your user the roles/iap.tunnelResourceAccessor role on the instance or project.
2. Ensure a firewall rule allows ingress on tcp:22 from IAP's source range 35.235.240.0/20.
3. Connect with: gcloud compute ssh INSTANCE_NAME --zone=ZONE --tunnel-through-iap.

> **Exam tip:** To SSH into a VM with no external IP, use gcloud compute ssh --tunnel-through-iap with IAP TCP forwarding.

</details>

📖 Learn this topic: [Managing Compute Engine, GKE, and Cloud Run: Day-2 Operations](https://www.savemycert.com/revision/gcp-associate-cloud-engineer/gcp-managing-compute-gke-instances-autoscaling/?utm_source=github&utm_medium=readme&utm_campaign=gcp-associate-cloud-engineer-study-guide)

---

## Question 13

*Ensuring the successful operation of a cloud solution*

**Your security team wants SSH access to all Compute Engine instances in a project to be governed centrally through IAM roles instead of per-instance SSH keys in metadata. Engineers who leave the company must lose VM access the moment their Google account is disabled. What should you configure?** Choose one.

- **a.** Require engineers to connect only through the Cloud Console SSH-in-browser button.
- **b.** Store each engineer's public SSH key in project-wide metadata so keys are managed in one place.
- **c.** Enable OS Login by setting the enable-oslogin=TRUE metadata key at the project level and grant engineers the roles/compute.osLogin IAM role.
- **d.** Create a shared service account key that engineers use to SSH into instances.

<details>
<summary>Show answer and explanation</summary>

**Answer: c**

- **a** ❌ SSH-in-browser is just a client; without OS Login the Console still provisions metadata-based keys, so access is not tied to IAM role membership.
- **b** ❌ Project metadata keys are still static SSH keys: disabling a Google account does not remove the key, so departed engineers can keep connecting until keys are manually cleaned up.
- **c** ✅ OS Login links Linux accounts to Google identities, so SSH access is granted through IAM roles and is revoked automatically when the Google account is disabled.
- **d** ❌ Sharing one credential removes individual accountability and revoking a single engineer means rotating the key for everyone; it is the opposite of centralized per-identity control.

**The concept.** OS Login replaces metadata-managed SSH keys with IAM-governed access: a user's POSIX identity and SSH capability derive from their Google account and the roles/compute.osLogin or roles/compute.osAdminLogin roles.

**Why this is correct.** With enable-oslogin=TRUE set in project metadata, every instance in the project requires OS Login. Access is then purely a function of live IAM bindings, so suspending or deleting a Google Workspace account instantly removes SSH access with no key cleanup. Metadata SSH keys and shared service account keys persist after offboarding, and SSH-in-browser without OS Login still relies on metadata keys.

**How to reason it out**

1. Set enable-oslogin=TRUE in project-wide metadata (gcloud compute project-info add-metadata --metadata enable-oslogin=TRUE).
2. Grant engineers roles/compute.osLogin (or osAdminLogin for sudo) on the project or specific instances.
3. Have engineers connect with gcloud compute ssh; their POSIX account is derived from their Google identity.
4. Offboard by disabling the Google account or removing the IAM binding — no key rotation needed.

> **Exam tip:** OS Login ties SSH access to IAM identities, so revoking the Google account revokes VM access instantly.

</details>

📖 Learn this topic: [Managing Compute Engine, GKE, and Cloud Run: Day-2 Operations](https://www.savemycert.com/revision/gcp-associate-cloud-engineer/gcp-managing-compute-gke-instances-autoscaling/?utm_source=github&utm_medium=readme&utm_campaign=gcp-associate-cloud-engineer-study-guide)

---

## Question 14

*Ensuring the successful operation of a cloud solution*

**A security audit of a Cloud Storage bucket finds a confusing mix of per-object ACLs and bucket-level IAM bindings, and no one can say definitively who can read which object. You must ensure access is controlled exclusively through IAM at the bucket level going forward. What should you do?** Choose one.

- **a.** Enable uniform bucket-level access on the bucket so ACLs are disabled and IAM policies alone govern access.
- **b.** Enable object versioning so previous permission states can be reviewed.
- **c.** Set the predefined ACL private on every existing object with a batch job.
- **d.** Move the objects into a new bucket protected by VPC Service Controls.

<details>
<summary>Show answer and explanation</summary>

**Answer: a**

- **a** ✅ Uniform bucket-level access turns off object ACLs entirely, making bucket IAM the single, auditable source of truth for permissions.
- **b** ❌ Versioning preserves object data generations; it records nothing about permissions and does not change how access is evaluated.
- **c** ❌ Rewriting ACLs still leaves the dual ACL+IAM model in place — future objects can get divergent ACLs again, so the audit problem returns.
- **d** ❌ VPC Service Controls constrain which network/service perimeters can reach the API; they don't consolidate the ACL-versus-IAM permission model inside the bucket.

**The concept.** Cloud Storage supports two permission systems — fine-grained per-object ACLs and bucket-level IAM. Uniform bucket-level access disables ACLs so that only IAM applies, which is Google's recommended model.

**Why this is correct.** Enabling uniform bucket-level access makes every access decision flow through the bucket's IAM policy (plus inherited project/folder/org policies), eliminating the per-object ACL exceptions the audit could not account for. It can be enforced org-wide with the constraints/storage.uniformBucketLevelAccess org policy. Rewriting ACLs is a temporary cleanup, versioning is a data-protection feature, and VPC Service Controls solve perimeter exfiltration, not intra-bucket permission sprawl.

**How to reason it out**

1. Review existing object ACLs to identify grants that must be re-expressed as IAM bindings.
2. Enable the setting: gcloud storage buckets update gs://BUCKET --uniform-bucket-level-access.
3. Add equivalent IAM bindings (e.g., roles/storage.objectViewer) for principals who still need access.
4. Note that after 90 days the setting becomes permanent, locking in the IAM-only model.

> **Exam tip:** Uniform bucket-level access disables ACLs so bucket IAM is the only permission system — the recommended state for auditable buckets.

</details>

📖 Learn this topic: [Managing Cloud Storage and Databases: Lifecycle, Backups, and CMEK](https://www.savemycert.com/revision/gcp-associate-cloud-engineer/gcp-managing-cloud-storage-databases-cmek/?utm_source=github&utm_medium=readme&utm_campaign=gcp-associate-cloud-engineer-study-guide)

---

## Question 15

*Ensuring the successful operation of a cloud solution*

**Your team's subnet in a custom-mode VPC network was created with the range 10.10.0.0/24 and is nearly out of usable addresses. Running Compute Engine VMs in the subnet must keep their current IP addresses. What should you do?** Choose one.

- **a.** Add a secondary IP range to the subnet and migrate the VMs' primary interfaces onto it.
- **b.** Switch the VPC network to auto mode so that Google automatically enlarges the subnet ranges in every region.
- **c.** Delete the subnet and recreate it with a larger range, then restart the VMs so they pick up new addresses.
- **d.** Run gcloud compute networks subnets expand-ip-range to widen the subnet's primary range to a larger CIDR block such as /22.

<details>
<summary>Show answer and explanation</summary>

**Answer: d**

- **a** ❌ Secondary ranges are used for alias IPs (for example, GKE Pods and Services), not for VM primary interface addresses. Moving primary interfaces would also change the VMs' IPs.
- **b** ❌ You can convert auto mode to custom mode, not the reverse, and auto-mode subnets use fixed /20 ranges carved from 10.128.0.0/9 — there is no automatic enlargement of an existing custom subnet.
- **c** ❌ A subnet cannot be deleted while resources use it, and recreating it would force new IP assignments — exactly what the requirement forbids. Expansion in place avoids all of this.
- **d** ✅ Correct. A subnet's primary IPv4 range can be expanded in place to a larger (numerically smaller prefix length) CIDR block without disrupting running VMs, and existing IPs are preserved.

**The concept.** Subnet primary IPv4 ranges in Google Cloud can be expanded in place, and expansion is the supported way to add capacity without disturbing existing workloads.

**Why this is correct.** The gcloud compute networks subnets expand-ip-range command grows the primary range to a shorter prefix (for example, /24 to /22) while the network keeps serving traffic. Existing VM addresses fall inside the new, larger range, so nothing needs to be recreated or re-addressed. Deleting and recreating the subnet is blocked while it is in use and would change IPs; auto mode cannot be enabled on a custom-mode network; secondary ranges serve alias IPs, not primary interfaces.

**How to reason it out**

1. Confirm the new, larger CIDR block does not overlap any other subnet in the VPC network or any peered network.
2. Run gcloud compute networks subnets expand-ip-range SUBNET --region=REGION --prefix-length=22.
3. Verify the new range with gcloud compute networks subnets describe and confirm existing VMs retained their addresses.

> **Exam tip:** Subnet ranges can only be expanded, never shrunk — use expand-ip-range to add capacity without touching running VMs.

</details>

📖 Learn this topic: [Managing VPC Networking: Subnets, Static IPs, Cloud DNS, and Cloud NAT](https://www.savemycert.com/revision/gcp-associate-cloud-engineer/gcp-managing-vpc-networking-dns-nat/?utm_source=github&utm_medium=readme&utm_campaign=gcp-associate-cloud-engineer-study-guide)

---

## Question 16

*Ensuring the successful operation of a cloud solution*

**Your operations team must be emailed whenever CPU utilization on a production Compute Engine VM stays above 90% for 10 minutes. Which Cloud Monitoring configuration accomplishes this?** Choose one.

- **a.** An alerting policy with a metric-threshold condition on CPU utilization (90% for a 10-minute duration) and an email notification channel attached.
- **b.** A log-based metric counting CPU log lines, exported to BigQuery for daily analysis.
- **c.** An uptime check against the VM's external IP with email alerting.
- **d.** A dashboard with a CPU utilization chart that the team checks each morning.

<details>
<summary>Show answer and explanation</summary>

**Answer: a**

- **a** ✅ Correct. Alerting policies combine a condition (metric, threshold, retest window/duration) with notification channels; this matches the requirement exactly.
- **b** ❌ CPU utilization is a system metric, not a log entry, and a daily BigQuery report is not real-time notification. This pipeline solves a different problem.
- **c** ❌ Uptime checks probe availability of an endpoint from external locations; they cannot measure CPU utilization or any resource metric.
- **d** ❌ Dashboards visualize metrics but never send notifications. Sustained 90% CPU could go unnoticed for hours between manual checks.

**The concept.** Cloud Monitoring alerting policies pair one or more conditions (typically a metric threshold sustained for a duration) with notification channels such as email, SMS, PagerDuty, or Pub/Sub.

**Why this is correct.** The requirement names a metric (CPU utilization), a threshold (90%), a duration (10 minutes), and a delivery method (email) — the four ingredients of a standard metric-threshold alerting policy. The duration window prevents flapping on brief spikes. Dashboards are passive, uptime checks test reachability rather than resource metrics, and log pipelines add latency and complexity for data Monitoring already collects.

**How to reason it out**

1. Create an email notification channel in Cloud Monitoring (Alerting > Notification channels).
2. Create an alerting policy with a condition on compute.googleapis.com/instance/cpu/utilization, threshold above 0.9, and a 10-minute duration.
3. Attach the email channel, add runbook documentation to the policy, and test by generating load on a non-critical VM.

> **Exam tip:** Alerting policy = condition (metric + threshold + duration) + notification channel; dashboards alone never notify anyone.

</details>

📖 Learn this topic: [Cloud Monitoring and Logging: Alerts, Log Sinks, and Audit Logs](https://www.savemycert.com/revision/gcp-associate-cloud-engineer/gcp-cloud-monitoring-logging-alerts/?utm_source=github&utm_medium=readme&utm_campaign=gcp-associate-cloud-engineer-study-guide)

---

## Question 17

*Configuring access and security*

**A security auditor asks you to produce a list of every principal that currently has a role granted directly on the project my-app-prod. Which gcloud command should you run?** Choose one.

- **a.** gcloud iam service-accounts list --project my-app-prod
- **b.** gcloud projects add-iam-policy-binding my-app-prod
- **c.** gcloud iam roles list --project my-app-prod
- **d.** gcloud projects get-iam-policy my-app-prod

<details>
<summary>Show answer and explanation</summary>

**Answer: d**

- **a** ❌ This lists the service accounts that exist in the project as resources; it does not show which principals hold roles on the project.
- **b** ❌ add-iam-policy-binding modifies the policy by granting a role to a principal; it does not display the existing policy, and it requires --member and --role flags.
- **c** ❌ This lists the custom roles defined in the project, not who has been granted roles. It says nothing about principals or bindings.
- **d** ✅ Correct. get-iam-policy returns the project's IAM policy, which lists every role binding and the principals (users, groups, service accounts) in each binding.

**The concept.** An IAM policy is a collection of role bindings attached to a resource, where each binding pairs a role with a list of principals. Viewing the policy on a project shows exactly who can do what at that level.

**Why this is correct.** gcloud projects get-iam-policy <PROJECT_ID> prints the IAM policy attached to the project, including every binding of role to principals. That is precisely what an access audit needs. The other commands either modify the policy (add-iam-policy-binding), list role definitions (iam roles list), or list service account resources (service-accounts list) — none of them reports the project's role bindings.

**How to reason it out**

1. Identify the resource whose access you need to audit — here, the project my-app-prod.
2. Run gcloud projects get-iam-policy my-app-prod to retrieve the policy attached to that project.
3. Read the bindings array: each entry shows a role and the members (principals) granted that role.
4. Remember that this shows only direct grants at the project level; roles inherited from the folder or organization appear in the ancestors' policies.

> **Exam tip:** Use gcloud projects get-iam-policy to view a project's role bindings; add-iam-policy-binding changes them.

</details>

📖 Learn this topic: [Managing Cloud IAM: Roles, Policies, and Inheritance](https://www.savemycert.com/revision/gcp-associate-cloud-engineer/gcp-iam-roles-policies-management/?utm_source=github&utm_medium=readme&utm_campaign=gcp-associate-cloud-engineer-study-guide)

---

## Question 18

*Configuring access and security*

**You need to grant the user dana@example.com the ability to view (but not modify) Compute Engine resources in the project web-prod using the command line. Which command follows Google-recommended practice?** Choose one.

- **a.** gcloud projects add-iam-policy-binding web-prod --member="user:dana@example.com" --role="roles/viewer"
- **b.** gcloud projects set-iam-policy web-prod --member="user:dana@example.com" --role="roles/compute.viewer"
- **c.** gcloud projects add-iam-policy-binding web-prod --member="user:dana@example.com" --role="roles/compute.viewer"
- **d.** gcloud iam roles update compute.viewer --add-user dana@example.com

<details>
<summary>Show answer and explanation</summary>

**Answer: c**

- **a** ❌ roles/viewer is a basic role that grants read access to nearly every service in the project, far more than Compute Engine. It violates least privilege when only Compute visibility is needed.
- **b** ❌ set-iam-policy replaces the entire policy from a JSON/YAML file and does not accept --member/--role flags. Using it carelessly can wipe existing bindings.
- **c** ✅ Correct. This grants the predefined, least-privilege compute.viewer role to the user principal on the project using the standard binding syntax.
- **d** ❌ gcloud iam roles update edits a custom role's definition (its permission list); it does not grant roles to principals, and there is no --add-user flag.

**The concept.** Granting access in Cloud IAM means adding a binding of a role to a principal on a resource. Predefined roles scoped to one service (like roles/compute.viewer) are preferred over basic roles (Owner/Editor/Viewer) because they follow least privilege.

**Why this is correct.** add-iam-policy-binding is the safe, additive command for granting a single role: it reads the current policy, appends the binding, and writes it back. roles/compute.viewer grants read-only Compute Engine permissions and nothing else, matching the requirement exactly. The basic roles/viewer role grants read access across almost all services, and set-iam-policy is a full policy replacement that risks clobbering other bindings.

**How to reason it out**

1. Choose the narrowest role that satisfies the need — a predefined service-specific role like roles/compute.viewer rather than a basic role.
2. Identify the principal type prefix: user: for a human Google Account.
3. Run gcloud projects add-iam-policy-binding with --member and --role to append the binding safely.
4. Verify with gcloud projects get-iam-policy that the binding was added as intended.

> **Exam tip:** Grant the narrowest predefined role with add-iam-policy-binding; avoid basic roles and full-policy replacement.

</details>

📖 Learn this topic: [Managing Cloud IAM: Roles, Policies, and Inheritance](https://www.savemycert.com/revision/gcp-associate-cloud-engineer/gcp-iam-roles-policies-management/?utm_source=github&utm_medium=readme&utm_campaign=gcp-associate-cloud-engineer-study-guide)

---

## Question 19

*Configuring access and security*

**You need to create a new user-managed service account named build-runner in the project ci-tools using the command line. Which command is correct?** Choose one.

- **a.** gcloud iam service-accounts create build-runner --project=ci-tools --display-name="Build runner"
- **b.** gcloud iam service-accounts keys create build-runner --project=ci-tools
- **c.** gcloud projects add-iam-policy-binding ci-tools --member="serviceAccount:build-runner" --role="roles/iam.serviceAccountUser"
- **d.** gcloud iam roles create build-runner --project=ci-tools

<details>
<summary>Show answer and explanation</summary>

**Answer: a**

- **a** ✅ Correct. gcloud iam service-accounts create takes the account ID (which becomes the email prefix) plus an optional display name, in the target project.
- **b** ❌ keys create mints a key for an existing service account (and requires --iam-account); it does not create the account itself — and generating keys should be avoided anyway.
- **c** ❌ add-iam-policy-binding grants a role to an existing principal; it cannot create the service account, and the member string is not even a full SA email.
- **d** ❌ This creates a custom role, not a service account — roles and service accounts are entirely different objects.

**The concept.** A service account is a special identity for workloads, created as a resource inside a project. User-managed service accounts get an email of the form <id>@<project>.iam.gserviceaccount.com.

**Why this is correct.** gcloud iam service-accounts create build-runner creates the account resource; the ID becomes the local part of build-runner@ci-tools.iam.gserviceaccount.com. The other commands operate on different objects: roles create defines a custom role, add-iam-policy-binding grants roles to existing principals, and keys create issues credentials for an account that must already exist.

**How to reason it out**

1. Run gcloud iam service-accounts create build-runner --project=ci-tools --display-name="Build runner".
2. Note the generated email build-runner@ci-tools.iam.gserviceaccount.com — this is the principal you will grant roles to.
3. Grant the account only the narrow roles its workload needs with add-iam-policy-binding.
4. Attach the account to the workload (VM, Cloud Run service, GKE) instead of exporting keys.

> **Exam tip:** gcloud iam service-accounts create makes the identity; granting roles and attaching it to workloads are separate follow-up steps.

</details>

📖 Learn this topic: [Service Accounts, Impersonation, and Workload Identity Federation](https://www.savemycert.com/revision/gcp-associate-cloud-engineer/gcp-service-accounts-workload-identity/?utm_source=github&utm_medium=readme&utm_campaign=gcp-associate-cloud-engineer-study-guide)

---

## Question 20

*Configuring access and security*

**A new operations engineer must be able to start, stop, and reconfigure Compute Engine VM instances in a production project, but must not be able to modify Cloud Storage buckets, BigQuery datasets, or IAM policies. Following Google-recommended practices, which role should you grant?** Choose one.

- **a.** The basic role roles/editor on the project
- **b.** The predefined role roles/compute.instanceAdmin.v1 on the project
- **c.** The predefined role roles/compute.viewer on the project
- **d.** The basic role roles/owner on the project

<details>
<summary>Show answer and explanation</summary>

**Answer: b**

- **a** ❌ Editor grants modify access to nearly every service in the project, including Cloud Storage and BigQuery, which the engineer must not have. Google recommends avoiding basic roles in production.
- **b** ✅ Correct. This predefined role grants full management of Compute Engine instances (create, start, stop, modify) without touching storage, analytics, or IAM — least privilege for the stated job.
- **c** ❌ compute.viewer is read-only: the engineer could see instances but could not start, stop, or reconfigure them.
- **d** ❌ Owner includes everything Editor has plus the ability to manage IAM policies — the opposite of the requirement and the broadest possible grant.

**The concept.** Cloud IAM offers basic roles (Owner, Editor, Viewer), predefined roles maintained by Google per service, and custom roles you assemble yourself. Predefined roles are the recommended default because they scope permissions to one service and one job function.

**Why this is correct.** roles/compute.instanceAdmin.v1 contains the permissions to create, modify, start, and stop VM instances and their disks, but includes no Cloud Storage, BigQuery, or IAM-administration permissions. It exactly matches 'manage VMs, nothing else'. Editor and Owner are project-wide basic roles that sweep in every other service (Owner even adds IAM control), and compute.viewer cannot change instance state at all.

**How to reason it out**

1. List the actions the principal actually needs: start, stop, and reconfigure VM instances.
2. Search the predefined roles for the Compute Engine role whose permission set matches that job — roles/compute.instanceAdmin.v1.
3. Confirm the role does not include permissions on the services that must stay off-limits.
4. Grant it at the project level with add-iam-policy-binding, and revisit if the scope of the job changes.

> **Exam tip:** Prefer service-specific predefined roles over basic Owner/Editor — they are how least privilege is done on GCP.

</details>

📖 Learn this topic: [Managing Cloud IAM: Roles, Policies, and Inheritance](https://www.savemycert.com/revision/gcp-associate-cloud-engineer/gcp-iam-roles-policies-management/?utm_source=github&utm_medium=readme&utm_campaign=gcp-associate-cloud-engineer-study-guide)

---

[← Back to the ACE study guide](README.md)
