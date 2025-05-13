# Managaed Services

## Databases

---
layout: image
image: /databases-intro.png
---

<!--
Cloud Dev girl sketches are excellent. See: https://www.thecloudgirl.dev/sketches
-->

---
layout: image
image: /databases-which-one.png
---

<!--
Cloud Dev girl sketches are excellent. See: https://www.thecloudgirl.dev/sketches
-->

---

# Managed Services: Cloud SQL

---

# Features

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

---

# High Availability (HA)

* Provides **regional availability** against zonal failures.
* When enabled, Cloud SQL provisions a **primary instance** in one zone and a **standby instance** in a different zone within the same region.
* Data is **synchronously replicated** from the primary to the standby using a regional persistent disk.
* **Automatic Failover:** If the primary instance becomes unresponsive, Cloud SQL automatically fails over to the standby instance. The instance IP address and connections are redirected.
* Failover typically takes a few minutes.
**Connecting to Cloud SQL:**
* **Public IP:** Accessible over the internet (use SSL, strong passwords, firewall rules - Authorized Networks).
* **Private IP:** (Recommended for internal access) Uses VPC Network Peering to connect your VPC to Google's services network. Cloud SQL instance gets an internal IP in your VPC.
* **Cloud SQL Auth Proxy:** Securely connect from local machines or Compute Engine instances without needing to whitelist IPs or configure SSL directly. It uses IAM for authentication.

---

# Read Replicas

* **What are Read Replicas?** Copies of a primary Cloud SQL instance that are read-only.
* **Purpose:**
  * **Scale Read Traffic:** Offload read-intensive queries from the primary instance, improving its performance for write operations.
  * **Serve Different Workloads:** Use replicas for analytics, reporting, or data warehousing queries without impacting the transactional performance of the primary.
* **How they Work:**
  * **Asynchronous Replication:** Changes from the primary are replicated to the read replicas, can be lag.
  * Replicas can be located in the same region or a different region (cross-region replication).
  * You connect to a read replica using its own unique connection name/IP address.
* **Creating and Managing Read Replicas:**
  * Can be created from the Cloud Console or `gcloud`.
  * You can create replicas of replicas (cascading replicas, with potential for increased lag).
  * Replicas can have different machine types than the primary.

---

# Read Replicas

* **Promotion of a Replica:**
  * A read replica can be promoted to become a standalone, writable Cloud SQL instance.
  * This breaks the replication link from the original primary.
  * Useful for DR scenarios or for creating a new independent instance based on a replica's state.
* **Use Cases:**
  * Scaling read-heavy web or mobile applications.
  * Running business intelligence (BI) tools or reporting dashboards.
  * Data analysis workloads that don't require real-time data from the primary.
  * Can be used as a standby for disaster recovery if promoted (though HA configuration is better for automatic failover).

---

# Managed Services: Memorystore

---

# Memorystore for Redis - Features

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

---

# Memorystore for Redis - Use Cases

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

# Memorystore for Memcached - Features

* **What is Memorystore for Memcached?** A fully managed, in-memory key-value store service compatible with the Memcached protocol.
* **Key Features:**
  * **Fully Managed:** Google handles provisioning, patching, and monitoring.
  * **Scalability:** Scales by adding or removing nodes from the cluster. Data is distributed (sharded) across nodes.
  * **High Throughput & Low Latency:** Designed for very fast access to cached data.
  * **Multi-threaded Architecture:** Can handle many concurrent connections efficiently.
  * **VPC Native:** Instances are accessible via internal IP addresses within your VPC. Auto-discovery of nodes within the cluster.

---

# Memorystore for Memcached - Use Cases

* **Common Use Cases:**
  * **Caching Frequently Accessed Data:** Similar to Redis, used to cache database query results, API responses, rendered HTML, etc., to reduce latency and backend load.
  * **Web Application Acceleration:** Speed up dynamic web pages by caching components.
  * **Suitable for read-heavy workloads** where data is relatively simple key-value pairs.

---
zoom: 0.9
---

# Memcached vs. Redis

