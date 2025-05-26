# Week 3: Elastic Google Cloud Infrastructure: Scaling & Automation

* **Core Concepts:**
  * **Elasticity:** Ability to acquire resources as you need them and release resources when you no longer need them.
  * **Scalability:** Ability of a system to handle a growing amount of work by adding resources.
  * **Automation:** Using tools and scripts to manage and operate infrastructure with minimal human intervention.
* **Key GCP Services for Elasticity & Automation:**
  * Managed Instance Groups (MIGs)
  * Autoscaler
  * Cloud Load Balancing
  * (Instance Templates - covered in Week 2, but crucial foundation)

---

# Managed Instance Groups (MIGs): Overview

* **What are MIGs?**
  * A collection of homogeneous (identical) Compute Engine VM instances treated as a single logical unit.
  * Created from a common **Instance Template**.
* **Key Benefits:**
  * **High Availability (HA):**
    * **Self-healing:** Automatically recreates failed/unhealthy instances based on health checks.
    * **Multi-zone distribution (Regional MIGs):** Spreads instances across multiple zones in a region to protect against zonal failures.
  * **Scalability:** Works with Autoscaler to dynamically add or remove instances based on load.
  * **Automated Updates:** Supports rolling updates and canary deployments to update instances with minimal disruption.
* **Instance Template Requirement:**
  * All instances in a MIG are created from the same instance template, ensuring uniformity.
  * To change the VM configuration (e.g., machine type, disk image), you update the MIG to use a new instance template.

---

# MIGs: Types - Zonal vs. Regional

* **Zonal MIG:**
  * Instances are located within a **single zone**.
  * **Pros:** Simpler to manage if regional distribution isn't required.
  * **Cons:** Vulnerable to zonal outages. If the zone fails, all instances in the MIG become unavailable.
  * **Use Case:** Workloads that can tolerate zonal failure or where regional distribution adds unnecessary complexity or cost.
* **Regional MIG:**
  * Instances are distributed across **multiple zones within a single region**.
  * You specify the zones where instances can be created.
  * **Pros:** Higher availability; protects against zonal failures. The MIG attempts to maintain the target number of instances across the selected zones.
  * **Cons:** Slightly more complex to set up (managing target distribution shapes).
  * **Use Case:** Most production workloads requiring high availability. This is the recommended approach for resilient applications.
  * **Target Distribution Shape:**
    * `EVEN` (default for >1 zone): Distributes instances as evenly as possible across selected zones.
    * `BALANCED`: Prioritizes creating instances in zones where resources are available, and attempts to balance, but may lead to uneven distribution if one zone has capacity issues.
    * `ANY_SINGLE_ZONE`: All instances run in a single zone (chosen by MIG), but can failover to another selected zone if needed (less common).

---

# MIGs: Self-Healing

* **Purpose:** To automatically maintain the health and target number of running instances in the MIG.
* **Mechanism:**
    1. **Application-based Health Check:** You configure a health check (HTTP, HTTPS, TCP, SSL) that probes your application on each instance.
    2. **Health State:** Each instance is marked as `HEALTHY` or `UNHEALTHY` based on the health check responses.
    3. **Autohealing Action:** If an instance remains `UNHEALTHY` for a configured period (grace period), the MIG automatically:
       * Deletes the unhealthy instance.
       * Recreates a new instance from the instance template to replace it.
* **Configuration:**
  * Health check is specified when creating or updating the MIG.
  * `initialDelaySec`: Time to wait after an instance starts before starting health checks.
* **Benefit:** Improves application availability by automatically recovering from individual instance failures or application crashes without manual intervention.

---

# MIGs: Updating Instances - Strategies

MIGs provide mechanisms to update the software or configuration of instances in a controlled manner. This typically involves creating a new version of your instance template.

* **Rolling Update (`rolling-action start-update`):**
  * Gradually replaces old instances with new ones based on the updated instance template.
  * **Configuration:**
    * `maxSurge`: Number or percentage of additional instances created above target size during update.
    * `maxUnavailable`: Number or percentage of instances that can be unavailable during update.
    * `minReadySec`: Time a new instance must be healthy before it's considered updated.
  * **Process:** New instances are created, old ones are deleted one by one or in batches.
  * **Proactive vs. Opportunistic:**
    * `PROACTIVE`: MIG actively replaces instances.
    * `OPPORTUNISTIC`: MIG only updates instances when they are manually or autohealed/recreated.
