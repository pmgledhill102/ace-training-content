# Cloud Storage (GCS): Scalable Object Storage

* **What:** Google's managed, highly durable, and available **Object Storage** service.
* **Object vs. Block:**
    * Stores data as immutable **Objects** (files) in containers called **Buckets**.
    * Different from **Block Storage** (like Persistent Disks) which presents storage as volumes mountable by an OS.
* **Use Cases:**
    * Website content (static files, images, videos).
    * Backups & Archives.
    * Data Lakes for Big Data analytics.
    * Storing user-generated content.
    * Application assets & binaries.
* **Access:** Via APIs, Client Libraries, `gsutil` CLI, Cloud Console, Cloud Storage FUSE.

---

# GCS Buckets: Global Containers

* **Buckets:** Basic containers holding your Cloud Storage objects.
* **Global Namespace:** Bucket names must be **globally unique** across all of Google Cloud.
    * Example: `my-unique-project-assets-123abc`
    * Naming rules similar to DNS (lowercase, numbers, hyphens, dots).
    * Common creation failure reason: Name already taken.
* **Location Types:** Defines where bucket data is physically stored (impacts latency, availability, cost):
    * **Region:** Data stored redundantly across zones *within* a single region (e.g., `us-central1`). Lowest latency for users/apps in that region.
    * **Dual-region:** Data stored redundantly across zones in *two specific regions* (e.g., `nam4` covering `us-central1` & `us-east1`). High availability & low latency across those two regions.
    * **Multi-region:** Data stored redundantly across zones in *multiple regions* within a large geographic area (e.g., `US`, `EU`, `ASIA`). Highest availability & lowest latency over a wide area.

---

# GCS Objects: Immutable Data Units

* **Objects:** The individual pieces of data (files) stored within a bucket.
* **Structure:** Consists of:
    * **Object data:** The actual bytes of the file.
    * **Object metadata:** Key-value pairs describing the object (e.g., `Content-Type`, `Cache-Control`, custom metadata).
* **Naming:** Object names must be unique *within* a given bucket.
    * Can include `/` characters to simulate directory structures (e.g., `images/logos/logo.png`), but it's still a flat namespace.
* **Immutability:** Objects are immutable – you cannot perform partial updates. To change an object, you must overwrite it entirely (uploading a new version). Versioning can be enabled on buckets to keep old versions.

---

# GCS Storage Classes: Optimize Cost & Access

* Assign a storage class to buckets or objects based on access frequency & retrieval needs.
* **Classes:**
    * **Standard:** Frequent access ("hot" data), lowest access cost, highest storage cost. (Default)
        * *Use Case:* Website content, active data processing.
    * **Nearline:** Infrequent access (~monthly), low storage cost, small retrieval cost, **30-day min duration**.
        * *Use Case:* Backups, data accessed occasionally.
    * **Coldline:** Very infrequent access (~quarterly), lower storage cost, higher retrieval cost, **90-day min duration**.
        * *Use Case:* Archival needing relatively quick (hours) retrieval, disaster recovery.
    * **Archive:** Extremely infrequent access (~yearly), lowest storage cost, highest retrieval cost, **365-day min duration**.
        * *Use Case:* Long-term preservation, compliance archives, data rarely needed.
* **Trade-off:** Lower storage cost classes have higher access/retrieval costs and minimum storage durations.

---

# GCS Lifecycle Management: Automate Data Handling

* **What:** Rules configured on a bucket to automatically take actions on objects based on conditions (primarily age, but also version count, creation date, storage class, etc.).
* **Actions:**
    * **SetStorageClass:** Transition objects to a cheaper storage class (e.g., Standard -> Nearline -> Coldline -> Archive).
    * **Delete:** Permanently delete objects.
    * **AbortIncompleteMultipartUpload:** Clean up unfinished uploads.
* **Common Use Cases:**
    * **Cost Optimization:** Automatically move older data to colder, cheaper storage classes.
    * **Compliance:** Automatically delete data after a required retention period expires (e.g., delete logs older than 7 years).
    * **Cleanup:** Remove old noncurrent object versions if versioning is enabled.
* **Benefit:** Reduces manual effort and ensures consistent application of data retention and cost-saving policies.
