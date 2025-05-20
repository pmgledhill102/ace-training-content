# Recap Quiz

---

**Question 1:** Your team frequently deploys web server instances that require a specific version of Nginx, a custom monitoring agent, and several standard network tags. What is the most efficient and consistent way to ensure all new web server VMs are provisioned with this exact configuration?

- A. Manually configure the first VM, then clone its disk for subsequent VMs.
- B. Create a detailed startup script that installs and configures everything, and provide it each time a VM is created.
- C. Create a Compute Engine Instance Template that includes the desired boot disk image (with Nginx and agent pre-installed) and specifies the network tags.
- D. Use `gcloud compute instances update` on each new VM to apply the tags and run configuration scripts.

---

**Question 1:** Your team frequently deploys web server instances that require a specific version of Nginx, a custom monitoring agent, and several standard network tags. What is the most efficient and consistent way to ensure all new web server VMs are provisioned with this exact configuration?

- **Incorrect: A. Manually configure the first VM, then clone its disk for subsequent VMs:** Cloning disks addresses the software, but doesn't inherently manage other VM properties like machine type, network tags, or service accounts in a reusable, template-driven way.
- **Incorrect: B. Create a detailed startup script that installs and configures everything, and provide it each time a VM is created:** While a startup script can install software, it increases boot time and doesn't bundle the base image configuration. An Instance Template can include a pre-configured image *and* the startup script if needed, along with other VM settings.
- **Correct: C. Create a Compute Engine Instance Template that includes the desired boot disk image (with Nginx and agent pre-installed) and specifies the network tags:** Instance Templates are designed for this exact purpose – defining a complete VM configuration (machine type, image, disks, tags, metadata, service account) for repeatable and consistent deployments.
- **Incorrect: D. Use `gcloud compute instances update` on each new VM to apply the tags and run configuration scripts:** This is a reactive approach, applying settings after creation, and is less efficient and consistent than defining the entire configuration upfront with a template.

---

**Question 2:** You are running a large batch processing job that can be broken into many fault-tolerant tasks. The job needs to complete as cheaply as possible, and you can tolerate interruptions as long as tasks can be restarted. Which Compute Engine VM option is most suitable for this workload?

- A. Standard on-demand VMs
- B. Spot VMs
- C. Sole-tenant nodes
- D. N2 machine series with Committed Use Discounts

---

**Question 2:** You are running a large batch processing job that can be broken into many fault-tolerant tasks. The job needs to complete as cheaply as possible, and you can tolerate interruptions as long as tasks can be restarted. Which Compute Engine VM option is most suitable for this workload?

- **Incorrect: A. Standard on-demand VMs:** These provide reliability but are not the cheapest option, especially if interruptions are tolerable.
- **Correct: B. Spot VMs:** Offer the largest discounts (60-91% off on-demand) and are ideal for fault-tolerant, interruptible workloads like batch processing. They can be preempted but fit the cost and tolerance requirements.
- **Incorrect: C. Sole-tenant nodes:** These are premium offerings for dedicated hardware, primarily for compliance or licensing, and are not cost-effective for this type of workload.
- **Incorrect: D. N2 machine series with Committed Use Discounts:** CUDs offer good savings for predictable, long-running workloads, but Spot VMs offer even deeper discounts for interruptible tasks.

---

**Question 3:** Your company has a software license that requires the underlying server hardware not to be shared with any other customer. You need to deploy several VMs for this application on Google Cloud. Which Compute Engine feature should you use?

- A. Custom Machine Types
- B. Shielded VMs
- C. Sole-Tenant Nodes
- D. Preemptible VMs

---

**Question 3:** Your company has a software license that requires the underlying server hardware not to be shared with any other customer. You need to deploy several VMs for this application on Google Cloud. Which Compute Engine feature should you use?

