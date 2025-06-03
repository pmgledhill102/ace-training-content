---

**Question 16:** Your application is running on Compute Engine. You notice that while you can see basic CPU and network traffic in Cloud Monitoring, you're missing memory utilization and detailed disk I/O metrics. You want to collect this additional telemetry.

What should you do?

- A. Enable more APIs in the Google Cloud console for Compute Engine.
- B. Install and configure the Ops Agent on your Compute Engine instances.
- C. Create custom metrics using the Cloud Monitoring API.
- D. Configure VPC Flow Logs for the subnet your instances are in.

---

**Question 16:** Your application is running on Compute Engine. You notice that while you can see basic CPU and network traffic in Cloud Monitoring, you're missing memory utilization and detailed disk I/O metrics. You want to collect this additional telemetry.

What should you do?

- **Incorrect: A. Enable more APIs in the Google Cloud console for Compute Engine.** Enabling APIs is for service access, but specific telemetry like memory usage within a VM requires an agent.
- **Correct: B. Install and configure the Ops Agent on your Compute Engine instances.** The Ops Agent is the primary agent for collecting detailed telemetry from Compute Engine instances, including memory usage, disk I/O, and process metrics, which are not available to the hypervisor by default. [cite: 35, 37, 52]
- **Incorrect: C. Create custom metrics using the Cloud Monitoring API.** While you could write code to collect and send these metrics, the Ops Agent provides this functionality out-of-the-box for standard system metrics.
- **Incorrect: D. Configure VPC Flow Logs for the subnet your instances are in.** VPC Flow Logs record samples of network flows for network monitoring and forensics, not detailed VM-internal metrics like memory utilization. [cite: 232, 233]

---

**Question 17:** You are running a GKE cluster and want to monitor your workloads using Prometheus metrics. You prefer a fully managed solution that integrates with Cloud Monitoring and allows you to query data using PromQL.

Which Google Cloud service should you enable and configure?

- A. Cloud Profiler.
- B. Ops Agent with Prometheus receiver.
- C. Google Cloud Managed Service for Prometheus.
- D. Cloud Trace with OpenTelemetry exporters.

---

**Question 17:** You are running a GKE cluster and want to monitor your workloads using Prometheus metrics. You prefer a fully managed solution that integrates with Cloud Monitoring and allows you to query data using PromQL.

Which Google Cloud service should you enable and configure?

- **Incorrect: A. Cloud Profiler.** Cloud Profiler is used for continuous low-impact CPU and heap profiling of applications, not for general Prometheus metric collection. [cite: 585, 586]
- **Incorrect: B. Ops Agent with Prometheus receiver.** While Ops Agent can collect Prometheus metrics, it's primarily for Compute Engine. For GKE, Managed Service for Prometheus is the more integrated and recommended managed solution. [cite: 36, 157]
- **Correct: C. Google Cloud Managed Service for Prometheus.** This service is a fully managed offering that collects, stores, and analyzes Prometheus metrics from GKE and other environments, allowing querying with PromQL and integration with Cloud Monitoring. [cite: 135, 136, 140]
- **Incorrect: D. Cloud Trace with OpenTelemetry exporters.** Cloud Trace is for distributed latency tracing, not for collecting and querying Prometheus-style metrics. [cite: 546, 547]

---

**Question 18:** Your team has enabled Google Cloud Managed Service for Prometheus on your GKE cluster. After enabling managed collection, no metrics are appearing in your monitoring dashboards. You have an application exposing metrics on a port named `metrics` with the label `app: prom-example`.

What is the next step required to start scraping these metrics?

- A. Restart the GKE nodes to apply the new configuration.
- B. Manually configure a Prometheus server to scrape the pods.
- C. Deploy a `PodMonitoring` resource that selects pods with the label `app: prom-example` and specifies the `metrics` port.
- D. Install the Ops Agent on each GKE node.

---