* **Rolling Restart/Replace (`rolling-action restart` or `replace`):**
  * Restarts or replaces existing instances without changing the instance template. Useful for applying OS patches that require a reboot.
* **Canary Deployments (Conceptual with MIGs):**
  * Not a direct MIG command, but achieved by:
        1.  Creating a new MIG (or separate MIG with new template) for the canary version.
        2.  Directing a small percentage of traffic (via Load Balancer) to the canary MIG.
        3.  Monitoring performance. If successful, gradually increase traffic and/or update the main MIG.
* **IAM Permission:** `compute.instanceGroupManagers.update`

---

# Autoscaling: Introduction & Benefits

* **What is Autoscaling?** The process of automatically adding or removing VM instances from a Managed Instance Group (MIG) in response to changes in load or other defined metrics.
* **Purpose:**
  * **Maintain Performance:** Ensure enough instances are running to handle application load smoothly.
  * **Improve Availability:** Scale out to handle unexpected traffic spikes.
  * **Optimize Costs:** Scale in (remove instances) during periods of low load to avoid paying for unused resources.
* **How it Works:**
  * An **Autoscaler** is configured for a MIG.
  * It monitors signals (CPU, metrics, LB capacity, schedule).
  * Based on the configured policy, it calculates the recommended number of instances.
  * It then instructs the MIG to change its target size.
* **Key Principle:** Matches capacity to demand dynamically.

---

# Autoscaling: Scaling Policies & Signals

Autoscalers make decisions based on one or more scaling signals/policies:

* **1. Average CPU Utilization:**
  * Most common signal.
  * You set a **target CPU utilization level** (e.g., 60%).
  * If average CPU across the MIG exceeds the target, the autoscaler scales out.
  * If average CPU is below the target, it scales in.
* **2. Cloud Monitoring Metrics:**
  * Scale based on any standard or custom Cloud Monitoring metric.
  * **Standard Metrics:** e.g., `pubsub.googleapis.com/subscription/num_undelivered_messages` for a worker pool processing Pub/Sub messages.
  * **Custom Metrics:** Metrics your application exports to Cloud Monitoring.
  * Requires more configuration to define how the metric relates to instance count.
* **3. Load Balancing Serving Capacity:**
  * Scales based on the `serving_capacity_utilization` of the backend service associated with an HTTP(S), SSL Proxy, or TCP Proxy Load Balancer.
  * You define the `max_utilization` (e.g., 80%) or `max_rate_per_instance` or `max_connections_per_instance`.
  * Autoscaler tries to keep utilization below the target.
* **4. Schedules (Schedule-based Autoscaling):**
  * Change the minimum number of instances at specific times (e.g., scale up during business hours, scale down at night/weekends).
  * Defines `minRequiredInstances` for a given cron-like schedule.
  * Useful for predictable load patterns.
* **Multiple Policies:** An autoscaler can use multiple policies. It calculates the recommended number of instances for each policy and picks the largest number.

---

# Autoscaling: Key Configuration Parameters

When configuring an autoscaler, several parameters control its behavior:

* **Target Utilization/Metric Value:** The desired level for the chosen signal (e.g., 60% CPU, 1000 undelivered Pub/Sub messages).
* **Minimum Number of Instances (`minNumReplicas`):** The smallest number of instances the MIG can scale down to. Ensures a baseline capacity.
* **Maximum Number of Instances (`maxNumReplicas`):** The largest number of instances the MIG can scale out to. Prevents runaway scaling and controls costs.
* **Cooldown Period (`coolDownPeriodSec`):**
  * The number of seconds the autoscaler waits after a scaling activity (scale out or in) before it initiates another scaling activity.
  * Prevents the autoscaler from reacting too aggressively to short-term fluctuations or from adding instances before previous ones are fully initialized and serving traffic. Default: 60 seconds.
* **Stabilization Period (Predictive Autoscaling):**
  * Autoscaler observes historical load and predicts future load.
  * If load is predicted to drop, the autoscaler waits for the stabilization period before scaling down to ensure the drop isn't temporary. Default: 10 minutes.