- **Incorrect: A. Custom Machine Types:** These allow you to define specific vCPU/memory but do not guarantee dedicated hardware.
- **Incorrect: B. Shielded VMs:** These provide verifiable integrity of VMs but do not address the shared hardware concern for licensing.
- **Correct: C. Sole-Tenant Nodes:** These provide physical servers dedicated solely to your project's VMs, ensuring no hardware sharing with other customers, which is often a requirement for specific licensing or compliance needs.
- **Incorrect: D. Preemptible VMs:** These are low-cost, short-lived instances and do not offer dedicated hardware.

---

**Question 4:** An application running on a Compute Engine VM needs to dynamically retrieve its own instance ID and the project ID it belongs to. What is the recommended method for the application to obtain this information?

- A. Store these details in environment variables when the VM is created.
- B. Query the Google Cloud Resource Manager API from the VM.
- C. Access the Compute Engine instance metadata server.
- D. Hardcode the values into the application configuration.

---

**Question 4:** An application running on a Compute Engine VM needs to dynamically retrieve its own instance ID and the project ID it belongs to. What is the recommended method for the application to obtain this information?

- **Incorrect: A. Store these details in environment variables when the VM is created:** While possible, it requires manual setup or scripting during VM creation and is less dynamic than using the metadata server.
- **Incorrect: B. Query the Google Cloud Resource Manager API from the VM:** This is overly complex for retrieving instance-specific details and requires broader API scopes than necessary.
- **Correct: C. Access the Compute Engine instance metadata server:** The metadata server (accessible at `http://metadata.google.internal/computeMetadata/v1/`) provides a wealth of instance-specific information, including instance ID, project ID, zone, network configuration, and custom metadata, directly to the VM.
- **Incorrect: D. Hardcode the values into the application configuration:** This is inflexible and error-prone, especially if VMs are part of dynamic groups or templates.

---

**Question 5:** You need to create a standardized "golden image" for your company's Java application servers. This image should include a specific OS version, the correct Java Development Kit (JDK), and pre-configured application server settings. What Compute Engine resource should you create to achieve this?

- A. A Persistent Disk snapshot of a running server.
- B. An Instance Template.
- C. A Custom Image.
- D. A startup script stored in Cloud Storage.

---

**Question 5:** You need to create a standardized "golden image" for your company's Java application servers. This image should include a specific OS version, the correct Java Development Kit (JDK), and pre-configured application server settings. What Compute Engine resource should you create to achieve this?

- **Incorrect: A. A Persistent Disk snapshot of a running server:** While a snapshot can be used to create new disks, a Custom Image is the formal resource for creating reusable, bootable disk images intended for new VM deployments.
- **Incorrect: B. An Instance Template:** An Instance Template *uses* an image (either public or custom) as part of its definition; it is not the image itself.
- **Correct: C. A Custom Image:** Custom Images are bootable disk images containing your specific OS, software, and configurations. They are ideal for creating standardized "golden images" for consistent VM deployments.
- **Incorrect: D. A startup script stored in Cloud Storage:** A startup script applies configurations at boot time but doesn't create a pre-configured, reusable boot disk image.

---

**Question 6:** Your company has `vpc-dev` for development and `vpc-test` for testing. You establish VPC Network Peering between them. Later, `vpc-test` is also peered with `vpc-prod` for production. Can VMs in `vpc-dev` now directly communicate with VMs in `vpc-prod` using internal IPs via `vpc-test`?

- A. Yes, because `vpc-test` acts as a transit network.
- B. No, because VPC Network Peering is non-transitive.
- C. Yes, if Cloud Router is configured in `vpc-test`.
- D. No, because the IP ranges will likely overlap.

---

**Question 6:** Your company has `vpc-dev` for development and `vpc-test` for testing. You establish VPC Network Peering between them. Later, `vpc-test` is also peered with `vpc-prod` for production. Can VMs in `vpc-dev` now directly communicate with VMs in `vpc-prod` using internal IPs via `vpc-test`?