**Question 18:** Your team has enabled Google Cloud Managed Service for Prometheus on your GKE cluster. After enabling managed collection, no metrics are appearing in your monitoring dashboards. You have an application exposing metrics on a port named `metrics` with the label `app: prom-example`.

What is the next step required to start scraping these metrics?

- **Incorrect: A. Restart the GKE nodes to apply the new configuration.** Enabling managed collection doesn't typically require a node restart; specific scrape targets need to be defined.
- **Incorrect: B. Manually configure a Prometheus server to scrape the pods.** The purpose of Managed Service for Prometheus with managed collection is to avoid manual Prometheus server management. [cite: 153]
- **Correct: C. Deploy a `PodMonitoring` resource that selects pods with the label `app: prom-example` and specifies the `metrics` port.** After enabling managed collection, you must deploy a `PodMonitoring` (or `ClusterPodMonitoring`) resource to define which pods and endpoints should be scraped for metrics. [cite: 172, 173, 175]
- **Incorrect: D. Install the Ops Agent on each GKE node.** The Ops Agent is primarily for Compute Engine VMs. For GKE with Managed Service for Prometheus, metric collection is handled by its own components, configured via resources like `PodMonitoring`. [cite: 36]

---

**Question 19:** A developer needs to instrument their custom application to send application-specific metrics (e.g., number of active user sessions) to Cloud Monitoring. They prefer to use an open-standard protocol.

Which of the following is the recommended approach for this scenario?

- A. Write directly to the Cloud Logging API and create log-based metrics.
- B. Use the OpenTelemetry Protocol (OTLP) with the Ops Agent configured with an OTLP receiver.
- C. Create a custom Cloud Function to periodically poll the application and write to the Monitoring API.
- D. Use `gcloud compute scp` to transfer metric files to a Cloud Storage bucket for batch ingestion.

---

**Question 19:** A developer needs to instrument their custom application to send application-specific metrics (e.g., number of active user sessions) to Cloud Monitoring. They prefer to use an open-standard protocol.

Which of the following is the recommended approach for this scenario?

- **Incorrect: A. Write directly to the Cloud Logging API and create log-based metrics.** While possible, this is less direct for custom metrics and doesn't align with the preference for an open-standard *metrics* protocol.
- **Correct: B. Use the OpenTelemetry Protocol (OTLP) with the Ops Agent configured with an OTLP receiver.** The Ops Agent can be configured with an OTLP receiver to collect user-defined metrics from applications and send them to Cloud Monitoring. OpenTelemetry is an open standard. [cite: 196, 199]
- **Incorrect: C. Create a custom Cloud Function to periodically poll the application and write to the Monitoring API.** This introduces additional infrastructure and complexity for metric collection. Direct instrumentation is preferred.
- **Incorrect: D. Use `gcloud compute scp` to transfer metric files to a Cloud Storage bucket for batch ingestion.** This is not a standard or efficient way to send real-time application metrics to Cloud Monitoring.

---

**Question 20:** You are using Google Cloud Managed Service for Prometheus and want to query the average CPU utilization across your GKE cluster nodes using PromQL in the Metrics Explorer.

Which of the following PromQL queries would you most likely use?

- A. `SELECT AVG(cpu_utilization) FROM gke_nodes`
- B. `avg(gke_node_cpu_utilization_seconds_total)`
- C. `gke_node_cpu_utilization > 0.8`
- D. `rate(gke_node_cpu_seconds_total[5m])` (and then potentially average this rate)

---

**Question 20:** You are using Google Cloud Managed Service for Prometheus and want to query the average CPU utilization across your GKE cluster nodes using PromQL in the Metrics Explorer.

Which of the following PromQL queries would you most likely use?

