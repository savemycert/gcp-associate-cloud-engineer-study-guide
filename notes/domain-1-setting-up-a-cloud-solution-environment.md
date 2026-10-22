# Domain 1: Setting up a cloud solution environment (20%)

This domain is the foundation the rest of the exam builds on: the resource hierarchy, identities, APIs, quotas, and billing. Most questions ask where to attach a policy or grant, or which role or setting solves a stated setup problem.

## Setting up cloud projects and accounts

- **Resource hierarchy** (managed by Resource Manager):

| Level        | What it is                                                                                 | Typical use                                                 |
| ------------ | ------------------------------------------------------------------------------------------ | ----------------------------------------------------------- |
| Organization | Root node for the company, tied to a verified domain in Cloud Identity or Google Workspace | Company-wide guardrails and security teams                  |
| Folder       | Optional grouping under the organization, can nest                                         | Departments, teams, or environments (`eng/prod`, `eng/dev`) |
| Project      | Container for resources; APIs, billing links, and quotas all apply per project             | The unit of isolation between workloads                     |
| Resource     | VMs, buckets, databases, topics                                                            | Where the work actually runs                                |

- **Standalone organization** (no Google Workspace): sign up for free Cloud Identity, verify the domain, and the organization node appears once a user from that domain creates the first project or billing account.
- **Project identifiers:**
  - Display **name**: can be changed any time.
  - **Project ID**: you pick it at creation, it must be globally unique, and it never changes. gcloud and the APIs use it.
  - **Project number**: Google assigns it, and it never changes.
  - Create under a folder: `gcloud projects create shop-prod --folder=FOLDER_ID` (or `--organization=ORG_ID`).
- "Keep two workloads isolated" almost always means **separate projects**.
- **Granting IAM in a project:** `gcloud projects add-iam-policy-binding shop-prod --member=group:ops@example.com --role=roles/compute.viewer`.
  - Habits the exam rewards: bind to **groups**, use **predefined** roles, and bind at the **lowest level** that covers the need.
  - Inheritance is additive: a grant made higher up cannot be taken away by a narrower grant lower down.
- **IAM vs organization policy:**

|                           | IAM                      | Organization policy                                                   |
| ------------------------- | ------------------------ | --------------------------------------------------------------------- |
| Controls                  | Who can do what          | What may be done at all, whatever the identity                        |
| Applies to project Owners | Owners hold broad grants | Yes, Owners are constrained too                                       |
| Lower level can override  | No, grants only add up   | Yes, unless the parent blocks overrides                               |
| Built from                | Role bindings            | Constraints: **boolean** (on/off) or **list** (allowed/denied values) |

- Constraint examples:
  - `constraints/gcp.resourceLocations`: limit which regions resources can be created in.
  - `constraints/compute.vmExternalIpAccess`: control which VMs may have external IPs.
  - `constraints/iam.disableServiceAccountKeyCreation`: block key downloads.
  - `constraints/compute.disableSerialPortAccess`: turn off serial port access.
- Setting policies needs **Organization Policy Administrator** (`roles/orgpolicy.policyAdmin`), granted at the organization.
- Cue: "prevent anyone, even Owners, from..." or "enforce across every project in the folder" → organization policy, not IAM.
- **Cloud Identity** holds the human accounts. IAM grants roles but never creates identities.
  - A Super Admin manages it in the **Admin console** (`admin.google.com`), not in the Google Cloud console.
  - **Manual**: add users and groups by hand. Fine for a small team, but it drifts over time.
  - **Google Cloud Directory Sync (GCDS)**: runs on-premises and syncs one way from LDAP/Active Directory into Cloud Identity. It never writes back.
  - **SAML SSO**: users sign in with corporate credentials, and their accounts still exist in Cloud Identity.
  - **Provisioning** (Admin SDK Directory API or an IdP connector): creates and suspends accounts automatically as people join and leave.
  - Pattern: sync users automatically → groups that mirror job functions → IAM roles bound to those groups.
