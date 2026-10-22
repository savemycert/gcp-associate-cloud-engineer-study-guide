# ACE Sample Questions with Answers

20 worked practice questions for the **Google Cloud Associate Cloud Engineer (ACE)** exam. Try each one before you open the answer.

For many more, with the same explanation on every option, use the [ACE practice questions](https://www.savemycert.com/practice/gcp-associate-cloud-engineer/?utm_source=github&utm_medium=readme&utm_campaign=gcp-associate-cloud-engineer-study-guide) on SaveMyCert.

## Question 1

*Setting up a cloud solution environment*

**A new administrator is drawing your company's Google Cloud resource hierarchy for an onboarding document. The company uses an organization node, folders for departments, and projects for workloads. Which ordering from the root down is correct?** Choose one.

- **a.** Folders, then organization, then projects, then resources
- **b.** Projects, then organization, then folders, then resources
- **c.** Organization, then projects, then folders, then resources
- **d.** Organization, then folders, then projects, then resources

<details>
<summary>Show answer and explanation</summary>

**Answer: d**

- **a** ❌ The organization node is the root of the hierarchy. Folders are created beneath an organization, never above it.
- **b** ❌ A project cannot be the parent of an organization. Projects are children of a folder or of the organization node itself.
- **c** ❌ Projects cannot contain folders. Folders sit between the organization and projects, and a project only contains service resources.
- **d** ✅ The organization is the root, folders group projects beneath it, projects sit in a folder or directly under the organization, and every VM, bucket or database belongs to exactly one project.

**The concept.** The Google Cloud resource hierarchy is a tree managed by Resource Manager: one organization node at the root, optional (and nestable) folders, projects, and the service resources inside projects. IAM allow policies and organization policies attached to a node are inherited by everything below it.

**Why this is correct.** Only the organization-folders-projects-resources ordering matches the model. The organization node is always the root; folders give optional intermediate grouping for departments or environments; each resource belongs to one project; and a project is parented by a folder or by the organization. The other orderings either put folders under projects, which is impossible, or place something above the organization node.

**How to reason it out**

1. Identify the root: the organization node, created from a Cloud Identity or Google Workspace domain.
2. Place folders as the optional grouping layer beneath it (folders can nest).
3. Place projects under folders or directly under the organization.
4. Remember that every service resource lives inside exactly one project.

> **Exam tip:** Organization > folders > projects > resources, and policies inherit downward along that path.

</details>

📖 Learn this topic: [Setting Up Google Cloud Projects, Resource Hierarchy, and IAM](https://www.savemycert.com/revision/gcp-associate-cloud-engineer/gcp-projects-resource-hierarchy-iam-setup/?utm_source=github&utm_medium=readme&utm_campaign=gcp-associate-cloud-engineer-study-guide)

---

## Question 2

*Setting up a cloud solution environment*

**Your 15-person data engineering team needs the BigQuery Data Editor role on every project in the analytics folder, which has 12 projects today and gets new projects every quarter. Team membership changes monthly. You want the fewest IAM changes over time. What should you do?** Choose one.

- **a.** Grant the role to the team's Google group on each of the 12 projects
- **b.** Grant the role to the team's Google group on the analytics folder
- **c.** Grant the role to the team's Google group on the organization node
- **d.** Grant the role to each engineer on each project in the analytics folder

<details>
<summary>Show answer and explanation</summary>

**Answer: b**

- **a** ❌ Membership changes are handled by the group, but every new project added to the folder needs another binding, so access drifts as the folder grows.
- **b** ✅ One binding on the folder is inherited by every current and future project in it, and joiners or leavers are handled by editing group membership rather than IAM policies.
- **c** ❌ Inheritance would cover the analytics projects, but it also grants BigQuery Data Editor on every other project in the company, which breaks least privilege.
- **d** ❌ This creates one binding per person per project and must be edited for every joiner, leaver and new project, which is the most IAM maintenance of all the choices.

**The concept.** Two practices combine here: bind roles to groups rather than individual users, and attach the binding at the lowest hierarchy node that covers all the resources that need it. Folder-level bindings are inherited by every project in the folder, including projects created later.

**Why this is correct.** A single group binding on the analytics folder satisfies both moving parts: people change through group membership, and new projects inherit the folder binding automatically. Per-project group grants handle people but not new projects; an organization-level grant handles both but over-scopes access to every project; per-user grants handle neither.

**How to reason it out**

1. List what changes over time: team members and the number of projects.
2. Group-based binding absorbs membership changes without IAM edits.
3. A folder-level binding absorbs new projects through inheritance.
4. Reject the organization node because it grants access beyond the analytics folder.

> **Exam tip:** Group plus the lowest common ancestor folder: people change in the group, projects inherit from the folder.

</details>

📖 Learn this topic: [Setting Up Google Cloud Projects, Resource Hierarchy, and IAM](https://www.savemycert.com/revision/gcp-associate-cloud-engineer/gcp-projects-resource-hierarchy-iam-setup/?utm_source=github&utm_medium=readme&utm_campaign=gcp-associate-cloud-engineer-study-guide)

---

## Question 3

*Setting up a cloud solution environment*

**A security auditor holds the Viewer role on the organization node and must keep it for every project except one sensitive project, which the auditor must no longer be able to read. The organization grant is managed by a central team and must not be changed. What should you do?** Choose one.

- **a.** Add a deny binding for the auditor to the sensitive project's allow policy
- **b.** Attach an IAM deny policy to the project denying the auditor's read permissions
- **c.** Remove the auditor's Viewer binding from the sensitive project's allow policy
- **d.** Set an organization policy constraint on the project that restricts the auditor

<details>
<summary>Show answer and explanation</summary>

**Answer: b**

- **a** ❌ Allow policies only contain role bindings that grant access. Denials are expressed in a separate IAM deny policy, not as an entry in the allow policy.
- **b** ✅ Deny policies can be attached to a project and are evaluated before allow policies, so denying the auditor's read permissions there blocks the inherited Viewer access on that project only.
- **c** ❌ The Viewer binding is not on the project's allow policy; it is inherited from the organization node, so there is nothing on the project to remove and the inherited access continues.
- **d** ❌ Organization policy constraints restrict resource configurations such as locations or external IPs. They do not take permissions away from a specific principal.

**The concept.** IAM allow policies are additive: the effective allow policy on a project is the union of its own bindings and every inherited binding, so nothing in a child allow policy can subtract an ancestor's grant. IAM deny policies are a separate policy type that can be attached to an organization, folder or project; IAM checks deny policies before allow policies.

**Why this is correct.** With the organization grant off-limits, the only way to remove access on one project is a deny policy attached to that project that names the auditor and the read permissions to block (permission groups such as all get or list permissions of a service are supported). Removing a project binding fails because the grant is inherited, an allow policy has no deny entries, and organization policy constraints govern configurations rather than principals.

**How to reason it out**

1. Locate the grant: it is inherited from the organization node, not set on the project.
2. Recall that allow policies only add access, so a child allow policy cannot revoke it.
3. Recall that deny policies are a separate policy type attachable at the project and checked first.
4. Discard organization policy constraints, which do not target principals.

> **Exam tip:** Allow policies only add; to carve an exception out of an inherited grant, attach an IAM deny policy lower down.

</details>

📖 Learn this topic: [Setting Up Google Cloud Projects, Resource Hierarchy, and IAM](https://www.savemycert.com/revision/gcp-associate-cloud-engineer/gcp-projects-resource-hierarchy-iam-setup/?utm_source=github&utm_medium=readme&utm_campaign=gcp-associate-cloud-engineer-study-guide)

---

## Question 4

*Setting up a cloud solution environment*

**A startup runs a dev project and a prod project, both paid by one Cloud Billing account. A new data team asks whether their upcoming analytics project can also use that account, and how charges flow. Which statement describes the relationship correctly?** Choose one.

- **a.** The analytics project can link to several billing accounts and split its charges
- **b.** Each project needs a dedicated billing account, so the team creates a new one
- **c.** The billing account lives inside the prod project and is deleted along with it
- **d.** One billing account can pay for many projects; each project links to one at a time

<details>
<summary>Show answer and explanation</summary>

**Answer: d**

- **a** ❌ A project is linked to at most one billing account at a time; cost splitting between teams is done with labels, reports and exports, not multiple links.
- **b** ❌ Billing accounts are built to be shared: one account routinely pays for many projects, so a new project does not need its own account.
- **c** ❌ A billing account is a standalone resource with its own IAM policy and payments profile; it is not created inside a project and survives project deletion.
- **d** ✅ Correct. The relationship is one-to-many: the shared account can fund the analytics project too, and that project charges to exactly one billing account (or none if billing is disabled).

**The concept.** A Cloud Billing account is a standalone resource that defines who pays (through its payments profile). Projects link to a billing account to use paid services: one billing account to many projects, each project to at most one billing account at a time.

**Why this is correct.** The shared account can fund the new analytics project because the billing-account-to-project relationship is one-to-many. A dedicated account per project is unnecessary overhead; a project cannot split charges across several accounts (allocation is done with labels and the BigQuery export); and a billing account is not a project-owned resource, so deleting a project never deletes it.

**How to reason it out**

1. Recall the cardinality: billing account to projects is one-to-many.
2. Recall the reverse: a project charges to at most one billing account at a time.
3. Remember the billing account sits outside projects, with its own IAM policy.
4. Use labels and exports, not multiple links, to divide costs between teams.

> **Exam tip:** One billing account funds many projects; each project links to at most one billing account.

</details>

📖 Learn this topic: [Google Cloud Billing Accounts, Budgets, Alerts, and BigQuery Export](https://www.savemycert.com/revision/gcp-associate-cloud-engineer/gcp-billing-accounts-budgets-alerts/?utm_source=github&utm_medium=readme&utm_campaign=gcp-associate-cloud-engineer-study-guide)

---

## Question 5

*Setting up a cloud solution environment*

**Your compliance team requires that no Compute Engine VM in the company can be given an external IP address, including VMs in projects created next year, and including VMs created by project Owners. You want a single control with the least ongoing effort. What should you do?** Choose one.

- **a.** Set the compute.vmExternalIpAccess constraint to deny all on each existing project
- **b.** Set the compute.restrictVpcPeering constraint to deny all on the organization node
- **c.** Set the compute.vmExternalIpAccess constraint to deny all on the organization node
- **d.** Create a hierarchical firewall policy on the organization that denies ingress from 0.0.0.0/0

<details>
<summary>Show answer and explanation</summary>

**Answer: c**

- **a** ❌ This is the right constraint at the wrong scope. Projects created next year would not have it, so someone must remember to repeat it on every new project.
- **b** ❌ This is a real constraint at the right scope, but it controls VPC Network Peering, not whether VMs can have external IP addresses.
- **c** ✅ The list constraint governs which VMs may have external IPs, applies regardless of the creator's IAM roles, and is inherited by every current and future project under the organization.
- **d** ❌ Firewall rules filter traffic. A VM could still be created with an external IP address, which is what the requirement forbids.

**The concept.** Organization policy constraints restrict what configurations are allowed, no matter which IAM roles the actor holds, and they are inherited down the hierarchy from where they are set. The compute.vmExternalIpAccess list constraint defines which VM instances (if any) may use external IP addresses.

**Why this is correct.** Setting compute.vmExternalIpAccess to deny all on the organization node meets every requirement at once: it blocks external IPs even for Owners, and new projects inherit it automatically. Applying it per project gives the right behaviour only until the next project appears; a hierarchical firewall policy blocks traffic but still lets the IP be assigned; compute.restrictVpcPeering is a real organization-level constraint that governs a different feature.

**How to reason it out**

1. Recognise a configuration guardrail that must hold regardless of IAM roles: an organization policy.
2. Pick the constraint that governs external IPs on VMs: compute.vmExternalIpAccess.
3. Pick the scope that covers future projects: the organization node.
4. Reject controls that act on traffic rather than on the configuration.

> **Exam tip:** Guardrails for every project, now and later, go on the organization node as an organization policy constraint.

</details>

📖 Learn this topic: [Setting Up Google Cloud Projects, Resource Hierarchy, and IAM](https://www.savemycert.com/revision/gcp-associate-cloud-engineer/gcp-projects-resource-hierarchy-iam-setup/?utm_source=github&utm_medium=readme&utm_campaign=gcp-associate-cloud-engineer-study-guide)

---

## Question 6

*Planning and implementing a cloud solution*

**Your team is deploying a stateless REST API that is already packaged as a container image. Traffic is bursty, and the API often receives no requests for several hours overnight. The team does not want to manage nodes or VMs, and compute charges should stop while no requests arrive. What should you do?** Choose one.

- **a.** Deploy the container to a GKE Autopilot cluster.
- **b.** Deploy the container as a Cloud Run service.
- **c.** Deploy the container to a GKE Standard cluster.
- **d.** Deploy the container to a managed instance group.

<details>
<summary>Show answer and explanation</summary>

**Answer: b**

- **a** ❌ Autopilot removes node management, but the cluster carries a management fee and the Deployment's Pods keep running and billing while idle, so charges do not stop overnight.
- **b** ✅ Cloud Run runs stateless containers with no nodes or VMs to manage, autoscales on request traffic and scales to zero instances when idle, so there are no instance charges overnight.
- **c** ❌ GKE Standard leaves node pools for the team to size, patch and pay for, and the nodes keep billing overnight even when no requests arrive.
- **d** ❌ A managed instance group runs VMs the team must image and patch, and the running VMs bill whether or not the API is serving requests.

**The concept.** Compute selection maps workload shape to the platform that manages the most for you: Cloud Run for stateless request-driven containers, GKE for orchestrated container fleets, Compute Engine when you need the VM itself.

**Why this is correct.** The API is stateless, already containerized and request-driven with long idle periods, and the team wants no nodes and no idle charges: that is the Cloud Run profile, because Cloud Run scales to zero instances. GKE Autopilot is the tempting near-twin since it also hides nodes, but the cluster fee and the running Pods keep billing while idle. GKE Standard and managed instance groups add node or VM management on top of idle charges.

**How to reason it out**

1. Note the workload shape: stateless container, HTTP requests, bursty, idle for hours.
2. Apply the two constraints: no node or VM management, and no charges while idle.
3. Eliminate options that keep billable infrastructure running while idle (both GKE modes, managed instance groups).
4. Choose Cloud Run, which scales to zero.

> **Exam tip:** Stateless container + no infrastructure to manage + nothing to pay while idle = Cloud Run; Autopilot hides nodes but does not stop billing when idle.

</details>

📖 Learn this topic: [Choosing and Deploying Compute: Compute Engine, GKE, and Cloud Run](https://www.savemycert.com/revision/gcp-associate-cloud-engineer/gcp-compute-engine-gke-cloud-run-selection/?utm_source=github&utm_medium=readme&utm_campaign=gcp-associate-cloud-engineer-study-guide)

---

## Question 7

*Planning and implementing a cloud solution*

**Your company is migrating an on-premises inventory application to Google Cloud. The application loads a proprietary Linux kernel module at boot, its installer needs root access to the operating system, and the software vendor does not support running it in containers. Where should you run the application?** Choose one.

- **a.** On GKE Standard, using privileged Pods on the nodes
- **b.** On Cloud Run, using a custom container base image
- **c.** On GKE Autopilot, using a DaemonSet on the nodes
- **d.** On Compute Engine VMs, using a custom image

<details>
<summary>Show answer and explanation</summary>

**Answer: d**

- **a** ❌ Privileged Pods on Standard nodes could touch the node kernel, but this is still a containerized deployment, which the vendor does not support.
- **b** ❌ Cloud Run runs containers in a managed sandbox with no access to the host kernel, and the vendor does not support containers at all.
- **c** ❌ Autopilot manages the node OS and restricts privileged workloads, and it also requires containers, which the vendor does not support.
- **d** ✅ Compute Engine gives you the whole VM: you choose the OS image, have root access and can load kernel modules, which is what an uncontainerizable application with kernel dependencies needs.

**The concept.** When a workload needs OS-level control (kernel modules, root access, a specific OS image) or cannot be containerized, Compute Engine is the layer to use; container and serverless platforms deliberately abstract the OS away.

**Why this is correct.** Only Compute Engine lets the team own the guest OS end to end: custom image, root access and kernel modules. GKE Standard with privileged Pods is the tempting option because it can reach the node kernel, but it is still a container deployment, which the vendor does not support. Cloud Run and Autopilot manage the host for you and require containers.

**How to reason it out**

1. List the hard requirements: kernel module, root access, no containers.
2. Eliminate every container platform because the vendor forbids containers.
3. Choose Compute Engine, where you control the guest OS.

> **Exam tip:** Kernel or OS-level requirements plus no-container support point to Compute Engine VMs.

</details>

📖 Learn this topic: [Choosing and Deploying Compute: Compute Engine, GKE, and Cloud Run](https://www.savemycert.com/revision/gcp-associate-cloud-engineer/gcp-compute-engine-gke-cloud-run-selection/?utm_source=github&utm_medium=readme&utm_campaign=gcp-associate-cloud-engineer-study-guide)

---

## Question 8

*Planning and implementing a cloud solution*

**Your team is moving a regional order-management application from an on-premises MySQL 8.0 server to Google Cloud. The 300 GB database needs ACID transactions and must survive the loss of a zone, and the team no longer wants to handle OS patching, backups or replication itself. All users are in one region. What should you do?** Choose one.

- **a.** Deploy an AlloyDB for PostgreSQL cluster in the region
- **b.** Deploy a Spanner instance in a regional configuration
- **c.** Deploy a Cloud SQL for MySQL instance with regional HA
- **d.** Deploy a self-managed MySQL server on a Compute Engine VM

<details>
<summary>Show answer and explanation</summary>

**Answer: c**

- **a** ❌ AlloyDB is managed and highly available, but it is PostgreSQL-compatible only. Moving a MySQL application onto it means converting the schema and queries, which the scenario gives no reason to do.
- **b** ❌ Spanner would run the workload, but it is not MySQL: the application and schema would have to be migrated to a different SQL dialect, and Spanner is priced for horizontal scale this 300 GB regional database does not need.
- **c** ✅ Cloud SQL for MySQL keeps the engine, handles patching and automated backups, and regional availability adds a synchronous standby in a second zone with automatic failover, covering the zone-loss requirement.
- **d** ❌ This keeps MySQL compatibility but leaves patching, backups and replication with the team, which is exactly the operational work they want to stop doing.

**The concept.** Cloud SQL is the managed relational service for regional MySQL, PostgreSQL and SQL Server workloads: Google runs patching, backups and replication, and the REGIONAL availability type adds a standby in another zone of the same region.

**Why this is correct.** Three constraints decide it: the engine stays MySQL, the scope is one region, and operations must be managed. Cloud SQL for MySQL with regional HA meets all three. Spanner and AlloyDB are managed and resilient but force a change of database engine, and Spanner is the over-engineered choice for a 300 GB regional database. A MySQL VM keeps the engine but fails the managed-operations requirement.

**How to reason it out**

1. Note the engine dependency: the application speaks MySQL.
2. Note the scope: one region, modest size, so global scale-out is not needed.
3. Note the operations constraint: patching, backups and replication must be managed by Google.
4. Choose Cloud SQL for MySQL and set --availability-type=REGIONAL so a zonal failure triggers automatic failover.

> **Exam tip:** Regional relational OLTP that must keep MySQL or PostgreSQL and drop server management is Cloud SQL; regional HA covers zone loss.

</details>

📖 Learn this topic: [Choosing Storage and Databases: Cloud SQL, BigQuery, Spanner, and More](https://www.savemycert.com/revision/gcp-associate-cloud-engineer/gcp-storage-data-cloud-sql-bigquery-spanner/?utm_source=github&utm_medium=readme&utm_campaign=gcp-associate-cloud-engineer-study-guide)

---

## Question 9

*Planning and implementing a cloud solution*

**Your organization is designing a VPC network for a production workload that will run in three specific regions. The on-premises network already uses 10.128.0.0/9 and will later connect to the VPC over Cloud VPN, so no subnet range may overlap it. The network team also wants subnets only in the regions where workloads run. What should you do?** Choose one.

- **a.** Create a custom mode VPC network and add subnets in the three regions from a non-overlapping range.
- **b.** Use the project's default network and add firewall rules that block the overlapping 10.128.0.0/9 range.
- **c.** Create an auto mode VPC network and delete the subnets in regions the workload does not use.
- **d.** Create an auto mode VPC network, convert it to custom mode, then add subnets in the three regions.

<details>
<summary>Show answer and explanation</summary>

**Answer: a**

- **a** ✅ Custom mode starts with no subnets, so the team chooses both the regions and CIDR ranges and can stay clear of 10.128.0.0/9.
- **b** ❌ The default network is an auto mode network. Firewall rules filter traffic; they do not remove overlapping subnet routes, so hybrid routing would still conflict.
- **c** ❌ Auto mode subnets are allocated from 10.128.0.0/9, so the remaining subnets still overlap the on-premises range; auto-created subnets also cannot be deleted until the network is converted to custom mode.
- **d** ❌ Converting to custom mode keeps the subnets auto mode already created from 10.128.0.0/9, so the overlap and the unwanted regions remain.

**The concept.** A VPC network is created in auto mode, which creates one subnet per region from the fixed 10.128.0.0/9 block, or in custom mode, which creates no subnets until you define them.

**Why this is correct.** The on-premises network already uses 10.128.0.0/9, which is exactly the block auto mode draws from, so any auto mode network (including the default network) produces overlapping routes once Cloud VPN connects the two sides. A custom mode network starts empty, so the team adds subnets only in the three regions and picks ranges outside the on-premises space. Converting an auto mode network to custom mode is a one-way change, but it keeps the subnets that were already created, so it does not fix the overlap. Firewall rules cannot fix an addressing conflict because they filter packets rather than change routes.

**How to reason it out**

1. Note the address space already used on premises (10.128.0.0/9).
2. Recognise that auto mode and the default network allocate subnets from that same block.
3. Create a custom mode network, which starts with no subnets.
4. Add one subnet per required region from a range that does not overlap on-premises or other connected networks.

> **Exam tip:** Production and hybrid networks use custom mode VPCs; auto mode's 10.128.0.0/9 subnets often collide with other networks.

</details>

📖 Learn this topic: [VPC Networks, Firewall Rules, and Load Balancing on Google Cloud](https://www.savemycert.com/revision/gcp-associate-cloud-engineer/gcp-vpc-subnets-firewall-load-balancing/?utm_source=github&utm_medium=readme&utm_campaign=gcp-associate-cloud-engineer-study-guide)

---

## Question 10

*Planning and implementing a cloud solution*

**A teammate added a google-beta provider block and a resource that uses a preview feature to your team's Terraform configuration. You pull the change into your existing clone and run terraform plan, which fails before it reads any infrastructure because the google-beta provider is not available locally. What should you do?** Choose one.

- **a.** Run terraform validate to check the new blocks, then rerun terraform plan.
- **b.** Run terraform init in the clone's root, then rerun terraform plan.
- **c.** Run gcloud auth application-default login, then rerun terraform plan.
- **d.** Run terraform refresh to update state, then rerun terraform plan.

<details>
<summary>Show answer and explanation</summary>

**Answer: b**

- **a** ❌ validate needs an initialized directory with the providers installed, so it cannot run until the provider is downloaded; it checks syntax, it does not fetch plugins.
- **b** ✅ terraform init installs every provider the configuration requires (including a newly added google-beta) and updates the dependency lock file; it must be rerun whenever providers, modules or the backend change.
- **c** ❌ Application Default Credentials fix authentication failures when the provider calls Google Cloud APIs; they do not download a provider plugin that is missing.
- **d** ❌ terraform refresh (an alias for apply -refresh-only) still needs every required provider loaded, so it fails with the same missing-provider error; it also only updates state, it never installs anything.

**The concept.** terraform init prepares a working directory: it downloads the providers and modules the configuration declares, records provider versions in the dependency lock file and configures the state backend.

**Why this is correct.** Adding a provider (google-beta here) changes the configuration's dependencies, so the existing working directory is no longer fully initialized. Only terraform init installs providers, and it is safe to rerun in an existing directory. validate and terraform refresh both load providers first, so they fail the same way, and gcloud credentials solve a different problem: authentication at API-call time.

**How to reason it out**

1. Notice the failure happens before Terraform reads any infrastructure, so it is a local setup problem, not a credentials or state problem.
2. Identify what changed: a new provider requirement in the configuration.
3. Recall that providers, modules and backend settings are installed or configured only by terraform init.
4. Rerun terraform init, then terraform plan.

> **Exam tip:** New provider, new module or changed backend: run terraform init again before plan or apply.

</details>

📖 Learn this topic: [Terraform, Config Connector, and AI-Assisted Tooling on Google Cloud](https://www.savemycert.com/revision/gcp-associate-cloud-engineer/gcp-iac-terraform-deployment-tooling/?utm_source=github&utm_medium=readme&utm_campaign=gcp-associate-cloud-engineer-study-guide)

---

## Question 11

*Planning and implementing a cloud solution*

**A media company runs a rendering job on Compute Engine for about three hours each night. The job checkpoints its progress to Cloud Storage every few minutes and can resume from the last checkpoint if a worker VM stops. The company wants to lower the cost of the job without changing its code. What should you do?** Choose one.

- **a.** Run the rendering workers as Spot VMs
- **b.** Run the rendering workers under a 1-year committed use discount
- **c.** Run the rendering workers to earn sustained use discounts
- **d.** Run the rendering workers on sole-tenant nodes

<details>
<summary>Show answer and explanation</summary>

**Answer: a**

- **a** ✅ Spot VMs carry the deepest Compute Engine discount in exchange for possible preemption, which this checkpointed, resumable job tolerates by design.
- **b** ❌ Committed use discounts bill the committed capacity around the clock, which suits steady 24/7 usage, not a job that runs three hours a night.
- **c** ❌ Sustained use discounts apply automatically only once a VM runs for more than about 25% of the month; three hours a night is roughly 12% of the month, so the job earns little or nothing.
- **d** ❌ Sole-tenant nodes dedicate a physical host for isolation or licensing reasons and cost more than shared capacity.

**The concept.** Spot VMs are spare Compute Engine capacity sold at a steep discount (typically 60-91% off on-demand) that Compute Engine can preempt at any time; they are the standard cost lever for fault-tolerant, interruptible work.

**Why this is correct.** Because the job checkpoints every few minutes, a preemption costs only a little rework, so Spot pricing fits it exactly. A committed use discount is tempting, but you pay for the commitment 24 hours a day while the job runs three. Sustained use discounts only start once a VM runs for more than a quarter of the month, which a three-hour nightly job never reaches, and sole-tenant nodes add a premium.

**How to reason it out**

1. Confirm the workload tolerates interruption: it checkpoints and resumes.
2. Note the usage pattern: about three hours a night, roughly 12% of the month.
3. Rule out discounts tied to steady usage: committed use (billed 24/7) and sustained use (needs more than 25% of the month).
4. Match interruptible, short-duration work to Spot VMs.

> **Exam tip:** Interruptible, checkpointed work points to Spot VMs; committed use and sustained use discounts only pay off for steady usage.

</details>

📖 Learn this topic: [Choosing and Deploying Compute: Compute Engine, GKE, and Cloud Run](https://www.savemycert.com/revision/gcp-associate-cloud-engineer/gcp-compute-engine-gke-cloud-run-selection/?utm_source=github&utm_medium=readme&utm_campaign=gcp-associate-cloud-engineer-study-guide)

---

## Question 12

*Ensuring the successful operation of a cloud solution*

**A Compute Engine VM was created without an external IP address. An engineer must open an SSH session to it from a laptop on the public internet. The company has no VPN or Interconnect into the VPC, will not run a bastion host, and does not allow a public IP on the VM, even temporarily. What should the engineer do?** Choose one.

- **a.** Attach an ephemeral external IP, run gcloud compute ssh, then remove the IP.
- **b.** Run gcloud compute ssh with the --internal-ip flag.
- **c.** Run gcloud compute ssh with the --tunnel-through-iap flag.
- **d.** Add a Cloud NAT gateway to the subnet, then run gcloud compute ssh.

<details>
<summary>Show answer and explanation</summary>

**Answer: c**

- **a** ❌ This works mechanically but puts a public IP on the VM, which the stem forbids even temporarily.
- **b** ❌ --internal-ip makes gcloud connect to the VM's private address, which only works from a network with a route into the VPC (VPN, Interconnect, another VM); a laptop on the internet has none.
- **c** ✅ IAP TCP forwarding carries the SSH session from the laptop through Identity-Aware Proxy to the VM's internal IP, so no public IP, VPN or bastion is needed.
- **d** ❌ Cloud NAT translates outbound connections from the VM; it does not accept inbound SSH sessions from the internet.

**The concept.** Identity-Aware Proxy (IAP) TCP forwarding lets you SSH or RDP to Compute Engine VMs that have only internal IPs, by tunnelling the connection through Google's IAP service and authorising it with IAM.

**Why this is correct.** gcloud compute ssh --tunnel-through-iap wraps the SSH session in an IAP tunnel that ends on the VM's internal IP, so the VM stays private and the laptop needs nothing but gcloud and IAM permission. The tempting near-twin is --internal-ip: it also avoids a public IP, but it needs network reachability to the private address, which the stem rules out (no VPN or Interconnect). A temporary external IP breaks the stated policy, and Cloud NAT is outbound-only.

**How to reason it out**

1. Note the constraints: no external IP, no bastion, no private network path from the laptop.
2. Eliminate anything that adds a public IP or relies on a route into the VPC (--internal-ip).
3. Remember Cloud NAT is egress-only, so it cannot carry an inbound SSH session.
4. Choose IAP TCP forwarding: gcloud compute ssh VM --zone=ZONE --tunnel-through-iap.

> **Exam tip:** No external IP and no private network path: gcloud compute ssh --tunnel-through-iap; --internal-ip needs a VPN, Interconnect or a host inside the VPC.

</details>

📖 Learn this topic: [Managing Compute Engine, GKE, and Cloud Run: Day-2 Operations](https://www.savemycert.com/revision/gcp-associate-cloud-engineer/gcp-managing-compute-gke-instances-autoscaling/?utm_source=github&utm_medium=readme&utm_campaign=gcp-associate-cloud-engineer-study-guide)

---

## Question 13

*Ensuring the successful operation of a cloud solution*

**Your security team wants SSH access to every Linux VM in a project to be granted and revoked through IAM, with no SSH keys stored in metadata to clean up. When an engineer leaves and their Google account is disabled, their VM access must end at once. Engineers do not need sudo. What should you configure?** Choose one.

- **a.** Set enable-oslogin=TRUE in project metadata and grant roles/compute.viewer.
- **b.** Add each engineer's public key to project metadata and grant roles/compute.instanceAdmin.v1.
- **c.** Set enable-oslogin=TRUE in project metadata and grant roles/compute.osLogin.
- **d.** Set block-project-ssh-keys=TRUE on each VM and grant roles/iap.tunnelResourceAccessor.

<details>
<summary>Show answer and explanation</summary>

**Answer: c**

- **a** ❌ OS Login is enabled, but compute.viewer has no OS Login permission, so the engineers could not log in at all.
- **b** ❌ Project metadata keys are static: disabling the Google account does not remove the key, so access persists until someone deletes it.
- **c** ✅ OS Login maps each Linux account to a Google identity and checks IAM at login, so disabling the account or removing the role ends access; compute.osLogin gives login without sudo.
- **d** ❌ Blocking project keys pushes keys into instance metadata instead, and the IAP role only authorises tunnels; neither ties the Linux login to IAM.

**The concept.** OS Login replaces metadata-managed SSH keys with IAM-governed access: a user's POSIX account and right to log in come from their Google identity plus roles/compute.osLogin (or roles/compute.osAdminLogin for sudo).

**Why this is correct.** With enable-oslogin=TRUE in project metadata, every VM in the project uses OS Login, so login is decided by live IAM bindings at connection time. Disabling the Google account or removing the binding ends access with no key cleanup. The near-twin with compute.viewer enables OS Login but grants no login permission; metadata keys survive offboarding; the IAP tunnel role controls the network path, not the Linux login. If the VM runs as a service account, engineers also need roles/iam.serviceAccountUser on it.

**How to reason it out**

1. Identify the requirement: login decided by IAM, nothing to clean up on offboarding.
2. Enable OS Login project-wide with enable-oslogin=TRUE in project metadata.
3. Pick the role by privilege: compute.osLogin for standard users, compute.osAdminLogin for sudo.
4. Offboard by disabling the account or removing the binding; no key rotation is needed.

> **Exam tip:** OS Login = enable-oslogin=TRUE plus roles/compute.osLogin (osAdminLogin for sudo); access then follows the Google account.

</details>

📖 Learn this topic: [Managing Compute Engine, GKE, and Cloud Run: Day-2 Operations](https://www.savemycert.com/revision/gcp-associate-cloud-engineer/gcp-managing-compute-gke-instances-autoscaling/?utm_source=github&utm_medium=readme&utm_campaign=gcp-associate-cloud-engineer-study-guide)

---

## Question 14

*Ensuring the successful operation of a cloud solution*

**A security audit of an existing Cloud Storage bucket finds a mix of per-object ACL entries and IAM bindings on the bucket, and no one can say who can read which object. The team wants every future access decision on this bucket, including for objects uploaded later, to come from IAM policies alone. What should you do?** Choose one.

- **a.** Enable uniform bucket-level access on the bucket and re-create needed grants as IAM bindings.
- **b.** Enforce the storage.uniformBucketLevelAccess organization policy constraint on the project.
- **c.** Enable public access prevention and remove the allUsers bindings from the bucket IAM policy.
- **d.** Run a batch job that resets each existing object's ACL to the private predefined ACL.

<details>
<summary>Show answer and explanation</summary>

**Answer: a**

- **a** ✅ Uniform bucket-level access disables object ACLs on this bucket, so bucket IAM (plus inherited policies) is the only permission system for current and future objects.
- **b** ❌ This constraint requires uniform bucket-level access on newly created buckets; it does not switch an existing fine-grained bucket over, so this bucket keeps its ACLs.
- **c** ❌ Public access prevention blocks grants to allUsers and allAuthenticatedUsers, but non-public object ACLs keep working, so the dual permission model remains.
- **d** ❌ Resetting ACLs cleans up today's objects, but ACLs stay active, so a later upload or script can attach a divergent object ACL again.

**The concept.** Cloud Storage has two permission systems: bucket-level IAM and per-object ACLs (fine-grained access). Uniform bucket-level access turns ACLs off for a bucket so IAM alone decides.

**Why this is correct.** The requirement is about this existing bucket and about objects uploaded later, so the fix must disable ACLs on the bucket itself. Enabling uniform bucket-level access does exactly that, after which grants are expressed only as IAM bindings. Resetting ACLs is a one-time cleanup that leaves ACLs usable, public access prevention only blocks public principals, and the organization policy constraint governs new buckets rather than converting an existing one.

**How to reason it out**

1. Identify the requirement: one permission model (IAM) for this existing bucket, now and for future uploads.
2. Recognise that per-object ACLs are the second model and must be disabled, not just cleaned.
3. Separate look-alikes: public access prevention targets public principals only; the org constraint applies to new buckets.
4. Enable uniform bucket-level access, then re-create any needed access as IAM bindings (it can be reverted only within 90 days).

> **Exam tip:** To make IAM the only permission system on a bucket, enable uniform bucket-level access on that bucket; ACL cleanups and public access prevention don't remove ACLs.

</details>

📖 Learn this topic: [Managing Cloud Storage and Databases: Lifecycle, Backups, and CMEK](https://www.savemycert.com/revision/gcp-associate-cloud-engineer/gcp-managing-cloud-storage-databases-cmek/?utm_source=github&utm_medium=readme&utm_campaign=gcp-associate-cloud-engineer-study-guide)

---

## Question 15

*Ensuring the successful operation of a cloud solution*

**A regional managed instance group places its VMs in app-subnet, a custom mode subnet in us-central1 with the primary range 10.10.0.0/24. Autoscaling now fails because the subnet has no free addresses. Running VMs must keep their internal IPs, the instance template must keep using app-subnet, and addresses 10.10.1.0 through 10.10.3.255 are unused in this and every connected network. Which command should you run? (The --region flag is omitted.)** Choose one.

- **a.** gcloud compute networks subnets update app-subnet --add-secondary-ranges=more=10.10.4.0/24
- **b.** gcloud compute networks subnets expand-ip-range app-subnet --prefix-length=26
- **c.** gcloud compute networks subnets create app-subnet-2 --range=10.10.1.0/24
- **d.** gcloud compute networks subnets expand-ip-range app-subnet --prefix-length=22

<details>
<summary>Show answer and explanation</summary>

**Answer: d**

- **a** ❌ A secondary range serves alias IP ranges, such as GKE Pods. New VMs still take their primary interface address from the full primary range, so autoscaling keeps failing.
- **b** ❌ A /26 is smaller than the current /24. The command only accepts a shorter prefix (a larger range), so this request is rejected.
- **c** ❌ A second subnet adds addresses, but the instance template must keep using app-subnet, so the group never places VMs in the new subnet.
- **d** ✅ Lowering the prefix length from /24 to /22 grows the primary range in place. Running VMs keep their addresses and the template still points at app-subnet.

**The concept.** A subnet's primary IPv4 range is resized in place by lowering its prefix length with gcloud compute networks subnets expand-ip-range.

**Why this is correct.** expand-ip-range with --prefix-length=22 turns 10.10.0.0/24 into 10.10.0.0/22, four times the space, without downtime and without changing any running VM's address. The managed instance group keeps using app-subnet, so new VMs get addresses from the larger range. A secondary range does not feed VM primary interfaces, a new subnet is ignored by a template pinned to app-subnet, and a longer prefix such as /26 would shrink the range, which the command does not allow.

**How to reason it out**

1. Identify what is exhausted: the primary range that VM primary interfaces draw from.
2. Note the constraints: same subnet, unchanged VM addresses, no recreation.
3. Choose expand-ip-range with a shorter prefix length (/24 to /22).
4. Confirm the new range overlaps nothing in the network or connected networks before running it.

> **Exam tip:** More addresses in the same subnet means expand-ip-range with a shorter prefix; it never goes the other way.

</details>

📖 Learn this topic: [Managing VPC Networking: Subnets, Static IPs, Cloud DNS, and Cloud NAT](https://www.savemycert.com/revision/gcp-associate-cloud-engineer/gcp-managing-vpc-networking-dns-nat/?utm_source=github&utm_medium=readme&utm_campaign=gcp-associate-cloud-engineer-study-guide)

---

## Question 16

*Ensuring the successful operation of a cloud solution*

**Your operations team must be emailed when CPU utilization on a production Compute Engine VM stays above 90% for 10 minutes. During the nightly batch window, jobs push CPU above 90% for one or two minutes several times an hour, and those spikes must not notify anyone. What should you configure in Cloud Monitoring?** Choose one.

- **a.** An alerting policy with a CPU utilization threshold of 90%, a 10-minute retest window, and an email channel.
- **b.** An uptime check against the VM's external IP at a 10-minute interval, with an alerting policy and an email channel.
- **c.** A log-based alerting policy that matches high-CPU warnings in the VM's system log over 10 minutes, with an email channel.
- **d.** An alerting policy with a CPU utilization threshold of 90%, a 5-minute retest window, and an email channel.

<details>
<summary>Show answer and explanation</summary>

**Answer: a**

- **a** ✅ Correct. The threshold plus a 10-minute retest window means the condition must hold for the whole window before an incident opens, so short batch spikes never notify.
- **b** ❌ Uptime checks test whether an endpoint answers; they report reachability and latency, not the VM's CPU utilization.
- **c** ❌ CPU utilization is a metric that Monitoring already collects; a log-match condition fires per matching entry and has no notion of a value sustained for 10 minutes.
- **d** ❌ A 5-minute retest window ignores the one- or two-minute batch spikes, but it notifies on load sustained for only 5 minutes, earlier than the 10-minute requirement.

**The concept.** A metric-threshold alerting policy combines a metric, a comparison and threshold, a retest window (duration) and notification channels.

**Why this is correct.** The requirement has two parts: notify on CPU sustained above 90% for 10 minutes, and stay quiet on brief spikes. The retest window sets how long the condition must stay true before the policy opens an incident, so a 10-minute window matches the requirement exactly. A 5-minute window is the tempting near-twin: it also ignores the short spikes, but it alerts on 5-minute episodes the team did not ask about. Uptime checks measure reachability, and a log-match alert fires per matching log entry rather than on a sustained metric value.

**How to reason it out**

1. Identify the signal: CPU utilization is a resource metric Monitoring already collects for the VM.
2. Pick a metric-threshold condition at 90%.
3. Set a 10-minute retest window so short spikes do not meet the condition.
4. Attach an email notification channel to the policy.

> **Exam tip:** Threshold + retest window + notification channel: the retest window is what separates a sustained problem from a spike.

</details>

📖 Learn this topic: [Cloud Monitoring and Logging: Alerts, Log Sinks, and Audit Logs](https://www.savemycert.com/revision/gcp-associate-cloud-engineer/gcp-cloud-monitoring-logging-alerts/?utm_source=github&utm_medium=readme&utm_campaign=gcp-associate-cloud-engineer-study-guide)

---

## Question 17

*Configuring access and security*

**A security auditor asks you to produce a list of every principal that currently has a role granted directly on the project my-app-prod. Which gcloud command should you run?** Choose one.

- **a.** gcloud iam service-accounts list --project my-app-prod
- **b.** gcloud iam roles list --project my-app-prod
- **c.** gcloud projects add-iam-policy-binding my-app-prod
- **d.** gcloud projects get-iam-policy my-app-prod

<details>
<summary>Show answer and explanation</summary>

**Answer: d**

- **a** ❌ This lists the service accounts that exist in the project as resources; it does not show which principals hold roles on the project.
- **b** ❌ This lists the custom roles defined in the project, not who has been granted roles. It says nothing about principals or bindings.
- **c** ❌ add-iam-policy-binding modifies the policy by granting a role to a principal; it does not display the existing policy, and it requires --member and --role flags.
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

**Dana (dana@example.com) needs to list and inspect the VMs, disks and networks in project web-prod, which sits in a folder alongside other teams' projects. She must not change anything, must not see other services' data, and must get nothing in the folder's other projects. Which command should you run?** Choose one.

- **a.** gcloud resource-manager folders add-iam-policy-binding 4521 --member="user:dana@example.com" --role="roles/compute.viewer"
- **b.** gcloud projects add-iam-policy-binding web-prod --member="user:dana@example.com" --role="roles/viewer"
- **c.** gcloud projects add-iam-policy-binding web-prod --member="user:dana@example.com" --role="roles/compute.viewer"
- **d.** gcloud resource-manager folders add-iam-policy-binding 4521 --member="user:dana@example.com" --role="roles/viewer"

<details>
<summary>Show answer and explanation</summary>

**Answer: c**

- **a** ❌ The role is right, but a folder binding is inherited by every project in the folder, so she would also see the other teams' Compute Engine resources.
- **b** ❌ The project is the right target, but roles/viewer is the basic role that reads nearly every service in the project, not only Compute Engine.
- **c** ✅ Correct. roles/compute.viewer is the predefined read-only Compute Engine role, and binding it on web-prod limits it to that one project.
- **d** ❌ This is wrong on both counts: the basic Viewer role reads every service, and the folder binding reaches every project in the folder.

**The concept.** A role grant is a binding of one role to a principal on one node of the resource hierarchy; both the role and the node decide how far the access reaches.

**Why this is correct.** Two choices must both be narrow. The role: roles/compute.viewer is the predefined read-only Compute Engine role, while basic roles/viewer reads almost every service. The node: binding on the project web-prod covers only that project, while a folder binding is inherited by every project in the folder. Only the project-level compute.viewer grant satisfies both constraints.

**How to reason it out**

1. Identify the job: read-only, Compute Engine only, one project.
2. Pick the narrowest predefined role: roles/compute.viewer, not the basic Viewer role.
3. Pick the lowest node that covers the need: the project, not its parent folder.
4. Check the command form: add-iam-policy-binding with --member=user:EMAIL and --role=roles/NAME.

> **Exam tip:** Narrow both axes of a grant: the narrowest predefined role, bound at the lowest node that covers the need.

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

**A new operations engineer must be able to start, stop, and reconfigure Compute Engine VM instances in a production project. They must not be able to change VPC networks, firewall rules, Cloud Storage buckets, or IAM policies. Following Google-recommended practices, which role should you grant on the project?** Choose one.

- **a.** roles/compute.admin
- **b.** roles/compute.instanceAdmin.v1
- **c.** roles/compute.storageAdmin
- **d.** roles/compute.osAdminLogin and roles/compute.viewer

<details>
<summary>Show answer and explanation</summary>

**Answer: b**

- **a** ❌ compute.admin covers instances but also every other Compute Engine resource, including networks and firewall rules, which the engineer must not change.
- **b** ✅ Correct. This predefined role covers creating, starting, stopping and modifying instances without network, firewall, storage or IAM administration.
- **c** ❌ compute.storageAdmin manages persistent disks, images and snapshots. It does not let the engineer start, stop or reconfigure the instances themselves.
- **d** ❌ Together these let the engineer see instances and log in to them with administrator rights on the guest OS, but they grant no Compute Engine API permission to start, stop or reconfigure instances.

**The concept.** Predefined roles are Google-maintained permission sets scoped to one service and job function. Several Compute Engine roles sound similar but cover different resources.

**Why this is correct.** The engineer needs instance lifecycle and configuration control and nothing else. roles/compute.instanceAdmin.v1 is the predefined role for exactly that. roles/compute.admin would also let them start and stop VMs, which makes it tempting, but it adds network and firewall administration the stem rules out. compute.storageAdmin covers disks and images, and osAdminLogin plus compute.viewer gives guest-OS login and visibility, not control of the instance resource.

**How to reason it out**

1. List the actions required: start, stop, reconfigure instances.
2. List what is forbidden: networks, firewalls, storage, IAM.
3. Find the predefined role whose scope matches the allowed actions only.
4. Eliminate roles that cover the wrong resource (disks, guest login) or too many resources (compute.admin).

> **Exam tip:** Choose the predefined role scoped to the resource the person manages: instances means compute.instanceAdmin.v1, not compute.admin.

</details>

📖 Learn this topic: [Managing Cloud IAM: Roles, Policies, and Inheritance](https://www.savemycert.com/revision/gcp-associate-cloud-engineer/gcp-iam-roles-policies-management/?utm_source=github&utm_medium=readme&utm_campaign=gcp-associate-cloud-engineer-study-guide)

---

[← Back to the ACE study guide](README.md)
