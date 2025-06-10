
**Question 1:** Your development team has built a containerized application and pushed the image to Artifact Registry. You need to deploy this application to a new GKE cluster.

Which of the following is the correct sequence of steps to accomplish this?

- A. Deploy the application using `kubectl apply`, then create the GKE cluster.
- B. Create the GKE cluster, configure `kubectl` to connect to it, then deploy the application using `kubectl apply`.
- C. Create a VM instance, install `kubectl` and the container, then create the GKE cluster.
- D. Configure `kubectl`, deploy the application, and let GKE automatically provision a cluster.

---

**Question 1:** Your development team has built a containerized application and pushed the image to Artifact Registry. You need to deploy this application to a new GKE cluster.

Which of the following is the correct sequence of steps to accomplish this?

- **Incorrect: A. Deploy the application using `kubectl apply`, then create the GKE cluster.** You cannot deploy an application to a cluster that does not exist. The cluster must be created and accessible first.
- **Correct: B. Create the GKE cluster, configure `kubectl` to connect to it, then deploy the application using `kubectl apply`.** This follows the standard workflow. [cite_start]You must first provision the GKE cluster, then configure your local command-line interface to authenticate with the cluster, and finally deploy your containerized application using `kubectl`. [cite: 11, 2012]
- **Incorrect: C. Create a VM instance, install `kubectl` and the container, then create the GKE cluster.** Deploying containers directly to a VM is not the standard way to deploy to GKE. The container should be deployed to the cluster itself.
- **Incorrect: D. Configure `kubectl`, deploy the application, and let GKE automatically provision a cluster.** GKE does not automatically provision a cluster when you attempt to deploy an application. You must explicitly create the cluster.

---

**Question 2:** You need to deploy a GKE cluster for a stateless web application. The operations team wants to minimize management overhead by having Google manage the nodes, scaling, and security configurations of the control plane and the nodes.

Which GKE configuration mode should you choose?

- A. Regional Cluster
- B. Private Cluster
- C. GKE Enterprise
- D. Autopilot

---

**Question 2:** You need to deploy a GKE cluster for a stateless web application. The operations team wants to minimize management overhead by having Google manage the nodes, scaling, and security configurations of the control plane and the nodes.

Which GKE configuration mode should you choose?

- **Incorrect: A. Regional Cluster.** A regional cluster provides high availability for the control plane and nodes across multiple zones, but you are still responsible for managing the node pools and their configuration.
- **Incorrect: B. Private Cluster.** A private cluster restricts node and control plane access to internal IP addresses, which enhances security but does not reduce the management overhead of the nodes themselves.
- **Incorrect: C. GKE Enterprise.** GKE Enterprise offers advanced features for multi-cluster management and governance but does not inherently abstract away node management in the way that Autopilot does.
- **Correct: D. Autopilot.** GKE Autopilot is a mode of operation where Google fully manages the cluster's underlying infrastructure, including nodes and node pools. [cite_start]This minimizes operational overhead and allows you to focus on the workloads. [cite: 11]

---

**Question 3:** As an administrator for a GKE cluster, you need to see a list of all the running Pods in the `default` namespace and check their current status.

Which `kubectl` command should you use?

- A. `kubectl get nodes`
- B. `kubectl get services`
- C. `kubectl get pods`
- D. `kubectl describe cluster`

---

**Question 3:** As an administrator for a GKE cluster, you need to see a list of all the running Pods in the `default` namespace and check their current status.

Which `kubectl` command should you use?

- **Incorrect: A. `kubectl get nodes`** This command lists the worker nodes (VM instances) that are part of the cluster, not the application Pods running on them.
- **Incorrect: B. `kubectl get services`** This command lists the Kubernetes Service resources, which are used to expose applications and provide networking, not the Pods themselves.
- **Correct: C. `kubectl get pods`** This command is used to view the current running Pod inventory. [cite_start]By default, it lists Pods in the current namespace (`default` unless specified otherwise) and shows their status (e.g., Running, Pending, CrashLoopBackOff). [cite: 17]
- **Incorrect: D. `kubectl describe cluster`** This is not a standard `kubectl` command. To get information about the cluster, you would typically use `gcloud container clusters describe`.