- **Incorrect: A. `SELECT AVG(cpu_utilization) FROM gke_nodes`** This is SQL syntax, not PromQL.
- **Incorrect: B. `avg(gke_node_cpu_utilization_seconds_total)`** While `avg()` is a PromQL function, `gke_node_cpu_utilization_seconds_total` is typically a counter. Averaging a raw counter is usually not meaningful for current utilization.
- **Incorrect: C. `gke_node_cpu_utilization > 0.8`** This is a filter expression, not an aggregation to find the average.
- **Correct: D. `rate(gke_node_cpu_seconds_total[5m])` (and then potentially average this rate)** Prometheus CPU metrics are often counters of seconds spent in various modes. To get utilization, you typically take the `rate()` of a CPU time counter over a period (e.g., `gke_node_cpu_seconds_total{mode="idle"}`). To get overall utilization, you'd calculate 1 minus the rate of idle time, or sum rates of non-idle modes, and then you could average that result. The `rate()` function is key for counter-based CPU metrics.

---

**Question 21:** Your application, deployed on App Engine flexible environment, is experiencing high latency. You need to understand the time taken by various internal operations and RPC calls within a single user request to pinpoint the bottleneck.

Which Application Performance Management (APM) tool in Google Cloud is best suited for this task?

- A. Error Reporting
- B. Cloud Profiler
- C. Cloud Trace
- D. Cloud Monitoring Dashboards

---

**Question 21:** Your application, deployed on App Engine flexible environment, is experiencing high latency. You need to understand the time taken by various internal operations and RPC calls within a single user request to pinpoint the bottleneck.

Which Application Performance Management (APM) tool in Google Cloud is best suited for this task?

- **Incorrect: A. Error Reporting.** Error Reporting aggregates and analyzes application crashes and exceptions, it does not provide detailed latency breakdowns of requests. [cite: 481, 485]
- **Incorrect: B. Cloud Profiler.** Cloud Profiler helps identify CPU and memory-intensive functions within your application code, but it doesn't trace the flow of a request across operations or RPC calls. [cite: 585, 586]
- **Correct: C. Cloud Trace.** Cloud Trace is a distributed tracing system that collects latency data for requests as they propagate through an application, including suboperations and RPC calls. It's designed to identify performance bottlenecks. [cite: 546, 547, 565]
- **Incorrect: D. Cloud Monitoring Dashboards.** Dashboards can display latency metrics, but they don't offer the granular, request-level breakdown that Cloud Trace provides.

---

**Question 22:** You are using Cloud Profiler to analyze the performance of a Java application. You want to identify functions that are consuming the most CPU time, excluding time spent waiting for I/O or locks.

Which profile type should you select in the Cloud Profiler interface?

- A. Heap
- B. Wall time
- C. CPU time
- D. Contention

---

**Question 22:** You are using Cloud Profiler to analyze the performance of a Java application. You want to identify functions that are consuming the most CPU time, excluding time spent waiting for I/O or locks.

Which profile type should you select in the Cloud Profiler interface?

- **Incorrect: A. Heap.** Heap profiles show memory allocation, not CPU execution time. [cite: 605]
- **Incorrect: B. Wall time.** Wall time is the total time taken to run a block of code, including all wait times like I/O and locks. [cite: 603]
- **Correct: C. CPU time.** CPU time specifically measures the time the CPU spends executing a block of code, excluding wait times. This is what's needed to identify CPU-bound functions. [cite: 601]
- **Incorrect: D. Contention.** Contention profiles show information about threads stuck waiting for other threads or resources, focusing on synchronization issues. [cite: 608]

---

**Question 23:** Your team has an application running on Compute Engine. You want to ensure that error reporting is set up correctly. This includes granting the necessary permissions for the application to send error data to the Error Reporting service.

Which IAM role is typically required for a service account used by an application to send data to Error Reporting?

- A. Error Reporting Viewer
- B. Error Reporting Writer
- C. Logs Writer
- D. Cloud Trace Agent

---

**Question 23:** Your team has an application running on Compute Engine. You want to ensure that error reporting is set up correctly. This includes granting the necessary permissions for the application to send error data to the Error Reporting service.

Which IAM role is typically required for a service account used by an application to send data to Error Reporting?

