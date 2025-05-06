# Week 1 Quiz

---

**Question 1:** Your team needs to ensure that developers only have permissions to manage Compute Engine instances within a specific development project, but cannot modify IAM policies for that project. Which IAM role is most appropriate?

- A. `roles/compute.admin`
- B. `roles/compute.instanceAdmin.v1`
- C. `roles/editor`
- D. `roles/owner`

---

**Question 1:** Your team needs to ensure that developers only have permissions to manage Compute Engine instances within a specific development project, but cannot modify IAM policies for that project. Which IAM role is most appropriate?

- **Incorrect: A. `roles/compute.admin`:** This role includes permissions like `compute.instances.setIamPolicy`, allowing modification of instance-level IAM policies, and often implies broader project permissions depending on how it's granted. It's generally broader than just instance administration.
- **Correct: B. `roles/compute.instanceAdmin.v1`:** This role grants comprehensive control over Compute Engine instances (create, delete, start, stop, SSH, etc.) *without* granting permissions to modify project-level IAM policies or Compute Engine IAM policies. It adheres to the principle of least privilege for this specific task.
- **Incorrect: C. `roles/editor`:** This is a primitive role granting broad edit access across *most* services within the project, including potentially modifying IAM policies (depending on context) and is far too permissive.
- **Incorrect: D. `roles/owner`:** This primitive role grants full control over all resources and IAM policies within the project, providing excessive permissions.

---

**Question 2:** You need to store large, infrequently accessed data backups in Google Cloud at the lowest possible cost. High availability is required (meaning the data should survive the loss of a zone), but immediate retrieval isn't necessary (retrieval time of hours is acceptable). Which Cloud Storage class should you use?

- A. Standard Storage
- B. Nearline Storage
- C. Coldline Storage
- D. Archive Storage

---

**Question 2:** You need to store large, infrequently accessed data backups in Google Cloud at the lowest possible cost. High availability is required (meaning the data should survive the loss of a zone), but immediate retrieval isn't necessary (retrieval time of hours is acceptable). Which Cloud Storage class should you use?

- **Incorrect: A. Standard Storage:** Designed for frequently accessed ("hot") data with low latency access; has the highest storage cost.
- **Incorrect: B. Nearline Storage:** Designed for data accessed less than once a month; lower cost than Standard, but higher than Coldline/Archive. Retrieval is typically in seconds.
- **Incorrect: C. Coldline Storage:** Designed for data accessed less than once per 90 days; lower cost than Nearline, but higher than Archive. Retrieval is typically in seconds to minutes.
- **Correct: D. Archive Storage:** Offers the lowest storage cost, designed for data accessed less than once a year. Retrieval times are typically measured in hours, matching the requirement. All standard classes (Std, Nearline, Coldline, Archive) offer high durability and availability options (e.g., Regional or Multi-regional buckets).

---

**Question 3:** You are setting up a new Google Cloud project for a production application. To apply consistent IAM policies and organizational constraints for all production workloads, where should you ideally create this project within your organization's resource hierarchy?

- A. Directly under the Organization node.
- B. Inside a dedicated "Production" Folder.
- C. Inside your personal user Folder (if one exists).
- D. It doesn't matter where the project is created as long as billing is set up.

---

**Question 3:** You are setting up a new Google Cloud project for a production application. To apply consistent IAM policies and organizational constraints for all production workloads, where should you ideally create this project within your organization's resource hierarchy?

- **Incorrect: A. Directly under the Organization node:** While possible, this makes it harder to apply specific policies only to production projects without affecting others (like development or staging) directly under the Organization.
- **Correct: B. Inside a dedicated "Production" Folder:** Folders allow grouping of projects (e.g., by environment, team, or application). Applying IAM policies or Organization Policies at the Folder level ensures consistency across all projects within that folder (like "Production") and aids manageability.
- **Incorrect: C. Inside your personal user Folder (if one exists):** Personal or user-specific folders are generally inappropriate for shared, critical production applications.
- **Incorrect: D. It doesn't matter where the project is created as long as billing is set up:** The placement within the hierarchy is crucial for governance, policy inheritance, and organization.

---

**Question 4:** A developer needs to quickly run some `gcloud` commands to check the status of Compute Engine instances in a project. They are working from their local machine which already has the Cloud SDK installed. What is the first `gcloud` command they should run to ensure their commands target the correct project?

- A. `gcloud compute instances list`
- B. `gcloud config set project <PROJECT_ID>`
- C. `gcloud auth login`
- D. `gcloud init`