---

**Question 4:** You are running a GKE cluster with a single node pool. You need to add a new set of nodes with a more powerful machine type, including GPUs, to run machine learning workloads without affecting the existing applications.

What GKE feature should you use?

- A. Create a new GKE cluster with the desired machine type and migrate all workloads.
- B. Perform a rolling update on the existing node pool to change its machine type.
- C. Add a new node pool to the existing cluster with the specified machine type and GPUs.
- D. Manually create Compute Engine instances with GPUs and join them to the cluster.

---

**Question 4:** You are running a GKE cluster with a single node pool. You need to add a new set of nodes with a more powerful machine type, including GPUs, to run machine learning workloads without affecting the existing applications.

What GKE feature should you use?

- **Incorrect: A. Create a new GKE cluster with the desired machine type and migrate all workloads.** This is disruptive and more complex than necessary. GKE allows for heterogeneous node configurations within a single cluster.
- **Incorrect: B. Perform a rolling update on the existing node pool to change its machine type.** While you can update a node pool, changing the machine type would affect all existing workloads scheduled on it and might not be suitable if they don't require GPUs.
- **Correct: C. Add a new node pool to the existing cluster with the specified machine type and GPUs.** GKE supports multiple node pools, allowing you to have different machine types, autoscaling settings, and hardware (like GPUs) within the same cluster. [cite_start]This lets you schedule specific workloads to the appropriate nodes without disrupting others. [cite: 17]
- **Incorrect: D. Manually create Compute Engine instances with GPUs and join them to the cluster.** While GKE nodes are Compute Engine instances, they should be managed through the GKE control plane via node pools, not manually.

---

**Question 5:** Your GKE application is experiencing sudden traffic spikes. You want to automatically increase the number of Pods running the application when CPU utilization gets high, and decrease the number of Pods when the traffic subsides.

Which Kubernetes feature should you configure?

- A. Vertical Pod Autoscaler (VPA)
- B. Cluster Autoscaler
- C. Horizontal Pod Autoscaler (HPA)
- D. Node pool version upgrades

---

**Question 5:** Your GKE application is experiencing sudden traffic spikes. You want to automatically increase the number of Pods running the application when CPU utilization gets high, and decrease the number of Pods when the traffic subsides.

Which Kubernetes feature should you configure?

- **Incorrect: A. Vertical Pod Autoscaler (VPA).** VPA automatically adjusts the CPU and memory *requests* for Pods, scaling them "up" or "down" to match resource needs. [cite_start]It does not change the number of Pods. [cite: 17]
- **Incorrect: B. Cluster Autoscaler.** The Cluster Autoscaler automatically adds or removes *nodes* in a node pool based on the resource demands of the Pods. It works in conjunction with HPA but doesn't scale the Pods directly.
- **Correct: C. Horizontal Pod Autoscaler (HPA).** HPA automatically scales the number of Pods in a deployment or replicaset based on observed metrics like CPU utilization or custom metrics. [cite_start]This is the correct tool for scaling out the number of replicas in response to load. [cite: 17]
- **Incorrect: D. Node pool version upgrades.** This is a maintenance operation for updating the Kubernetes version on the nodes and is unrelated to application scaling.

---

**Question 6:** Your organization wants to ensure that all container images used in your GKE clusters are stored in a centrally managed, secure location within Google Cloud. You need to configure your GKE cluster to pull images from this service.

Which Google Cloud service should the GKE cluster be configured to access?

- A. Cloud Storage
- B. Artifact Registry
- C. Cloud Source Repositories
- D. Container Registry

---

**Question 6:** Your organization wants to ensure that all container images used in your GKE clusters are stored in a centrally managed, secure location within Google Cloud. You need to configure your GKE cluster to pull images from this service.

Which Google Cloud service should the GKE cluster be configured to access?