* **Scaling Modes:**
  * `ON`: Enables both scale-out and scale-in.
  * `OFF`: Disables autoscaling. MIG size remains fixed unless manually changed.
  * `ONLY_SCALE_OUT` (or `ONLY_UP`): Allows autoscaler to add instances but not remove them. Useful for specific scenarios like ensuring capacity during critical events without automatic scale-down.
* **IAM Permission:** `compute.autoscalers.update`

---

# Autoscaling: Understanding Decisions & Best Practices

* **Monitoring Autoscaler Activity:**
  * **Cloud Console:** View autoscaler recommendations, current size, and recent scaling events in the MIG details page.
  * **Cloud Logging:** Autoscaler logs its decisions and the reasons behind them. Useful for troubleshooting.
  * **Cloud Monitoring:** Monitor metrics like instance count, CPU utilization, and autoscaler-specific metrics.
* **Best Practices for Autoscaling:**
  * **Choose the Right Scaling Signal:** CPU is a good start, but consider metrics more directly related to your application's performance bottlenecks (e.g., queue length, request latency).
  * **Set Realistic Min/Max Instances:** Avoid setting `minNumReplicas` too low (can impact availability) or `maxNumReplicas` too high (can lead to unexpected costs).
  * **Configure Health Checks Properly:** Autoscaler relies on the MIG's health checks. Unhealthy instances don't contribute to load metrics effectively.
  * **Tune Cooldown Period:** Adjust based on instance startup time and application initialization. Too short can cause thrashing; too long can delay response to load changes.
  * **Test Your Autoscaling Configuration:** Simulate load to ensure the autoscaler behaves as expected.
  * **Combine with Scheduled Autoscaling:** For predictable peaks, use schedules to proactively scale, then let metric-based scaling handle unpredictable variations.
  * **Stateless Applications:** Autoscaling works best with stateless applications where any instance can handle any request. For stateful applications, use Stateful MIGs and consider if autoscaling is appropriate or how state is managed.

---

# Cloud Load Balancing: Introduction & Core Concepts

* **What is Load Balancing?** Distributing network traffic or application workload across multiple backend servers (e.g., Compute Engine VMs, GKE pods, Cloud Storage buckets).
* **Key Benefits:**
  * **High Availability:** Distributes traffic away from unhealthy or overloaded instances. If one backend fails, others can take over.
  * **Scalability:** Works with autoscaled backends (like MIGs) to handle varying levels of traffic.
  * **Performance:** Directs users to the closest or least loaded server, potentially reducing latency.
  * **Cost Efficiency:** By distributing load, you can often use smaller, more numerous instances effectively.
* **Key Distinctions in GCP Load Balancers:**
  * **Global vs. Regional:** Where the frontend IP and backends can be.
  * **External vs. Internal:** Internet-facing vs. VPC-internal traffic.
  * **Layer 7 (Application) vs. Layer 4 (Network):** Protocol awareness.
  * **Proxy-based vs. Pass-through:** How traffic is handled.

---

# Cloud Load Balancing: Global vs. Regional

* **Global Load Balancing:**
  * **Frontend IP:** Single **Anycast IP address** that is globally advertised. Users are routed to the closest Google Point of Presence (POP).
  * **Backends:** Can be located in multiple regions. The load balancer can direct traffic to the healthiest region closest to the user or based on capacity.
  * **Resilience:** Highly resilient to regional outages (if backends are multi-regional).
  * **Services:**
    * Global External HTTP(S) Load Balancer (Application Load Balancer)
    * Global External SSL Proxy Load Balancer
    * Global External TCP Proxy Load Balancer
* **Regional Load Balancing:**
  * **Frontend IP:** IP address is specific to a single region.
  * **Backends:** Must be located within the same region as the load balancer.
  * **Resilience:** Resilient to zonal outages within the region (if backends are multi-zonal).
  * **Services:**
    * Regional External Network Load Balancer (TCP/UDP)
    * Regional Internal HTTP(S) Load Balancer
    * Regional Internal TCP/UDP Load Balancer

---

# Cloud Load Balancing: External vs. Internal

