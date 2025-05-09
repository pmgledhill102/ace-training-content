# Compute Engine - Deep Dive

---

# New instance - Original

```sh
gcloud beta compute instances create cs2server \
    --project=play-pen-pup \
    --zone=europe-west2-a \
    --machine-type=c2d-highcpu-2 \
    --network-interface=network-tier=PREMIUM,stack-type=IPV4_ONLY,subnet=default \
    --no-restart-on-failure \
    --max-run-duration=604800s \
    --maintenance-policy=TERMINATE \
    --instance-termination-action=STOP \
    --service-account=728241575576-compute@developer.gserviceaccount.com \
    --scopes=https://www.googleapis.com/auth/devstorage.read_only,https://www.googleapis.com/auth/logging.write,https://www.googleapis.com/auth/monitoring.write,https://www.googleapis.com/auth/servicecontrol,https://www.googleapis.com/auth/service.management.readonly,https://www.googleapis.com/auth/trace.append \
    --tags=game-server \
    --create-disk=auto-delete=yes,boot=yes,device-name=instance-1,image=projects/ubuntu-os-pro-cloud/global/images/ubuntu-minimal-pro-2404-noble-amd64-v20250502,mode=rw,size=75,type=pd-balanced \
    --no-shielded-secure-boot \
    --shielded-vtpm \
    --shielded-integrity-monitoring \
    --labels=goog-ec-src=vm_add-gcloud \
    --reservation-affinity=any
```

---

# New instance - Create Template

```sh
gcloud compute instance-templates create cs2-template \
    --project=play-pen-pup \
    --region=europe-west2 \
    --machine-type=c2d-highcpu-2 \
    --network-interface=network-tier=PREMIUM,stack-type=IPV4_ONLY,subnet=default \
    --no-restart-on-failure \
    --max-run-duration=604800s \
    --maintenance-policy=TERMINATE \
    --instance-termination-action=STOP \
    --service-account=728241575576-compute@developer.gserviceaccount.com \
    --scopes=https://www.googleapis.com/auth/devstorage.read_only,https://www.googleapis.com/auth/logging.write,https://www.googleapis.com/auth/monitoring.write,https://www.googleapis.com/auth/servicecontrol,https://www.googleapis.com/auth/service.management.readonly,https://www.googleapis.com/auth/trace.append \
    --tags=game-server \
    --create-disk=auto-delete=yes,boot=yes,device-name=instance-1,image=projects/ubuntu-os-pro-cloud/global/images/ubuntu-minimal-pro-2404-noble-amd64-v20250502,mode=rw,size=75,type=pd-balanced \
    --no-shielded-secure-boot \
    --shielded-vtpm \
    --shielded-integrity-monitoring \
    --labels=goog-ec-src=vm_add-gcloud \
    --reservation-affinity=any
```

---

# New instance - Using Template

```sh
gcloud compute instances create cs2-instance \
    --project=play-pen-pup \
    --zone=europe-west2-a \
    --source-instance-template=cs2-template
```

---

# Instance Templates - Overview

* **What are they?** Reusable definitions for VM instances. Think of them as a blueprint.
* **Key Components Defined in a Template:**
  * Machine type (e.g., `e2-medium`, custom types)
  * Boot disk image (Public, Custom, Snapshot) and disk size/type
  * Additional data disks
  * Network interfaces (VPC, Subnet, IP settings)
  * Network tags
  * Labels
  * Service account & access scopes
  * Metadata (including startup and shutdown scripts)

---

# Instance Templates - Benefits

* **Benefits:**
  * **Consistency:** Ensures all VMs created from the template are identical.
  * **Speed:** Rapidly deploy new instances without re-specifying configurations.
  * **Automation:** Essential for Managed Instance Groups (MIGs) for autoscaling and self-healing.
  * **Versioning (Implicit):** Update a configuration by creating a new template; old templates can be retained.
* **Creating a Template:**
  * Via Cloud Console: Navigate to Compute Engine > Instance Templates > Create Instance Template.
  * Via `gcloud`: `gcloud compute instance-templates create my-template --machine-type=e2-medium ...`

---

# Custom Machine Types - Deep Dive

