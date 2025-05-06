---
class: gslides-content-slide
---

# IAM: Who does What on Which Resource?

* **Identity and Access Management (IAM):** The foundation of security in Google Cloud.
* **Core Principle:** Controls **who** (principal/member) can perform **what actions** (permissions defined in a role) on **which specific Google Cloud resource**.
* **Analogy:** Think of it like a building's security system:
    * **Who:** The person trying to enter (User, Service Account, Group).
    * **What:** The key or access card they have (Role, e.g., "Office Access", "Server Room Access").
    * **Which Resource:** The specific door or room they are trying to access (Project, VM, Storage Bucket).
* **Purpose:** Ensures authenticated and authorized access, enforcing the principle of least privilege.

---

# IAM Principals (The "Who")

* **Principal (or Member):** An identity that can be granted access to a resource.
* **Types:**
    * **Google Account:** An individual user account (e.g., `user@gmail.com`, `user@your-domain.com` via Cloud Identity/Workspace). Represents a human user.
    * **Service Account:** A special account used by applications, VMs, or services (not humans) to authenticate and access GCP APIs (e.g., `my-app@<project-id>.iam.gserviceaccount.com`). *Crucial for secure application authentication.*
    * **Google Group:** A collection of Google Accounts and/or Service Accounts (e.g., `dev-team@your-domain.com`). Granting a role to a group grants it to all members – simplifies management.
    * **Cloud Identity / Google Workspace Domain:** Represents all users within a specific domain (e.g., `your-company.com`). Useful for granting broad access within an organization.
    * **Special Identifiers:**
        * `allUsers`: Anyone on the internet (extreme caution required. E.g. Public websites on Cloud Storage)
        * `allAuthenticatedUsers`: Anyone authenticated with *any* Google Account.

---

# IAM Roles (The "What")

* **Role:** A collection of granular **permissions**. Permissions define specific actions allowed on resources (e.g., `compute.instances.start`, `storage.objects.list`). You assign *roles*, not individual permissions directly.
* **Types of Roles:**
    * **Primitive Roles (Basic Roles):** Broad, project-level roles (`Owner`, `Editor`, `Viewer`).
        * *Legacy:* Existed before granular roles.
        * *Caution:* Often grant far more permissions than needed. Avoid if a Predefined role exists.
    * **Predefined Roles:** Curated by Google for specific services or job functions (e.g., `roles/compute.viewer`, `roles/storage.admin`, `roles/iam.serviceAccountUser`).
        * *Preferred:* Designed for **least privilege**. Hundreds available. Use these whenever possible!
    * **Custom Roles:** Create your own roles by combining specific permissions if no Predefined role meets your exact needs. Useful for highly specific requirements but require careful management.
* **Principle of Least Privilege:** Grant only the minimum permissions necessary for a principal to perform its intended task. Predefined roles are key to implementing this.

---

# IAM Policies (The Binding)

* **Policy:** Defines and enforces **who** gets **what** access to **which resource**. It binds one or more principals to one or more roles. A policy contains a list of **bindings**. Each binding maps a list of principals to a single role.
    * *Example Binding:* "Grant the `roles/compute.viewer` role to `user:alice@example.com` and `group:dev-team@example.com`."

* **Attachment:** Policies are attached to resources in the hierarchy:
    * Organization
    * Folder
    * Project
    * Service-level Resources (e.g., specific Compute Instance, Storage Bucket, Pub/Sub Topic).
* **Inheritance:** Policies are inherited down the hierarchy. A user granted `Viewer` at the Folder level will have `Viewer` permissions on all Projects within that Folder (unless overridden by a more specific policy).
* **Evaluation:** Access is granted if *any* applicable policy (direct or inherited) allows the action. It's a **union** of policies; there's no explicit "deny" in standard IAM policies (though Organization Policies can enforce restrictions).
