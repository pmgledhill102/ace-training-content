# Cloud Storage: Bucket Naming, Locations, and Dual-Region Details

* **Bucket Naming (Recap):**
  * Must be **globally unique** across all of Google Cloud.
  * Adhere to DNS naming conventions (lowercase, numbers, hyphens, dots; no leading/trailing dots/hyphens).
  * Example: `my-company-app-prod-backups-unique123`
* **Bucket Locations - Impact on Latency, Availability, Cost:**
  * **Region:**
    * Data stored redundantly across zones *within* that single region (e.g., `us-central1`).
    * Lowest latency for users/apps *in or near* that region.
    * SLA: Typically 99.9% or 99.95% (check current docs).
    * Cost: Baseline storage cost for that region.
  * **Dual-region:**
    * Data stored redundantly across zones in *two specific, pre-defined regions* (e.g., `nam4` which is `us-central1` and `us-east1`).
    * Provides low latency access from both regions and high availability if one region becomes unavailable.
    * SLA: Typically 99.95% or 99.99%.
    * Cost: Higher than single-region storage, but lower than multi-region for the same data.
  * **Multi-region:**
    * Data stored redundantly across zones in *multiple regions* within a large geographic area (e.g., `US`, `EU`, `ASIA`).
    * Highest availability against regional outages. Lowest latency for users spread across that continent/area.
    * SLA: Typically 99.99% or 99.995%.
    * Cost: Highest storage cost per GB.
* **Choosing a Location:**
  * Consider where your users/applications are.
  * What are your availability requirements (protection against zonal vs. regional outage)?
  * What is your budget?
  * Data sovereignty or regulatory requirements might dictate location.

---

# Cloud Storage: Signed URLs - Generation & Security

* **What are Signed URLs?** Time-limited URLs that provide temporary access to a specific Cloud Storage object, even if the object is private.
* **How they Work:**
  * An identity with appropriate permissions (e.g., a service account with `roles/storage.objectViewer` for downloads, or the `iam.serviceAccounts.signBlob` permission) generates a cryptographic signature for a specific object, HTTP method, and expiration time.
  * This signature is embedded in the URL.
  * When a user accesses the URL, Cloud Storage validates the signature and expiration.
* **Components of a Signed URL (Simplified):**
  * Bucket and Object path.
  * Expiration timestamp.
  * Signature.
  * Google Access ID (of the signer).
* **Supported HTTP Methods:** `GET` (download), `PUT` (upload), `DELETE`, `HEAD`.
* **Generation:**
  * Using client libraries (Python, Java, Node.js, etc.).
  * Using `gsutil signurl` command.
* **Security Considerations:**
  * **Short Expiration Times:** Grant access for the minimum necessary duration.
  * **HTTPS:** Always use HTTPS to protect the URL in transit.
  * **Protect the Signer:** The ability to generate signed URLs is powerful. Secure the service account keys or user credentials that have signing permissions.
  * **Principle of Least Privilege:** The signer should only have permissions for the intended action (e.g., read-only if generating download URLs).
* **Use Cases:**
  * Allowing users to download their private files (e.g., invoices, reports) from your application.
  * Enabling users to upload files directly to a private bucket from a web browser.
  * Securely delivering software updates or large media files.
  * Temporary access for external collaborators.

---

# Cloud Storage: Object Versioning

* **What is Object Versioning?** A bucket-level setting that, when enabled, keeps a history of object modifications. Each overwrite or deletion of an object creates a new version or a noncurrent (archived) version.
* **Enabling Versioning:**
  * Can be enabled on a bucket at any time. Cannot be disabled once enabled (but can be suspended).
  * `gsutil versioning set on gs://BUCKET_NAME`
