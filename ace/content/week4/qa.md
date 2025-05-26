# Week 4 - Questions with Answers

---

**Question 1:** A regional managed instance group (MIG) is configured with a target size of 10 instances across 3 zones (zone-a, zone-b, zone-c). If zone-a experiences a complete outage, what will the MIG attempt to do?

- A. Terminate all instances and wait for zone-a to recover.
- B. Maintain 10 instances by distributing them across the remaining healthy zones (zone-b and zone-c).
- C. Reduce its target size to approximately 2/3 of the original, around 6-7 instances.
- D. Create all 10 instances in a single new, healthy zone within the region.

---

**Question 1:** A regional managed instance group (MIG) is configured with a target size of 10 instances across 3 zones (zone-a, zone-b, zone-c). If zone-a experiences a complete outage, what will the MIG attempt to do?

- **Incorrect: A. Terminate all instances and wait for zone-a to recover:** MIGs are designed for high availability and will try to maintain capacity.
- **Correct: B. Maintain 10 instances by distributing them across the remaining healthy zones (zone-b and zone-c):** Regional MIGs aim to maintain the target number of instances by utilizing the available configured zones. If one zone fails, it will create replacement instances in the other healthy zones.
- **Incorrect: C. Reduce its target size to approximately 2/3 of the original, around 6-7 instances:** The target size remains the same; the MIG tries to meet it with available resources.
- **Incorrect: D. Create all 10 instances in a single new, healthy zone within the region:** While it will use healthy zones, it will distribute according to its configuration (e.g., `EVEN` or `BALANCED`) across the *remaining specified* healthy zones, not necessarily pick one new one for all.

---

**Question 2:** You have configured an autoscaler for a Managed Instance Group based on an average CPU utilization target of 70%. What action will the autoscaler most likely take if the average CPU utilization across the MIG consistently stays at 40% for an extended period?

- A. Increase the maximum number of instances.
- B. Decrease the number of instances in the MIG (scale in).
- C. Send an alert notification but take no scaling action.
- D. Change the target CPU utilization to 40%.

---

**Question 2:** You have configured an autoscaler for a Managed Instance Group based on an average CPU utilization target of 70%. What action will the autoscaler most likely take if the average CPU utilization across the MIG consistently stays at 40% for an extended period?

- **Incorrect: A. Increase the maximum number of instances:** Low utilization signals a need for fewer, not more, instances.
- **Correct: B. Decrease the number of instances in the MIG (scale in):** If the actual CPU utilization (40%) is significantly below the target (70%), the autoscaler will remove instances to increase the utilization of the remaining instances, optimizing costs, provided it doesn't go below `minNumReplicas`.
- **Incorrect: C. Send an alert notification but take no scaling action:** Autoscalers are designed to take action, not just alert.
- **Incorrect: D. Change the target CPU utilization to 40%:** The autoscaler acts based on the configured target; it doesn't change the target itself.

---

**Question 3:** Which type of Cloud Load Balancer would you choose to distribute internet-facing HTTP and HTTPS traffic to backends located in multiple regions, providing a single global Anycast IP address to your users?

- A. Regional External Network Load Balancer
- B. Regional Internal HTTP(S) Load Balancer
- C. Global External HTTP(S) Load Balancer
- D. Global External TCP Proxy Load Balancer

---

**Question 3:** Which type of Cloud Load Balancer would you choose to distribute internet-facing HTTP and HTTPS traffic to backends located in multiple regions, providing a single global Anycast IP address to your users?

- **Incorrect: A. Regional External Network Load Balancer:** This is regional, not global, and operates at Layer 4.
- **Incorrect: B. Regional Internal HTTP(S) Load Balancer:** This is for internal traffic within a VPC and is regional.
- **Correct: C. Global External HTTP(S) Load Balancer:** This is a Layer 7 load balancer that provides a global Anycast IP and can distribute HTTP/HTTPS traffic to backends in multiple regions.
- **Incorrect: D. Global External TCP Proxy Load Balancer:** This is for TCP traffic, not specifically HTTP/HTTPS, and operates at Layer 4.