---

**Question 4:** A developer needs to quickly run some `gcloud` commands to check the status of Compute Engine instances in a project. They are working from their local machine which already has the Cloud SDK installed. What is the first `gcloud` command they should run to ensure their commands target the correct project?

- **Incorrect: A. `gcloud compute instances list`:** This command lists instances, but it will run against the *currently configured* project in the Cloud SDK settings. If the wrong project is configured, it won't target the intended one.
- **Correct: B. `gcloud config set project <PROJECT_ID>`:** This command explicitly sets the active project for subsequent `gcloud` commands in the current configuration. This ensures commands target the intended project.
- **Incorrect: C. `gcloud auth login`:** This command is used to authenticate the user, which is necessary but doesn't set the target project. Authentication might already be valid from a previous session.
- **Incorrect: D. `gcloud init`:** This command initializes or reinitializes a configuration profile, including setting project, account, region/zone. While it *can* set the project, it's a more involved process than simply setting the active project for the current configuration, which is sufficient here.

---

**Question 5:** You need to create a new Compute Engine VM instance that will host a web server. You want the VM to automatically install Apache upon startup. What is the standard Compute Engine feature used to run scripts when an instance boots up?

- A. Instance Template
- B. Startup Script
- C. Network Tag
- D. Persistent Disk Snapshot

---

**Question 5:** You need to create a new Compute Engine VM instance that will host a web server. You want the VM to automatically install Apache upon startup. What is the standard Compute Engine feature used to run scripts when an instance boots up?

- **Incorrect: A. Instance Template:** An Instance Template defines the configuration of a VM (machine type, disk, network, etc.) and *can include* a startup script, but the template itself is not the script execution mechanism.
- **Correct: B. Startup Script:** Compute Engine allows you to provide metadata, including a startup script (specified directly or via a URL like GCS), which runs automatically the first time an instance boots or restarts (depending on OS configuration). This is the standard way to perform initial setup tasks like installing software.
- **Incorrect: C. Network Tag:** Network tags are labels applied to VMs used primarily for applying firewall rules and routes.
- **Incorrect: D. Persistent Disk Snapshot:** Snapshots are point-in-time backups of persistent disks, used for data recovery or creating new disks, not for running scripts on boot.

---

**Question 6:** Your application requires a storage solution for user-uploaded images. These images need to be accessible globally with low latency and high durability. Which Google Cloud storage service and configuration is most appropriate?

- A. Compute Engine Persistent Disk (Regional SSD)
- B. Cloud SQL Database
- C. Cloud Storage Bucket (Multi-Regional)
- D. Cloud Storage Bucket (Regional)

---

**Question 6:** Your application requires a storage solution for user-uploaded images. These images need to be accessible globally with low latency and high durability. Which Google Cloud storage service and configuration is most appropriate?

- **Incorrect: A. Compute Engine Persistent Disk (Regional SSD):** Persistent disks are block storage attached to specific VMs within a zone. They are not designed for direct global access via HTTP(S) and are not suitable for storing large amounts of unstructured data like images for web delivery.
- **Incorrect: B. Cloud SQL Database:** Cloud SQL is a managed relational database service. While it *could* technically store image data (e.g., as BLOBs), it's highly inefficient, costly, and not designed for serving static files globally at low latency.
- **Correct: C. Cloud Storage Bucket (Multi-Regional):** Cloud Storage is Google's object storage service, ideal for unstructured data like images. A Multi-Regional bucket stores data redundantly across multiple geographically distant regions, providing high durability and low-latency access for users across a continent or wider area.
- **Incorrect: D. Cloud Storage Bucket (Regional):** While Cloud Storage is the right service, a Regional bucket stores data redundantly only within zones of a single region. This provides lower latency for users *within* that region but higher latency for global users compared to a Multi-Regional bucket.

---

**Question 7:** You need to allow HTTP traffic (port 80) from any IP address on the internet to your Compute Engine instances tagged with `web-server`. Which component should you configure in your VPC network?

- A. A VPC Network Peering connection
- B. A Cloud VPN tunnel
- C. A Cloud Router
- D. A VPC Firewall Rule

---

**Question 7:** You need to allow HTTP traffic (port 80) from any IP address on the internet to your Compute Engine instances tagged with `web-server`. Which component should you configure in your VPC network?