* **How it Works:**
  * Each object version is identified by its name and a unique **generation number**.
  * The most recent version is the **live version**.
  * When you overwrite a live object: The existing live version becomes a **noncurrent version**, and the new content becomes the new live version.
  * When you delete a live object (without specifying a generation): The live version becomes a **noncurrent version** (it's not truly deleted from storage immediately).
  * To permanently delete an object, you must delete its specific generation or all its versions (or use lifecycle rules).
* **Accessing Versions:**
  * Live version: Accessed by default using the object name.
  * Noncurrent versions: Accessed by specifying the object name and its generation number.
* **Restoring Noncurrent Versions:**
  * Copy the noncurrent version to become the new live version.
  * Delete the current live version (which makes the latest noncurrent version effectively live if no other live version exists, though copying is more explicit).
* **Use Cases:**
  * **Protection against accidental deletion:** Easily recover deleted objects.
  * **Protection against accidental overwrites:** Revert to a previous version of an object.
  * **Audit trail / History:** Maintain a history of changes to objects.
* **Cost Implications:** Noncurrent versions incur storage costs at the same rate as live versions based on their storage class. Use Lifecycle Management to manage the retention of noncurrent versions.

---

# Cloud Storage: Uniform Bucket-Level Access (UBLA)

* **The Problem UBLA Solves:**
  * Historically, Cloud Storage supported two access control systems: IAM and ACLs (Access Control Lists).
  * ACLs can be applied to individual objects, leading to complex, fine-grained permissions that can be hard to manage and audit, potentially conflicting with bucket-level IAM.
  * This dual system can be confusing and lead to unintended access configurations.
* **What is Uniform Bucket-Level Access?**
  * A bucket-level setting that **disables ACLs** for all objects in the bucket.
  * When enabled, access control is solely managed by **IAM permissions** applied at the bucket or project/folder/org level.
  * This simplifies permission management and aligns Cloud Storage with how most other GCP services handle access control.
* **Benefits of UBLA:**
  * **Simplicity:** Only one system (IAM) to manage and understand.
  * **Consistency:** Access control works like other GCP services.
  * **Easier Auditing:** Simpler to determine who has access to what.
  * **Prevents ACL-based "surprises":** Eliminates the risk of an object ACL granting unintended access that bypasses IAM policies.
* **Enabling UBLA:**
  * Can be set during bucket creation or enabled on an existing bucket.
  * If enabling on an existing bucket with ACLs, review existing ACLs as they will no longer be effective. Google provides tools to analyze ACLs before migration.
* **Recommendation:**
  * **Enable Uniform Bucket-Level Access for all new buckets.**
  * Consider migrating existing buckets to UBLA for improved security posture and manageability.
  * Only use ACLs if you have a very specific legacy use case requiring per-object permissions that cannot be achieved with IAM conditions (which are becoming more powerful).

---

# Cloud Storage: Requester Pays

* **Standard Billing Model:** By default, the **bucket owner** pays for:
  * Storage costs of the data.
  * Network egress (data download) costs.
  * Operation costs (e.g., GET, PUT requests).
* **What is Requester Pays?**
  * A bucket-level setting that shifts the cost of **network egress and operation charges** to the **requester** of the data, rather than the bucket owner.
  * The bucket owner still pays for storage costs.
* **Enabling Requester Pays:**
  * Set on a bucket via Cloud Console, `gsutil`, or API.
  * `gsutil requesterpays set on gs://BUCKET_NAME`
* **How Requests are Made to a Requester Pays Bucket:**
  * The requester must include a **billing project ID** in their request (e.g., using the `-u <project_id>` flag in `gsutil`, or appropriate headers/parameters in API calls).
  * This project will be billed for the download and operation costs.
  * If the billing project is not provided, the request will fail (unless the requester is the bucket owner).
* **Use Cases:**
  * **Sharing large datasets:** Make large datasets (e.g., scientific data, public domain archives) available without the bucket owner incurring all the download costs.
  * **Service providers:** Offer data access where consumers pay for their own usage.
* **Considerations:**
  * Not suitable for all scenarios (e.g., serving a typical website where you want to cover user download costs).
  * Users must be aware that they need to specify a billing project.
  * Anonymous access is not possible with Requester Pays (as a billing project is needed).
