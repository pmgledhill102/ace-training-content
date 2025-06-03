# Deployment & Automation: Cloud Marketplace - Benefits & Deployment Process

* **What is Cloud Marketplace?** A curated catalog of software solutions, datasets, and services from Google Cloud, open-source projects, and third-party vendors that are optimized to run on GCP.
* **Discovering Solutions:**
  * Search by keyword, category (e.g., OS, Database, Security, DevOps, ML), or vendor.
  * Filter by pricing model, VM type, etc.
* **Solution Types:**
  * **Virtual Machines:** Pre-configured Compute Engine VMs with software installed (e.g., WordPress, LAMP stack, Jenkins, specific databases).
  * **Kubernetes Applications:** Packaged applications ready to deploy on Google Kubernetes Engine (GKE).
  * **SaaS (Software as a Service):** Subscribe to third-party services directly through GCP billing.
  * **Datasets:** Public or commercial datasets ready for use with BigQuery or AI Platform.
* **Deployment Process (for VM-based solutions):**
    1.  Select the solution.
    2.  Review details (overview, pricing, documentation, support).
    3.  Click "Launch" or "Get Started."
    4.  Configure deployment options (e.g., VM name, zone, machine type, disk size, network settings, specific software settings).
    5.  Review estimated costs.
    6.  Deploy. The solution is automatically provisioned.
* **Managing Deployed Solutions:**
  * Typically managed like any other GCP resource (e.g., Compute Engine instances).
  * Updates and support are often provided by the solution vendor.
* **Benefits:**
  * **Speed & Convenience:** Quickly deploy common software without manual installation and configuration.
  * **Vetted Solutions:** Solutions are often tested and optimized for GCP.
  * **Simplified Billing:** Charges for third-party solutions can be consolidated on your GCP bill.
  * **Discoverability:** Easy way to find tools and services that integrate with GCP.

---

# Deployment & Automation: Infrastructure as Code (IaC) - Core Principles

* **What is IaC?** The practice of managing and provisioning computing infrastructure (networks, VMs, load balancers, databases, etc.) through machine-readable definition files (code), rather than through physical hardware configuration or interactive configuration tools (like the Cloud Console).
* **Declarative vs. Imperative Approach:**
  * **Declarative (e.g., Terraform, Deployment Manager):** You define the *desired state* of your infrastructure. The IaC tool figures out how to achieve that state (create, update, or delete resources). *This is the dominant approach.*
  * **Imperative (e.g., scripts using `gcloud`):** You define the *sequence of commands* to execute to reach the desired state.
* **Idempotency:** A key principle. Applying the same IaC configuration multiple times should result in the same infrastructure state, without unintended side effects. The IaC tool should only make changes if the current state differs from the desired state.
* **Core Benefits of IaC:**
  * **Automation:** Eliminates manual, error-prone processes.
  * **Consistency & Standardization:** Ensures environments are provisioned identically every time.
  * **Repeatability:** Easily recreate environments (dev, test, prod) or specific resource sets.
  * **Version Control:** Infrastructure definitions can be stored in source control (like Git), providing history, collaboration, and rollback capabilities.
  * **Auditability:** Changes to infrastructure are tracked through code changes.
  * **Disaster Recovery:** Quickly rebuild infrastructure in a new region from code.
  * **Collaboration:** Teams can work together on infrastructure definitions.
  * **Cost Savings:** Reduces manual effort and can help optimize resource usage through standardized configurations.
* **Relation to DevOps:** IaC is a fundamental practice in DevOps, enabling CI/CD for infrastructure.

---

# Deployment & Automation: Cloud Deployment Manager (CDM) - Overview

* **What is it?** Google Cloud's native Infrastructure as Code service.
* **Key Components:**
  * **Configurations (`.yaml` files):** Declarative files written in YAML that describe the GCP resources you want to create and their properties.
  * **Templates (Jinja2 or Python):** Reusable building blocks that can define parts of your infrastructure. Configurations can import and use templates to create more complex and modular deployments.
    * Jinja2 (`.jinja`): A popular templating engine, allows for loops, conditionals, variables.
    * Python (`.py`): Allows for more complex logic and generation of resource definitions.
  * **Deployments:** An instantiation of a configuration. Represents the collection of resources created by CDM.
* **Basic CDM Workflow & Commands (`gcloud`):**
  * `gcloud deployment-manager deployments create my-deployment --config=my-config.yaml`
  * `gcloud deployment-manager deployments update my-deployment --config=my-config.yaml`
  * `gcloud deployment-manager deployments describe my-deployment`
  * `gcloud deployment-manager deployments delete my-deployment`
  * `gcloud deployment-manager deployments list`
* **State Management:** Google Cloud manages the state of your deployments automatically. It knows what resources it has created and can determine what changes are needed when you update a configuration.
* **Preview Mode:** You can preview the changes CDM will make before applying them (`--preview` flag).
* **Pros:**
  * **Native GCP Integration:** Deeply integrated with GCP services and IAM.
  * No external tools or state files to manage locally (GCP handles state).
  * Supports all GCP resources that have an API.
* **Cons:**
  * **GCP-Specific:** Only works for provisioning Google Cloud resources. Not suitable for multi-cloud IaC.
  * YAML can become verbose for very large configurations if not well-modularized with templates.
  * Community and ecosystem are smaller compared to tools like Terraform.

---

# Deployment & Automation: Terraform - Overview (Preview for Week 6)

* **What is it?** A popular open-source Infrastructure as Code tool created by HashiCorp.
* **Key Characteristics:**
  * **Multi-Cloud:** Supports numerous cloud providers (GCP, AWS, Azure, etc.) as well as other services (e.g., Kubernetes, Datadog) through **Providers**.
  * **Declarative Language:** Uses HashiCorp Configuration Language (HCL), which is designed to be human-readable and machine-friendly. Files typically end in `.tf`.
  * **Providers:** Plugins that interface with the APIs of specific services. The **Google Cloud Provider** allows Terraform to manage GCP resources.
* **Core Terraform Workflow:**
    1.  **`terraform init`:** Initializes the working directory, downloads provider plugins, and sets up the backend for state storage.
    2.  **`terraform plan`:** Creates an execution plan. Terraform compares the desired state (in your `.tf` files) with the current state (in the state file or actual infrastructure) and shows what changes it will make (create, update, delete).
    3.  **`terraform apply`:** Applies the changes described in the plan to reach the desired state. Prompts for confirmation by default.
    4.  **`terraform destroy`:** Removes all resources managed by the current configuration.
* **State File Management:**
  * Terraform uses a **state file** (e.g., `terraform.tfstate`) to keep track of the resources it manages and their current configuration.
  * **Local State (Default):** State file stored locally. Not suitable for teams.
  * **Remote Backends (Recommended):** Store the state file remotely (e.g., in a Google Cloud Storage bucket). This enables collaboration, locking (to prevent concurrent modifications), and versioning.
* **Pros:**
  * **Multi-Cloud Capability:** Manage infrastructure across different cloud providers with a consistent workflow.
  * **Large Community & Ecosystem:** Extensive documentation, modules, and community support.
  * Mature and widely adopted.
  * Modular (Terraform Modules allow reusable infrastructure components).
* **Cons:**
  * External tool; not native to GCP (though the Google provider is well-maintained by Google).
  * You are responsible for managing the state file securely and correctly, especially when using remote backends.
  * Learning curve for HCL and Terraform concepts.
