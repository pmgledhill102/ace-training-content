**Question 1:** You need to deploy a fleet of 10 identical web server VMs. You want to ensure each VM is configured with the same machine type, boot disk image, network tags, and a specific startup script. What is the most efficient way to achieve this in Compute Engine?

- A. Manually create each VM, carefully selecting the same options each time.
- B. Create one VM, then take a snapshot of its boot disk and create the other 9 VMs from that snapshot.
- C. Define an Instance Template with the desired configuration and use it to create the 10 VMs.
- D. Write a complex shell script that uses `gcloud compute instances create` with all parameters specified for each VM.

---

**Question 1:** You need to deploy a fleet of 10 identical web server VMs. You want to ensure each VM is configured with the same machine type, boot disk image, network tags, and a specific startup script. What is the most efficient way to achieve this in Compute Engine?

- **Incorrect: A. Manually create each VM, carefully selecting the same options each time:** This is inefficient, error-prone, and not scalable.
- **Incorrect: B. Create one VM, then take a snapshot of its boot disk and create the other 9 VMs from that snapshot:** While a snapshot captures the disk state, it doesn't inherently define other VM properties like machine type, network tags, or metadata like startup scripts in a reusable template format. You'd still need to specify these for each new VM.
- **Correct: C. Define an Instance Template with the desired configuration and use it to create the 10 VMs:** Instance Templates are specifically designed to define all aspects of a VM's configuration (machine type, image, disks, tags, metadata including startup scripts) for repeatable deployments. This is the most efficient and consistent method.
- **Incorrect: D. Write a complex shell script that uses `gcloud compute instances create` with all parameters specified for each VM:** While scripting is better than manual creation, an Instance Template abstracts the configuration, making the script simpler (it would just call the template) and easier to manage changes to the VM configuration itself.

---

**Question 2:** Your application running on a Compute Engine instance needs to write temporary processing files that require extremely high IOPS and low latency. The data does not need to persist if the VM is stopped or restarted. Which disk type is most suitable for this temporary storage?

- A. Standard Persistent Disk (`pd-standard`)
- B. SSD Persistent Disk (`pd-ssd`)
- C. Local SSD
- D. A Cloud Storage bucket mounted via `gcsfuse`

---

**Question 2:** Your application running on a Compute Engine instance needs to write temporary processing files that require extremely high IOPS and low latency. The data does not need to persist if the VM is stopped or restarted. Which disk type is most suitable for this temporary storage?

- **Incorrect: A. Standard Persistent Disk (`pd-standard`):** HDD-based, offers lower IOPS and higher latency, not suitable for extreme performance needs.
- **Incorrect: B. SSD Persistent Disk (`pd-ssd`):** Offers good SSD performance and is persistent, but Local SSDs provide even higher IOPS and lower latency for ephemeral needs.
- **Correct: C. Local SSD:** Physically attached to the VM host, offering the highest IOPS and lowest latency. Data is ephemeral (lost on stop/restart), which aligns with the requirement for temporary, non-persistent storage.
- **Incorrect: D. A Cloud Storage bucket mounted via `gcsfuse`:** Cloud Storage is object storage, and `gcsfuse` provides file system access. While flexible, it will have significantly higher latency and lower IOPS compared to Local SSDs, making it unsuitable for extreme performance temporary file needs.

---

**Question 3:** You have two VPC networks, `vpc-A` and `vpc-B`, in the same organization but different projects. You want VMs in `vpc-A` to communicate with VMs in `vpc-B` using their internal IP addresses without traffic traversing the public internet. The CIDR ranges of the subnets in `vpc-A` and `vpc-B` do not overlap. What GCP networking feature should you use?

- A. Cloud VPN between `vpc-A` and `vpc-B`.
- B. VPC Network Peering between `vpc-A` and `vpc-B`.
- C. Shared VPC, making one VPC the host and the other a service project.
- D. External HTTP(S) Load Balancer.

---

**Question 3:** You have two VPC networks, `vpc-A` and `vpc-B`, in the same organization but different projects. You want VMs in `vpc-A` to communicate with VMs in `vpc-B` using their internal IP addresses without traffic traversing the public internet. The CIDR ranges of the subnets in `vpc-A` and `vpc-B` do not overlap. What GCP networking feature should you use?

- **Incorrect: A. Cloud VPN between `vpc-A` and `vpc-B`:** While VPN can connect networks, VPC Network Peering is simpler and uses Google's internal backbone directly for VPC-to-VPC communication within Google Cloud.
- **Correct: B. VPC Network Peering between `vpc-A` and `vpc-B`:** Peering allows private IP connectivity between VPC networks in the same or different organizations. It's non-transitive and requires non-overlapping CIDR ranges. This perfectly fits the scenario.
- **Incorrect: C. Shared VPC, making one VPC the host and the other a service project:** Shared VPC is for sharing subnets from a host project to service projects, allowing centralized network admin. It's not primarily for connecting two independent, existing VPCs.
- **Incorrect: D. External HTTP(S) Load Balancer:** This is for distributing external traffic to backends and does not facilitate internal IP communication between VPCs.