* **External Load Balancers:**
  * Distribute traffic coming from the **internet** to your Google Cloud backends.
  * Require a publicly routable IP address for the frontend.
  * **Examples:**
    * Global External HTTP(S) Load Balancer
    * Global External SSL Proxy / TCP Proxy Load Balancers
    * Regional External Network Load Balancer
* **Internal Load Balancers:**
  * Distribute traffic originating **within your VPC network** (or connected networks like on-premises via VPN/Interconnect).
  * Use an internal IP address from your VPC subnet for the frontend.
  * Traffic stays within Google's private network.
  * **Examples:**
    * Regional Internal HTTP(S) Load Balancer
    * Regional Internal TCP/UDP Load Balancer
  * **Use Case:** Load balancing between tiers of an application within your VPC (e.g., web tier to app tier).

---

# Cloud Load Balancing: Layer 7 (Application) vs. Layer 4 (Network)

* **Layer 7 (Application) Load Balancers:**
  * Operate at the application layer of the OSI model.
  * Understand application protocols like HTTP, HTTPS.
  * Can inspect application traffic (URLs, headers, cookies).
  * **Features:** Content-based routing (URL maps), SSL offload, session affinity based on cookies, integration with Cloud CDN, IAP, Google Cloud Armor.
  * **GCP Examples:**
    * Global External HTTP(S) Load Balancer
    * Regional Internal HTTP(S) Load Balancer
* **Layer 4 (Network) Load Balancers:**
  * Operate at the transport layer (TCP, UDP) or network layer (IP).
  * Distribute traffic based on IP address and port information.
  * Do not typically inspect application content.
  * **Features:** Can be pass-through (preserving client IP) or proxy-based. High performance for TCP/UDP traffic.
  * **GCP Examples:**
    * Global External SSL Proxy / TCP Proxy Load Balancers (Proxy-based Layer 4)
    * Regional External Network Load Balancer (Pass-through Layer 4)
    * Regional Internal TCP/UDP Load Balancer (Pass-through Layer 4)

---

# External Load Balancer: Global External HTTP(S) Load Balancer

* **Type:** Global, External, Layer 7 (Application).
* **Frontend:** Single global Anycast IP address.
* **Protocols:** HTTP, HTTPS.
* **Key Features:**
  * **Content-based routing:** URL Maps direct traffic based on host, path, headers.
  * **SSL Offload:** Terminates SSL/TLS at the load balancer, reducing load on backends. Supports Google-managed and self-managed SSL certificates.
  * **Backend Services:** Define groups of backends (MIGs, NEGs).
  * **Health Checks:** Ensure traffic only goes to healthy backends.
  * **Cloud CDN Integration:** Cache static content closer to users.
  * **Identity-Aware Proxy (IAP) Integration:** Secure access to applications based on user identity.
  * **Google Cloud Armor Integration:** Web Application Firewall (WAF) capabilities (DDoS protection, IP black/whitelisting, custom rules).
  * **Session Affinity:** Client IP, Generated Cookie, HTTP Header.
  * **Supports various backend types:**
    * Managed Instance Groups (MIGs)
    * Zonal Network Endpoint Groups (NEGs) for GCE VMs (`GCE_VM_IP_PORT`)
    * Serverless NEGs (Cloud Run, App Engine, Cloud Functions)
    * Internet NEGs (external backends)
    * Hybrid Connectivity NEGs (on-premises/other cloud backends via VPN/Interconnect)
    * Cloud Storage Buckets (for static content serving)
* **Use Case:** Most common choice for public-facing web applications and APIs.

---

# External Load Balancer: SSL Proxy & TCP Proxy

* **Global External SSL Proxy Load Balancer:**
  * **Type:** Global, External, Layer 4 (Proxy-based).
  * **Frontend:** Single global Anycast IP address.
  * **Protocols:** TCP with SSL offload.
  * **How it works:** Terminates client SSL connections at the load balancer, then forwards unencrypted TCP traffic to backends (or can re-encrypt to backends).
  * **Use Case:** Non-HTTP(S) SSL traffic (e.g., IMAPS, SMTPS, custom SSL protocols) requiring global reach and SSL termination.
  * **Backends:** Typically MIGs.
