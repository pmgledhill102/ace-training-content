---
class: gslides-content-slide
---

# Core Compute: Compute Engine

---

# Compute Engine (GCE): Virtual Machines on Demand

* **What:** Google's Infrastructure as a Service (IaaS) offering.
* **Provides:** Scalable, high-performance Virtual Machines (VMs), called **Instances**.
* **Control:** You manage the OS, libraries, applications, and configurations.
* **Use Cases:**
    * Migrating existing applications ("lift-and-shift").
    * Hosting custom software & web servers.
    * Running backend services & batch processing.
    * Foundation for other services (like GKE nodes).
* **Key Components:** Machine Type, Boot Disk, Network Interface, Optional Storage (Persistent Disks / Local SSDs).

---

# GCE Machine Types: Finding the Right Fit

* Defines the virtual hardware allocated to your instance (vCPUs, Memory, Network Bandwidth).
* **Predefined Machine Types:** Optimized for specific workloads:
    * `E2`, `N2`, `N2D`, `N1`: **General-purpose**
    * `C2`, `C2D`: **Compute-optimized**
    * `M1`, `M2`, `M3`: **Memory-optimized**
    * `A2`, `G2`: **Accelerator-optimized**
* **Custom Machine Types (N1, N2, N2D, E2):**
    * Flexibility to define specific vCPU counts and memory sizes.
    * Pay only for the resources you configure.
    * Ideal for workloads that don't fit predefined shapes.

---

# Example Workload Costs

| Instance Type | vCPU | Memory | Network | OnDemand | Spot | Time Trial | Real  |
|---------------|------|--------|---------|----------|------|------------|-------|
| n2-highcpu-2  | 2    | 2      | 10      | 67.44    |  9.15 | 14m 44s   | 12716 |
| n2d-highcpu-2 | 2    | 2      | 10      | 58.67    | 11.18 | 12m 15s   | 8436  |
| n2-standard-2 | 2    | 8      | 10      | 91.34    | 12.38 | 14m 7s    | 12663 |
| c2d-highcpu-2 | 2    | 4      | 32      | 70.50    | 12.83 | 11m 10s   | 8537  |
| c2d-standard-2| 2    | 8      | 32      | 85.40    | 15.53 | 11m 0s    | 8463  |
| e2-medium     | 2    | 4      | 2       | 31.51    |  9.45 | 23m 55s   | 13560 |

---

# GCE Pricing Models: Optimizing Costs

* **On-Demand Pricing:** Standard pay-as-you-go pricing (per second, 1-min minimum). No commitment
* **Sustained Use Discounts (SUDs):**
    * *Automatic* discounts applied for running instances for significant portions of the billing month.
    * Applies to N1, N2, N2D, E2, C2 machine types.
    * The longer an instance runs, the greater the discount (up to 30% for N1/N2/N2D).
* **Committed Use Discounts (CUDs):**
    * Purchase 1 or 3 year commitments for specific resource usage (vCPU, memory, GPUs) (57%+ discount)
* **Spot VMs (Current Offering):**
    * Offer the largest discounts (60-91% off on-demand) by using spare Google Cloud capacity.
    * Pricing is variable based on supply and demand.
    * *Can be terminated (preempted) by Compute Engine* with 30s notice if resources needed elsewhere.
    * **No maximum runtime limit.**
    * Ideal for fault-tolerant, stateless batch jobs, short-lived workloads, or dev/test environments. *Not* suitable for critical, long-running applications needing guaranteed uptime.

---

# Preemptible VMs (Legacy)

* **Preemptible VMs (Legacy):**
    * Older version, largely superseded by Spot VMs.
    * Similar concept (using spare capacity) but had a **fixed maximum runtime of 24 hours**.
    * Offered significant savings (up to 80%).
    * Also preempted with 30 seconds notice.
---

# Persistent Disks (PD): Durable Block Storage

* **What:** Network-attached, durable block storage for GCE instances.
* **Durability:** Data persists even if the VM is stopped or terminated. Zonal or Regional options available.
* **Types (Balance Performance & Cost):**
    * `pd-standard` (HDD): Lowest cost, throughput-optimized (logs, big data).
    * `pd-balanced` (SSD): Good balance of cost & IOPS (general purpose, web servers, small DBs).
    * `pd-ssd` (SSD): High IOPS/low latency (databases, performance-sensitive apps).
    * `pd-extreme` (SSD): Highest IOPS/throughput for specific machine types (extreme DB workloads).
* **Use Cases:** Boot disks, application data, database storage, file shares (when mounted).
* **Contrast:** *Local SSDs* offer highest performance but are **ephemeral** (data lost on stop/termination). Use for cache/scratch only.

---

# Persistent Disk Snapshots: Backup & Recovery

* **What:** Point-in-time backups of Persistent Disks.
* **Purpose:**
    * Disaster Recovery: Restore disk state after failure or corruption.
    * Data Migration: Create new disks (potentially in different zones/regions) from a snapshot.
    * Cloning: Create identical disks for new VMs.
* **Characteristics:**
    * Stored independently from the source disk (often in Cloud Storage).
    * Differential: First snapshot is full, subsequent ones only store changed blocks (saves space & time).
    * Can be created while the disk is attached and running (though quiescing I/O is recommended for consistency).
* **Restoration:** Create a *new* Persistent Disk from a snapshot. You cannot directly "mount" a snapshot.