- **Incorrect: A. Cloud Storage.** Cloud Storage is a general-purpose object store and is not a container registry.
- **Correct: B. Artifact Registry.** Artifact Registry is the recommended service for storing and managing container images and other artifacts in Google Cloud. [cite_start]GKE clusters must be configured with the appropriate permissions to pull images from it. [cite: 17]
- **Incorrect: C. Cloud Source Repositories.** This service is a private Git repository for storing source code, not built container images.
- **Incorrect: D. Container Registry.** While Container Registry is a predecessor service, Artifact Registry is the evolution and recommended service for new projects, offering repository modes and regional locations.

---

**Question 7:** For a production-critical application, you need to ensure the GKE control plane is highly available and resilient to a single zonal failure.

Which type of GKE cluster configuration provides this resilience by default?

- A. A single-zone cluster
- B. A private cluster
- C. A regional cluster
- D. An Autopilot cluster

---

**Question 7:** For a production-critical application, you need to ensure the GKE control plane is highly available and resilient to a single zonal failure.

Which type of GKE cluster configuration provides this resilience by default?

- **Incorrect: A. A single-zone cluster.** A single-zone cluster has its control plane and nodes located in a single zone, making it vulnerable to a zonal failure.
- **Incorrect: B. A private cluster.** A private cluster enhances network security but doesn't inherently change the availability characteristics related to zones.
- **Correct: C. A regional cluster.** A regional cluster replicates the control plane across multiple zones within a region. [cite_start]If one zone becomes unavailable, the control plane remains accessible, and workloads can run in the remaining zones, providing high availability. [cite: 11]
- **Incorrect: D. An Autopilot cluster.** While Autopilot clusters are regional by default, the core feature that provides this specific resilience is the "regional" aspect, which is also available in GKE Standard.

---

**Question 8:** You have deployed an application to GKE using a Deployment that creates several Pods. You need to expose this application to other applications within the same cluster using a stable hostname and IP address.

Which Kubernetes resource should you create?

- A. An Ingress
- B. A Service
- C. A StatefulSet
- D. A Network Policy

---

**Question 8:** You have deployed an application to GKE using a Deployment that creates several Pods. You need to expose this application to other applications within the same cluster using a stable hostname and IP address.

Which Kubernetes resource should you create?

- **Incorrect: A. An Ingress.** An Ingress is used to manage external HTTP/S access to services within the cluster, not for internal service-to-service communication.
- **Correct: B. A Service.** A Kubernetes Service provides a stable abstraction over a set of Pods. It creates a stable internal IP address and DNS name that other applications can use to access the Pods, regardless of individual Pod failures or IP changes. [cite_start]This is a fundamental concept of working with Kubernetes resources. [cite: 17]
- **Incorrect: C. A StatefulSet.** A StatefulSet is used for managing stateful applications and provides stable identities for its Pods, but it's not the primary mechanism for exposing a set of Pods as a network service.
- **Incorrect: D. A Network Policy.** A Network Policy is a security mechanism used to control traffic flow between Pods; it doesn't expose them as a service.

---

**Question 9:** You are operating a GKE cluster and need to ensure that detailed logs and metrics from the Kubernetes system components, such as the API Server and Scheduler, are collected for troubleshooting and monitoring.

How is this typically enabled for a GKE cluster?

- A. It must be configured manually by deploying Prometheus and Fluentd to the cluster.
- B. It is enabled by default when a GKE cluster is created on Google Cloud.
- C. It requires installing the Ops Agent on the control plane nodes.
- D. It is configured through Firewall Rules Logging.

---

**Question 9:** You are operating a GKE cluster and need to ensure that detailed logs and metrics from the Kubernetes system components, such as the API Server and Scheduler, are collected for troubleshooting and monitoring.

How is this typically enabled for a GKE cluster?

