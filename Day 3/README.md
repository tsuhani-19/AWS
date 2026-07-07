# Amazon EC2 (Elastic Compute Cloud)

## Introduction

Amazon **Elastic Compute Cloud (EC2)** is a web service that provides
secure, resizable compute capacity in the cloud. EC2 allows you to
launch virtual servers, called **EC2 Instances**, within minutes and pay
only for what you use.

### What is Compute?

In cloud computing, **compute** refers to the processing power required
to run applications. When you launch an EC2 instance, AWS allocates
virtual CPU (vCPU), memory (RAM), storage, and networking resources.

### Why is it called Elastic?

The word **Elastic** means resources can grow or shrink based on demand.

-   Scale Up → Increase CPU/RAM by choosing a larger instance.
-   Scale Out → Add more EC2 instances.
-   Scale Down → Reduce capacity when traffic decreases.

This flexibility helps reduce infrastructure costs.

## Why EC2 is Important

-   Secure, resizable virtual servers
-   99.99% SLA availability
-   AWS Nitro System security
-   Pay-as-you-go pricing
-   Multiple purchasing options
-   Easy integration with other AWS services

### Why Cloud Instead of Managing Servers?

If you manage your own servers, you must handle:

-   Hardware maintenance
-   Security updates
-   OS patching
-   Scaling
-   Networking
-   Monitoring
-   Disaster recovery

With EC2, AWS manages the physical infrastructure while you manage your
operating system and application.



# EC2 Use Cases

-   Web applications
-   Backend APIs
-   Gaming servers
-   Machine Learning
-   High Performance Computing (HPC)
-   Development & Testing
-   Data Analytics
-   Batch Processing



# EC2 Instance Types

## 1. General Purpose

Balanced CPU, RAM and networking.

Examples: - Web servers - Development - Small databases

Popular families: T, M



## 2. Compute Optimized

High CPU performance.

Examples: - Gaming servers - Scientific simulations - Batch jobs - Video
encoding

Popular family: C



## 3. Memory Optimized

Large RAM.

Examples: - Redis - SAP HANA - Real-time analytics

Popular families: R, X, U



## 4. Storage Optimized

High-speed local storage.

Examples: - Data warehousing - Log processing - Elasticsearch

Popular families: I, D



## 5. Accelerated Computing

GPU/FPGA/ASIC based instances.

Examples: - AI/ML - Deep Learning - Rendering - Video processing

Popular families: G, P, F, Inf, Trn



# Instance Families

  Family   Purpose
  -------- ----------------------------
  C        Compute Optimized
  D        Dense Storage
  F        FPGA
  G        GPU
  Hpc      High Performance Computing
  I        High I/O
  Inf      AWS Inferentia
  M        General Purpose
  P        GPU
  R        Memory Optimized
  T        Burstable
  Trn      AWS Trainium
  U        Ultra High Memory
  VT       Video Transcoding
  X        Extra Large Memory

### Additional Suffixes

  Suffix   Meaning
  -------- ----------------------
  a        AMD CPU
  g        AWS Graviton
  i        Intel CPU
  d        Local NVMe Storage
  n        Enhanced Networking
  e        Extra Memory/Storage
  z        High Performance CPU

------------------------------------------------------------------------

# EC2 Instance Basics

## AMI (Amazon Machine Image)

An AMI is a template used to launch an EC2 instance.

It contains: - Operating System - Software - Configuration - Application
packages

Example: Ubuntu 24.04 AMI

------------------------------------------------------------------------

## Instance Types

Defines: - CPU - RAM - Network - Storage capability

Example: `t3.micro`

------------------------------------------------------------------------

## Instance States

-   Pending
-   Running
-   Stopping
-   Stopped
-   Rebooting
-   Shutting-down
-   Terminated

------------------------------------------------------------------------

# Purchasing Options

## On-Demand

Pay only when used.

Best for: - Learning - Testing - Short-term workloads

------------------------------------------------------------------------

## Reserved Instances

Commit for 1 or 3 years.

Best for: - Production applications

------------------------------------------------------------------------

## Spot Instances

Use spare AWS capacity at large discounts.

Best for: - Batch jobs - CI/CD - Fault-tolerant workloads

------------------------------------------------------------------------

# Launching an EC2 Instance

1.  Open AWS Console
2.  Go to EC2
3.  Launch Instance
4.  Select an AMI
5.  Choose an Instance Type
6.  Configure networking
7.  Add storage
8.  Create or select a Key Pair
9.  Configure Security Group
10. Review and Launch

------------------------------------------------------------------------

# Security Groups

A Security Group acts as a virtual firewall.

Common Rules:

SSH → Port 22

HTTP → Port 80

HTTPS → Port 443

------------------------------------------------------------------------


### AWS Regions and Availability Zones

A **Region** is a geographical location where AWS has multiple data
centers. Each Region contains multiple **Availability Zones (AZs)**.

An **Availability Zone** is an isolated data center with independent
power, cooling, and networking.

**Example:** If your application is deployed only in Mumbai AZ-a and a
power outage or short circuit affects that AZ, your application may
experience downtime. If you deploy your application across multiple
Availability Zones (AZ-a, AZ-b, and AZ-c), AWS can continue serving
traffic from the healthy zones, improving availability and fault
tolerance.

### Steps to Launch an EC2 Instance

1.  Open the AWS Management Console.
2.  Go to EC2.
3.  Click **Launch Instance**.
4.  Enter an instance name.
5.  Select an Operating System (AMI). For learning, choose a **Free Tier
    Eligible** AMI.
6.  Choose an instance type such as **t3.micro** (or the current
    free-tier eligible instance).
7.  Create or select a **Key Pair**.
8.  Configure networking and Security Groups.
9.  Choose storage. **8 GB** is sufficient for most learning purposes.
10. Click **Launch Instance**.

### Key Pair

AWS disables password login for Linux EC2 instances by default.
Authentication is performed using a **Key Pair**, which consists of a
**Public Key** (stored by AWS) and a **Private Key (.pem)** (downloaded
by you). The private key is required to securely connect to your EC2
instance using SSH.

 bash
ssh -i my-key.pem ubuntu@<public-ip>



------------------------------------------------------------------------

# Managing EC2 Instances

-   Start
-   Stop
-   Reboot
-   Terminate

Monitor using: - Amazon CloudWatch - EC2 Console - AWS CLI

------------------------------------------------------------------------

# Real-World Example

Suppose an e-commerce website receives normal traffic most of the year
but experiences a huge spike during a festival sale.

Instead of buying expensive physical servers, the company launches
additional EC2 instances during the sale and terminates them afterward.
This elasticity reduces costs while maintaining performance.

------------------------------------------------------------------------

# Best Practices

-   Use IAM Roles instead of storing credentials.
-   Restrict Security Group access.
-   Enable monitoring with CloudWatch.
-   Use Auto Scaling for variable workloads.
-   Choose the right instance family.
-   Stop unused instances to save cost.

------------------------------------------------------------------------

# Summary

Amazon EC2 provides secure, scalable, and flexible virtual servers in
the cloud. It enables organizations to quickly deploy applications
without managing physical infrastructure, while offering multiple
instance types and pricing models to optimize both performance and cost.