- **Incorrect: A. Yes, because `vpc-test` acts as a transit network:** VPC Network Peering does not allow for transitive routing.
- **Correct: B. No, because VPC Network Peering is non-transitive:** If VPC-A peers with VPC-B, and VPC-B peers with VPC-C, VPC-A cannot communicate with VPC-C through VPC-B unless a direct peering or other routing mechanism (like VPN) is established between A and C.
- **Incorrect: C. Yes, if Cloud Router is configured in `vpc-test`:** Cloud Router is for dynamic routing with external networks (VPN/Interconnect), not for enabling transitivity in VPC Peering.
- **Incorrect: D. No, because the IP ranges will likely overlap:** While non-overlapping IPs are a prerequisite for peering, the primary reason for lack of communication in this scenario is non-transitivity, not necessarily IP overlap (though overlap would also prevent peering).

---

**Question 7:** A large organization wants its central networking team to manage all VPC subnets, routes, and firewalls in a "Host Project." Application teams in separate "Service Projects" need to deploy their VMs into these centrally managed subnets. Which GCP feature is designed for this scenario?

- A. VPC Network Peering
- B. Shared VPC (XPN)
- C. Cloud VPN with dynamic routing
- D. Hierarchical Firewall Policies

---

**Question 7:** A large organization wants its central networking team to manage all VPC subnets, routes, and firewalls in a "Host Project." Application teams in separate "Service Projects" need to deploy their VMs into these centrally managed subnets. Which GCP feature is designed for this scenario?

- **Incorrect: A. VPC Network Peering:** Peering connects distinct VPCs but doesn't involve sharing subnets from one project for resource creation in another.
- **Correct: B. Shared VPC (XPN):** Allows a Host Project to share its subnets with Service Projects. Resources in Service Projects can then use these shared subnets, enabling centralized network administration while delegating resource management. This precisely matches the scenario.
- **Incorrect: C. Cloud VPN with dynamic routing:** This is for connecting VPCs to external networks (e.g., on-premises), not for sharing networks between GCP projects for resource deployment.
- **Incorrect: D. Hierarchical Firewall Policies:** These allow central definition of firewall rules at Org/Folder level but don't enable the sharing of subnets for resource deployment in different projects.

---

**Question 8:** You need to establish a highly available, private connection between your on-premises data center and your Google Cloud VPC. You anticipate consistently high bandwidth requirements (multiple Gbps) and require low, predictable latency. Which connectivity option is most suitable?

- A. Multiple HA VPN tunnels over the public internet.
- B. Cloud Interconnect - Dedicated or Partner.
- C. VPC Network Peering.
- D. Using `gsutil` over a fast internet connection.

---

**Question 8:** You need to establish a highly available, private connection between your on-premises data center and your Google Cloud VPC. You anticipate consistently high bandwidth requirements (multiple Gbps) and require low, predictable latency. Which connectivity option is most suitable?

- **Incorrect: A. Multiple HA VPN tunnels over the public internet:** While HA VPN provides high availability, performance over the public internet can be variable and may not consistently meet multi-Gbps low-latency needs.
- **Correct: B. Cloud Interconnect - Dedicated or Partner:** Cloud Interconnect provides dedicated physical or partner-provided connections to Google's network, offering high bandwidth, low latency, and private connectivity, ideal for such requirements.
- **Incorrect: C. VPC Network Peering:** This connects GCP VPCs to each other, not to on-premises data centers.
- **Incorrect: D. Using `gsutil` over a fast internet connection:** `gsutil` is a tool for Cloud Storage, not a network connectivity solution for VPCs.

---

**Question 9:** You have enabled Object Versioning on a Cloud Storage bucket. What happens when you upload a new file with the same name as an existing object in the bucket?

- A. The existing object is overwritten and permanently lost.
- B. The upload fails because an object with that name already exists.
- C. The existing object becomes a noncurrent (archived) version, and the new file becomes the live version.
- D. The new file is stored as a noncurrent version, and the existing object remains the live version.

---

**Question 9:** You have enabled Object Versioning on a Cloud Storage bucket. What happens when you upload a new file with the same name as an existing object in the bucket?