- **Incorrect: A. It must be configured manually by deploying Prometheus and Fluentd to the cluster.** While you can deploy your own monitoring stack, GKE provides a managed, integrated solution.
- [cite_start]**Correct: B. It is enabled by default when a GKE cluster is created on Google Cloud.** When you create a GKE cluster on Google Cloud, integration with Cloud Logging and Cloud Monitoring is enabled by default, providing observability specifically tailored for Kubernetes, including control plane logs and metrics. [cite: 123, 126]
- **Incorrect: C. It requires installing the Ops Agent on the control plane nodes.** The control plane is managed by Google, and you do not have access to install agents on its nodes. The integration is native.
- **Incorrect: D. It is configured through Firewall Rules Logging.** Firewall Rules Logging tracks firewall rule evaluations and is unrelated to collecting logs from Kubernetes components.

---

**Question 10:** An application running on your GKE cluster needs to access a Cloud SQL database. To follow the principle of least privilege, you want to grant database access specifically to the Pods of this application without using long-lived credentials.

What is the recommended approach for this?

- A. Create a service account key, store it as a Kubernetes secret, and mount it into the Pods.
- B. Use Workload Identity to associate a Kubernetes service account with a Google Cloud service account.
- C. Grant the `Cloud SQL Client` role to the GKE node's service account.
- D. Hardcode the database credentials directly into the application's container image.

---

**Question 10:** An application running on your GKE cluster needs to access a Cloud SQL database. To follow the principle of least privilege, you want to grant database access specifically to the Pods of this application without using long-lived credentials.

What is the recommended approach for this?

- **Incorrect: A. Create a service account key, store it as a Kubernetes secret, and mount it into the Pods.** This involves managing static, long-lived keys, which is less secure than using short-lived credentials.
- **Correct: B. Use Workload Identity to associate a Kubernetes service account with a Google Cloud service account.** Workload Identity is the recommended way for GKE workloads to access Google Cloud services. It allows you to bind a Kubernetes service account to a Google Cloud service account, letting Pods automatically authenticate using short-lived credentials without the need for exporting keys.
- **Incorrect: C. Grant the `Cloud SQL Client` role to the GKE node's service account.** This would grant access to *all* Pods running on that node, violating the principle of least privilege.
- **Incorrect: D. Hardcode the database credentials directly into the application's container image.** This is a major security risk and should always be avoided.

---

**Question 11:** Your security team requires that no resources in your GKE cluster, including the nodes, have public IP addresses to minimize the external attack surface. However, workloads in the cluster still need to be able to access the public internet for tasks like pulling external dependencies.

Which GKE cluster configuration and Google Cloud networking service would you use together to meet these requirements?

- A. A regional cluster with Cloud Armor.
- B. A GKE Enterprise cluster with Service Mesh.
- C. A private GKE cluster with Cloud NAT.
- D. A public GKE cluster with VPC firewall rules blocking all ingress.

---

**Question 11:** Your security team requires that no resources in your GKE cluster, including the nodes, have public IP addresses to minimize the external attack surface. However, workloads in the cluster still need to be able to access the public internet for tasks like pulling external dependencies.

Which GKE cluster configuration and Google Cloud networking service would you use together to meet these requirements?

- **Incorrect: A. A regional cluster with Cloud Armor.** Cloud Armor is for DDoS protection and WAF policies for external load balancers; it doesn't control outbound internet access for nodes.
- **Incorrect: B. A GKE Enterprise cluster with Service Mesh.** A service mesh manages traffic between services but is not the primary tool for enabling outbound internet access from nodes without public IPs.
- [cite_start]**Correct: C. A private GKE cluster with Cloud NAT.** A private GKE cluster ensures nodes do not have public IP addresses. [cite: 11] [cite_start]Cloud NAT allows instances without external IP addresses to send outbound traffic to the internet through a managed gateway. [cite: 345, 352]
- **Incorrect: D. A public GKE cluster with VPC firewall rules blocking all ingress.** While this would block incoming traffic, the nodes in a public cluster would still have public IP addresses, which violates the primary security requirement.

---

**Question 12:** You are using Helm to manage deployments on your GKE cluster. You need to deploy a new version of a Redis chart from a public repository.

Which Infrastructure as Code tool and command are you most likely to use?

- A. Terraform, using `terraform apply`
- B. `kubectl apply -f redis.yaml`
- C. Helm, using `helm install`
- D. Config Connector, by creating a `Redis` CRD.

---