- **Incorrect: A. Error Reporting Viewer.** The Viewer role allows viewing error reports but not sending new error data.
- **Correct: B. Error Reporting Writer.** The Error Reporting Writer IAM role is needed for an application or service account to send error data to the Error Reporting service. [cite: 510]
- **Incorrect: C. Logs Writer.** While errors can be ingested via Cloud Logging, the specific permission to write to the Error Reporting service itself is `errorreporting.writer`. [cite: 502]
- **Incorrect: D. Cloud Trace Agent.** This role is for sending trace data to Cloud Trace, not error reports. [cite: 576]

---

**Question 24:** Cloud Trace analysis reports can compare the latency distribution of your application's requests against a baseline.

What are the typical baselines you can compare against in an analysis report?

- A. Only the previous hour's data.
- B. Data from a different Google Cloud project.
- C. A previous time range (e.g., last 24 hours, last week) or a different version of the service.
- D. A theoretical optimal latency defined by your SLO.

---

**Question 24:** Cloud Trace analysis reports can compare the latency distribution of your application's requests against a baseline.

What are the typical baselines you can compare against in an analysis report?

- **Incorrect: A. Only the previous hour's data.** While comparing to recent data is an option, it's not the only one.
- **Incorrect: B. Data from a different Google Cloud project.** Analysis reports focus on comparing data within the same service and project.
- **Correct: C. A previous time range (e.g., last 24 hours, last week) or a different version of the service.** Analysis reports allow comparing current latency distributions to those from a previous time range or a different deployed version of the same service to detect performance regressions or improvements. [cite: 550]
- **Incorrect: D. A theoretical optimal latency defined by your SLO.** Analysis reports compare against actual historical performance data, not arbitrary SLO targets.

---

**Question 25:** Your organization is concerned about the cost of Cloud Logging. A particular application generates a large volume of informational logs that provide little value for production monitoring but are useful for occasional debugging by developers. These logs are significantly increasing storage costs.

What is a recommended best practice to control costs in this scenario while still allowing developers access to these logs if needed?

- A. Increase the default retention period for all logs to ensure they are available.
- B. Create a log exclusion filter to drop these informational logs entirely.
- C. Export the informational logs to a Cloud Storage bucket with a cheaper storage class (e.g., Nearline or Coldline) and then exclude them from Cloud Logging ingestion.
- D. Advise developers to reduce the verbosity of their logging.

---

**Question 25:** Your organization is concerned about the cost of Cloud Logging. A particular application generates a large volume of informational logs that provide little value for production monitoring but are useful for occasional debugging by developers. These logs are significantly increasing storage costs.

What is a recommended best practice to control costs in this scenario while still allowing developers access to these logs if needed?

- **Incorrect: A. Increase the default retention period for all logs to ensure they are available.** This would further increase storage costs, not reduce them.
- **Incorrect: B. Create a log exclusion filter to drop these informational logs entirely.** This would reduce costs but make the logs unavailable for debugging, which is a stated requirement. [cite: 751]
- **Correct: C. Export the informational logs to a Cloud Storage bucket with a cheaper storage class (e.g., Nearline or Coldline) and then exclude them from Cloud Logging ingestion.** This strategy allows for cost-effective long-term storage for debugging purposes while avoiding the higher ingestion and storage costs of keeping them in Cloud Logging's active storage. Log exports themselves are free, though the target resource incurs costs. [cite: 764, 768]
- **Incorrect: D. Advise developers to reduce the verbosity of their logging.** While good practice, this doesn't address the existing high-volume logs or provide a mechanism for developers to access them when needed.

---

**Question 26:** You are troubleshooting network connectivity between two VMs in different subnets within the same VPC. You suspect a firewall rule might be blocking the traffic. You want to log connections that are evaluated by a specific "allow" firewall rule to verify if the traffic is indeed passing through it.

What is the most direct way to achieve this?