* **Global External TCP Proxy Load Balancer:**
  * **Type:** Global, External, Layer 4 (Proxy-based).
  * **Frontend:** Single global Anycast IP address.
  * **Protocols:** TCP (non-SSL).
  * **How it works:** Terminates client TCP connections and opens new TCP connections to backends.
  * **Use Case:** Non-HTTP, non-SSL TCP traffic requiring global reach (e.g., custom TCP protocols, some database access).
  * **Backends:** Typically MIGs.
* **Key Benefit of Proxy LBs:** Can handle large amounts of non-HTTP traffic and provide some protection by not directly exposing backends.

---

# External Load Balancer: Regional External Network Load Balancer

* **Type:** Regional, External, Layer 4 (Pass-through).
  * Sometimes referred to as just "Network Load Balancer" in older contexts.
* **Frontend:** Regional external IP address.
* **Protocols:** TCP, UDP, SSL (pass-through), ESP, GRE, ICMP.
* **How it works (Pass-through):**
  * Directly forwards packets from clients to backend VMs without terminating connections or inspecting content.
  * **Preserves client source IP address** on the backend VMs.
  * Uses direct server return (DSR) for responses.
* **Backend Options:**
  * **Backend Service-based:** (Recommended) Uses regional backend services with MIGs or Zonal NEGs as backends. Allows for more sophisticated health checks and connection draining.
  * **Target Pool-based:** (Legacy) Uses target pools with instances directly added. Simpler health checks (HTTP/HTTPS only).
* **Use Case:**
  * When client source IP preservation is critical.
  * For protocols other than TCP/UDP (ESP, GRE, ICMP).
  * High-performance, direct path for UDP traffic (e.g., gaming, streaming).
  * When you need to terminate SSL on the backend instances themselves.
* **Note:** Does not offer features like SSL offload or content-based routing.

---

# Internal Load Balancer: Regional Internal HTTP(S) Load Balancer

* **Type:** Regional, Internal, Layer 7 (Application).
* **Frontend:** Regional internal IP address (from a proxy-only subnet you create in your VPC).
* **Protocols:** HTTP, HTTPS, HTTP/2.
* **How it works:**
  * Managed service based on Envoy proxies deployed in your VPC.
  * Clients within your VPC (or connected networks) connect to the internal IP.
  * Provides Layer 7 features like URL maps for path/host-based routing, header manipulation, etc.
* **Backend Types:**
  * Managed Instance Groups (MIGs)
  * Zonal Network Endpoint Groups (NEGs) for GCE VMs (`GCE_VM_IP_PORT`)
  * Hybrid Connectivity NEGs (on-premises/other cloud backends)
* **Use Case:**
  * Load balancing internal microservices or tiers of an application (e.g., web tier to app tier).
  * Modernizing legacy internal applications by putting an L7 load balancer in front.
  * Internal APIs.
* **Key Benefit:** Provides advanced Layer 7 routing for internal traffic within your private network.

---

# Internal Load Balancer: Regional Internal TCP/UDP Load Balancer

* **Type:** Regional, Internal, Layer 4 (Pass-through).
* **Frontend:** Regional internal IP address (from a regular subnet in your VPC).
* **Protocols:** TCP, UDP.
* **How it works (Pass-through):**
  * Directly forwards packets from internal clients to backend VMs within the same VPC or connected networks.
  * Preserves client source IP address (if clients are in the same VPC).
* **Backend Types:**
  * Managed Instance Groups (MIGs)
  * Zonal Network Endpoint Groups (NEGs) for GCE VMs (`GCE_VM_IP_PORT`)
* **Use Case:**
  * Load balancing internal TCP/UDP applications where Layer 7 features are not needed.
  * Internal database load balancing (for some architectures).
  * High-performance internal traffic distribution.
* **Access:** Can be accessed by clients in the same VPC network, or from networks connected via VPN, Interconnect, or VPC Peering (if configured to exchange custom static routes).

---

# Load Balancer Components: Forwarding Rules & Target Pools/Proxies