* **Supported Series:** E2, N1, N2, N2D series allow for custom configurations.
* **Flexibility:** Define specific vCPU counts and memory sizes.
  * **vCPUs:** Must be an even number (unless 1 vCPU). Specific ranges apply per series.
  * **Memory:** Typically between 0.9 GB and 6.5 GB per vCPU for N1, or up to 8GB per vCPU for N2/N2D/E2 (check current docs for exact limits and increments).
  * **Extended Memory:** Some series allow memory beyond the standard per-vCPU limits for an additional cost.
* **When to Use:**
  * **Right-sizing:** Precisely match VM resources to workload demands, avoiding over-provisioning.
  * **Cost Optimization:** Pay only for the resources you configure, potentially saving money compared to the next largest predefined type.
  * Workloads with specific needs (e.g., high memory with low CPU, or vice-versa, within limits).
* **Comparison:**
  * **Predefined:** Simpler to choose, optimized for common workloads.
  * **Custom:** Maximum flexibility, best for cost/resource optimization when predefined don't fit.
* **Creating a Custom VM (Example):**
  * Console: During VM creation, select series (e.g., E2) and choose "Custom" for Cores and Memory.
  * `gcloud`: `gcloud compute instances create my-custom-vm --custom-cpu=6 --custom-memory=20GB ...`

---

# Spot VMs - Characteristics & Use Cases

* **What are they?** VMs that use spare Google Cloud capacity at significantly lower prices (60-91% off on-demand).
* **Key Characteristic: Preemption**
  * Compute Engine can reclaim (preempt) Spot VMs at any time if resources are needed elsewhere.
  * A **30-second warning** is given via ACPI G2 Soft Off signal or metadata server before preemption.
* **Pricing:** Variable, changes based on supply and demand. You can set a maximum price you're willing to pay (optional).
* **Runtime:** **No maximum runtime limit** (unlike legacy Preemptible VMs which had a 24-hour limit). They run until preempted or manually stopped.
* **Best Practices for Using Spot VMs:**
  * Workloads must be **fault-tolerant** and able to handle preemption gracefully.
  * Ideal for **stateless applications** or those that can checkpoint progress.
  * Use startup/shutdown scripts to save state or clean up.
* **Common Use Cases:**
  * Batch processing jobs (e.g., data analysis, rendering, transcoding).
  * High-performance computing (HPC) simulations.
  * Continuous Integration/Continuous Deployment (CI/CD) agents.
  * Stateless web servers (often in Managed Instance Groups with Spot VMs).
  * Cost-sensitive development and test environments.
* **Not Suitable For:** Critical, stateful applications requiring guaranteed uptime (e.g., primary databases, interactive user-facing apps sensitive to interruption).

---

# GPUs & Accelerator-Optimized VMs

* **What are GPUs?** Graphics Processing Units, specialized hardware for parallel processing.
* **Attaching GPUs:**
  * Can be attached to general-purpose N1 machine types or specialized Accelerator-Optimized A2 or G2 machine types.
  * Specific GPU types (e.g., NVIDIA Tesla T4, V100, A100, L4) are available, varying by region and zone.
  * Number of GPUs per VM is limited based on machine type.
* **Driver Installation:** You are responsible for installing the appropriate NVIDIA drivers on the VM. Google provides resources and some images may come with drivers pre-installed.
* **Use Cases:**
  * **Machine Learning (ML):** Training complex models, running ML inference.
  * **High-Performance Computing (HPC):** Scientific simulations, financial modeling, genomics.
  * **Graphics Rendering & Virtual Workstations:** Video editing, 3D visualization, game development.
* **Pricing:** GPUs are billed per second while attached to a running VM, in addition to the VM cost. Spot VMs can also have GPUs attached for further cost savings.
* **Quotas:** GPU usage is subject to quotas, which may need to be increased.

---

# Sole-Tenant Nodes - Isolation & Compliance