- A. Enable VPC Flow Logs for both subnets and filter for the specific VMs.
- B. Use Packet Mirroring to capture all traffic from the source VM.
- C. Enable Firewall Rules Logging specifically for that "allow" firewall rule.
- D. Set up Connectivity Tests between the two VMs.

---

**Question 26:** You are troubleshooting network connectivity between two VMs in different subnets within the same VPC. You suspect a firewall rule might be blocking the traffic. You want to log connections that are evaluated by a specific "allow" firewall rule to verify if the traffic is indeed passing through it.

What is the most direct way to achieve this?

- **Incorrect: A. Enable VPC Flow Logs for both subnets and filter for the specific VMs.** VPC Flow Logs record a sample of flows and show whether traffic reached a VM, but they don't explicitly show which firewall rule permitted the traffic. [cite: 232, 244]
- **Incorrect: B. Use Packet Mirroring to capture all traffic from the source VM.** Packet Mirroring captures full packet data but doesn't directly indicate firewall rule evaluation. It's more for deep packet inspection. [cite: 372, 375]
- **Correct: C. Enable Firewall Rules Logging specifically for that "allow" firewall rule.** Firewall Rules Logging can be enabled on a per-rule basis and will log connections (TCP and UDP) that are matched and evaluated by that specific rule, showing if it allowed the connection. [cite: 272, 280, 281]
- **Incorrect: D. Set up Connectivity Tests between the two VMs.** Connectivity Tests analyze the configuration and can tell you if connectivity *should* work according to firewall rules, but enabling logging on the rule itself provides direct evidence of traffic evaluation. [cite: 415, 416]

---

**Question 27:** Your company wants to monitor packet loss and latency between all Google Cloud zones where your VMs are deployed to proactively identify potential network performance issues. You want this monitoring to happen without consuming resources on your actual VMs.

Which feature of the Network Intelligence Center is designed for this?

- A. Network Topology
- B. Connectivity Tests
- C. Performance Dashboard
- D. Firewall Insights

---

**Question 27:** Your company wants to monitor packet loss and latency between all Google Cloud zones where your VMs are deployed to proactively identify potential network performance issues. You want this monitoring to happen without consuming resources on your actual VMs.

Which feature of the Network Intelligence Center is designed for this?

- **Incorrect: A. Network Topology.** Network Topology visualizes your network configuration and traffic flow but doesn't actively probe for packet loss or latency between zones. [cite: 409]
- **Incorrect: B. Connectivity Tests.** Connectivity Tests diagnose reachability between specific endpoints based on configuration and live data plane probing, but the Performance Dashboard provides an aggregated, ongoing view of inter-zonal performance. [cite: 415, 416]
- **Correct: C. Performance Dashboard.** The Performance Dashboard within Network Intelligence Center shows aggregated packet loss (via active probing from physical hosts) and latency (sampled from actual TCP traffic) metrics between Google Cloud zones, without consuming VM resources. [cite: 421, 422, 425, 427]
- **Incorrect: D. Firewall Insights.** Firewall Insights helps understand and optimize firewall rule usage; it does not monitor inter-zonal packet loss or latency. [cite: 432]

---

**Question 28:** Which Google Cloud Observability service would you use to continuously gather CPU usage and memory-allocation information from your production applications, attributing that information to the source code, to help identify parts of the application consuming the most resources without significant performance overhead?

- A. Cloud Monitoring with custom metrics
- B. Cloud Trace
- C. Cloud Profiler
- D. Error Reporting

---

**Question 28:** Which Google Cloud Observability service would you use to continuously gather CPU usage and memory-allocation information from your production applications, attributing that information to the source code, to help identify parts of the application consuming the most resources without significant performance overhead?