- **Workforce Identity Federation** gives employees Google Cloud access through an external IdP (SAML 2.0 or OIDC) **without Google Accounts**.
  - Create a **workforce identity pool** (organization level), then a **provider** with the IdP metadata and **attribute mappings**.
  - Grant roles to `principalSet://iam.googleapis.com/locations/global/workforcePools/POOL_ID/...` identifiers.
  - Users sign in through a federated console URL, or gcloud configured for workforce federation.
  - Choose sync into Cloud Identity when users need full Google Accounts (Workspace apps, widest product support). Choose federation when the IdP should remain the only account lifecycle.
  - Trap: _Workforce_ = people; _Workload_ Identity Federation = software (Domain 4).
- **APIs** start out disabled in every new project.
  - Manage them with `gcloud services enable compute.googleapis.com`, `gcloud services list --enabled`, and `gcloud services disable`.
  - Enabling needs `serviceusage.services.enable`, and some APIs create Google-managed service agents when enabled.
  - An "API not enabled / disabled" error is fixed by enabling the API, not by changing IAM.
- **Quotas** (per project, many also per region):
  - **Rate quotas** reset over a time window, such as API calls per minute.
  - **Allocation quotas** cap what exists at once, such as regional CPUs, VPC networks, or static IPs.
  - View and request increases on the **Cloud Quotas** page (IAM & Admin > Quotas). Google reviews each request, approval is not guaranteed, and it takes time, so request ahead of scaling events.
  - A higher quota costs nothing by itself. You pay only for what you use.
- **Google Cloud Observability baseline** (per project, no setup needed):
  - Platform metrics flow into Cloud Monitoring.
  - Platform logs land in the `_Default` log bucket. Admin Activity audit logs go to the fixed `_Required` bucket.
  - Setup work on top of that:
    - A **metrics scope** in a central scoping project to monitor many projects.
    - The **Ops Agent** on VMs for memory and disk-space metrics.
    - **Log sinks** (BigQuery, Cloud Storage, Pub/Sub) and exclusion filters.
    - Bucket retention and **notification channels** for later alerting.
- **Initial networking:**
  - New projects get a **default auto mode VPC**: a subnet in every region plus permissive firewall rules. Fine for experiments only.
  - For production, create a **custom mode** VPC (`gcloud compute networks create core-vpc --subnet-mode=custom`) with subnets only where needed, using ranges that avoid on-premises and peered networks.
  - **Shared VPC**: a host project owns the network, and service projects deploy into it.
- **Regional availability:** products, machine types, and GPUs differ by region and zone. Check before committing:
  - `gcloud compute regions list` and `gcloud compute zones list`.
  - `gcloud compute machine-types list --zones=ZONE`.
  - The product's locations page.
- **Cloud Asset Inventory:**
  - Search resources and IAM policies across the organization with `gcloud asset search-all-resources`.
  - Export snapshots to BigQuery or Cloud Storage.
  - Review about five weeks of change history.
- **Gemini Cloud Assist** in the console: ask natural-language questions about your resources, get summaries, and receive optimization suggestions.