* **Forwarding Rules:**
  * Define the "frontend" of the load balancer.
  * Specify:
    * **IP Address:** The IP address clients connect to (external or internal).
    * **Protocol:** (e.g., TCP, UDP, HTTP, HTTPS, SSL).
    * **Port Range:** (e.g., 80, 443, 1-65535).
    * **Network Tier (for external LBs):** Premium (global network) or Standard (regional network, lower cost).
  * Directs matching traffic to a **Target Proxy** (for proxy LBs) or a **Target Pool/Backend Service** (for pass-through LBs).
* **Target Pools (Legacy for Network LB):**
  * Used by Regional External Network Load Balancers (pass-through) if not using backend service mode.
  * A collection of instances that can receive traffic.
  * Simpler health checks (HTTP/HTTPS only).
* **Target HTTP(S) Proxies / Target SSL Proxies / Target TCP Proxies:**
  * Used by proxy-based load balancers (Global External HTTP(S), SSL Proxy, TCP Proxy, Internal HTTP(S)).
  * Receive traffic from the forwarding rule.
  * For HTTP(S) Proxies, they consult a **URL Map** to determine which backend service to route to.
  * For SSL/TCP Proxies, they route directly to a single backend service.

---

# Load Balancer Components: URL Maps & Backend Services

* **URL Maps (for HTTP(S) Load Balancers):**
  * Define rules for routing HTTP(S) requests to different backend services based on:
    * **Host rules:** Match based on the `Host` header (e.g., `example.com`, `api.example.com`).
    * **Path matchers:** Match based on the URL path (e.g., `/images/*`, `/videos/*`, `/api/v1/*`).
  * Can specify default backend service if no rules match.
  * Allows for sophisticated content-based routing.
* **Backend Services:**
  * Define a group of **backends** that will handle traffic for a specific service.
  * **Backends can be:**
    * Managed Instance Groups (MIGs)
    * Zonal Network Endpoint Groups (NEGs - GCE VMs, containers)
    * Serverless NEGs (Cloud Run, App Engine, Cloud Functions)
    * Internet NEGs / Hybrid NEGs
    * Cloud Storage Buckets (for Global External HTTP(S) LB)
  * **Key Configurations:**
    * **Health Checks:** Specifies the health check to use for backends in this service.
    * **Balancing Mode:**
        * `UTILIZATION`: (Default for HTTP(S) LB with MIGs) Spreads load to keep backend utilization (e.g., CPU) below a target.
        * `RATE`: Spreads load based on target requests/packets per second per instance/endpoint.
        * `CONNECTION`: Spreads load based on target concurrent connections per instance/endpoint.
    * **Session Affinity:** Configures how requests from the same client are routed (e.g., Client IP, Generated Cookie, HTTP Header).
    * **Connection Draining Timeout:** How long to wait for existing connections to complete before removing a backend.
    * **Cloud CDN (for Global External HTTP(S) LB):** Enable/disable caching.
    * **Protocol:** Protocol used to communicate with backends (e.g., HTTP, HTTPS, HTTP/2, TCP, SSL).

---

# Load Balancer Components: Health Checks - Ensuring Reliability

