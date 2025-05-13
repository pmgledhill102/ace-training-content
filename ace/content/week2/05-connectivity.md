# VPC Connectivity Options

---
zoom: 0.7
---

# Standalone Project

![Default Network Subnets Screenshot](/shared-vpc-1.png)

<!--
- One Project
- Two Subnets
- An instance in each
-->

---

# VPC Network Peering

* **What is it?** Allows private RFC 1918 connectivity between two VPC networks using Google's internal backbone, regardless of whether they are in the same project or organization.
* **How it Works:**
  * Each VPC network maintains its own routes and firewall rules.
  * When peered, subnet routes from each network are exchanged, allowing resources to communicate using internal IP addresses.
  * Traffic stays within Google's network, not traversing the public internet.
* **Key Characteristics:**
  * **Non-Transitive:** If VPC-A is peered with VPC-B, and VPC-B is peered with VPC-C, VPC-A *cannot* automatically reach VPC-C through VPC-B. Direct peering between A and C would be required.
  * **No Overlapping IP CIDR Ranges:** Subnet IP ranges in peered networks must not overlap.
  * **Firewall Rules Not Exchanged:** Each VPC enforces its own firewall rules. You must ensure firewall rules in *both* VPCs allow the desired traffic between peered subnets.
  * **IAM Permissions:**  `compute.networks.addPeering` and `compute.networks.removePeering` needed.

---

# VPC Network Peering

* **Configuration Steps:**
    1. Create a peering connection from VPC-A to VPC-B.
    2. Create a corresponding peering connection from VPC-B to VPC-A.
    3. Once both are established and active, routes are exchanged.
* **Use Cases:**
  * Connecting services in different projects/VPCs within the same organization.
  * SaaS providers offering private connectivity to their services from customer VPCs.
  * Merging networks after company acquisitions.
  * Segmenting workloads into different VPCs for administrative or security boundaries while allowing inter-communication.

---

![Default Network Subnets Screenshot](/network-peering.png)

<!--

* **Configuration Steps:**
    1.  Create a peering connection from VPC-A to VPC-B.
    2.  Create a corresponding peering connection from VPC-B to VPC-A.
    3.  Once both are established and active, routes are exchanged.
-->

---
zoom: 0.9
---

# Shared VPC (XPN) - Architecture & Benefits

* **What is Shared VPC (Cross-Project Networking - XPN)?** Allows an organization to connect resources from multiple **Service Projects** to a common, centrally managed VPC network in a **Host Project**.
* **Key Roles:**
  * **Host Project:** Owns and controls the Shared VPC network (subnets, routes, firewalls).
  * **Service Projects:** Projects that are attached to the Host Project. Can create and manage their own resources (e.g., VMs, GKE clusters, Cloud SQL instances) that use subnets from the Host Project's Shared VPC.
* **What Can Be Shared:**
  * The Host Project administrator can choose to share all subnets or specific subnets with Service Projects.
* **Benefits:**
  * **Centralized Network Administration:** Network policies, security rules, and connectivity are managed centrally by a network admin team in the Host Project.
  * **Separation of Duties:** Application teams in Service Projects can manage their resources without needing network admin privileges.
  * **Consistent Network Policies:** Ensures all connected resources adhere to common security and connectivity standards.
  * **Simplified IP Management:** Avoids IP conflicts and simplifies IP allocation across an organization.

---
zoom: 0.9
---

# Shared VPC (XPN) - Use Cases & Permissions

* **Use Cases:**
  * Large enterprises with a dedicated central networking team.
  * Organizations needing to enforce consistent network security policies across multiple projects or departments.
  * Multi-tenant applications where tenants (in Service Projects) need access to a shared network infrastructure.
* **IAM Roles for Shared VPC:**
  * **Host Project Admin (`compute.xpnAdmin`):** Configures Shared VPC, attaches Service Projects.
  * **Service Project Admin (`compute.xpnHostUser` - less common):** Can use shared network resources.
  * **Service Project User (`compute.networkUser`):** Granted to users/service accounts in Service Projects to allow them to create resources that use the shared network. This role is granted at the Host Project level or on specific shared subnets.