- **Incorrect: A. A VPC Network Peering connection:** Peering connects two VPC networks for internal IP communication. It doesn't control traffic from the internet.
- **Incorrect: B. A Cloud VPN tunnel:** VPN connects your VPC to an external network (like on-premises) securely. It doesn't control general internet ingress traffic.
- **Incorrect: C. A Cloud Router:** Cloud Routers manage dynamic routing (BGP) for VPNs and Interconnects. They don't directly filter traffic based on ports or tags.
- **Correct: D. A VPC Firewall Rule:** Firewall rules control incoming (ingress) and outgoing (egress) traffic to/from VM instances within a VPC network. You would create an ingress rule allowing TCP traffic on port 80, specifying the source IP range as `0.0.0.0/0` (any IP) and the target as the `web-server` tag.

---

**Question 8:** Your company uses Google Cloud and wants to monitor cloud spending closely. You need to configure an alert that notifies the finance team via email if the total cost accrued for the month in your primary billing account exceeds $10,000. What should you configure?

- A. A Cloud Monitoring Alerting Policy based on a metric.
- B. A Log Sink exporting billing data to BigQuery.
- C. A Billing Budget with an Alert Threshold.
- D. An Organization Policy restricting resource creation.

---

**Question 8:** Your company uses Google Cloud and wants to monitor cloud spending closely. You need to configure an alert that notifies the finance team via email if the total cost accrued for the month in your primary billing account exceeds $10,000. What should you configure?

- **Incorrect: A. A Cloud Monitoring Alerting Policy based on a metric:** While Monitoring handles operational metrics, specific billing cost alerts are managed directly within the Billing service using Budgets.
- **Incorrect: B. A Log Sink exporting billing data to BigQuery:** Exporting billing data allows for detailed analysis but doesn't provide real-time alerting based on exceeding a threshold amount.
- **Correct: C. A Billing Budget with an Alert Threshold:** Billing Budgets allow you to set a target amount (e.g., $10,000) for a specific scope (entire billing account, specific projects, etc.) over a time period (usually monthly). You can then configure percentage-based alert thresholds (e.g., at 50%, 90%, 100% of the budget) that trigger notifications (like email) when actual or forecasted costs reach those levels.
- **Incorrect: D. An Organization Policy restricting resource creation:** Organization Policies enforce constraints on resource configuration (e.g., allowed locations, VM types), which can *indirectly* impact cost, but they don't provide direct cost alerting based on spend amounts.

---

**Question 9:** Which Google Cloud resource hierarchy level is used to group projects, allowing you to apply common IAM or Organization Policies to multiple projects simultaneously?

- A. Organization
- B. Folder
- C. Project
- D. Resource

---

**Question 9:** Which Google Cloud resource hierarchy level is used to group projects, allowing you to apply common IAM or Organization Policies to multiple projects simultaneously?

- **Incorrect: A. Organization:** The Organization node is the root of the hierarchy. Policies applied here affect *all* resources within the organization, but it doesn't group specific subsets of projects.
- **Correct: B. Folder:** Folders are specifically designed to group projects (or other folders) beneath the Organization node. This allows for logical structuring (e.g., by environment, department, application) and the application of policies at the folder level, which are then inherited by the projects within that folder.
- **Incorrect: C. Project:** Projects contain resources. While policies can be applied at the project level, a project itself doesn't group *other* projects.
- **Incorrect: D. Resource:** Resources (like VMs, buckets, databases) reside *within* projects and are the lowest level where policies can sometimes be applied, but they don't group projects.

---

**Question 10:** You are creating a Cloud Storage bucket via the `gsutil` command-line tool. You need to ensure the bucket name is unique across all of Google Cloud. What naming convention characteristic helps achieve this?

- A. Bucket names must contain the project ID.
- B. Bucket names share a global namespace.
- C. Bucket names are only unique within a project.
- D. Bucket names must include the region.

---

**Question 10:** You are creating a Cloud Storage bucket via the `gsutil` command-line tool. You need to ensure the bucket name is unique across all of Google Cloud. What naming convention characteristic helps achieve this?

- **Incorrect: A. Bucket names must contain the project ID:** There is no requirement for bucket names to contain the project ID.
- **Correct: B. Bucket names share a global namespace:** All Cloud Storage bucket names must be unique across the entire Google Cloud platform, regardless of the project they are created in or their location. This global uniqueness ensures that each bucket has a distinct identity.
- **Incorrect: C. Bucket names are only unique within a project:** Bucket names must be globally unique, not just within a single project.
- **Incorrect: D. Bucket names must include the region:** While you specify a location (region, multi-region, dual-region) when creating a bucket, the region is not part of the bucket name itself, and the name must still be globally unique.
