# Managing Compute Engine Instances: Persistent Disk Snapshots - Creation & Management

* **What are Snapshots?** Point-in-time backups of Persistent Disks.
* **How they Work:**
  * Stored as differential files in Cloud Storage (you don't directly interact with the GCS objects).
  * **First snapshot** is a full copy of all disk data.
  * **Subsequent snapshots** are incremental, only storing new or changed blocks since the previous snapshot. This saves storage space and creation time.
  * When you delete a snapshot, its unique data is merged into the next snapshot if needed, ensuring restorability.
* **Creating Snapshots:**
  * Via Cloud Console: Compute Engine > Snapshots > Create Snapshot.
  * Via `gcloud`: `gcloud compute snapshots create my-snapshot --source-disk=my-disk --source-disk-zone=us-central1-a`
  * **Scheduled Snapshots:** Automate snapshot creation (hourly, daily, weekly) with retention policies. Highly recommended for regular backups.
* **Snapshot Consistency:**
  * **Crash-consistent:** Default if disk is attached and running. Captures data as if the system crashed. Most apps can recover.
  * **Application-consistent:** Requires quiescing I/O or using VSS (Windows) to ensure applications have flushed data to disk. More reliable for databases.
* **Managing Snapshots:**
  * Listing, describing, deleting snapshots.
  * Applying labels for organization.
  * Snapshot storage location (Regional or Multi-regional, chosen at creation).
* **Cost of Snapshots:** Billed based on the total size of the compressed snapshot data.

---

# Managing Compute Engine Instances: Restoring from Snapshots & Use Cases

* **Restoring from a Snapshot:**
  * You create a **new Persistent Disk** from a snapshot. You cannot "mount" a snapshot directly or restore "in-place" to an existing disk.
  * The new disk can be the same size or larger than the original snapshot's source disk.
  * Can be created in any zone within the snapshot's location (if regional snapshot) or any region (if multi-regional snapshot, though this may incur network costs).
  * Attach the new disk to an existing VM or a new VM.
* **Common Use Cases for Snapshots:**
  * **Data Backup & Disaster Recovery (DR):** Regularly snapshot critical disks. If data loss or corruption occurs, create a new disk from the latest good snapshot.
  * **Data Migration / Cloning:**
    * Create a snapshot of a disk in one zone/region.
    * Create a new disk from that snapshot in a different zone/region.
    * Attach to a VM in the new location.
  * **Cloning Environments:** Snapshot a configured VM's boot disk (and data disks), then create new disks/VMs from these snapshots to replicate dev/test/staging environments.
  * **Testing Upgrades/Changes:** Snapshot a disk before making significant changes (e.g., OS upgrade, major software deployment). If issues arise, revert by creating a disk from the snapshot.
  * **Forensics/Auditing:** Create a snapshot of a disk for offline analysis without impacting the running instance.

---

# Managing Compute Engine Instances: Custom Images - Creation & Sharing

* **What are Custom Images?** Bootable disk images that you create and manage. They contain your customized operating system, installed software, and configurations.
* **Sources for Custom Images:**
  * From an existing Persistent Disk (boot disk or data disk).
  * From a Persistent Disk snapshot.
  * From another existing image (public or custom).
  * By importing virtual disk files (e.g., VMDK, VHD) from other environments.
* **Image Families:**
  * A way to group related versions of an image.
  * Allows you to refer to the latest version of an image in a family by using the family name (e.g., `my-app-family` would point to `my-app-v3` if that's the latest).
  * Simplifies updating instance templates or scripts to use the newest image.
* **Creating Images:**
  * Via Cloud Console: Compute Engine > Images > Create Image.
  * Via `gcloud`: `gcloud compute images create my-custom-image --source-disk=my-instance-boot-disk --source-disk-zone=us-central1-a --family=my-app-family`
* **Sharing Images:**
  * **Within a project:** Images are project-specific by default.
  * **Across projects:** Grant IAM roles (`roles/compute.imageUser`) to users, groups, or service accounts from other projects to allow them to use your custom image.
  * **Publicly:** Possible, but generally not recommended unless you intend for wide distribution.
* **Deprecating Images:** Mark images as `DEPRECATED` to discourage new use, `OBSOLETE` to prevent new use but allow existing users, or `DELETED`.

---

# Managing Compute Engine Instances: Images vs. Snapshots vs. Templates

| Feature            | Custom Image                                  | Persistent Disk Snapshot                      | Instance Template                             |
| :----------------- | :-------------------------------------------- | :-------------------------------------------- | :-------------------------------------------- |
| **Primary Purpose** | Create new, bootable VMs with custom OS/software | Backup & Disaster Recovery for disks          | Define & quickly deploy full VM configurations |
| **Content** | Full OS, applications, configurations         | Block-level copy of a disk's data             | VM specification (machine type, image, disks, network, metadata, etc.) |
| **Source** | Disk, Snapshot, Image, Virtual Disk File      | Persistent Disk                               | Manual Configuration / Existing VM            |
| **Output** | A bootable image resource                     | A snapshot resource                           | A template resource                           |
| **Mutability** | Immutable (create new versions)               | Immutable (create new snapshots)              | Immutable (create new versions)               |
| **Use to Create** | New Persistent Disks (for booting new VMs)    | New Persistent Disks (for restore/cloning)    | New VM Instances                              |
| **Scope** | Global or Regional                            | Regional or Multi-regional                    | Global                                        |
| **Key Benefit** | Standardization of VM builds                  | Data protection & recovery                    | Automation, consistency, MIGs                 |

**How they relate:**
* You can create a **Custom Image** *from* a **Snapshot** (or a disk).
* An **Instance Template** typically references a **Custom Image** (or public image) for its boot disk.
* You would take **Snapshots** of the disks of VMs created *from* an **Instance Template** (which used an **Image**).
