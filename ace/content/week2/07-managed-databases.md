# Managed Services: Cloud SQL - Features & High Availability

* **What is Cloud SQL?** A fully managed relational database service.
* **Supported Engines:**
  * MySQL
  * PostgreSQL
  * SQL Server
* **Key "Managed" Features (Google Handles These):**
  * **Provisioning:** Easy instance creation and configuration.
  * **Patching & Updates:** OS and database software are kept up-to-date.
  * **Automated Backups:** Configurable daily backups with point-in-time recovery (PITR) using binary logs.
  * **Replication:** Read replicas, cross-region replication (for DR or geo-distribution of reads).
  * **Scaling:**
    * Storage: Can be increased manually or automatically (with downtime for some changes).
    * Machine Type: CPU and memory can be scaled up/down (requires instance restart).
* **High Availability (HA) Configuration:**
  * Provides **regional availability** against zonal failures.
  * When enabled, Cloud SQL provisions a **primary instance** in one zone and a **standby instance** in a different zone within the same region.
  * Data is **synchronously replicated** from the primary to the standby using a regional persistent disk.
  * **Automatic Failover:** If the primary instance becomes unresponsive, Cloud SQL automatically fails over to the standby instance. The instance IP address and connections are redirected.
  * Failover typically takes a few minutes.
* **Connecting to Cloud SQL:**
  * **Public IP:** Accessible over the internet (use SSL, strong passwords, firewall rules - Authorized Networks).
  * **Private IP:** (Recommended for internal access) Uses VPC Network Peering to connect your VPC to Google's services network. Cloud SQL instance gets an internal IP in your VPC.
  * **Cloud SQL Auth Proxy:** Securely connect from local machines or Compute Engine instances without needing to whitelist IPs or configure SSL directly. It uses IAM for authentication.

---

# Managed Services: Cloud SQL - Read Replicas & Use Cases

* **What are Read Replicas?** Copies of a primary Cloud SQL instance that are read-only.
* **Purpose:**
  * **Scale Read Traffic:** Offload read-intensive queries from the primary instance, improving its performance for write operations.
  * **Serve Different Workloads:** Use replicas for analytics, reporting, or data warehousing queries without impacting the transactional performance of the primary.
* **How they Work:**
  * **Asynchronous Replication:** Changes from the primary are replicated to the read replicas. There can be a small replication lag.
  * Replicas can be located in the same region or a different region (cross-region replication).
  * You connect to a read replica using its own unique connection name/IP address.
* **Creating and Managing Read Replicas:**
  * Can be created from the Cloud Console or `gcloud`.
  * You can create replicas of replicas (cascading replicas, with potential for increased lag).
  * Replicas can have different machine types than the primary.
* **Use Cases:**
  * Scaling read-heavy web or mobile applications.
  * Running business intelligence (BI) tools or reporting dashboards.
  * Data analysis workloads that don't require real-time data from the primary.
  * Can be used as a standby for disaster recovery if promoted (though HA configuration is better for automatic failover).
* **Promotion of a Replica:**
  * A read replica can be promoted to become a standalone, writable Cloud SQL instance.
  * This breaks the replication link from the original primary.
  * Useful for DR scenarios or for creating a new independent instance based on a replica's state.

---

# Managed Services: Memorystore for Redis - Features & Use Cases

* **What is Memorystore for Redis?** A fully managed, in-memory data store service compatible with the Redis protocol.
* **Key Features:**
  * **Fully Managed:** Google handles provisioning, patching, monitoring, and some scaling aspects.
  * **High Availability (Standard Tier):**
    * Provides a primary and a replica node in different zones within a region.
    * Automatic failover to the replica if the primary fails.
    * Offers 99.9% availability SLA.
  * **Basic Tier:** Single-node instance, lower cost, no HA. Suitable for dev/test or cache-only scenarios where data loss is acceptable.
  * **Sizing & Scaling:** Choose instance capacity (GB). Can scale capacity up or down (may involve downtime/data flush for some operations). Read replicas (for Redis 6+) can scale read throughput.
  * **VPC Native:** Instances are accessible via an internal IP address within your VPC network.
  * **Security:** IAM integration for instance management, AUTH command for Redis client authentication.
