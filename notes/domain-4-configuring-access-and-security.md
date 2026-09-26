# Domain 4: Configuring access and security (20%)

This domain covers Cloud IAM and service accounts: who can act, with which roles, at which level of the hierarchy, and how workloads authenticate without keys. Most questions ask for the least-privilege role, the right attachment point, or the keyless authentication method for where a workload runs.

## Managing IAM

- **The model:**
  - **Principal** (who): a user (`user:`), group (`group:`), service account (`serviceAccount:`), domain (`domain:`), `allAuthenticatedUsers`, or `allUsers`.
  - **Role** (what): a named set of permissions such as `compute.instances.start`. **Permissions are never granted on their own**, only inside roles.
  - **Binding**: one role plus a list of principals, with an optional **IAM condition** (for example, an expiry time or a resource-name prefix).
  - **Allow policy**: all the bindings on one resource. It can attach to the organization, a folder, a project, or a resource that supports its own policy (bucket, topic, service account).
  - Missing one permission → find or build a role that includes it.
- **Viewing and editing:**

| Task | Command |
|---|---|
| View project policy (with `etag`) | `gcloud projects get-iam-policy PROJECT_ID` |
| View at other levels | `gcloud organizations get-iam-policy`, `gcloud resource-manager folders get-iam-policy`, `gcloud storage buckets get-iam-policy gs://B` |
| Grant | `gcloud projects add-iam-policy-binding shop-prod --member=group:web@example.com --role=roles/run.developer` |
| Revoke | `remove-iam-policy-binding` with the same flags |
| Bulk edit | Export, edit, then `set-iam-policy PROJECT_ID file.json` (the etag stops you from overwriting a concurrent change) |