---

**Question 4:** What is the primary purpose of configuring a Health Check for a backend service used by a Cloud Load Balancer?

- A. To encrypt traffic between the load balancer and the backend instances.
- B. To determine if backend instances are healthy and able to receive traffic.
- C. To automatically update the software on backend instances.
- D. To control the IP addresses that can access the backend instances.

---

**Question 4:** What is the primary purpose of configuring a Health Check for a backend service used by a Cloud Load Balancer?

- **Incorrect: A. To encrypt traffic between the load balancer and the backend instances:** Encryption is handled by SSL/TLS configurations, not health checks.
- **Correct: B. To determine if backend instances are healthy and able to receive traffic:** Health checks allow the load balancer to periodically probe backends and send traffic only to those that respond successfully, thus ensuring reliability and availability.
- **Incorrect: C. To automatically update the software on backend instances:** Software updates are managed through MIG update mechanisms or manual processes, not health checks.
- **Incorrect: D. To control the IP addresses that can access the backend instances:** Access control is managed by firewall rules or services like IAP, not health checks.

---

**Question 5:** You are configuring a rolling update for a Managed Instance Group. You want to ensure that during the update, the total number of instances running (old and new combined) can temporarily exceed the MIG's target size. Which rolling update parameter should you configure?

- A. `maxUnavailable`
- B. `minReadySec`
- C. `maxSurge`
- D. `cooldownPeriodSec`

---

**Question 5:** You are configuring a rolling update for a Managed Instance Group. You want to ensure that during the update, the total number of instances running (old and new combined) can temporarily exceed the MIG's target size. Which rolling update parameter should you configure?

- **Incorrect: A. `maxUnavailable`:** This specifies how many instances can be *unavailable* during the update, effectively reducing capacity temporarily.
- **Incorrect: B. `minReadySec`:** This is the time a new instance must be healthy before it's considered part of the updated set.
- **Correct: C. `maxSurge`:** This parameter allows the MIG to create a specified number or percentage of additional instances *above* its target size during an update. This is useful for maintaining capacity or even increasing it slightly while old instances are being replaced.
- **Incorrect: D. `cooldownPeriodSec`:** This is an autoscaler parameter, not directly for MIG rolling updates.

---

**Question 6:** Which Cloud Load Balancing component is responsible for inspecting the host and path of an incoming HTTP(S) request and directing it to the appropriate backend service?

- A. Forwarding Rule
- B. Target Proxy
- C. URL Map
- D. Health Check

---

**Question 6:** Which Cloud Load Balancing component is responsible for inspecting the host and path of an incoming HTTP(S) request and directing it to the appropriate backend service?

- **Incorrect: A. Forwarding Rule:** Defines the frontend IP, port, and protocol, and directs traffic to a target proxy.
- **Incorrect: B. Target Proxy:** Receives traffic from the forwarding rule and, for HTTP(S), consults the URL map.
- **Correct: C. URL Map:** This component contains rules that match against the host and path of HTTP(S) requests to determine which backend service should handle the request. It's central to content-based routing.
- **Incorrect: D. Health Check:** Monitors the health of backend instances.

---

**Question 7:** You need to load balance TCP traffic to internal applications within your VPC network. The client's source IP address must be preserved at the backend instances. Which load balancer is most suitable?

- A. Regional Internal HTTP(S) Load Balancer
- B. Global External TCP Proxy Load Balancer
- C. Regional Internal TCP/UDP Load Balancer
- D. Regional External Network Load Balancer

---

**Question 7:** You need to load balance TCP traffic to internal applications within your VPC network. The client's source IP address must be preserved at the backend instances. Which load balancer is most suitable?