* **Common Use Cases:**
  * **Caching:**
    * **Web Session Management:** Store user session data for fast retrieval.
    * **Database Query Results Caching:** Reduce load on backend databases by caching frequent queries.
    * **Object Caching:** Store frequently accessed objects or rendered web pages.
  * **Leaderboards & Gaming:** Real-time updates and sorted sets make Redis ideal.
  * **Real-time Counters & Analytics:** Fast increment/decrement operations.
  * **Rate Limiting:** Track request counts per user/IP.
  * **Simple Message Broker / Queue (Pub/Sub):** Redis lists can be used for basic queuing.
* **Data Persistence (Optional):** RDB snapshots and AOF logging can be enabled for data persistence, though primarily an in-memory store.

---

# Managed Services: Memorystore for Memcached - Features & Use Cases

* **What is Memorystore for Memcached?** A fully managed, in-memory key-value store service compatible with the Memcached protocol.
* **Key Features:**
  * **Fully Managed:** Google handles provisioning, patching, and monitoring.
  * **Scalability:** Scales by adding or removing nodes from the cluster. Data is distributed (sharded) across nodes.
  * **High Throughput & Low Latency:** Designed for very fast access to cached data.
  * **Multi-threaded Architecture:** Can handle many concurrent connections efficiently.
  * **VPC Native:** Instances are accessible via internal IP addresses within your VPC. Auto-discovery of nodes within the cluster.
* **Common Use Cases:**
  * **Caching Frequently Accessed Data:** Similar to Redis, used to cache database query results, API responses, rendered HTML, etc., to reduce latency and backend load.
  * **Web Application Acceleration:** Speed up dynamic web pages by caching components.
  * **Suitable for read-heavy workloads** where data is relatively simple key-value pairs.
* **Differences from Memorystore for Redis:**
  * **Data Persistence:** Memcached is purely in-memory; no built-in persistence options like Redis RDB/AOF. Data is lost if a node fails or the cluster restarts.
  * **Data Structures:** Memcached is a simple key-value store. Redis offers richer data structures (lists, sets, sorted sets, hashes, streams).
  * **High Availability:** Memcached clusters achieve HA through data sharding and client-side logic to route around failed nodes. Memorystore for Redis (Standard Tier) offers a managed primary/replica failover.
  * **Complexity:** Memcached is generally simpler in its feature set and API.
* **Choosing:** Use Memcached for simpler, volatile caching. Use Redis if you need data persistence, richer data structures, or managed HA failover.

---

# Managed Services: Introduction to NoSQL Options (Firestore & Bigtable - Brief)

* While Cloud SQL covers relational databases, GCP offers powerful NoSQL options for different data models and scale requirements.
* **Cloud Firestore:**
  * **Type:** NoSQL Document Database (stores data in JSON-like documents, organized into collections).
  * **Key Features:**
    * Highly scalable and available (multi-regional options).
    * Real-time data synchronization with clients (web, mobile).
    * Offline support for mobile and web clients.
    * Strong consistency for reads within a region.
    * Serverless (no instances to manage).
  * **Use Cases:** Mobile application backends, web applications, real-time collaborative tools, IoT data, user profiles, game states.
  * **Datastore Mode:** Firestore can operate in "Datastore mode," which is API-compatible with the older Google Cloud Datastore service (a key-value and document database).
* **Cloud Bigtable:**
  * **Type:** NoSQL Wide-column Store (think of a massive, sparse, sorted map).
  * **Key Features:**
    * Extremely high throughput and low latency for very large datasets (Petabytes).
    * Designed for large analytical and operational workloads.
    * Integrates well with Hadoop, Dataflow, Dataproc.
    * Managed service, scales by adding nodes.
  * **Use Cases:** Time series data, financial market data, IoT sensor data streams, recommendation engines, monitoring data, ad tech.
  * **Note:** Bigtable is powerful but more specialized. Less likely to be a deep focus for ACE compared to Cloud SQL or Firestore basics, but good to know it exists for massive scale NoSQL.