- The add and remove commands read the current policy, change one binding, and write it back.
- Changing a policy needs `setIamPolicy` on that resource (Owner, or the service's admin role).
- In the console, the IAM page labels inherited grants with the level they come from.
- **Policy Troubleshooter** explains why a principal does or doesn't have a permission. **IAM Recommender** suggests narrower roles based on the permissions actually used.
- **Role types:**

| Type | Who defines it | Breadth | Use |
|---|---|---|---|
| Basic (`roles/owner`, `roles/editor`, `roles/viewer`) | Google, legacy | Thousands of permissions across every service in the project | Avoid in production |
| Predefined (`roles/storage.objectViewer`, `roles/pubsub.publisher`) | Google, per service | One job on one service, updated by Google as services change | Default choice |
| Custom | You, at project or organization level | Exactly the permissions you list | Only when no predefined role fits |

- **Editor** can modify almost everything in the project. **Owner** adds IAM management. Billing is handled by billing-account roles.
- An answer that gives Editor for a narrow task is almost always wrong when a predefined role is offered.
- **Inheritance:**
  - Bindings flow down: organization → folders → projects → resources.
  - **Effective policy = the union** of what is set on the resource plus every ancestor's bindings.
  - Allow policies only **add**. A Viewer grant on a project doesn't reduce an Editor grant inherited from the folder. Fix it where the broad grant was made.
  - Deny policies exist as a separate, advanced guardrail. Assume the allow-only model unless a question mentions them.
- **Where to bind:**
  - The **lowest level** whose whole subtree needs the access. A contractor reading one bucket gets the role on that bucket, not the project.
  - The same access across all projects in a department → **one folder binding**, not a copy in each project.
  - Organization-wide functions (for example `roles/iam.securityReviewer` for a security team) → the organization.
- **Custom roles:**
  - Created at **project** level (usable only in that project) or **organization** level (usable in any project). **Never at folder level**.
  - Share a custom role across projects by defining it at the organization.
  - `gcloud iam roles create instanceOperator --project=PROJECT_ID --title="Instance operator" --permissions=compute.instances.stop,compute.instances.start --stage=GA`. Swap in `--organization=ORG_ID` for an org-level role, or use `--file=role.yaml`.
  - Helpers:
    - `gcloud iam list-testable-permissions RESOURCE`: which permissions can be granted on it. Not every permission is allowed in custom roles.
    - `gcloud iam roles describe roles/...`: a predefined role's permission list.
    - `gcloud iam roles copy`: clone a role, then trim it.
  - **Launch stages**: `ALPHA`, `BETA`, `GA`, `DEPRECATED`, `DISABLED`. The stage is a label only and doesn't change what the role allows. `DISABLED` turns off the role's bindings.
  - Google never updates custom roles. New permissions for a service don't appear until you edit the role.
- **Worked pattern:**
  - A deploy team manages VMs in every project of a folder → `roles/compute.instanceAdmin.v1` on the **folder**, bound to a group. New projects in the folder are covered automatically.
  - Analysts read one bucket → `roles/storage.objectViewer` on the **bucket**.
  - An admin who manages access but shouldn't have Owner's resource powers → `roles/resourcemanager.folderIamAdmin` on the folder.
  - If only start/stop is allowed → an **organization-level custom role**.
- **Question triage:**
  - Pick the principal (prefer a group).
  - Pick the narrowest role (predefined → custom → basic).
  - Pick the lowest attachment point (resource → project → folder → organization).
  - "Has more access than the project policy shows" → inherited from a folder or the organization.

📖 Full lesson: [Managing Cloud IAM: Roles, Policies, and Inheritance](https://www.savemycert.com/revision/gcp-associate-cloud-engineer/gcp-iam-roles-policies-management/?utm_source=github&utm_medium=readme&utm_campaign=gcp-associate-cloud-engineer-study-guide)

## Managing service accounts

- **Three kinds:**

| Kind | Created by | Notes |
|---|---|---|
| Service agents (Google-managed) | Google, often when an API is enabled | Google uses them to act in your project. You can't create or delete them or see their credentials. Leave their roles alone unless the service's docs say otherwise |
| Default service accounts | Auto-created when certain services are enabled (for example the Compute Engine default) | Editable, but historically granted project Editor. Don't use them for real workloads |
| User-managed | You, `NAME@PROJECT_ID.iam.gserviceaccount.com` | One per workload, with minimum roles |

- **Create and manage:**
  - `gcloud iam service-accounts create orders-api --display-name="Orders API"`, then `list`.
  - To retire one, **disable** it first (reversible) rather than deleting it.
- **Least privilege for workloads:**
  - Bind roles with `--member=serviceAccount:EMAIL`, on the specific topic or bucket where the service supports it.
  - Example: `roles/pubsub.publisher` on one topic plus `roles/storage.objectCreator` on one bucket. Never Editor.
  - **One account per workload** keeps the blast radius small, makes audit logs show which app acted, and lets you revoke access for one app only.
  - Several apps sharing one powerful account is a problem to fix, not a pattern to copy.
- **Attaching to resources** (code gets auto-refreshed short-lived tokens through Application Default Credentials, with no secret on disk):
  - **Compute Engine**: `--service-account=EMAIL --scopes=cloud-platform`.
    - Access scopes are the legacy limit. Effective access is the intersection of scopes and IAM, so use the broad scope and let IAM restrict.
    - Changing the account requires stopping the VM.
  - **Cloud Run** and **Cloud Run functions**: `--service-account` at deploy time.
  - **GKE**: don't give app permissions to the **node** service account, because every Pod on the node would share them. Use Workload Identity Federation for GKE instead.
  - Attaching needs `roles/iam.serviceAccountUser` **on that service account**.
- **A service account is a principal and a resource:**
  - As a principal: its roles on other resources (what it can do).
  - As a resource: its own policy (who can use it). `gcloud iam service-accounts get-iam-policy EMAIL` and `add-iam-policy-binding EMAIL`.

| Role on the service account | Grants |
|---|---|
| `roles/iam.serviceAccountUser` | Act as the account and attach it to VMs and Cloud Run services |
| `roles/iam.serviceAccountTokenCreator` | Impersonate: mint short-lived tokens and sign as the account |
| `roles/iam.serviceAccountAdmin` | Create, update, disable, delete, and manage the account's policy |
| `roles/iam.serviceAccountKeyAdmin` | Create and delete keys (grant to almost no one) |

- Grant these **per account**. At project level they cover **every** service account in the project.
- Escalation trap: VM create rights + `serviceAccountUser` on a powerful account = a VM running with that account's permissions.
- **Keys:**
  - Downloadable JSON, **never expire**, work from anywhere for whoever holds them, leak easily (repos, images, CI logs), and theft is hard to detect.
  - Commands: `keys create|list|delete --iam-account=EMAIL`. Deleting a key revokes it at once, which is the response to a leak. Rotate by creating a new key, switching over, then deleting the old one.
  - Block key creation with `constraints/iam.disableServiceAccountKeyCreation`, allowing exceptions only for vendors that truly need a key file.
- **Impersonation and short-lived credentials:**
  - Needs `serviceAccountTokenCreator` on the target account.
  - The Service Account Credentials API issues OAuth 2.0 access tokens, ID tokens, or signed JWTs. Access tokens last **one hour by default**, and longer lifetimes need an organization policy opt-in.
  - gcloud:
    - `--impersonate-service-account=EMAIL` on a single command.
    - `gcloud config set auth/impersonate_service_account EMAIL` for the whole session.
    - `gcloud auth print-access-token --impersonate-service-account=EMAIL` to print a token.
  - Why it beats keys: engineers sign in as themselves, audit logs record the human behind each action, and offboarding is a group change.
  - Delegation chains let a low-privilege account impersonate a stronger one for one step only.
- **Workload Identity Federation for GKE** (a Kubernetes service account acting as a Google service account):
  - Enable it on the cluster, which creates the pool `PROJECT_ID.svc.id.goog`.
  - Create the KSA and the Google service account.
  - Grant `roles/iam.workloadIdentityUser` on the Google service account to `serviceAccount:PROJECT_ID.svc.id.goog[NAMESPACE/KSA]`.
  - Add the annotation `iam.gke.io/gcp-service-account=GSA_EMAIL` to the KSA, and have the Pod run as that KSA.
  - Result: per-Pod, short-lived, keyless credentials, with the same app code as on a VM.
- **Workload Identity Federation** (workloads outside Google Cloud: CI/CD, on-premises, other clouds):
  - Make a **workload identity pool** with `gcloud iam workload-identity-pools create`.
  - Register an **OIDC or SAML provider** in it.
  - Set **attribute mappings and conditions**, for example trusting one repository only.
  - The workload trades its own token for short-lived Google credentials through the **Security Token Service**. Then either grant roles to `principalSet://` members or have it impersonate a service account.
  - Workforce = people, Workload = software.
- **Ranking authentication options:**

| Workload location | Use | Standing secret |
|---|---|---|
| Compute Engine, Cloud Run | Attached service account | No |
| GKE Pods | Workload Identity Federation for GKE | No |
| Human or automation with a Google identity | Impersonation | No |
| Outside Google Cloud with an OIDC/SAML issuer | Workload Identity Federation | No |
| None of the above works | Service account key (last resort) | Yes |

📖 Full lesson: [Service Accounts, Impersonation, and Workload Identity Federation](https://www.savemycert.com/revision/gcp-associate-cloud-engineer/gcp-service-accounts-workload-identity/?utm_source=github&utm_medium=readme&utm_campaign=gcp-associate-cloud-engineer-study-guide)

[← Back to the study guide](../README.md)
