# Amazon EC2 Interview Questions and Detailed Answers

## 1. What is Amazon EC2?

Amazon Elastic Compute Cloud (EC2) is an AWS service that provides
secure, scalable, and resizable virtual servers in the cloud. These
virtual servers are called EC2 instances. It allows users to deploy
applications without purchasing or maintaining physical hardware.

------------------------------------------------------------------------

## 2. Why is EC2 called Elastic?

Elastic means resources can scale based on demand.

-   Scale Up: Increase CPU/RAM.
-   Scale Out: Add more instances.
-   Scale Down: Remove instances to reduce cost.

This enables applications to handle traffic spikes efficiently.

------------------------------------------------------------------------

## 3. What is an EC2 Instance?

An EC2 Instance is a virtual machine running inside AWS. It contains
CPU, memory, storage, networking, and an operating system.

------------------------------------------------------------------------

## 4. What is an AMI?

AMI (Amazon Machine Image) is a template used to launch EC2 instances.
It includes: - Operating System - Software packages - Configuration -
Startup permissions

Without an AMI, an EC2 instance cannot be launched.

------------------------------------------------------------------------

## 5. Explain EC2 Instance Types.

General Purpose: Balanced CPU and memory.

Compute Optimized: High CPU workloads.

Memory Optimized: Large RAM workloads.

Storage Optimized: High-speed storage workloads.

Accelerated Computing: GPU/FPGA/Inferentia based workloads.

------------------------------------------------------------------------

## 6. Difference Between Stop, Reboot and Terminate?

Stop: VM shuts down but EBS remains.

Reboot: Restarts OS without deleting data.

Terminate: Deletes the instance permanently.

------------------------------------------------------------------------

## 7. What is a Security Group?

A Security Group is a virtual firewall controlling inbound and outbound
traffic at the instance level. It is stateful, meaning return traffic is
automatically allowed.

------------------------------------------------------------------------

## 8. What is a Key Pair?

A Key Pair consists of a public and private key used for SSH
authentication.

Linux: ssh -i key.pem ubuntu@public-ip

Windows: Use RDP with the administrator password.

------------------------------------------------------------------------

## 9. What are On-Demand, Reserved and Spot Instances?

On-Demand: Pay by the second/hour. Best for short-term use.

Reserved: Commit for 1 or 3 years for discounts.

Spot: Uses spare AWS capacity at deep discounts but can be interrupted.

------------------------------------------------------------------------

## 10. What are EC2 Instance States?

Pending Running Stopping Stopped Rebooting Shutting-down Terminated

------------------------------------------------------------------------

## 11. What is EBS?

Amazon Elastic Block Store provides persistent storage for EC2
instances. Data remains even if the instance is stopped.

------------------------------------------------------------------------

## 12. Difference Between EBS and Instance Store?

EBS: Persistent storage.

Instance Store: Temporary local storage. Data is lost if the instance
stops or terminates.

------------------------------------------------------------------------

## 13. What is Elastic IP?

An Elastic IP is a static public IPv4 address that can be attached to an
EC2 instance and remapped if needed.

------------------------------------------------------------------------

## 14. What is User Data?

User Data is a startup script executed when an EC2 instance boots. It is
commonly used for installing packages and configuring servers
automatically.

------------------------------------------------------------------------

## 15. What is the AWS Nitro System?

The AWS Nitro System is the underlying hardware and virtualization
platform that improves security, networking, and performance while
reducing virtualization overhead.

------------------------------------------------------------------------

## 16. What is Auto Scaling?

Auto Scaling automatically launches or terminates EC2 instances based on
demand, helping maintain performance and reduce cost.

------------------------------------------------------------------------

## 17. What is an Application Load Balancer?

An Application Load Balancer distributes incoming HTTP/HTTPS traffic
across multiple EC2 instances to improve availability and scalability.

------------------------------------------------------------------------

## 18. What is CloudWatch?

Amazon CloudWatch monitors EC2 metrics such as CPU utilization, network
usage, disk activity, and memory (with custom agents). It can trigger
alarms and automate actions.

------------------------------------------------------------------------

## 19. Real Interview Scenario

Question: Your website suddenly receives ten times more traffic. How
would you handle it?

Answer: Deploy EC2 instances behind an Application Load Balancer,
configure Auto Scaling based on CPU utilization, monitor using
CloudWatch, and use multiple Availability Zones for high availability.

------------------------------------------------------------------------

## 20. Why Should Companies Use EC2?

Benefits: - No hardware purchase - Pay only for usage - Rapid
deployment - Global infrastructure - High availability - Secure
environment - Easy integration with AWS services

------------------------------------------------------------------------

# Frequently Asked Interview Questions

-   Difference between EC2 and Lambda?
-   Difference between Security Group and NACL?
-   Difference between EBS and EFS?
-   Can an EC2 instance have multiple security groups?
-   What happens when an EC2 instance is stopped?
-   How do you resize an EC2 instance?
-   What is an IAM Role?
-   What are Placement Groups?
-   What is the difference between Public and Private EC2 instances?
-   Explain the EC2 pricing models.

------------------------------------------------------------------------

# Tips for Interviews

-   Understand the EC2 launch process.
-   Practice SSH into Linux instances.
-   Learn VPC basics.
-   Understand Security Groups thoroughly.
-   Know instance purchasing models.
-   Be comfortable with Auto Scaling and Load Balancers.