📖 Full lesson: [Setting Up Google Cloud Projects, Resource Hierarchy, and IAM](https://www.savemycert.com/revision/gcp-associate-cloud-engineer/gcp-projects-resource-hierarchy-iam-setup/?utm_source=github&utm_medium=readme&utm_campaign=gcp-associate-cloud-engineer-study-guide)

## Managing billing configuration

- **Model:**
  - A Cloud Billing account pays for projects. One account can fund many projects, while a project draws on a single account at any moment.
  - The billing account holds no resources. It connects to a **payments profile** (payment instrument, tax, invoicing details) and has its own IAM policy, separate from any project.
- **Account types:**
  - **Self-serve (online)**: charged automatically to a card or bank account. Anyone can create one.
  - **Invoiced (offline)**: monthly invoice paid by wire or check. The business applies and Google approves it.
  - Both behave the same for linking and budgets.
- **Creating accounts:**
  - Billing > Create account. The **country and currency** are fixed at creation, and the creator becomes Billing Account Administrator.
  - Inside an organization this needs **Billing Account Creator** (`roles/billing.creator`) at the org level. Keep it scarce so nobody attaches company projects to a personal card.
  - Create several accounts only for real separation: different payment sources, currencies, legal entities, or subsidiaries. Otherwise use **fewer accounts, many projects**, and slice costs with projects and labels.
  - `gcloud billing accounts list` shows account IDs (`XXXXXX-XXXXXX-XXXXXX`) and whether each is `open`. A closed account stops paying for its projects.
- **Linking and unlinking:**
  - Link: `gcloud billing projects link shop-prod --billing-account=ACCOUNT_ID`.
  - Unlink (disables billing): `gcloud billing projects unlink shop-prod`.
  - Linking needs permission **on both sides**:
    - On the billing account: **Billing Account User** (or Administrator), which carries `billing.resourceAssociations.create`.
    - On the project: **Project Billing Manager** (Project Owner also covers it).
  - Neither role alone is enough. Owning a project doesn't let you spend from someone else's billing account, and holding Billing Account User doesn't let you take over projects you don't manage.
  - Scenario: a developer's new sandbox project has no billing, so Compute Engine won't enable. The least-privilege fix is Billing Account User on that one billing account.
- **Billing roles:**

| Role                          | Granted on      | Allows                                                                               |
| ----------------------------- | --------------- | ------------------------------------------------------------------------------------ |
| Billing Account Administrator | Billing account | Everything: payment settings, link/unlink, budgets, exports, billing IAM, view spend |
| Billing Account User          | Billing account | Link projects to the account; no spend visibility or settings                        |
| Billing Account Viewer        | Billing account | Read-only costs and transactions (finance, auditors)                                 |
| Project Billing Manager       | Project         | Attach or detach the project's billing account, no other project access              |
| Billing Account Creator       | Organization    | Open additional self-serve accounts                                                  |

- Task → minimal role:
  - Link a project → Billing Account User + Project Billing Manager.
  - See charges → Billing Account Viewer.
  - Update the payment method → Administrator.
  - New billing account → Billing Account Creator.
- **Separation of duties:** finance gets billing-account roles and no project roles. Engineers get project roles and no billing-account roles.
- **Budgets and alerts** (Billing > Budgets & alerts):
  - A budget **only notifies**. Spending continues past 100%. Any option claiming a budget "stops" or "prevents" spend is wrong.
  - **Scope**: the whole account, or narrowed to projects, services, or labels. One account can have many budgets.
  - **Amount**: a fixed figure, or last period's spend.
  - **Threshold rules**: percentages of the amount (defaults 50%, 90%, 100%), each measured against **actual** or **forecasted** spend. Forecasted rules warn before the money is gone.
  - Default email recipients are **Billing Account Administrators and Users**, not Project Owners. Add people through Cloud Monitoring **notification channels**.
  - **Programmatic notifications** publish budget status to a **Pub/Sub** topic for automation.
  - For a real hard cap, build it yourself: a Cloud Run function subscribed to the budget topic disables billing on the project. It works, but it takes the workload down.
- **Billing export to BigQuery** (Billing > Billing export, configured **per billing account**):
  - **Standard usage cost**: a row per line item (service, SKU, project, labels, usage, cost, credits).
  - **Detailed usage cost**: standard plus **resource-level** rows, such as a single VM or disk.
  - **Pricing**: the account's price list.
  - Create the dataset first, and choose its location carefully.
  - Requires Billing Account Administrator plus BigQuery permissions in the target project.
  - **Not retroactive**: data starts from when you enable it, so enable it on day one.
  - A legacy file export to Cloud Storage exists, but BigQuery export is the supported analysis path.
- **Cost analysis:**
  - The built-in **Reports** page groups spend by project, service, SKU, region, or label.
  - **Labels** (`team=checkout`, `env=prod`) flow into reports and the export. Labels added later don't reclassify past spend.
- **Disabling billing** on a project:
  - Paid services stop (VMs won't run, billed APIs refuse requests), and some data may eventually be deleted. The project and its configuration remain.
  - It's the blunt, destructive way to stop spend.

📖 Full lesson: [Google Cloud Billing Accounts, Budgets, Alerts, and BigQuery Export](https://www.savemycert.com/revision/gcp-associate-cloud-engineer/gcp-billing-accounts-budgets-alerts/?utm_source=github&utm_medium=readme&utm_campaign=gcp-associate-cloud-engineer-study-guide)

[← Back to the study guide](../README.md)
