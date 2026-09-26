# ACE Glossary

154 ACE terms and services, each in one plain-English sentence.

[A](#a) · [B](#b) · [C](#c) · [D](#d) · [E](#e) · [F](#f) · [G](#g) · [H](#h) · [I](#i) · [K](#k) · [L](#l) · [M](#m) · [N](#n) · [O](#o) · [P](#p) · [R](#r) · [S](#s) · [T](#t) · [U](#u) · [V](#v) · [W](#w)

## A

- **Access scope**: A legacy per-VM limit on what an attached service account's tokens can reach.
- **Agent Runtime**: The managed hosting service for AI agents on the Gemini Enterprise Agent Platform.
- **Alerting policy**: A Cloud Monitoring rule of conditions and notification channels that opens incidents.
- **Allocation quota**: A limit on how many resources can exist at once, such as CPUs in a region.
- **Allow policy**: The set of role bindings attached to one resource.
- **AlloyDB**: A PostgreSQL-compatible managed database built for demanding transactional and analytical workloads.
- **Application Default Credentials**: The client-library mechanism that finds credentials automatically from the environment.
- **Application Design Center**: A visual tool for designing application architectures that generates infrastructure as code.
- **Attached service account**: A service account assigned to a resource so code running there receives tokens automatically.
- **Attribute mapping**: The rule that translates claims from an external identity provider into attributes usable in IAM bindings.
- **Auto mode VPC**: A VPC network that automatically has one subnet in every region from a fixed range.
- **Autoclass**: A Cloud Storage feature that moves each object between storage classes based on its access.
- **Availability policy**: A VM's settings for what happens during host maintenance and after a crash.

## B

- **Basic role**: One of the legacy Owner, Editor, or Viewer roles that span every service in a project.
- **Bigtable**: A wide-column NoSQL database for very high throughput at low latency over large datasets.
- **Billing Account User**: The billing role that only allows linking projects to a billing account.
- **Billing Account Viewer**: The read-only billing role for viewing costs and transactions.
- **Boolean constraint**: An organization policy constraint that is either enforced or not, such as disabling serial port access.
- **Budget**: A spending target with threshold rules that send alerts but never stop resources.

## C

- **Cloud Asset Inventory**: A service for searching, exporting, and tracking the history of resources and IAM policies across an organization.
- **Cloud Billing account**: The payment configuration that pays for the projects linked to it.
- **Cloud Billing export**: A per-billing-account feature that writes cost and pricing data into BigQuery tables from the time it is enabled.
- **Cloud Identity**: Google's identity service that holds user accounts and groups for a domain, managed in the Admin console.
- **Cloud KMS**: Google Cloud's managed service for creating, rotating, and controlling encryption keys.
- **Cloud NAT**: A managed service that gives resources without external IPs outbound-only internet access.
- **Cloud Quotas page**: The console page for viewing quota usage and requesting increases.
- **Cloud Router**: A managed router that exchanges BGP routes and hosts Cloud NAT configuration.
- **Cloud Run**: A serverless platform that runs stateless containers and scales them with requests, down to zero.
- **Cloud Run functions**: A function-as-a-service offering that builds and runs single-purpose code in response to events or HTTP calls.
- **Cloud Workstations**: Managed, preconfigured developer environments launched from an admin-defined configuration.
- **Cluster autoscaler**: The GKE feature that adds nodes for unschedulable Pods and removes underused ones.
- **CMEK**: Customer-managed encryption keys held in Cloud KMS that protect data in a Google Cloud service.
- **Config Connector**: A GKE add-on that manages Google Cloud resources as Kubernetes objects and keeps them reconciled.
- **Custom image**: A reusable boot image built from a disk, snapshot, or image to launch identical VMs.
- **Custom machine type**: A VM shape with the exact vCPU and memory you specify.
- **Custom mode VPC**: A VPC network with no automatic subnets, where you create each subnet and range yourself.
- **Custom role**: A role you define with an exact permission list at the project or organization level.
- **Custom static route**: A user-defined route sending a destination range to a chosen next hop.

## D

- **Database Center**: A console view that inventories the database fleet and flags availability, security, performance, and cost issues.
- **Dataflow**: A managed Apache Beam service for processing streaming and batch data.
- **Default service account**: A service account created automatically when certain services are enabled, such as the Compute Engine default.
- **Deny policy**: A separate IAM policy type that can block permissions regardless of allow grants.
- **disableServiceAccountKeyCreation**: The organization policy constraint that blocks creating service account keys.
- **Drain (Dataflow)**: Stopping a streaming job after it finishes processing in-flight data.
- **Dry run (BigQuery)**: A query option that reports bytes to be scanned without running or billing the query.
- **Dual-region bucket**: A Cloud Storage bucket stored in two named regions, optionally with turbo replication.

## E

- **Effective policy**: The combined access on a resource from its own policy and every inherited ancestor policy.
- **Etag**: A version marker in an IAM policy that prevents overwriting someone else's concurrent change.
- **Eventarc**: A service that routes events from Google sources to destinations such as Cloud Run.

## F

- **Fabric FAST**: Google's staged Terraform framework for building an organization landing zone.
- **Filestore**: Managed NFS file shares for VMs and GKE.
- **Firestore**: A serverless document database with real-time listeners and offline sync for mobile and web clients.
- **Folder**: An optional grouping of projects under the organization, often by department or environment.

## G

- **Gemini Cloud Assist**: The AI assistant in the Google Cloud console that helps analyze resources, draft commands, and investigate issues.
- **GKE Autopilot**: A GKE mode where Google manages the nodes and billing follows Pod resource requests.
- **Google Cloud Directory Sync (GCDS)**: An on-premises tool that copies users and groups one way from an LDAP directory into Cloud Identity.

## H

- **HA VPN**: A redundant IPsec VPN service that exchanges routes over BGP through Cloud Router.
- **Helm chart**: A parameterized package of Kubernetes manifests that installs as a versioned release.
- **Hierarchical firewall policy**: A firewall policy attached to an organization or folder that applies to every VPC beneath it.
- **Horizontal Pod Autoscaler**: A Kubernetes controller that changes a workload's replica count based on metrics.
- **Hyperdisk**: Block storage whose IOPS and throughput are provisioned independently of capacity.

## I

- **IAM condition**: An expression on a binding that limits when or where the grant applies.
- **IAM Recommender**: An Active Assist recommender that suggests smaller roles based on the permissions actually used.
- **IAP TCP forwarding**: A way to tunnel SSH or RDP through Identity-Aware Proxy to a VM that has no external IP.
- **Image family**: A named group of images that always resolves to its newest non-deprecated image.
- **ImagePullBackOff**: A Pod status meaning Kubernetes repeatedly failed to pull the container image.
- **Instance template**: An unchangeable definition of machine type, image, disks, and network used to create identical VMs.
- **Invoiced billing account**: A billing account for approved businesses that pays by monthly invoice.

## K

- **Kubernetes service account (KSA)**: The identity inside a Kubernetes cluster that a Pod runs under.

## L

- **Label**: A key-value pair on a resource that carries into billing data so costs can be grouped by team or environment.
- **Launch stage**: A custom role's maturity label, such as ALPHA, GA, or DEPRECATED, which does not change enforcement.
- **Lifecycle rule**: A bucket rule that changes an object's storage class or deletes it when conditions are met.
- **List constraint**: An organization policy constraint that allows or denies specific values, such as permitted resource locations.
- **Live migration**: Moving a running VM to another host during maintenance without stopping it.
- **Local SSD**: Very fast, physically attached storage whose data does not survive instance stop or termination.
- **Log Router**: The Cloud Logging component that checks every entry against sinks and delivers matches.
- **Log sink**: A filter and destination that exports matching logs to a bucket, BigQuery, Cloud Storage, or Pub/Sub.
- **Log-based metric**: A metric derived from log entries that match a filter.

## M

- **Managed instance group (MIG)**: A group of identical VMs that can autoscale and replace unhealthy instances.
- **Managed Service for Apache Kafka**: A managed service that runs Kafka clusters for Kafka-compatible workloads.
- **Managed Service for Prometheus**: A Google-run backend that stores Prometheus metrics and supports PromQL in Cloud Monitoring.
- **Memorystore**: A managed in-memory service for caching and session storage.
- **Metrics scope**: The set of projects whose metrics a Cloud Monitoring scoping project can view and alert on.
- **Minimum storage duration**: The period a colder Cloud Storage class bills for even if an object is deleted sooner.

## N

- **Network Service Tiers**: The Premium or Standard choice for how traffic travels between the internet and Google Cloud.
- **Node pool**: A group of GKE nodes that share one machine type and configuration.
- **Notification channel**: A destination such as email, chat, a webhook, or Pub/Sub that receives alert notifications.

## O

- **Object versioning**: A bucket feature that keeps noncurrent versions when objects are overwritten or deleted.
- **Ops Agent**: The agent installed on Compute Engine VMs to collect in-guest metrics such as memory and disk usage, plus logs.
- **Organization node**: The root of the hierarchy, representing a company and tied to a verified domain in Cloud Identity or Google Workspace.
- **Organization policy**: A constraint-based guardrail on how resources may be configured or used, applied to every identity alike.
- **OS Login**: A feature that grants SSH access to VMs through IAM roles instead of metadata keys.

## P

- **Payments profile**: The record holding the payment instrument, tax, and invoicing details behind a billing account.
- **Permission**: A single allowed action on a service resource, such as starting a VM, granted only through roles.
- **Personalized Service Health**: A dashboard of Google Cloud incidents that affect your own projects and locations.
- **Point-in-time recovery (PITR)**: Restoring a database to a chosen timestamp by replaying transaction logs onto a backup.
- **Policy inheritance**: The way bindings on the organization, folders, and projects apply to everything beneath them.
- **Policy Troubleshooter**: A tool that explains why a principal does or does not have a permission on a resource.
- **Predefined role**: A Google-maintained role scoped to a specific job on a specific service.
- **Principal**: An identity that can be granted access, such as a user, group, service account, or domain.
- **Private cluster**: A GKE cluster whose nodes have only internal IP addresses.
- **Private Google Access**: A per-subnet option giving VMs without external IPs a path to Google APIs.
- **Private managed zone**: A Cloud DNS zone that answers queries only from authorized VPC networks.
- **Programmatic budget notification**: A budget feature that publishes spend updates to a Pub/Sub topic for automated handling.
- **Project**: The container for resources, where APIs are enabled, billing is linked, and quotas are enforced.
- **Project Billing Manager**: The project-level role that allows attaching or detaching a project's billing account.
- **Project ID**: The globally unique, permanent identifier you choose for a project and use in gcloud commands.
- **Project number**: The permanent numeric identifier Google assigns to each project.

## R

- **Rate quota**: A limit on how many requests you can make in a time window, such as API calls per minute.
- **Regional Persistent Disk**: A block disk that synchronously replicates writes across two zones for failover.
- **Resource hierarchy**: The organization, folder, project, and resource tree that IAM and organization policies attach to and inherit down.
- **Resource Manager**: The service that creates and manages organizations, folders, and projects.
- **Revision**: An immutable version of a Cloud Run service created on each deployment.
- **Role**: A named collection of permissions that is granted to principals.
- **Role binding**: The pairing of one role with one or more principals inside an IAM policy.

## S

- **Secure Tag**: An IAM-governed resource tag that Cloud NGFW policy rules can match.
- **Security Token Service**: The Google API that exchanges an external token for short-lived Google Cloud credentials.
- **Self-serve billing account**: A billing account charged automatically to a card or bank account.
- **Service account**: A non-human identity that workloads use to call Google Cloud APIs.
- **Service Account Credentials API**: The API that issues short-lived credentials for service account impersonation.
- **Service account impersonation**: Temporarily obtaining a service account's credentials as another authenticated principal.
- **Service account key**: A downloadable private key that authenticates as a service account and does not expire.
- **Service Account Token Creator role**: The role that allows impersonating a service account by minting its short-lived credentials.
- **Service Account User role**: The role that allows attaching a service account to resources and acting as it.
- **Service agent**: A Google-managed service account that a Google service uses to act inside your project.
- **Shared VPC**: A setup where a host project's network is used by attached service projects.
- **Short-lived credential**: An access token, ID token, or signed JWT for a service account that expires quickly.
- **Signed URL**: A time-limited URL granting access to one Cloud Storage object without a Google identity.
- **Snapshot**: A point-in-time, incremental backup of a persistent disk or Hyperdisk.
- **Snapshot schedule**: A resource policy that takes disk snapshots automatically and deletes them after a retention period.
- **Spanner**: A horizontally scalable relational database with strong consistency, including across regions.
- **Spot VM**: A heavily discounted VM that can be reclaimed at any time and has no availability SLA.
- **Standalone organization**: An organization created through a free Cloud Identity account and a verified domain, without Google Workspace.
- **StatefulSet**: A Kubernetes workload giving each Pod a stable name, network identity, and its own persistent volume.
- **Static IP address**: A reserved IP that stays with a resource across stops, restarts, and re-creation.
- **Storage Transfer Service**: A managed service for large or scheduled transfers into Cloud Storage from other clouds, URLs, buckets, or on-premises.

## T

- **Terraform state**: The file mapping Terraform configuration to real resources, best kept in a versioned Cloud Storage backend.
- **Testable permissions**: The list of permissions that can be granted on a resource type and used in custom roles.
- **Threshold rule**: A budget percentage that triggers a notification when actual or forecasted spend reaches it.
- **TPU**: A Google-designed accelerator built for large-scale tensor and model-training workloads.
- **Traffic splitting**: Dividing requests between Cloud Run revisions by percentage for canaries and rollbacks.

## U

- **Uniform bucket-level access**: A bucket setting that disables object ACLs so IAM alone controls access.
- **User-managed service account**: A service account you create in your project for a specific workload.

## V

- **Vertical Pod Autoscaler**: A Kubernetes controller that adjusts Pod CPU and memory requests from observed usage.
- **VM Manager**: A suite for patching, inventorying, and enforcing configuration across Compute Engine VMs.
- **VPC Flow Logs**: Sampled records of network flows to and from VMs, enabled per subnet.
- **VPC Network Peering**: A private, non-transitive connection between two VPC networks.

## W

- **Workforce Identity Federation**: A way for employees to access Google Cloud with an external IdP's credentials and no Google Account.
- **Workforce identity pool**: The organization-level container for a set of federated human identities.
- **Workload Identity Federation**: Keyless access to Google Cloud for workloads outside it, based on trusting their existing OIDC or SAML tokens.
- **Workload Identity Federation for GKE**: The GKE feature that lets a Kubernetes service account obtain Google Cloud credentials without keys.
- **Workload identity pool**: A container for external workload identities and the providers that issue them.
- **Workload Identity User role**: The role that lets a Kubernetes service account act as a Google service account.
- **Writer identity**: The service account a sink uses, which needs write access on the destination.

[← Back to the study guide](README.md)
