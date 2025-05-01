---
title: ACE Prep Week 1
presenter: Paul Gledhill
date: 26-04-2025
layout: cover
# Define CSS for Google Style
style: |
  /* Basic Theme Approximation */
  :root {
    --gslide-font-family: 'Roboto', sans-serif;
    --gslide-title-color: #4285F4; /* Google Blue */
    --gslide-text-color: #3c4043;  /* Dark Gray */
    --gslide-bg-color: #ffffff;    /* White */
    --gslide-placeholder-color: #757575; /* Medium Gray */
  }

  /* Apply base font and color */
  .slidev-layout {
    font-family: var(--gslide-font-family);
    color: var(--gslide-text-color);
    background-color: var(--gslide-bg-color);
    padding: 2rem 4rem; /* Typical padding */
  }

  /* Title Styling */
  .slidev-layout h1 {
    color: var(--gslide-title-color);
    font-weight: 500; /* Medium weight */
    margin-bottom: 1.5rem;
  }

  /* Body Text Styling */
  .slidev-layout p,
  .slidev-layout li {
    font-size: 1.1rem;
    line-height: 1.6;
  }

  .slidev-layout ul {
    margin-left: 1.5rem; /* Indent lists */
  }

  /* Title Slide Specifics */
  .gslides-title-slide h1 {
    font-size: 2.8em; /* Larger title */
    color: var(--gslide-title-color);
  }
  .gslides-title-slide p { /* Style presenter/date if they are paragraphs */
    font-size: 1.2em;
    color: var(--gslide-text-color);
    margin-top: 0.5em;
  }

  /* Content Slide Specifics */
  .gslides-content-slide h1 {
     font-size: 2em; /* Standard content title size */
     text-align: left; /* Align title left */
  }

  /* Placeholder Text Styling */
  .placeholder-text {
    color: var(--gslide-placeholder-color);
    font-style: italic;
  }

  /* Center align class helper */
  .text-center {
    text-align: center;
  }

head: |
  <link rel="preconnect" href="https://fonts.googleapis.com">
  <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
  <link href="https://fonts.googleapis.com/css2?family=Roboto:wght@400;500;700&display=swap" rel="stylesheet">

---
layout: default # Explicitly default, or choose another like 'two-cols' if needed
class: gslides-content-slide

# Agenda

- Introduction to Google Cloud
- Global Infrastructure
- Resource Hierarchy
- Interacting with Google Cloud
- Billing & Cost Management
- Core Compute: Compute Engine
- Core Storage: Cloud Storage
- Core Networking: VPC
- Access Control: IAM Fundamentals
- Key Takeaways
- Next Steps

---

# Introduction to Google Cloud

- Public cloud platform by Google
- Provides IaaS, PaaS, SaaS services
- Highly scalable and secure infrastructure

**Speaker Notes:**  
Briefly define cloud computing models (IaaS, PaaS, SaaS) with examples relevant to banking/finance environments.

---

# Global Infrastructure

- Regions & Zones (availability, latency)
- Importance of High Availability (HA)
- Geographic redundancy

[Placeholder: Map of Google Cloud regions and zones]

**Speaker Notes:**  
Highlight the importance of selecting regions and zones to reduce latency, improve performance, and achieve redundancy.

---

# Resource Hierarchy

- Organization → Folders → Projects → Resources
- Centralized management and IAM

[Placeholder: Resource hierarchy diagram]

**Speaker Notes:**  
Explain how the hierarchy helps manage resources effectively, enforce policies, and simplify permissions.

---

# Interacting with Google Cloud

- Cloud Console (UI)
- Cloud SDK (gcloud, gsutil, bq)
- Cloud Shell (built-in terminal)

[Placeholder: Screenshot of Google Cloud Console and Cloud Shell]

**Speaker Notes:**  
Emphasize the versatility of Cloud Shell and SDK for automation and scripting.

---

# Billing & Cost Management

- Billing Accounts
- Budgets and Alerts
- Pricing Calculator

[Placeholder: Screenshot of Billing dashboard]

**Speaker Notes:**  
Stress the criticality of cost controls and alerts to prevent unexpected charges.

---

# Core Compute: Compute Engine

- Virtual Machines (VM Instances)
- Machine Types and Pricing
- Persistent Disks (Standard, SSD)
- Images and Snapshots

[Placeholder: Simple Compute Engine VM diagram]

**Speaker Notes:**  
Describe scenarios suitable for different machine types, disk types, and importance of regular snapshot backups.

---

# Core Storage: Cloud Storage

- Buckets (unique naming, location)
- Objects
- Storage Classes (Standard, Nearline, Coldline, Archive)
- Lifecycle Management

[Placeholder: Diagram of bucket and objects]

**Speaker Notes:**  
Explain appropriate storage classes and use cases (e.g., Nearline for audit logs, Archive for compliance).

---

# Core Networking: Virtual Private Cloud (VPC)

- Global VPC Networks
- Subnets (Regional, CIDR blocks)
- Firewall Rules (Ingress/Egress, Tags)
- Routes

[Placeholder: Simplified VPC architecture diagram]

**Speaker Notes:**  
Illustrate how VPCs provide secure, isolated network environments essential for regulated industries.

---

# Access Control: IAM Fundamentals

- IAM Model: "Who" can do "What" on "Which Resource"
- Members (Users, Service Accounts, Groups)
- Roles: Primitive, Predefined, Custom (focus on Predefined)
- Policies

[Placeholder: IAM policy diagram]

**Speaker Notes:**  
Highlight the importance of least-privilege principle and predefined roles.

---

# Key Takeaways

- Google Cloud basics: global, secure, scalable
- Importance of regions, zones, and resource hierarchy
- Cost awareness and management critical
- IAM roles and permissions essential for security

**Speaker Notes:**  
Brief recap reinforcing foundational concepts covered.

---

# Next Steps / Homework

- Review today's concepts
- Complete labs: "Google Cloud Fundamentals: Core Infrastructure" on Cloud Skills Boost
- Explore Cloud Console and Cloud Shell hands-on

**Speaker Notes:**  
Encourage colleagues to complete hands-on labs to reinforce today's learnings and prepare for next week.

---