| Feature             | Memorystore for Memcached                                     | Memorystore for Redis                                                     |
| :------------------ | :------------------------------------------------------------ | :------------------------------------------------------------------------ |
| **Primary Use**     | Simpler, volatile caching                                     | Caching, richer data structures, managed HA, potential persistence        |
| **Data Model**      | Simple Key-Value                                              | Rich Data Structures (Strings, Lists, Sets, Sorted Sets, Hashes, Streams) |
| **Data Persistence**| Purely in-memory; no built-in persistence. Data lost on restart/failure. | Optional RDB snapshots and AOF logging for persistence.                 |
| **High Availability**| Achieved via data sharding and client-side logic to route around failed nodes. | Standard Tier offers managed primary/replica with automatic failover.     |
| **Complexity**      | Generally simpler API and feature set.                        | More features, potentially more complex to leverage fully.                |
| **Best For**        | High-throughput, low-latency caching of simple, volatile data. | Scenarios requiring data persistence, advanced data structures, or managed HA failover for caching. |

---

# Cloud Firestore

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

---

# Cloud Spanner - Globally Distributed SQL

* **What is Cloud Spanner?** A fully managed, globally distributed, and strongly consistent relational database service that combines the benefits of relational databases (SQL, ACID transactions) with NoSQL-like horizontal scalability.
* **Key Features:**
  * **Global Distribution:** Data can be distributed across regions and continents.
  * **Strong External Consistency (ACID):** Guarantees transactional consistency across rows, regions, and continents. Uses TrueTime technology.
  * **Horizontal Scalability:** By adding nodes (processing units) to increase throughput and storage.
  * **SQL Semantics:** Supports standard ANSI 2011 SQL with extensions.
  * **High Availability:** Offers up to 99.999% availability for multi-region configurations, 99.99% for regional.
  * **Relational Schema:** Supports schemas, tables, columns, indexes (including interleaved tables for co-location).

---

# Cloud Spanner - Globally Distributed SQL