* **Purpose:** Critical for load balancers to determine which backend instances or endpoints are healthy and capable of serving traffic. Unhealthy backends are removed from the load balancing rotation.
* **How they Work:** The load balancer (or Google's health checking systems) periodically sends probes to backends based on the health check configuration.
* **Types of Health Checks:**
  * **HTTP:** Sends an HTTP GET request to a specified port and path. Expects a 200 OK response.
  * **HTTPS:** Similar to HTTP, but uses HTTPS.
  * **HTTP/2:** Similar to HTTPS, but uses HTTP/2.
  * **TCP:** Attempts to establish a TCP connection to a specified port. Success if connection established.
  * **SSL:** Attempts an SSL handshake on a specified port. Success if handshake completes.
* **Configurable Parameters:**
  * **Protocol & Port:** Which protocol and port to probe.
  * **Request Path (for HTTP/S, HTTP/2):** The URL path to request (e.g., `/healthz`).
  * **Check Interval:** How often to send a probe (e.g., every 5 seconds).
  * **Timeout:** How long to wait for a response before considering the probe failed (e.g., 5 seconds).
  * **Healthy Threshold:** Number of consecutive successful probes to mark an unhealthy backend as healthy.
  * **Unhealthy Threshold:** Number of consecutive failed probes to mark a healthy backend as unhealthy.
* **Global vs. Regional Health Checks:**
  * Global health checks are used with global backend services.
  * Regional health checks are used with regional backend services.
* **Best Practice:** Health check should verify the actual health of the application, not just that the server is up (e.g., check database connectivity if the app depends on it).

---

# Network Endpoint Groups (NEGs): Flexible Backend Definitions

* **What are NEGs?** A Network Endpoint Group specifies a group of backend endpoints for a load balancer. An endpoint is an IP address and port combination.
* **Why use NEGs?** Provide more flexibility than just using MIGs as backends, allowing for different types of backends and finer-grained control.
* **Types of NEGs:**
  * **Zonal NEGs (`GCE_VM_IP_PORT` type):**
    * Endpoints are primary internal IP addresses of Compute Engine VMs or RFC 1918 IP addresses of containers running on those VMs, plus a port.
    * VMs must be in the same zone as the NEG.
    * Allows load balancing to specific ports on VMs, useful for containerized workloads (e.g., with GKE, though GKE often manages its own NEGs for Services of type LoadBalancer).
  * **Serverless NEGs:**
    * Endpoints are Google Cloud serverless services:
        * Cloud Run services
        * App Engine services (Standard or Flex)
        * Cloud Functions
    * Allows you to use these serverless platforms as backends for Global External HTTP(S) Load Balancers.
  * **Internet NEGs:**
    * Endpoints are external to Google Cloud, identified by hostname and port or IP address and port.
    * **Use Case:** Load balancing to on-premises backends or services hosted in other clouds, often used with Cloud CDN for caching.
    * Two types: `INTERNET_IP_PORT` and `INTERNET_FQDN_PORT`.
  * **Hybrid Connectivity NEGs (`NON_GCP_PRIVATE_IP_PORT` type):**
    * Endpoints are on-premises or in other clouds, reachable via **Hybrid Connectors** (VPN or Cloud Interconnect).
    * Allows internal and external load balancers to send traffic to these non-GCP backends over private connections.
* **Usage:** NEGs are attached to Backend Services, just like MIGs.

---

# Cloud CDN: Caching Content Globally

* **What is Cloud CDN (Content Delivery Network)?**
  * Uses Google's globally distributed edge points of presence (POPs) to cache copies of your load-balanced web content closer to your users.
* **Integration:** Works with **Global External HTTP(S) Load Balancer**.
* **Benefits:**
  * **Reduced Latency:** Users fetch content from a nearby Google edge cache, significantly speeding up page load times.
  * **Reduced Origin Server Load:** Fewer requests hit your backend instances, reducing their workload and potentially costs.
  * **Improved Scalability & Availability:** Absorbs traffic spikes and can serve content even if the origin is temporarily slow or unavailable (if content is cached).
* **How it Works:**
    1. User requests content.
    2. Request hits the Global External HTTP(S) Load Balancer.
    3. If Cloud CDN is enabled for the backend service, CDN checks if the content is in a nearby edge cache.
    4. **Cache Hit:** Content is served directly from the edge cache.
    5. **Cache Miss:** Request is forwarded to the origin backend. The response is then cached by CDN (if cacheable) and served to the user.
* **Key Configuration Concepts:**
  * **Enable CDN:** Checkbox on the Backend Service configuration.
  * **Cache Modes:**
    * `CACHE_ALL_STATIC` (default): Caches common static content types based on `Content-Type` header. Respects `Cache-Control` headers from origin.
    * `USE_ORIGIN_HEADERS`: Strictly follows `Cache-Control` headers from the origin.
    * `FORCE_CACHE_ALL`: Caches all responses (use with caution, can cache dynamic or private content if not configured carefully with TTLs).
  * **Cache Keys:** How CDN identifies unique cacheable items (default: full URL). Can be customized.
  * **TTLs (Time To Live):** Control how long content stays in cache (client TTL, default TTL, max TTL). Can be set by origin `Cache-Control` headers or CDN policy.
  * **Signed URLs/Cookies for CDN:** Control access to cached private content.
* **Invalidation:** Manually remove content from CDN caches before its TTL expires.