- **Incorrect: A. The existing object is overwritten and permanently lost:** This is what happens if versioning is *not* enabled.
- **Incorrect: B. The upload fails because an object with that name already exists:** Cloud Storage allows overwrites (which become versions if versioning is on).
- **Correct: C. The existing object becomes a noncurrent (archived) version, and the new file becomes the live version:** With versioning enabled, overwriting an object preserves the old version as noncurrent and makes the newly uploaded content the live version, each with a unique generation number.
- **Incorrect: D. The new file is stored as a noncurrent version, and the existing object remains the live version:** The new upload always becomes the live version.

---

**Question 10:** What is the primary benefit of enabling Uniform Bucket-Level Access (UBLA) on a Cloud Storage bucket?

- A. It encrypts all objects in the bucket using a customer-managed encryption key (CMEK).
- B. It simplifies access control by disabling object ACLs and relying solely on IAM permissions.
- C. It automatically applies the cheapest storage class to all objects in the bucket.
- D. It enables global replication of the bucket's contents for lower latency access.

---

**Question 10:** What is the primary benefit of enabling Uniform Bucket-Level Access (UBLA) on a Cloud Storage bucket?

- **Incorrect: A. It encrypts all objects in the bucket using a customer-managed encryption key (CMEK):** UBLA is about access control, not encryption methods.
- **Correct: B. It simplifies access control by disabling object ACLs and relying solely on IAM permissions:** UBLA ensures that IAM is the single source of truth for permissions on a bucket and its objects, making management and auditing easier by removing object-level ACLs.
- **Incorrect: C. It automatically applies the cheapest storage class to all objects in the bucket:** Storage class is a separate setting from UBLA.
- **Incorrect: D. It enables global replication of the bucket's contents for lower latency access:** Bucket location (regional, dual-region, multi-region) determines data replication and access latency, not UBLA.

---

**Question 11:** For a Cloud SQL for MySQL instance, what is the primary mechanism used to achieve High Availability (HA) and automatic failover in the event of a zonal outage?

- A. Asynchronously replicating data to a read replica in another region.
- B. Manually promoting a read replica in a different zone upon failure.
- C. Synchronously replicating data to a standby instance in a different zone within the same region.
- D. Regularly backing up the instance to Cloud Storage and restoring it in a new zone if needed.

---

**Question 11:** For a Cloud SQL for MySQL instance, what is the primary mechanism used to achieve High Availability (HA) and automatic failover in the event of a zonal outage?

- **Incorrect: A. Asynchronously replicating data to a read replica in another region:** Read replicas use asynchronous replication and are primarily for read scaling or DR, not automatic HA failover for the primary.
- **Incorrect: B. Manually promoting a read replica in a different zone upon failure:** This is a manual DR step, not the automatic HA mechanism.
- **Correct: C. Synchronously replicating data to a standby instance in a different zone within the same region:** Cloud SQL HA involves a primary and a standby instance in different zones within the same region, with synchronous replication ensuring data consistency and enabling automatic failover.
- **Incorrect: D. Regularly backing up the instance to Cloud Storage and restoring it in a new zone if needed:** Backups are for DR and point-in-time recovery but do not provide automatic HA failover.

---

**Question 12:** You need an in-memory caching solution. Your application requires complex data structures like sorted sets and lists, and you also need the option for data persistence. Which Memorystore option is more suitable?

- A. Memorystore for Memcached, because it's simpler.
- B. Memorystore for Redis, because it supports richer data structures and persistence.
- C. Either, as both offer identical features for caching.
- D. Neither, Cloud SQL with SSD disks should be used for caching.

---

**Question 12:** You need an in-memory caching solution. Your application requires complex data structures like sorted sets and lists, and you also need the option for data persistence. Which Memorystore option is more suitable?

- **Incorrect: A. Memorystore for Memcached, because it's simpler:** Memcached is simpler but does not support rich data structures like sorted sets or offer built-in persistence.
- **Correct: B. Memorystore for Redis, because it supports richer data structures and persistence:** Redis offers a wide array of data structures (lists, sets, sorted sets, hashes) and has options for RDB snapshotting and AOF logging for persistence, making it suitable for these requirements.
- **Incorrect: C. Either, as both offer identical features for caching:** They have different feature sets beyond basic key-value caching.
- **Incorrect: D. Neither, Cloud SQL with SSD disks should be used for caching:** Cloud SQL is a relational database, not an in-memory cache. Memorystore is specifically designed for caching.