---

**Question 4:** A startup script on your Compute Engine instance needs to determine the instance's external IP address to register with a discovery service. How can the script securely obtain this information?

- A. Hardcode the external IP address into the startup script.
- B. Query the public "what is my IP" service on the internet.
- C. Query the Compute Engine instance metadata server.
- D. Store the IP in a Cloud Storage bucket and have the script read it.

---

**Question 4:** A startup script on your Compute Engine instance needs to determine the instance's external IP address to register with a discovery service. How can the script securely obtain this information?

- **Incorrect: A. Hardcode the external IP address into the startup script:** External IPs can change if not static, and this is not a dynamic or secure solution.
- **Incorrect: B. Query the public "what is my IP" service on the internet:** This relies on external services and might be blocked by egress firewall rules or be unreliable.
- **Correct: C. Query the Compute Engine instance metadata server:** The metadata server (accessible at `http://metadata.google.internal/computeMetadata/v1/...`) provides various instance details, including network interface information like external IP addresses, in a secure and reliable way from within the VM.
- **Incorrect: D. Store the IP in a Cloud Storage bucket and have the script read it:** This is overly complex and introduces an external dependency for information readily available via the metadata server.

---

**Question 5:** You want to grant a user temporary read-only access to a specific large log file stored in a private Cloud Storage bucket. You do not want to grant them IAM permissions on the bucket or project, nor make the object public. What is the most appropriate method?

- A. Add the user to the bucket's ACLs with `READER` permission.
- B. Create a new IAM role with `storage.objects.get` permission and assign it to the user for that object.
- C. Generate a Signed URL for the object with a short expiration time and share it with the user.
- D. Download the file and email it to the user.

---

**Question 5:** You want to grant a user temporary read-only access to a specific large log file stored in a private Cloud Storage bucket. You do not want to grant them IAM permissions on the bucket or project, nor make the object public. What is the most appropriate method?

- **Incorrect: A. Add the user to the bucket's ACLs with `READER` permission:** This grants ongoing access to the object until the ACL is removed and might be broader than needed if the bucket has Uniform Bucket-Level Access disabled. The request is for *temporary* access.
- **Incorrect: B. Create a new IAM role with `storage.objects.get` permission and assign it to the user for that object:** IAM permissions on individual objects are not directly supported; IAM is typically at the bucket or project level. While IAM conditions could potentially scope access, Signed URLs are simpler for this specific temporary, single-object use case.
- **Correct: C. Generate a Signed URL for the object with a short expiration time and share it with the user:** Signed URLs provide time-limited, authenticated access to a specific object without requiring the user to have GCP credentials or altering bucket/object permissions. This is ideal for temporary, targeted sharing.
- **Incorrect: D. Download the file and email it to the user:** For a "large log file," email is often impractical due to size limits and is less secure than a direct, authenticated download.

---

**Question 6:** Your organization has a central networking team that manages all VPCs, subnets, and firewall rules in a "Host Project." Application teams deploy their Compute Engine instances into "Service Projects" but need to use the centrally managed networks. Which GCP feature enables this model?

- A. VPC Network Peering
- B. Cloud VPN
- C. Shared VPC (XPN)
- D. Cloud Interconnect

---

**Question 6:** Your organization has a central networking team that manages all VPCs, subnets, and firewall rules in a "Host Project." Application teams deploy their Compute Engine instances into "Service Projects" but need to use the centrally managed networks. Which GCP feature enables this model?

- **Incorrect: A. VPC Network Peering:** Peering connects distinct VPCs but doesn't involve sharing subnets from one project for resource creation in another.
- **Incorrect: B. Cloud VPN:** Connects VPCs to external networks, not relevant for internal project resource sharing of networks.
- **Correct: C. Shared VPC (XPN):** Allows a Host Project to share its subnets with Service Projects. Resources in Service Projects can then use these shared subnets, enabling centralized network administration while delegating resource management. This exactly matches the scenario.
- **Incorrect: D. Cloud Interconnect:** Provides dedicated connectivity to on-premises networks, not for sharing networks between GCP projects.

---

**Question 7:** You need to deploy a common web application (e.g., WordPress) on a Compute Engine instance. You want to minimize the setup and configuration effort. What is the quickest way to achieve this?