---
zoom: 0.7
---

# Shared VPC

![Default Network Subnets Screenshot](/shared-vpc-2.png)

<!--
- Host Project
- Two Subnets
- 2 Service Projects
- Allocated IP in Host project's subnet
-->

---

# Cloud VPN - Types

* **What is Cloud VPN?** Securely connects your on-premises network or another cloud provider's network to your Google Cloud VPC network over an IPsec VPN tunnel.
* **Types of Cloud VPN Gateways:**
  * **Classic VPN: (deprecated on August 1, 2025)**
    * Supports static or policy-based routing.
    * Offers a 99.9% availability SLA.
    * Uses a regional VPN gateway resource.
    * Does not support dynamic routing (BGP) directly on the gateway.
  * **HA (High Availability) VPN:**
    * **Recommended option.** Offers a 99.99% availability SLA when configured correctly.
    * Uses an external VPN gateway resource in GCP representing your peer gateway, and a HA VPN gateway in GCP with two interfaces, each with its own external IP.
    * Requires two tunnels from your peer gateway to the two interfaces on the GCP HA VPN gateway.
    * **Supports dynamic routing (BGP)** with Cloud Router for automatic route exchange and failover.
    * Can also support static routing.

---

# Cloud VPN - Use Cases

* **Routing Options:**
  * **Static Routing:** Manually define routes on both sides of the tunnel. Simpler for small networks but less scalable and resilient.
  * **Dynamic Routing (BGP):** Use with Cloud Router (especially for HA VPN). Routes are learned and advertised automatically, enabling more complex topologies and faster failover.
  * **Policy-based Routing (Classic VPN):** Defines "interesting traffic" using local/remote IP selectors.
  * **Route-based VPN (Classic VPN):** Simpler than policy-based, defines a tunnel and routes traffic to it.
* **IPsec Configuration:** Uses IKEv1 or IKEv2 for key exchange.
* **Use Cases:**
  * Connecting on-premises data centers to GCP for hybrid cloud scenarios.
  * Linking development/test environments in GCP to corporate networks.
  * Connecting VPCs to networks in other cloud providers.
  * Securely accessing GCP resources from remote offices or users (though BeyondCorp Enterprise is often better for user access).

---
zoom: 0.95
---

# Cloud Interconnect

* **What is Cloud Interconnect?** Provides high-bandwidth, low-latency, private connectivity between your on-premises network and your Google Cloud VPC network. Traffic does not traverse the public internet.
* **Options:**
  * **Dedicated Interconnect:**
    * A direct physical connection between your on-premises network and Google's network at a Google colocation facility.
    * Provides **10 Gbps or 100 Gbps** circuits (one or more).
    * You are responsible for the circuit from your premises to the colocation facility.
    * Highest performance and reliability.
  * **Partner Interconnect:**
    * Connect to Google's network through a supported service provider partner.
    * Offers various bandwidth options (e.g., **50 Mbps to 50 Gbps**).
    * The partner provides the circuit from your premises to their network, which then connects to Google.
    * Often more flexible and quicker to provision than Dedicated Interconnect if you're not already in a Google colocation.

---
zoom: 0.95
---

# Cloud Interconnect

* **Benefits:**
  * **High Bandwidth & Low Latency:** Significantly better than internet-based connections or VPN.
  * **Consistent Performance:** More predictable network performance.
  * **Increased Security:** Private connectivity, traffic doesn't traverse the public internet.
  * **Potential Cost Savings:** Egress traffic from GCP over an Interconnect is often cheaper than standard internet egress (especially at high volumes).
* **Use Cases:**
  * Large-scale data migrations to GCP.
  * Hybrid applications requiring high-performance, reliable connectivity between on-premises and GCP.
  * Extending on-premises data centers to the cloud.
  * Workloads sensitive to network jitter or latency (e.g., real-time applications, large database synchronization).