---

**Question 13:** Your company is building a global application that requires a relational database with strong transactional consistency (ACID) across continents and horizontal scalability for petabytes of data. Which Google Cloud database service is designed for this specific set of requirements?

- A. Cloud SQL with cross-region read replicas.
- B. Cloud Firestore in Native Mode.
- C. Cloud Spanner.
- D. Cloud Bigtable.

---

**Question 13:** Your company is building a global application that requires a relational database with strong transactional consistency (ACID) across continents and horizontal scalability for petabytes of data. Which Google Cloud database service is designed for this specific set of requirements?

- **Incorrect: A. Cloud SQL with cross-region read replicas:** Cloud SQL is regional, and while read replicas can be cross-region, the primary instance and strong consistency are typically bound to a single region. It also doesn't scale horizontally to the same degree as Spanner for global ACID transactions.
- **Incorrect: B. Cloud Firestore in Native Mode:** Firestore is a NoSQL document database, not relational, though it offers strong consistency regionally.
- **Correct: C. Cloud Spanner:** This is Google's globally distributed, strongly consistent, relational database service that supports SQL, ACID transactions across continents, and horizontal scalability to petabytes.
- **Incorrect: D. Cloud Bigtable:** Bigtable is a NoSQL wide-column store, not relational, and does not offer ACID transactions in the same way as Spanner.

---

**Question 14:** You need to migrate 50 TB of data from an on-premises NFS server to Google Cloud Storage. You want a managed service that can handle the transfer efficiently, provide progress monitoring, and perform data integrity checks. Which Google Cloud service is most appropriate for this task?

- A. `gsutil cp -m` command.
- B. Storage Transfer Service with an on-premises agent configuration.
- C. BigQuery Data Transfer Service.
- D. Database Migration Service.

---

**Question 14:** You need to migrate 50 TB of data from an on-premises NFS server to Google Cloud Storage. You want a managed service that can handle the transfer efficiently, provide progress monitoring, and perform data integrity checks. Which Google Cloud service is most appropriate for this task?

- **Incorrect: A. `gsutil cp -m` command:** While `gsutil` can transfer data, for 50TB from on-premises, a managed service is better for reliability, monitoring, and performance over potentially unstable connections.
- **Correct: B. Storage Transfer Service with an on-premises agent configuration:** Storage Transfer Service is designed for large-scale data movement into Cloud Storage and supports on-premises file system transfers via agents, offering managed transfers, scheduling, monitoring, and integrity checks.
- **Incorrect: C. BigQuery Data Transfer Service:** This service is for loading data into BigQuery from various sources, not directly for migrating file systems to Cloud Storage.
- **Incorrect: D. Database Migration Service:** This service is for migrating databases to Cloud SQL or other managed databases, not for general file system to Cloud Storage transfers.

---

**Question 15:** Which of the following best describes Cloud Deployment Manager (CDM)?

- A. A third-party Infrastructure as Code tool that supports multiple cloud providers.
- B. A Google Cloud native Infrastructure as Code service that uses YAML configurations and Jinja2/Python templates.
- C. A command-line interface primarily used for interacting with Cloud Storage buckets.
- D. A service for discovering and deploying pre-configured solutions from third-party vendors.

---

**Question 15:** Which of the following best describes Cloud Deployment Manager (CDM)?

- **Incorrect: A. A third-party Infrastructure as Code tool that supports multiple cloud providers:** CDM is Google Cloud native and specific to GCP. Terraform is an example of a multi-cloud IaC tool.
- **Correct: B. A Google Cloud native Infrastructure as Code service that uses YAML configurations and Jinja2/Python templates:** This accurately describes Cloud Deployment Manager's nature, configuration language, and templating options.
- **Incorrect: C. A command-line interface primarily used for interacting with Cloud Storage buckets:** That describes `gsutil`.
- **Incorrect: D. A service for discovering and deploying pre-configured solutions from third-party vendors:** That describes Cloud Marketplace.