* **Architecture (Simplified):**
  * Separation of compute and storage.
  * Data stored in Colossus (Google's distributed file system).
  * Uses Paxos consensus algorithm for data replication and consistency.
* **Use Cases:**
  * Mission-critical, global-scale OLTP (Online Transaction Processing) applications.
  * Financial applications (ledgers, trading systems) requiring strong consistency.
  * Global inventory management systems.
  * Applications needing to scale beyond the limits of traditional regional relational databases.
  * Workloads requiring both strong consistency and high availability across geographic locations.
* **Considerations:** Higher cost than Cloud SQL, designed for specific large-scale needs. Not a direct replacement for all Cloud SQL use cases.

---

# Cloud Firestore - Scalable NoSQL Document Database

* **What is Cloud Firestore?** A flexible, scalable NoSQL document database for mobile, web, and server development. It's part of the Firebase platform and also available as a standalone GCP service.
* **Key Features:**
  * **Data Model:** Stores data in **documents** (JSON-like objects) organized into **collections**. Documents can contain subcollections.
  * **Scalability:** Automatically scales based on usage.
  * **Real-time Listeners:** Clients (web, mobile) can subscribe to changes in data, enabling real-time application updates.
  * **Offline Support:** Mobile (iOS, Android) and Web SDKs provide robust offline data persistence and synchronization when connectivity is restored.
  * **Serverless:** No servers to manage or provision. Pay for what you use (operations, storage, network).
  * **Strong Consistency:** Offers strong consistency for reads within a region. Transactions are atomic.
  * **Powerful Querying:** Rich querying capabilities on document fields and support for indexing.

---

# Cloud Firestore - Scalable NoSQL Document Database

* **Modes:**
  * **Native Mode:** The default and recommended mode, offering the latest features.
  * **Datastore Mode:** Provides API compatibility with the older Google Cloud Datastore service. Useful for migrating existing Datastore applications.
* **Use Cases:**
  * Mobile application backends (user profiles, application data, real-time chat).
  * Web applications needing flexible schema and real-time features.
  * Collaborative applications.
  * IoT data ingestion and management.
  * Gaming leaderboards and user state.
  * Product catalogs and inventory systems.
* **Security:** Integrates with Firebase Authentication and IAM for access control. Security rules define access to collections and documents.

---

# Cloud Bigtable - Petabyte-Scale NoSQL

* **What is Cloud Bigtable?** A fully managed, massively scalable NoSQL wide-column store. It's the same database that powers many core Google services like Search, Analytics, Maps, and Gmail.
* **Key Features:**
  * **Extreme Scalability:** Designed to handle petabytes of data and millions of operations per second.
  * **High Throughput & Low Latency:** Optimized for high-volume reads and writes with low, predictable latency.
  * **Wide-Column Data Model:** Data is stored in a sparse, distributed, persistent multi-dimensional sorted map. Rows are indexed by a single row key. Each row can have many columns, grouped into column families.
  * **HBase API Compatible:** Applications written for Apache HBase can often be migrated to Bigtable with minimal changes.
  * **Managed Service:** Google handles instance provisioning, replication, patching, and scaling (by adding/removing nodes).

---

# Cloud Bigtable - Petabyte-Scale NoSQL

* **Architecture (Simplified):**
  * Nodes (compute) are separate from storage (Colossus).
  * Data is sharded into tablets and distributed across nodes.
* **Use Cases:**
  * **Time Series Data:** Storing and analyzing data from sensors, logs, metrics.
  * **IoT Data Streams:** Ingesting and processing high-velocity data from connected devices.
  * **Financial Data:** Market data, transaction histories.
  * **Personalization & Recommendation Engines:** Storing user activity and preferences.
  * **Large-Scale Analytical Workloads:** Often used as a source for Dataflow or Dataproc jobs.
  * **Monitoring & Observability Data:** Storing application and infrastructure metrics.
* **Not a Relational Database:** Does not support SQL, joins, or ACID transactions across multiple rows. Best for workloads that fit its specific data model and access patterns (key-based lookups, range scans).

---

# BigQuery - Serverless Data Warehouse & Analytics

* **What is BigQuery?** A fully managed, serverless, highly scalable, and cost-effective multi-cloud data warehouse designed for business agility.
* **Key Features:**
  * **Serverless Architecture:** No infrastructure to manage or provision. Scales automatically.
  * **SQL Interface:** Uses standard ANSI SQL for querying data.
  * **Massively Parallel Processing (MPP):** Google's Dremel query engine distributes queries across thousands of servers for fast results on terabytes and petabytes of data.
  * **Separation of Storage and Compute:** Storage and compute resources scale independently, optimizing cost and performance.
  * **Built-in Machine Learning (BigQuery ML):** Create and run ML models directly using SQL.
  * **Real-time Analytics:** Supports streaming data ingestion for up-to-the-minute analysis.
  * **Federated Queries:** Query data in Cloud Storage, Cloud SQL, Spanner, etc., directly from BigQuery without loading it.
  * **Cost-Effective:** Pay-as-you-go pricing for storage and queries, or flat-rate pricing options.

---

# BigQuery - Serverless Data Warehouse & Analytics

* **Use Cases:**
  * **Data Warehousing:** Central repository for all analytical data.
  * **Business Intelligence (BI) & Reporting:** Powering dashboards and reports (integrates with Looker, Tableau, etc.).
  * **Ad-hoc SQL Analytics:** Exploring and analyzing large datasets.
  * **Log Analysis:** Processing and querying application and infrastructure logs.
  * **Predictive Analytics & Machine Learning:** Using BigQuery ML or exporting data for other ML platforms.
* **Role as a "Data Solution":** While not a traditional OLTP database for transactional applications, BigQuery is a critical managed data service for analytical workloads, data exploration, and large-scale data processing. It's often the destination for data from other operational databases.

---

# Choosing Your Database: Key Factors to Consider (1/2)

Selecting the right database is crucial for application success. Consider these factors:

* **1. Data Model & Structure:**
  * **Relational (SQL):** Structured data, well-defined schema, relationships, ACID properties needed? (Cloud SQL, Spanner)
  * **Document (NoSQL):** Semi-structured, flexible schema, JSON-like objects? (Firestore)
  * **Key-Value (NoSQL):** Simple lookups by key, high-speed access? (Memorystore, aspects of Bigtable/Firestore)
  * **Wide-Column (NoSQL):** Massive datasets, sparse data, dynamic columns per row, row key lookups/scans? (Bigtable)
  * **Analytical / Warehousing:** Large-scale aggregations, complex queries on historical data? (BigQuery)
* **2. Consistency Needs:**
  * **Strong Consistency (ACID):** Are immediate, consistent views of data critical for all operations (e.g., financial transactions)? (Cloud SQL, Spanner, Firestore for single doc/transaction)
  * **Eventual Consistency:** Is it acceptable for some replicas or views of data to be slightly delayed? (Often a trade-off for higher availability/scalability in some NoSQL systems, though many GCP NoSQL options offer strong consistency).
  * **Global Consistency:** Do you need strong consistency across geographic regions? (Spanner excels here).
* **3. Scale & Performance:**
  * **Storage Capacity:** How much data now and in the future (GBs, TBs, PBs)?
  * **Read/Write Throughput:** How many operations per second (QPS)? Peak vs. average.
  * **Latency Requirements:** How fast must queries return (milliseconds, sub-milliseconds)?
  * **Scalability Model:** Need to scale reads, writes, or both? Horizontal (add more nodes) or vertical (bigger nodes) scaling? Serverless auto-scaling?

---

# Choosing Your Database: Key Factors to Consider (2/2)

* **4. Query Patterns & Access:**
  * **Complexity:** Simple GET/PUT by key, complex SQL joins, analytical aggregations, full-text search?
  * **Access Method:** SQL, specific client libraries, REST APIs, mobile SDKs?
  * **Real-time Needs:** Do you need real-time data synchronization or streaming analytics? (Firestore, BigQuery streaming, Pub/Sub + DB)
* **5. Availability & Disaster Recovery (DR):**
  * **RPO (Recovery Point Objective):** How much data loss is acceptable?
  * **RTO (Recovery Time Objective):** How quickly must the service be restored after an outage?
  * **Availability Needs:** Zonal, regional, or global availability? SLA requirements (e.g., 99.9%, 99.99%, 99.999%)?
* **6. Operational Overhead & Management:**
  * **Fully Managed vs. Self-Managed:** Prefer a service that handles patching, backups, scaling with minimal intervention?
  * **Serverless:** Pay-per-use with automatic scaling, no instances to manage? (Firestore, BigQuery)
  * **Expertise:** Team familiarity with SQL vs. NoSQL concepts?
* **7. Cost:**
  * Storage costs, compute/instance costs, I/O operation costs, network egress.
  * Predictable vs. variable spending. Free tiers or cost-saving options.
* **8. Specific Features / Integrations:**
  * Mobile/Web SDKs with offline support? (Firestore)
  * Compatibility with existing tools or APIs (e.g., HBase API for Bigtable)?
  * Built-in Machine Learning? (BigQuery ML)
  * Geospatial support?

---
zoom: 0.7
---

# Google Cloud Database Spectrum: A Quick Guide

| Service          | Type                     | Data Model    | Scale        | Consistency      | Key Strengths                                       | Best For...                                                              |
| :--------------- | :----------------------- | :------------ | :----------- | :--------------- | :-------------------------------------------------- | :----------------------------------------------------------------------- |
| **Cloud SQL** | Relational (OLTP)        | SQL           | Regional (TBs) | Strong (ACID)    | Managed MySQL, PostgreSQL, SQL Server; HA; Read Replicas | Traditional relational apps, lift-and-shift, web frameworks              |
| **Cloud Spanner**| Relational (NewSQL, OLTP)| SQL           | Global (PBs) | Strong (Global ACID)| Horizontally scalable SQL, global distribution, 99.999% HA | Mission-critical global apps, financial ledgers, large-scale consistent OLTP |
| **Firestore** | NoSQL Document           | Document      | Global (PBs) | Strong (Regional) | Serverless, real-time sync, offline SDKs, flexible schema | Mobile/web backends, real-time collaboration, user profiles, IoT         |
| **Bigtable** | NoSQL Wide-Column        | Wide-Column   | Global (PBs+) | Eventual (configurable for reads) | Extreme throughput/scale, low latency for key lookups, HBase API | IoT, time series, large-scale analytics backend, monitoring, ad-tech   |
| **BigQuery** | Data Warehouse (OLAP)    | Columnar/SQL  | Global (PBs+) | ACID (for DML)   | Serverless, MPP, SQL analytics, BigQuery ML, streaming      | BI, data warehousing, ad-hoc analytics, log analysis, ML on large data |
| **Memorystore** | In-Memory Key-Value      | Key-Value     | Regional (GBs) | N/A (Cache)      | Managed Redis/Memcached, low latency, high throughput     | Caching, session management, leaderboards, rate limiting                 |

*This table provides a high-level comparison. Always dive deeper into specific service documentation based on your primary selection criteria.*

---