- **Incorrect: A. Regional Internal HTTP(S) Load Balancer:** This is a Layer 7 proxy-based load balancer; while internal, it doesn't preserve client IP in the same way a pass-through L4 LB does.
- **Incorrect: B. Global External TCP Proxy Load Balancer:** This is for external traffic and is a proxy, so it does not preserve the client IP at the backend.
- **Correct: C. Regional Internal TCP/UDP Load Balancer:** This is a Layer 4 pass-through load balancer for internal traffic. It preserves the client's source IP address when clients are in the same VPC or region.
- **Incorrect: D. Regional External Network Load Balancer:** This is for external traffic, not internal.

---

**Question 8:** What is the primary benefit of using a Regional Managed Instance Group (MIG) over a Zonal MIG?

- A. Lower cost per instance.
- B. Faster instance startup times.
- C. Higher availability due to protection against zonal failures.
- D. Ability to use custom machine types.

---

**Question 8:** What is the primary benefit of using a Regional Managed Instance Group (MIG) over a Zonal MIG?

- **Incorrect: A. Lower cost per instance:** Instance pricing is based on machine type and other factors, not directly on whether the MIG is zonal or regional.
- **Incorrect: B. Faster instance startup times:** Startup time is primarily determined by the image and instance configuration, not the MIG type.
- **Correct: C. Higher availability due to protection against zonal failures:** Regional MIGs distribute instances across multiple zones in a region, so if one zone fails, the MIG can continue serving traffic from instances in other zones and recreate instances in healthy zones.
- **Incorrect: D. Ability to use custom machine types:** Both Zonal and Regional MIGs use instance templates, which can specify custom machine types.

---

**Question 9:** An autoscaler is configured with a `cooldownPeriodSec` of 120 seconds. What does this mean?

- A. The autoscaler will wait 120 seconds after a VM starts before considering it for health checks.
- B. The autoscaler will wait 120 seconds after a scaling event (up or down) before it can initiate another scaling event.
- C. Each instance created by the autoscaler will run for a minimum of 120 seconds.
- D. If the load drops, the autoscaler will wait 120 seconds before deciding to scale down.

---

**Question 9:** An autoscaler is configured with a `cooldownPeriodSec` of 120 seconds. What does this mean?

- **Incorrect: A. The autoscaler will wait 120 seconds after a VM starts before considering it for health checks:** This is related to the health check's `initialDelaySec` or the MIG's autohealing configuration.
- **Correct: B. The autoscaler will wait 120 seconds after a scaling event (up or down) before it can initiate another scaling event:** The cooldown period prevents the autoscaler from reacting too quickly to fluctuating load or adding instances before the previous ones are fully ready, thus avoiding oscillations.
- **Incorrect: C. Each instance created by the autoscaler will run for a minimum of 120 seconds:** Cooldown period relates to autoscaler decision frequency, not minimum instance runtime.
- **Incorrect: D. If the load drops, the autoscaler will wait 120 seconds before deciding to scale down:** This describes the stabilization period (or part of its logic), not the cooldown period.

---

**Question 10:** Which of the following backend types can be used with a Global External HTTP(S) Load Balancer to serve content directly from Google's serverless platforms?

- A. Target Pools
- B. Zonal Network Endpoint Groups (NEGs) of type `GCE_VM_IP_PORT`
- C. Serverless Network Endpoint Groups (NEGs)
- D. Instance Templates

---

**Question 10:** Which of the following backend types can be used with a Global External HTTP(S) Load Balancer to serve content directly from Google's serverless platforms?

- **Incorrect: A. Target Pools:** These are a legacy backend option for Network Load Balancers, not for HTTP(S) LBs or serverless platforms.
- **Incorrect: B. Zonal Network Endpoint Groups (NEGs) of type `GCE_VM_IP_PORT`:** These are for GCE VMs or containers running on GCE VMs.
- **Correct: C. Serverless Network Endpoint Groups (NEGs):** Serverless NEGs allow Global External HTTP(S) Load Balancers to use Cloud Run, App Engine, and Cloud Functions services as backends.
- **Incorrect: D. Instance Templates:** Instance templates define VMs for MIGs; they are not a direct backend type for serverless platforms.

---