**Question 12:** You are using Helm to manage deployments on your GKE cluster. You need to deploy a new version of a Redis chart from a public repository.

Which Infrastructure as Code tool and command are you most likely to use?

- **Incorrect: A. Terraform, using `terraform apply`** While Terraform can manage GKE clusters, Helm is the specific tool designed for managing pre-packaged Kubernetes applications called charts.
- **Incorrect: B. `kubectl apply -f redis.yaml`** This command deploys resources from a raw Kubernetes manifest file. Helm provides templating, versioning, and lifecycle management on top of this.
- **Correct: C. Helm, using `helm install`** Helm is a package manager for Kubernetes that simplifies deploying and managing applications. The `helm install` command is used to deploy a "chart" (a package of pre-configured Kubernetes resources) to a cluster. [cite_start]Helm is listed as an IaC tool in the exam guide. [cite: 15]
- **Incorrect: D. Config Connector, by creating a `Redis` CRD.** Config Connector is used to manage Google Cloud resources (like Cloud SQL instances) via Kubernetes APIs, not for deploying open-source applications like Redis *onto* a cluster.

---

**Question 13:** You need to deploy a stateful application, such as a database, to your GKE cluster. This application requires that each replica has a stable, unique network identifier and its own persistent storage.

Which Kubernetes workload resource is designed for this use case?

- A. Deployment
- B. DaemonSet
- C. Job
- D. StatefulSet

---

**Question 13:** You need to deploy a stateful application, such as a database, to your GKE cluster. This application requires that each replica has a stable, unique network identifier and its own persistent storage.

Which Kubernetes workload resource is designed for this use case?

- **Incorrect: A. Deployment.** Deployments are best suited for stateless applications where any replica can be replaced by another without loss of identity or state.
- **Incorrect: B. DaemonSet.** A DaemonSet ensures that one copy of a Pod runs on all (or some) nodes in the cluster, which is useful for agents but not for replicated stateful applications.
- **Incorrect: C. Job.** A Job creates one or more Pods and ensures that a specified number of them successfully terminate. It's for batch processing, not long-running stateful services.
- **Correct: D. StatefulSet.** A StatefulSet is the workload API object used to manage stateful applications. [cite_start]It provides guarantees about the ordering and uniqueness of its Pods, including stable network identifiers and stable persistent storage. [cite: 17]

---

**Question 14:** A pod in your GKE cluster is repeatedly restarting and its status shows as `CrashLoopBackOff`.

What is the most effective first step to investigate the root cause of this issue?

- A. Check the GKE node's system logs in Cloud Logging.
- B. View the logs of the failing pod using `kubectl logs <pod-name>`.
- C. Run a connectivity test from the pod to its dependencies.
- D. Increase the CPU and memory requests for the pod.

---

**Question 14:** A pod in your GKE cluster is repeatedly restarting and its status shows as `CrashLoopBackOff`.

What is the most effective first step to investigate the root cause of this issue?

- **Incorrect: A. Check the GKE node's system logs in Cloud Logging.** While the node logs might contain relevant information, the application's own logs within the container are the most direct source of information for an application crash.
- **Correct: B. View the logs of the failing pod using `kubectl logs <pod-name>`.** A `CrashLoopBackOff` status means the container inside the pod is starting and then exiting with an error. The container's logs (`stdout`/`stderr`) will almost always contain the error message or stack trace explaining why it failed to run. [cite_start]Viewing these logs is a key part of researching an application issue. [cite: 21]
- **Incorrect: C. Run a connectivity test from the pod to its dependencies.** While a connectivity issue could be the cause, the pod's logs will likely indicate this (e.g., "cannot connect to database"). Checking the logs first is more direct.
- **Incorrect: D. Increase the CPU and memory requests for the pod.** While resource exhaustion (OOMKilled) can cause a crash, you should first check the logs to confirm this before changing resource allocations.

---

**Question 15:** You are defining a Kubernetes Deployment manifest. You want to ensure that each Pod is scheduled on a node with at least 1 vCPU and 2Gi of memory available.

Which fields should you specify in the Pod's container spec?