- A. Manually install all components (web server, PHP, database, WordPress) on a new Linux VM.
- B. Use an Instance Template you previously created for a different application.
- C. Launch a solution from the Cloud Marketplace.
- D. Write a complex startup script to automate the entire installation.

---

**Question 7:** You need to deploy a common web application (e.g., WordPress) on a Compute Engine instance. You want to minimize the setup and configuration effort. What is the quickest way to achieve this?

- **Incorrect: A. Manually install all components (web server, PHP, database, WordPress) on a new Linux VM:** This involves maximum effort and is prone to errors.
- **Incorrect: B. Use an Instance Template you previously created for a different application:** An instance template for a *different* application won't help deploy WordPress.
- **Correct: C. Launch a solution from the Cloud Marketplace:** Cloud Marketplace offers pre-configured solutions, including WordPress stacks, that can be deployed with minimal effort, often with just a few clicks. This is the quickest way for common applications.
- **Incorrect: D. Write a complex startup script to automate the entire installation:** While automation is good, for common applications, Marketplace solutions are typically faster to deploy than writing and debugging a complex script from scratch.

---

**Question 8:** Which of the following services is a fully managed, in-memory data store service on Google Cloud, suitable for caching and session management?

- A. Cloud SQL
- B. Cloud Spanner
- C. Memorystore
- D. Persistent Disk

---

**Question 8:** Which of the following services is a fully managed, in-memory data store service on Google Cloud, suitable for caching and session management?

- **Incorrect: A. Cloud SQL:** A managed relational database service (MySQL, PostgreSQL, SQL Server), not primarily an in-memory cache.
- **Incorrect: B. Cloud Spanner:** A globally distributed, strongly consistent relational database, not an in-memory cache.
- **Correct: C. Memorystore:** Provides fully managed Redis and Memcached services, which are in-memory data stores ideal for caching, session management, and other low-latency use cases.
- **Incorrect: D. Persistent Disk:** Block storage for VMs, not an in-memory data store service.

---

**Question 9:** What is the primary benefit of using Infrastructure as Code (IaC) tools like Cloud Deployment Manager or Terraform?

- A. They provide a graphical user interface for easier resource deployment.
- B. They automatically optimize cloud spending by selecting the cheapest resources.
- C. They enable consistent, repeatable, and version-controlled infrastructure provisioning.
- D. They eliminate the need for understanding underlying GCP services.

---

**Question 9:** What is the primary benefit of using Infrastructure as Code (IaC) tools like Cloud Deployment Manager or Terraform?

- **Incorrect: A. They provide a graphical user interface for easier resource deployment:** IaC tools are code-based, not primarily GUI-based. The GCP Console is the GUI.
- **Incorrect: B. They automatically optimize cloud spending by selecting the cheapest resources:** While IaC can help manage costs by defining specific resource types, they don't inherently perform automatic cost optimization in terms of dynamic selection.
- **Correct: C. They enable consistent, repeatable, and version-controlled infrastructure provisioning:** IaC allows infrastructure to be defined as code, leading to automated, reliable, and auditable deployments. Configurations can be versioned in source control.
- **Incorrect: D. They eliminate the need for understanding underlying GCP services:** Effective use of IaC still requires a good understanding of the GCP services being provisioned and their configurations.

---

**Question 10:** You are creating a custom Compute Engine instance and need 10 vCPUs but only 20 GB of memory. None of the predefined machine types match this specific ratio. What should you do?

- A. Choose an N1 predefined machine type with 16 vCPUs and 60 GB of memory, as it's the closest larger option.
- B. Use a Custom Machine Type (e.g., from the N1, N2, or E2 series) and specify 10 vCPUs and 20 GB of memory.
- C. Deploy two smaller predefined instances and try to split the workload.
- D. Submit a feature request to Google Cloud to add a new predefined machine type.

---

**Question 10:** You are creating a custom Compute Engine instance and need 10 vCPUs but only 20 GB of memory. None of the predefined machine types match this specific ratio. What should you do?

- **Incorrect: A. Choose an N1 predefined machine type with 16 vCPUs and 60 GB of memory, as it's the closest larger option:** This would lead to over-provisioning and unnecessary costs for unused resources.
- **Correct: B. Use a Custom Machine Type (e.g., from the N1, N2, or E2 series) and specify 10 vCPUs and 20 GB of memory:** Custom Machine Types allow you to define the exact number of vCPUs and amount of memory (within allowed ranges and ratios for the chosen series), providing flexibility and cost optimization for workloads with specific needs.
- **Incorrect: C. Deploy two smaller predefined instances and try to split the workload:** This adds complexity to the application architecture and may not be feasible or efficient.
- **Incorrect: D. Submit a feature request to Google Cloud to add a new predefined machine type:** While feedback is valuable, Custom Machine Types are designed to solve this exact problem immediately.