**Question 11:** You want to implement a Web Application Firewall (WAF) to protect your internet-facing application, which is served by a Global External HTTP(S) Load Balancer. Which Google Cloud service integrates with this load balancer to provide WAF capabilities?

- A. Cloud CDN
- B. Identity-Aware Proxy (IAP)
- C. Google Cloud Armor
- D. VPC Service Controls

---

**Question 11:** You want to implement a Web Application Firewall (WAF) to protect your internet-facing application, which is served by a Global External HTTP(S) Load Balancer. Which Google Cloud service integrates with this load balancer to provide WAF capabilities?

- **Incorrect: A. Cloud CDN:** This is for caching content, not WAF.
- **Incorrect: B. Identity-Aware Proxy (IAP):** This controls access based on user identity, not primarily for WAF-style threat protection.
- **Correct: C. Google Cloud Armor:** This service provides DDoS protection and WAF capabilities, allowing you to create security policies (IP whitelists/blacklists, geo-blocking, preconfigured WAF rules) that integrate directly with Global External HTTP(S) Load Balancers.
- **Incorrect: D. VPC Service Controls:** This creates security perimeters around GCP services to prevent data exfiltration, not a WAF for external load balancers.

---

**Question 12:** What does "session affinity" configure on a load balancer's backend service?

- A. It ensures all backends are in the same geographic session.
- B. It encrypts user session data between the client and the load balancer.
- C. It attempts to route all requests from the same client to the same backend instance.
- D. It determines how long a user's session remains active before timing out.

---

**Question 12:** What does "session affinity" configure on a load balancer's backend service?

- **Incorrect: A. It ensures all backends are in the same geographic session:** This is not what session affinity means.
- **Incorrect: B. It encrypts user session data between the client and the load balancer:** Encryption is handled by SSL/TLS.
- **Correct: C. It attempts to route all requests from the same client to the same backend instance:** Session affinity (or "stickiness") is used when an application requires a client to consistently connect to the same backend for the duration of their session (e.g., if session state is stored locally on that backend). Methods include Client IP, Generated Cookie, or HTTP Header.
- **Incorrect: D. It determines how long a user's session remains active before timing out:** Session timeout is typically an application-level concern or related to connection draining, not the primary purpose of session affinity.

---

**Question 13:** Which type of Network Endpoint Group (NEG) would you use if you want your Global External HTTP(S) Load Balancer to send traffic to backends located on your on-premises network, reachable via Cloud VPN or Interconnect?

- A. Zonal NEG (`GCE_VM_IP_PORT`)
- B. Serverless NEG
- C. Internet NEG (`INTERNET_IP_PORT`)
- D. Hybrid Connectivity NEG (`NON_GCP_PRIVATE_IP_PORT`)

---

**Question 13:** Which type of Network Endpoint Group (NEG) would you use if you want your Global External HTTP(S) Load Balancer to send traffic to backends located on your on-premises network, reachable via Cloud VPN or Interconnect?

- **Incorrect: A. Zonal NEG (`GCE_VM_IP_PORT`):** These are for GCE VMs within Google Cloud.
- **Incorrect: B. Serverless NEG:** These are for Google Cloud serverless platforms.
- **Incorrect: C. Internet NEG (`INTERNET_IP_PORT`):** These are for publicly routable external endpoints, not typically for private connectivity via VPN/Interconnect.
- **Correct: D. Hybrid Connectivity NEG (`NON_GCP_PRIVATE_IP_PORT`):** These are specifically designed to represent on-premises or multi-cloud backends that are reachable via hybrid connectivity solutions like Cloud VPN or Cloud Interconnect.

---

**Question 14:** When enabling Cloud CDN on a backend service for a Global External HTTP(S) Load Balancer, what is the default `Cache Mode`?

- A. `FORCE_CACHE_ALL`
- B. `USE_ORIGIN_HEADERS`
- C. `CACHE_ALL_STATIC`
- D. `NO_CACHE`

---