- A. `resources.requests`
- B. `resources.limits`
- C. `nodeSelector`
- D. `affinity`

---

**Question 15:** You are defining a Kubernetes Deployment manifest. You want to ensure that each Pod is scheduled on a node with at least 1 vCPU and 2Gi of memory available.

Which fields should you specify in the Pod's container spec?

- **Correct: A. `resources.requests`** The `requests` field specifies the minimum amount of resources a container needs. The Kubernetes scheduler uses this information to find a node that has enough available resources to run the Pod.
- **Incorrect: B. `resources.limits`** The `limits` field specifies the maximum amount of resources a container can use. If it exceeds this limit, it may be terminated (for memory) or throttled (for CPU). It doesn't determine initial placement.
- **Incorrect: C. `nodeSelector`** `nodeSelector` is used to constrain a Pod to run only on nodes that have specific labels. It doesn't consider resource availability.
- **Incorrect: D. `affinity`** Node affinity is a more expressive way to constrain which nodes your pod can be scheduled on, but `resources.requests` is the direct mechanism for declaring resource needs for scheduling.

---

**Question 16:** Your GKE cluster has a default node pool for general-purpose workloads. You need to add a new node pool specifically for workloads that require confidential computing capabilities.

What is the best way to achieve this?

- A. Create a new cluster with Confidential Computing enabled and delete the old one.
- B. Edit the existing node pool and enable the Confidential Computing setting.
- C. Add a new node pool to the cluster and enable the "Enable Confidential GKE Nodes" option for it.
- D. Apply a Kubernetes Network Policy to isolate the confidential workloads.

---

**Question 16:** Your GKE cluster has a default node pool for general-purpose workloads. You need to add a new node pool specifically for workloads that require confidential computing capabilities.

What is the best way to achieve this?

- **Incorrect: A. Create a new cluster with Confidential Computing enabled and delete the old one.** This is highly disruptive and unnecessary, as GKE supports heterogeneous node pools.
- **Incorrect: B. Edit the existing node pool and enable the Confidential Computing setting.** This would convert all nodes in the pool to confidential nodes, which may not be necessary or cost-effective for the general-purpose workloads.
- **Correct: C. Add a new node pool to the cluster and enable the "Enable Confidential GKE Nodes" option for it.** GKE allows you to add multiple node pools with different configurations. [cite_start]Creating a new node pool is the correct way to introduce nodes with specific features like Confidential Computing into an existing cluster without impacting other workloads. [cite: 17]
- **Incorrect: D. Apply a Kubernetes Network Policy to isolate the confidential workloads.** Network Policies control network traffic; they do not enable hardware-level security features like Confidential Computing.

---

**Question 17:** You need to update a containerized application running in your GKE cluster to a new image version with zero downtime.

Which strategy should you use with your Kubernetes Deployment resource?

- A. A Recreate strategy.
- B. Delete the old Pods and then create new ones with the updated image.
- C. A RollingUpdate strategy.
- D. Manually update the image on each GKE node.

---

**Question 17:** You need to update a containerized application running in your GKE cluster to a new image version with zero downtime.

Which strategy should you use with your Kubernetes Deployment resource?

- **Incorrect: A. A Recreate strategy.** The `Recreate` strategy terminates all old Pods before creating new ones, which will cause downtime.
- **Incorrect: B. Delete the old Pods and then create new ones with the updated image.** This is the manual equivalent of the `Recreate` strategy and will also cause downtime.
- **Correct: C. A RollingUpdate strategy.** `RollingUpdate` is the default strategy for Deployments. It gradually replaces old Pods with new ones, ensuring that a certain number of Pods are always running and available to serve traffic, thus achieving a zero-downtime update.
- **Incorrect: D. Manually update the image on each GKE node.** Kubernetes manages container images at the Pod level, not the node level. This approach is not a standard Kubernetes practice.

---

**Question 18:** For auditing purposes, you need to know who performed specific actions on your GKE cluster, such as creating a new node pool or deleting a service.

Which Google Cloud Observability feature should you consult?

