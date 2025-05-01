# Week 1

## **Week 1: Fundamentals: Core Infrastructure**

- **Topic:** Fundamentals: Core Infrastructure
- **Corresponding Cloud Skills Boost Module(s):** Google Cloud Fundamentals: Core Infrastructure [3]
- **Relevant ACE Exam Objectives:** This week lays the groundwork, covering foundational concepts essential for understanding subsequent topics and directly mapping to several ACE exam objectives, particularly around setting up the cloud environment and understanding core GCP tenets.

  **Focus Objectives:

  - **Section 1: Setting up a cloud solution environment:**
    - 1.1 Setting up cloud projects and accounts. (Understanding resource hierarchy: Organization, Folders, Projects)
    - 1.2 Managing billing configuration. (Concepts like Billing Accounts, Budgets, Alerts)
    - 1.3 Installing and configuring the command line interface (CLI), specifically Cloud SDK (gcloud, gsutil, bq).
  - **Section 2: Planning and configuring a cloud solution:**
    - 2.1 Planning and estimating GCP product use using the Pricing Calculator. (Basic understanding of pricing models for core services)
    - 2.2 Planning and configuring compute resources. (Introduction to Compute Engine, VM concepts, basic instance types)
    - 2.3 Planning and configuring data storage options. (Introduction to Cloud Storage, basic bucket concepts)
    - 2.4 Planning and configuring network resources. (Introduction to VPC concepts, subnets, firewall rules - high level)
  - **Section 3: Deploying and implementing a cloud solution:**
    - 3.1 Deploying and implementing Compute Engine resources. (Creating basic VMs via Console/gcloud)
  - **Section 4: Ensuring successful operation of a cloud solution:**
    - 4.1 Managing Compute Engine resources. (Start/stop VMs, SSH access)
  - **Section 5: Configuring access and security:**
    - 5.1 Managing Identity and Access Management (IAM). (Basic concepts: Roles - Primitive & Predefined, Members)
    - 5.2 Managing service accounts. (Concept of service accounts)
- **Content Covered (High-Level Slides):**
  - Introduction to Google Cloud: Regions, Zones, Global Infrastructure
  - Google Cloud Resource Hierarchy: Organization, Folders, Projects
  - Interacting with Google Cloud: Console, Cloud Shell, Cloud SDK (gcloud, gsutil)
  - Billing: Billing Accounts, Budgets, Alerts, Pricing Calculator Overview
  - Compute Engine Fundamentals: VM Instances, Machine Types, Disks (Persistent), Images, Snapshots
  - Cloud Storage Fundamentals: Buckets, Object Lifecycle Management, Storage Classes
  - VPC Networking Fundamentals: VPC Networks, Subnets, Firewall Rules (basic concepts)
  - Identity and Access Management (IAM): Roles (Primitive, Predefined), Members (Users, Groups, Service Accounts)
- **Key Talking Points:**
  - Emphasize the hierarchical structure (Org -> Folders -> Projects) and its importance for resource management and access control.
  - Highlight the different ways to interact with GCP and when to use each (Console for exploration, SDK for automation/scripting, Shell for quick tasks).
  - Explain the difference between Regions and Zones and their impact on availability and latency.
  - Introduce the core compute, storage, and networking services at a high level.
  - Explain the fundamental principle of IAM: "Who can do what on which resource?"
  - Stress the importance of understanding billing and setting up alerts.
  - Connect these foundational concepts to the ACE exam objectives listed above.
- **Q&A Session (Example Questions):**

  1. **Question:** Your team needs to ensure that developers only have permissions to manage Compute Engine instances within a specific development project, but cannot modify IAM policies for that project. Which IAM role is most appropriate?
      - A. roles/compute.admin
      - B. roles/compute.instanceAdmin.v1
      - C. roles/editor
      - D. roles/owner
      - **Answer:** B. roles/compute.instanceAdmin.v1
      - **Rationale:** `compute.admin` includes IAM permissions. `editor` and `owner` are too broad (primitive roles) and grant wider permissions across services, including potentially modifying IAM. `compute.instanceAdmin.v1` grants comprehensive control over Compute Engine instances *without* granting IAM policy modification rights within Compute Engine, aligning with the principle of least privilege.[14]

  2. **Question:** You need to store large, infrequently accessed data backups in Google Cloud at the lowest possible cost. High availability is required, but immediate retrieval isn't necessary (retrieval time of hours is acceptable). Which Cloud Storage class should you use?
      - A. Standard Storage
      - B. Nearline Storage
      - C. Coldline Storage
      - D. Archive Storage
      - **Answer:** D. Archive Storage
      - **Rationale:** Archive Storage offers the lowest storage cost for data accessed less than once a year and tolerates retrieval times measured in hours [15]. Standard is for frequently accessed data. Nearline (monthly access) and Coldline (quarterly access) have higher storage costs than Archive and faster retrieval times than needed here [15].

  3. **Question:** You are setting up a new Google Cloud project for a production application. Where should you ideally create this project within your organization's resource hierarchy?
      - A. Directly under the Organization node.
      - B. Inside a dedicated "Production" Folder.
      - C. Inside your personal user Folder.
      - D. It doesn't matter where the project is created.
      - **Answer:** B. Inside a dedicated "Production" Folder.
      - **Rationale:** Using Folders allows for logical grouping of projects (e.g., by environment like Production, Development, or by team). This facilitates applying policies (IAM, Organization Policies) at the Folder level, improving management and governance [16]. Creating projects directly under the Organization is possible but less organized for larger setups. Personal folders are inappropriate for production applications.