**Question 14:** When enabling Cloud CDN on a backend service for a Global External HTTP(S) Load Balancer, what is the default `Cache Mode`?

- **Incorrect: A. `FORCE_CACHE_ALL`:** This mode caches all content and can be risky if not configured carefully with TTLs. It's not the default.
- **Incorrect: B. `USE_ORIGIN_HEADERS`:** This mode strictly follows `Cache-Control` headers from the origin. While a valid mode, it's not the default.
- **Correct: C. `CACHE_ALL_STATIC`:** This is the default mode. Cloud CDN attempts to cache common static content types (based on `Content-Type` header and other heuristics) and also respects `Cache-Control` headers from the origin for those static assets.
- **Incorrect: D. `NO_CACHE`:** This would effectively disable caching, which is contrary to enabling CDN.

---

**Question 15:** A Managed Instance Group is configured for self-healing based on an HTTP health check. If an instance fails this health check repeatedly beyond the configured unhealthy threshold, what action will the MIG take?

- A. It will stop the instance and wait for manual intervention.
- B. It will attempt to SSH into the instance and restart the application.
- C. It will mark the instance as unhealthy but keep it running to preserve logs.
- D. It will delete the unhealthy instance and recreate a new one from the instance template.

---

**Question 15:** A Managed Instance Group is configured for self-healing based on an HTTP health check. If an instance fails this health check repeatedly beyond the configured unhealthy threshold, what action will the MIG take?

- **Incorrect: A. It will stop the instance and wait for manual intervention:** Self-healing is designed to be automatic.
- **Incorrect: B. It will attempt to SSH into the instance and restart the application:** MIG self-healing doesn't involve SSHing or application-level restarts; it operates at the instance level.
- **Incorrect: C. It will mark the instance as unhealthy but keep it running to preserve logs:** While it's marked unhealthy, the primary autohealing action is replacement. Log preservation would need separate configuration (e.g., log agent).
- **Correct: D. It will delete the unhealthy instance and recreate a new one from the instance template:** This is the core function of MIG self-healing: replace unhealthy instances to maintain the desired number of healthy, running instances.

---

**Question 16:** Which scaling signal for an autoscaler is most appropriate for a worker application that processes messages from a Pub/Sub subscription, where the goal is to keep the number of unacknowledged messages low?

- A. Average CPU utilization.
- B. Schedule-based scaling.
- C. Load balancing serving capacity.
- D. A Cloud Monitoring metric like `pubsub.googleapis.com/subscription/num_undelivered_messages`.

---

**Question 16:** Which scaling signal for an autoscaler is most appropriate for a worker application that processes messages from a Pub/Sub subscription, where the goal is to keep the number of unacknowledged messages low?

- **Incorrect: A. Average CPU utilization:** While CPU might correlate with message processing, the queue length (undelivered messages) is a more direct indicator of workload and backlog.
- **Incorrect: B. Schedule-based scaling:** Message arrival might not follow a predictable schedule.
- **Incorrect: C. Load balancing serving capacity:** This is relevant for load-balanced services, not typically for a Pub/Sub worker pool that pulls messages.
- **Correct: D. A Cloud Monitoring metric like `pubsub.googleapis.com/subscription/num_undelivered_messages`:** Scaling based on the number of undelivered messages directly addresses the backlog. If the count is high, scale out; if low, scale in.

---

**Question 17:** Which type of external load balancer in Google Cloud preserves the client's original source IP address all the way to the backend instances?

- A. Global External HTTP(S) Load Balancer
- B. Global External SSL Proxy Load Balancer
- C. Regional External Network Load Balancer (TCP/UDP)
- D. Global External TCP Proxy Load Balancer

---

**Question 17:** Which type of external load balancer in Google Cloud preserves the client's original source IP address all the way to the backend instances?