* **What are they?** Physical Compute Engine servers dedicated solely to your project's VMs. Your VMs do not share host hardware with VMs from other customers.
* **Benefits:**
  * **Security & Isolation:** Addresses concerns about co-tenancy for highly sensitive workloads.
  * **Compliance:** Helps meet specific regulatory or licensing requirements that mandate dedicated hardware (e.g., certain software licenses that are per-core or per-processor, Bring Your Own License - BYOL for Windows Server).
  * **Performance Predictability:** Can reduce performance variability sometimes caused by "noisy neighbors" on multi-tenant hardware (though GCP's standard multi-tenant environment is highly optimized).
* **Node Groups:** Sole-tenant nodes are provisioned in groups. You specify the node type (defines CPU, memory, local SSDs of the host).
* **VM Placement:** You then schedule your VMs to run on these dedicated nodes.
  * **Affinity/Anti-Affinity:** Policies can be set to control how VMs are placed on nodes within a group (e.g., pack VMs together or spread them out).
* **Cost Implications:** Sole-tenant nodes are a premium service and incur costs for the entire dedicated host, regardless of how many VMs are running on it. Sustained Use Discounts still apply.
* **When to Consider:**
  * Strict compliance or security mandates requiring physical isolation.
  * Specific third-party software licensing terms (BYOL scenarios).
  * Workloads highly sensitive to potential performance variations from multi-tenancy.

---

# Instance Metadata & Scripts - In-Depth

* **Metadata Server:** A special server accessible from within each VM at a well-known IP address (`169.254.169.254` or `metadata.google.internal`).
  * Endpoint: `http://metadata.google.internal/computeMetadata/v1/` (requires `Metadata-Flavor: Google` header).
  * Provides access to instance-specific information.
* **Common Metadata Keys (Examples):**
  * `instance/id`: Unique ID of the instance.
  * `instance/zone`: Full zone path (e.g., `projects/PROJECT_ID/zones/ZONE`).
  * `project/project-id`: The project ID.
  * `instance/network-interfaces/0/ip`: Internal IP of the first network interface.
  * `instance/network-interfaces/0/access-configs/0/external-ip`: External IP.
  * `instance/attributes/`: Custom metadata key-value pairs.
* **Custom Metadata:**
  * Key-value pairs you define when creating an instance or template.
  * Used to pass configuration data, license keys, role information, etc., to instances.
* **Startup Scripts (`startup-script` metadata key):**
  * Executed automatically when an instance boots up.
  * Can be run on first boot only or every boot (OS dependent, behavior can be configured).
  * Languages: Shell scripts (Linux), PowerShell/batch (Windows).
  * Best Practices: Make scripts idempotent (safe to run multiple times), log output, handle errors gracefully.
  * Use for: Installing software, configuring services, downloading application code.
* **Shutdown Scripts (`shutdown-script` metadata key):**
  * Executed when an instance is gracefully shut down (e.g., via API call, not preemption).
  * Best-effort execution: Given a limited time window (default 90s) to complete.
  * Use for: Graceful application shutdown, saving state, uploading logs, de-registering from services.

---

# Managing Compute Engine Instances: Resizing VMs - Process & Considerations

* **What is Resizing?** Changing the machine type (vCPUs, memory, series) of an existing VM.
* **Process:**
    1.  **Stop** the VM instance. Resizing cannot be done on a running instance.
    2.  **Edit** the instance configuration:
    * Select a new predefined machine type or define a new custom machine type.
    * The boot disk and any attached persistent disks remain.
    3.  **Start** the VM instance.
* **Impact on IP Addresses:**
  * **Internal IP:** Remains the same (as it's tied to the subnet and instance resource, not the machine type).
  * **External IP (Ephemeral):** If the VM has an ephemeral external IP, it *may change* when the instance is stopped and started.
  * **External IP (Static):** If a static external IP is assigned, it will be reattached.
* **OS & Application Considerations:**
  * The OS should generally adapt to new CPU/memory.
  * Ensure applications are configured to utilize available resources or can handle changes.
  * If boot disk was sized for a smaller machine, ensure sufficient space for OS/apps on the new, larger configuration.
* **When to Resize:**
  * **Scale Up:** Workload demands more CPU/memory for better performance.
  * **Scale Down:** Workload is over-provisioned; reduce resources to save costs.
  * Change machine series (e.g., N1 to E2 for cost, or to C2 for compute optimization).