- A. VPC Flow Logs
- B. Cloud Trace
- C. Cloud Audit Logs
- D. GKE application logs

---

**Question 18:** For auditing purposes, you need to know who performed specific actions on your GKE cluster, such as creating a new node pool or deleting a service.

Which Google Cloud Observability feature should you consult?

- **Incorrect: A. VPC Flow Logs.** VPC Flow Logs record network traffic to and from VMs (including GKE nodes), not administrative actions on the cluster itself.
- **Incorrect: B. Cloud Trace.** Cloud Trace follows requests through your application; it doesn't track administrative changes to the infrastructure.
- **Correct: C. Cloud Audit Logs.** Cloud Audit Logs, specifically the Admin Activity logs, record administrative actions taken on Google Cloud resources. This includes API calls that create, modify, or delete GKE resources like clusters and node pools. [cite_start]Configuring audit logs is a key part of monitoring. [cite: 21]
- **Incorrect: D. GKE application logs.** Application logs contain output from your running containers, not logs of administrative actions performed on the cluster infrastructure.

---

**Question 19:** You are deploying a containerized application to GKE. The application requires access to a secret stored in Google Cloud Secret Manager.

What is the recommended and most secure way to make this secret available to your container?

- A. Store the secret in a file on a Persistent Disk and mount it to the Pod.
- B. Hardcode the secret value as an environment variable in the Dockerfile.
- C. Use the Secret Manager CSI driver to mount the secret as a volume into the Pod.
- D. Grant the GKE node service account the Secret Manager Viewer role and have the application fetch it using the API.

---

**Question 19:** You are deploying a containerized application to GKE. The application requires access to a secret stored in Google Cloud Secret Manager.

What is the recommended and most secure way to make this secret available to your container?

- **Incorrect: A. Store the secret in a file on a Persistent Disk and mount it to the Pod.** This is less secure and more complex to manage than using a dedicated secrets management solution.
- **Incorrect: B. Hardcode the secret value as an environment variable in the Dockerfile.** This is a severe security anti-pattern as the secret becomes part of the container image, which is visible to anyone who can access the image.
- **Correct: C. Use the Secret Manager CSI driver to mount the secret as a volume into the Pod.** The Secret Store CSI (Container Storage Interface) driver is the recommended way to expose secrets from Secret Manager to GKE Pods. It mounts the secret as a file into the container's filesystem securely and integrates with Workload Identity for authentication.
- **Incorrect: D. Grant the GKE node service account the Secret Manager Viewer role and have the application fetch it using the API.** Granting permissions to the node's service account would give *all* pods on that node access to the secret, violating the principle of least privilege. Workload Identity should be used instead.

---

**Question 20:** You want to deploy a GKE cluster that spans multiple zones within the `us-central1` region to increase the availability of your application.

What type of cluster should you create?

- A. A multi-regional cluster in `us-central1` and `us-east1`.
- B. A regional cluster in `us-central1`.
- C. Three separate zonal clusters, one in each zone of `us-central1`, and federate them.
- D. An Autopilot cluster, as they are multi-zonal by default.

---

**Question 20:** You want to deploy a GKE cluster that spans multiple zones within the `us-central1` region to increase the availability of your application.

What type of cluster should you create?

- **Incorrect: A. A multi-regional cluster in `us-central1` and `us-east1`.** While possible with certain technologies like GKE Enterprise, a standard "multi-regional cluster" is not a GKE resource type. The standard high-availability option within a single region is a regional cluster.
- **Correct: B. A regional cluster in `us-central1`.** A regional cluster creates a control plane and nodes that are replicated across multiple zones within a single specified region. [cite_start]This provides high availability against a single zonal failure. [cite: 11]
- **Incorrect: C. Three separate zonal clusters, one in each zone of `us-central1`, and federate them.** This is a far more complex architecture to manage than a single regional cluster, which provides the desired multi-zone resilience natively.
- **Incorrect: D. An Autopilot cluster, as they are multi-zonal by default.** Autopilot clusters are *regional* by default, which means they are inherently multi-zonal. However, the fundamental cluster type providing this feature is "regional".