- **Incorrect: A. Global External HTTP(S) Load Balancer:** This is a proxy; the source IP seen by backends is from Google's proxy systems (though X-Forwarded-For header contains client IP).
- **Incorrect: B. Global External SSL Proxy Load Balancer:** This is a proxy and terminates the client connection.
- **Correct: C. Regional External Network Load Balancer (TCP/UDP):** This is a pass-through load balancer that directly forwards packets to backends, preserving the client's source IP address.
- **Incorrect: D. Global External TCP Proxy Load Balancer:** This is a proxy and terminates the client connection.

---

**Question 18:** You are setting up an Internal HTTP(S) Load Balancer. Where is its frontend IP address provisioned from?

- A. A global Anycast IP address range.
- B. A regional external IP address range.
- C. A dedicated proxy-only subnet within your VPC in the chosen region.
- D. The default gateway IP of the subnet where your backends reside.

---

**Question 18:** You are setting up an Internal HTTP(S) Load Balancer. Where is its frontend IP address provisioned from?

- **Incorrect: A. A global Anycast IP address range:** Internal LBs use internal, regional IPs.
- **Incorrect: B. A regional external IP address range:** Internal LBs use internal IPs.
- **Correct: C. A dedicated proxy-only subnet within your VPC in the chosen region:** Internal HTTP(S) Load Balancers are managed services that use Envoy proxies. These proxies require IP addresses from a special "proxy-only subnet" that you create in your VPC for this purpose.
- **Incorrect: D. The default gateway IP of the subnet where your backends reside:** The LB has its own distinct internal IP.

---

**Question 19:** What is the primary difference in how a Global External HTTP(S) Load Balancer and a Regional External Network Load Balancer handle incoming traffic?

- A. HTTP(S) LB uses URL maps for routing; Network LB uses target pools/backend services without URL maps.
- B. HTTP(S) LB is regional; Network LB is global.
- C. HTTP(S) LB preserves client IP; Network LB is a proxy.
- D. HTTP(S) LB is for internal traffic; Network LB is for external traffic.

---

**Question 19:** What is the primary difference in how a Global External HTTP(S) Load Balancer and a Regional External Network Load Balancer handle incoming traffic?

- **Correct: A. HTTP(S) LB uses URL maps for routing; Network LB uses target pools/backend services without URL maps:** Global External HTTP(S) LB is a Layer 7 LB that uses URL maps for content-based routing. Regional External Network LB is a Layer 4 pass-through (or can use backend services for more advanced health checking but still L4 logic) and doesn't use URL maps.
- **Incorrect: B. HTTP(S) LB is regional; Network LB is global:** It's the other way around for the *Global* HTTP(S) LB.
- **Incorrect: C. HTTP(S) LB preserves client IP; Network LB is a proxy:** Regional External Network LB (pass-through) preserves client IP. Global HTTP(S) LB is a proxy (client IP in XFF header).
- **Incorrect: D. HTTP(S) LB is for internal traffic; Network LB is for external traffic:** Both described here are *External* Load Balancers.

---

**Question 20:** If an autoscaler has multiple scaling policies configured (e.g., one for CPU, one for a custom metric), how does it determine the final number of instances for the MIG?

- A. It averages the recommendations from all policies.
- B. It picks the policy that recommends the smallest number of instances to save cost.
- C. It picks the policy that recommends the largest number of instances to ensure performance and availability.
- D. It uses the first policy defined and ignores the others.

---

**Question 20:** If an autoscaler has multiple scaling policies configured (e.g., one for CPU, one for a custom metric), how does it determine the final number of instances for the MIG?

- **Incorrect: A. It averages the recommendations from all policies:** This could lead to under-provisioning if one critical metric demands more instances.
- **Incorrect: B. It picks the policy that recommends the smallest number of instances to save cost:** This would prioritize cost over performance and could lead to overload if another metric indicates a need for more capacity.
- **Correct: C. It picks the policy that recommends the largest number of instances to ensure performance and availability:** The autoscaler evaluates all active policies and chooses the one that results in the highest number of instances. This ensures that if any signal indicates a need for more capacity, that need is met.
- **Incorrect: D. It uses the first policy defined and ignores the others:** All active policies are considered.