- **Incorrect: A. Cloud Monitoring with custom metrics.** While you can send CPU and memory metrics to Cloud Monitoring, it doesn't inherently attribute this consumption directly to source code lines or provide flame graphs for analysis with low overhead like Profiler does.
- **Incorrect: B. Cloud Trace.** Cloud Trace focuses on request latency and tracing the path of requests through distributed systems, not on detailed CPU/memory consumption at the code level. [cite: 546, 547]
- **Correct: C. Cloud Profiler.** Cloud Profiler is a statistical, low-overhead profiler that continuously gathers CPU usage and memory-allocation information from production applications and attributes it to the source code, helping identify resource-intensive parts. [cite: 585, 586]
- **Incorrect: D. Error Reporting.** Error Reporting counts, analyzes, and aggregates application crashes and exceptions. [cite: 481]

---

**Question 29:** You are using the Ops Agent to collect logs and metrics from your Compute Engine instances. To reduce Cloud Monitoring costs, you decide you don't need the detailed system metrics or metrics from third-party applications for your development VMs.

How can you reduce metric volume from these development VMs?

- A. Uninstall the Ops Agent from the development VMs.
- B. Configure the Ops Agent on development VMs to only send logs and not metrics.
- C. Create log exclusion filters for metrics originating from development VMs.
- D. Reduce the default retention period for metrics in Cloud Monitoring.

---

**Question 29:** You are using the Ops Agent to collect logs and metrics from your Compute Engine instances. To reduce Cloud Monitoring costs, you decide you don't need the detailed system metrics or metrics from third-party applications for your development VMs.

How can you reduce metric volume from these development VMs?

- **Incorrect: A. Uninstall the Ops Agent from the development VMs.** While this would reduce metric volume, it would also stop log collection via the Ops Agent and prevent collection of even basic agent-provided metrics, which might still be desired.
- **Correct: B. Configure the Ops Agent on development VMs to only send logs and not metrics.** The Ops Agent is customizable through configuration files. You can configure it to reduce the volume of metrics by not sending detailed system metrics or third-party app metrics, or even disable metric collection entirely while keeping log collection active. [cite: 784]
- **Incorrect: C. Create log exclusion filters for metrics originating from development VMs.** Log exclusion filters apply to logs, not directly to metrics ingested by the Ops Agent.
- **Incorrect: D. Reduce the default retention period for metrics in Cloud Monitoring.** Retention periods affect how long metric data is stored, not the volume of metrics ingested, which is what primarily drives costs.

---

**Question 30:** Your company has deployed a new web service and you want to monitor its external availability and latency from multiple points around the globe. The service exposes an HTTPS endpoint. You also need to be alerted if the service becomes unavailable.

Which combination of Google Cloud services and configurations would you use?

- A. Cloud Functions to periodically ping the service, writing results to Cloud Logging, with a log-based alert.
- B. Packet Mirroring to capture traffic to the service, with custom analysis scripts.
- C. An HTTPS Uptime Check in Cloud Monitoring, with an alerting policy configured for failures.
- D. Network Analyzer to detect connectivity issues, with email notifications for insights.

---

**Question 30:** Your company has deployed a new web service and you want to monitor its external availability and latency from multiple points around the globe. The service exposes an HTTPS endpoint. You also need to be alerted if the service becomes unavailable.

Which combination of Google Cloud services and configurations would you use?

- **Incorrect: A. Cloud Functions to periodically ping the service, writing results to Cloud Logging, with a log-based alert.** This is a custom solution that replicates functionality already provided natively and more robustly by Uptime Checks.
- **Incorrect: B. Packet Mirroring to capture traffic to the service, with custom analysis scripts.** Packet Mirroring captures detailed packet data for deep inspection but isn't designed for simple external availability and latency checks from global locations. [cite: 372]
- **Correct: C. An HTTPS Uptime Check in Cloud Monitoring, with an alerting policy configured for failures.** Uptime checks are specifically designed to monitor the availability and latency of external endpoints (HTTP, HTTPS, TCP) from various global locations. You can then create alerting policies based on the results of these checks.
- **Incorrect: D. Network Analyzer to detect connectivity issues, with email notifications for insights.** Network Analyzer detects misconfigurations and suboptimal configurations within your VPC network; it does not perform external availability checks. [cite: 456]
