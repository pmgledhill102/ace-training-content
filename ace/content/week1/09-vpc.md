---
class: gslides-content-slide
---

# VPC Networks: Global, Private Cloud Environments

* **What:** Virtual Private Cloud (VPC) provides a logically isolated section of Google Cloud where you can launch resources in a virtual network that you define.
* **Global Resource:** Unlike traditional networks, a single Google Cloud VPC network is a **global** resource. It is not associated with any specific region or zone initially.
    * This allows you to connect resources across different regions privately without needing complex VPNs or peering (though subnets *are* regional).
* **Isolation:** Provides network isolation for your projects. Resources within a VPC can communicate using internal IP addresses, shielded from the public internet unless explicitly allowed.
* **Default VPC:** New projects typically start with a `default` VPC network pre-configured with subnets in each available region, simplifying initial setup.
* **Custom VPCs:** Offer full control over IP address ranges, subnets, firewall rules, and routing. Recommended for production environments.

---

# Subnets: Regional IP Address Ranges

* **What:** While the VPC network itself is global, **Subnets** (subnetworks) are **regional** resources within a VPC.
* **Purpose:** Each subnet defines a specific range of IP addresses (a **CIDR block**, e.g., `10.128.0.0/20`) available for resources within that subnet.
* **Regional Scope:** A subnet exists within a single region (e.g., `us-central1`). Resources like Compute Engine VMs must be launched into a specific subnet within a zone that belongs to the subnet's region.
* **IP Allocation:** When a VM is created in a subnet, it gets an internal IP address from that subnet's primary CIDR range. Google reserves the first two and last two addresses in each subnet range.
* **Customization:** In custom VPCs, you define the subnets and their CIDR ranges. Ranges cannot overlap between subnets within the same VPC. You can expand CIDR ranges later if needed (within limits).

---

# Firewall Rules: Controlling Traffic Flow

* **What:** VPC Firewall Rules act as a distributed, stateful firewall controlling traffic to and from resources (primarily Compute Engine VMs) within your VPC network.
* **Key Components:**
    * **Direction:** `Ingress` (incoming) or `Egress` (outgoing).
    * **Action:** `Allow` or `Deny` matching traffic.
    * **Source/Destination:** IP ranges (CIDR) for source (ingress) or destination (egress). `0.0.0.0/0` means "any IP". Can also use Tags or Service Accounts.
    * **Protocols/Ports:** Specify protocols (e.g., `tcp`, `udp`, `icmp`) and ports (e.g., `tcp:80`, `tcp:22`, `udp:53`).
    * **Target:** Defines which instances the rule applies to (e.g., `All instances`, instances with specific **Network Tags**, instances using specific Service Accounts). Using Tags is common for grouping servers (e.g., `web-server`, `db-server`).
    * **Priority:** Number (0-65535, lower number = higher priority) determining evaluation order.

---

# Firewall Rules: Controlling Traffic Flow (Cont)

* **Implicit Rules:** Every VPC has two implied, lowest-priority (65535) rules:
    * **Deny all ingress:** Blocks all incoming traffic unless explicitly allowed by a higher-priority rule.
    * **Allow all egress:** Allows all outgoing traffic unless explicitly denied by a higher-priority rule.
* **Security Posture:** Requires you to explicitly *allow* desired inbound traffic.

---

# Routes: Directing Network Traffic

* **What:** Routes define paths for network traffic leaving resources (like VMs) within your VPC network. They tell packets where to go next based on their destination IP address.
* **Route Table:** Each VPC network has an associated route table. Routes are automatically applied to resources within the VPC.
* **System-Generated Routes:**
    * **Default Route:** (Priority 1000) Directs traffic destined for outside the VPC (e.g., the internet) to the internet gateway. You typically need a VM with an external IP or Cloud NAT for this to work.
    * **Subnet Routes:** Automatically created for each subnet, directing traffic destined for IPs within that subnet's range to stay within the VPC network for local delivery.

---

# Routes: Directing Network Traffic (Cont.)

* **Custom Routes:** You can create custom routes to override system routes or define specific paths.
    * **Common Use Cases:**
        * Directing traffic to a VPN tunnel or Cloud Interconnect for on-premises connectivity.
        * Routing traffic through a Network Virtual Appliance (NVA) like a custom firewall or NAT gateway.
        * Overriding the default internet route for specific destinations.
* **Next Hop:** Each route specifies a "next hop" – where the traffic should be sent (e.g., `Default internet gateway`, specific VM instance, VPN tunnel).
