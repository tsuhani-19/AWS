# Amazon VPC — Virtual Private Cloud

> **"A VPC is your own private network inside AWS. You decide who gets in, which rooms (subnets) resources live in, and which roads (routes) traffic takes."**

Complete notes on VPC, Subnets, Route Tables, Internet Gateway, NAT Gateway, Security Groups, NACLs — with real-world examples, architecture diagrams, and interview-ready explanations.

---

# 📚 Table of Contents

| # | Section |
|---|---------|
| 1 | [Introduction to VPC](#1-introduction-to-vpc) |
| 2 | [Real-World Analogy — Office Building](#2-real-world-analogy--office-building) |
| 3 | [Why VPC is Required](#3-why-vpc-is-required) |
| 4 | [Default VPC vs Custom VPC](#4-default-vpc-vs-custom-vpc) |
| 5 | [CIDR Blocks & IP Addressing](#5-cidr-blocks--ip-addressing) |
| 6 | [Subnets — Deep Dive](#6-subnets--deep-dive) |
| 7 | [Public vs Private Subnets](#7-public-vs-private-subnets) |
| 8 | [Route Tables — How Traffic Flows](#8-route-tables--how-traffic-flows) |
| 9 | [Internet Gateway (IGW)](#9-internet-gateway-igw) |
| 10 | [NAT Gateway & NAT Instance](#10-nat-gateway--nat-instance) |
| 11 | [Security Groups](#11-security-groups) |
| 12 | [Network ACL (NACL)](#12-network-acl-nacl) |
| 13 | [Availability Zones & Multi-AZ Design](#13-availability-zones--multi-az-design) |
| 14 | [VPC Endpoints](#14-vpc-endpoints) |
| 15 | [VPC Peering & Transit Gateway](#15-vpc-peering--transit-gateway) |
| 16 | [VPC Flow Logs](#16-vpc-flow-logs) |
| 17 | [Real-World Architecture — E-Commerce App](#17-real-world-architecture--e-commerce-app) |
| 18 | [Real-World Architecture — Banking App](#18-real-world-architecture--banking-app) |
| 19 | [Step-by-Step: Build a Custom VPC](#19-step-by-step-build-a-custom-vpc) |
| 20 | [Common Mistakes & Troubleshooting](#20-common-mistakes--troubleshooting) |
| 21 | [Key Takeaways & Quick Revision](#21-key-takeaways--quick-revision) |

---

## Learning Objectives

By the end of Day 4, you should be able to:

- Explain what a VPC is and why every AWS resource lives inside one
- Design public and private subnets with correct CIDR ranges
- Configure route tables for internet and private traffic
- Differentiate Security Groups vs NACLs
- Explain how NAT Gateway enables outbound-only internet for private subnets
- Design a production-ready 3-tier architecture across multiple AZs
- Troubleshoot common VPC connectivity issues

---

# Part 1 — VPC Foundations

---

## 1. Introduction to VPC

**Amazon Virtual Private Cloud (VPC)** is a logically isolated virtual network inside AWS where you launch resources like EC2, RDS, Lambda, and Load Balancers.

When you create an AWS account, AWS automatically creates a **Default VPC** in every region. Every resource you launch (EC2, RDS, etc.) runs inside a VPC — even if you never created one manually.

### What VPC Controls

| Control | Example |
|---------|---------|
| IP address range | `10.0.0.0/16` |
| Subnets | Public, private, database tiers |
| Routing | Traffic to internet, NAT, VPC peering |
| Internet access | Internet Gateway, NAT Gateway |
| Security | Security Groups, NACLs |
| DNS | Enable/disable DNS hostnames & resolution |

### Simple Definition

> A VPC is your **private data center in the cloud** — you own the network design, IP ranges, and security rules.

> 💡 **Hinglish Insight**
>
> VPC matlab AWS ke andar tumhara apna private network. Jaise ghar mein alag rooms hote hain, waise VPC mein alag subnets hote hain.

---

## 2. Real-World Analogy — Office Building

Think of AWS as a **huge city**. Your VPC is **your company's office building** inside that city.

| Real World | AWS VPC |
|------------|---------|
| City | AWS Cloud (Region) |
| Office Building | VPC |
| Floors / Departments | Subnets |
| Rooms | EC2 instances, RDS |
| Building Security Guard | Security Group |
| Floor Security Checkpoint | Network ACL (NACL) |
| Main Gate (entry/exit) | Internet Gateway |
| Back Door (staff only, no visitors) | NAT Gateway |
| Internal Roads | Route Tables |
| CCTV Cameras | VPC Flow Logs |
| Inter-building walkway | VPC Peering |
| Central Hub connecting buildings | Transit Gateway |

### E-Commerce Analogy

Imagine you're building **Flipkart's infrastructure**:

```
Internet (Customers)
        │
   Main Gate (Internet Gateway)
        │
   Reception / Showroom (Public Subnet)
   └── Load Balancer + Web Servers
        │
   Internal Office (Private Subnet)
   └── Backend API Servers
        │
   Vault (Private Subnet)
   └── Database (RDS) — NO direct public access
```

Customers enter through the main gate (IGW) → see the showroom (public subnet). They never walk into the vault (database subnet).

---

## 3. Why VPC is Required

### Without VPC (Hypothetical)

- All AWS customers would share one giant network
- No isolation between companies
- Anyone could potentially reach anyone else's servers
- No control over IP addressing or routing

### With VPC

| Benefit | Description |
|---------|-------------|
| **Network Isolation** | Your VPC is completely separate from other customers |
| **Custom IP Ranges** | Choose `10.0.0.0/16`, `172.16.0.0/16`, etc. |
| **Tiered Security** | Web tier public, database tier private |
| **Compliance** | Meet regulatory requirements (PCI-DSS, HIPAA) |
| **Hybrid Cloud** | Connect on-premises via VPN or Direct Connect |
| **Multi-Environment** | Separate VPCs for dev, staging, production |

### Real-World Example — Startup Scaling

**Scenario:** A food delivery startup (like Swiggy) launches in one city.

**Month 1:** Single EC2 in default VPC — works fine.

**Month 6:** 50 microservices, 3 databases, Redis cache, internal APIs.

**Without proper VPC design:** Everything in one flat network — one compromised server exposes everything.

**With VPC design:**
- Public subnet → ALB only
- Private subnet → API servers, workers
- Private subnet → RDS, ElastiCache
- NAT Gateway → outbound updates only
- Security Groups → least privilege per service

> 💡 **Hinglish Insight**
>
> VPC ke bina sab kuch ek hi room mein pada hota — database bhi, web server bhi. VPC se tum alag-alag rooms bana ke security control karte ho.

---

## 4. Default VPC vs Custom VPC

### Default VPC

AWS creates one automatically when your account is created.

| Feature | Default VPC |
|---------|-------------|
| CIDR | `172.31.0.0/16` (varies) |
| Subnets | One public subnet per AZ |
| Internet Gateway | Pre-attached |
| Route Table | Routes `0.0.0.0/0` → IGW |
| DNS | Enabled |
| Use case | Learning, quick tests |

**Good for:** First EC2 launch, tutorials, quick experiments.

**Bad for:** Production — you have no control over design.

---

### Custom VPC

You create and configure everything manually.

| You Configure | Why It Matters |
|---------------|----------------|
| CIDR block | Plan IP space for growth |
| Public subnets | Web tier, load balancers |
| Private subnets | Apps, databases |
| Route tables | Control traffic flow per subnet |
| NAT Gateway | Private subnet outbound internet |
| Security Groups | Per-service firewall rules |

**Good for:** Production, enterprise, compliance-heavy apps.

**Recommended for:** Every real project after learning phase.

### Comparison

| | Default VPC | Custom VPC |
|---|------------|------------|
| Created by | AWS automatically | You manually |
| Design control | Minimal | Full |
| Production ready | No | Yes |
| Learning | Easy start | Requires planning |
| IP planning | Fixed | You choose |

---

## 5. CIDR Blocks & IP Addressing

Every VPC needs an **IP address range** defined using **CIDR notation**.

### What is CIDR?

**CIDR** = Classless Inter-Domain Routing

Format: `IP_Address/Prefix`

Example: `10.0.0.0/16`

| Part | Meaning |
|------|---------|
| `10.0.0.0` | Network address (starting point) |
| `/16` | First 16 bits are network; remaining 16 bits are for hosts |

### CIDR Cheat Sheet

| CIDR | Total IPs | Usable IPs (AWS reserves 5) | Typical Use |
|------|-----------|----------------------------|-------------|
| `/16` | 65,536 | 65,531 | Entire VPC |
| `/24` | 256 | 251 | One subnet |
| `/28` | 16 | 11 | Small subnet (NAT, endpoints) |
| `/32` | 1 | 1 | Single host |

### AWS Reserved IPs (per subnet)

AWS reserves **5 IP addresses** in every subnet — you cannot assign them:

| IP | Purpose |
|----|---------|
| First IP | Network address |
| Second IP | VPC router |
| Third IP | DNS (if enabled) |
| Fourth IP | Reserved for future |
| Last IP | Broadcast address |

**Example:** Subnet `10.0.1.0/24` has 256 IPs but only **251 are usable**.

### Real-World CIDR Design — E-Commerce

```
VPC: 10.0.0.0/16  (65,536 IPs — room to grow)

Region: ap-south-1 (Mumbai)

AZ ap-south-1a:
  10.0.1.0/24  → Public Subnet   (Web/ALB)
  10.0.11.0/24 → Private Subnet  (App Servers)
  10.0.21.0/24 → Private Subnet  (Database)

AZ ap-south-1b:
  10.0.2.0/24  → Public Subnet   (Web/ALB)
  10.0.12.0/24 → Private Subnet  (App Servers)
  10.0.22.0/24 → Private Subnet  (Database)

AZ ap-south-1c:
  10.0.3.0/24  → Public Subnet
  10.0.13.0/24 → Private Subnet
  10.0.23.0/24 → Private Subnet
```

**Why this pattern?**
- `10.0.X.0/24` for public (X = 1, 2, 3 per AZ)
- `10.0.1X.0/24` for private app tier
- `10.0.2X.0/24` for private database tier
- Easy to read, easy to scale

> 💡 **Hinglish Insight**
>
> CIDR matlab tum decide karte ho kitne IP addresses chahiye. `/16` bada building hai, `/24` usme ek floor hai.

### Private IP Ranges (RFC 1918)

| Range | CIDR | Common in AWS |
|-------|------|---------------|
| Class A | `10.0.0.0` – `10.255.255.255` | Most common (`10.0.0.0/16`) |
| Class B | `172.16.0.0` – `172.31.255.255` | Default VPC uses this |
| Class C | `192.168.0.0` – `192.168.255.255` | Small networks |

**Important:** Choose a CIDR that does **not overlap** with your office network if you plan VPN/Direct Connect.

---

## 6. Subnets — Deep Dive

A **subnet** is a **segment of your VPC's IP range** that lives in **exactly one Availability Zone**.

### Key Rules

| Rule | Detail |
|------|--------|
| One AZ per subnet | A subnet cannot span multiple AZs |
| Subnet CIDR ⊆ VPC CIDR | Subnet range must be inside VPC range |
| No overlapping subnets | Two subnets cannot share IP ranges |
| AZ required at creation | You pick `ap-south-1a`, `1b`, or `1c` |

### Visual — Subnets Inside VPC

```
VPC: 10.0.0.0/16
┌─────────────────────────────────────────────────────────┐
│                                                         │
│  AZ: ap-south-1a          AZ: ap-south-1b               │
│  ┌─────────────────┐      ┌─────────────────┐          │
│  │ 10.0.1.0/24     │      │ 10.0.2.0/24     │          │
│  │ Public Subnet   │      │ Public Subnet   │          │
│  │ EC2: 10.0.1.50  │      │ EC2: 10.0.2.50  │          │
│  └─────────────────┘      └─────────────────┘          │
│                                                         │
│  ┌─────────────────┐      ┌─────────────────┐          │
│  │ 10.0.11.0/24    │      │ 10.0.12.0/24    │          │
│  │ Private Subnet  │      │ Private Subnet  │          │
│  │ RDS: 10.0.11.30 │      │ RDS: 10.0.12.30 │          │
│  └─────────────────┘      └─────────────────┘          │
│                                                         │
└─────────────────────────────────────────────────────────┘
```

### Why Multiple Subnets?

| Reason | Example |
|--------|---------|
| **Security tiers** | Web public, DB private |
| **High availability** | Same tier in multiple AZs |
| **Service isolation** | Lambda in separate subnet from EC2 |
| **Compliance** | PCI data in dedicated subnet |

### Real-World Example — Hospital App

A hospital management system:

| Subnet | Tier | Resources | Why |
|--------|------|-----------|-----|
| Public `10.0.1.0/24` | Web | ALB, CloudFront origin | Patient portal access |
| Private `10.0.11.0/24` | App | EC2 API servers | Business logic, no direct internet |
| Private `10.0.21.0/24` | Data | RDS (patient records) | HIPAA — strictest isolation |
| Private `10.0.31.0/24` | Management | Bastion host (optional) | Admin SSH access only |

---

## 7. Public vs Private Subnets

This is one of the **most asked interview topics**.

### What Makes a Subnet "Public"?

A subnet is **public** if its route table has a route:

```
Destination: 0.0.0.0/0  →  Target: igw-xxxx (Internet Gateway)
```

**Plus**, instances typically need:
- A **public IP** or **Elastic IP**
- Security Group allowing inbound traffic (e.g., port 80, 443)

### What Makes a Subnet "Private"?

A subnet is **private** if it has **no direct route** to the Internet Gateway.

Private subnets may have:
```
Destination: 0.0.0.0/0  →  Target: nat-xxxx (NAT Gateway)
```
This allows **outbound** internet (updates, API calls) but **blocks inbound** from internet.

### Side-by-Side Comparison

| | Public Subnet | Private Subnet |
|---|--------------|----------------|
| Route to IGW | Yes (`0.0.0.0/0 → IGW`) | No |
| Inbound from internet | Possible (if SG allows) | Blocked |
| Outbound to internet | Direct via IGW | Via NAT Gateway |
| Public IP on instances | Usually yes | Usually no |
| Typical resources | ALB, Bastion, NAT GW | App servers, RDS, Redis |
| Risk level | Higher (exposed) | Lower (protected) |

### Traffic Flow Diagrams

**Public Subnet — User visits website:**
```
User (203.0.113.50)
    │
    ▼
Internet
    │
    ▼
Internet Gateway
    │
    ▼
Route Table (0.0.0.0/0 → IGW)
    │
    ▼
Public Subnet (10.0.1.0/24)
    │
    ▼
ALB (10.0.1.10) → Web Server (10.0.1.50)
```

**Private Subnet — App server downloads package:**
```
EC2 in Private Subnet (10.0.11.50)
    │
    ▼
Route Table (0.0.0.0/0 → NAT GW)
    │
    ▼
NAT Gateway (in Public Subnet)
    │
    ▼
Internet Gateway
    │
    ▼
Internet (yum/apt update)
```

**Private Subnet — User CANNOT reach database:**
```
User (203.0.113.50)
    │
    ▼
Internet
    │
    ▼
Internet Gateway
    │
    ▼
Route Table — NO route to 10.0.21.0/24 from outside
    │
    ✗ BLOCKED

Only App Server (10.0.11.50) in same VPC can reach RDS (10.0.21.30)
```

> 💡 **Hinglish Insight**
>
> Public subnet = building ka main gate wala floor — bahar se aa sakte ho. Private subnet = andar ka server room — sirf internal access.

---

## 8. Route Tables — How Traffic Flows

A **Route Table** is a set of rules that determine **where network traffic is directed**.

Every subnet must be associated with **exactly one** route table.

### Route Table Anatomy

| Destination | Target | Meaning |
|-------------|--------|---------|
| `10.0.0.0/16` | `local` | Traffic within VPC stays local |
| `0.0.0.0/0` | `igw-xxx` | All other traffic → Internet Gateway |
| `0.0.0.0/0` | `nat-xxx` | All other traffic → NAT Gateway |
| `10.1.0.0/16` | `pcx-xxx` | Traffic to peer VPC → Peering connection |
| `pl-xxx` (S3 prefix list) | `vpce-xxx` | S3 traffic → VPC Endpoint |

### Default Local Route

Every VPC route table automatically includes:

```
Destination: 10.0.0.0/16  →  Target: local
```

This means instances in the same VPC can talk to each other without leaving the VPC.

### Public Route Table Example

```
Route Table: public-rt
Associated Subnets: 10.0.1.0/24, 10.0.2.0/24

┌──────────────────┬─────────────────────┐
│ Destination      │ Target              │
├──────────────────┼─────────────────────┤
│ 10.0.0.0/16      │ local               │
│ 0.0.0.0/0        │ igw-0abc123         │
└──────────────────┴─────────────────────┘
```

### Private Route Table Example

```
Route Table: private-rt
Associated Subnets: 10.0.11.0/24, 10.0.12.0/24

┌──────────────────┬─────────────────────┐
│ Destination      │ Target              │
├──────────────────┼─────────────────────┤
│ 10.0.0.0/16      │ local               │
│ 0.0.0.0/0        │ nat-0def456         │
└──────────────────┴─────────────────────┘
```

### Real-World Example — Route Decision

An API server at `10.0.11.50` wants to connect to RDS at `10.0.21.30`:

1. Check route table for `10.0.21.30`
2. Matches `10.0.0.0/16 → local`
3. Traffic stays inside VPC → reaches RDS directly

Same API server wants to call `api.stripe.com` (public internet):

1. Check route table for `0.0.0.0/0`
2. Matches `0.0.0.0/0 → nat-0def456`
3. Traffic goes to NAT Gateway → IGW → internet

### Main vs Custom Route Tables

| | Main Route Table | Custom Route Table |
|---|-----------------|-------------------|
| Created | Automatically with VPC | You create |
| Default association | Subnets not explicitly associated | Subnets you associate |
| Best practice | Don't use for production | One per tier (public, private, DB) |

> **Interview Note:** If an EC2 in a "private" subnet can still be reached from the internet, check: (1) Is the subnet actually associated with a private route table? (2) Does the route table have `0.0.0.0/0 → IGW`? (3) Does the instance have a public IP?

---

## 9. Internet Gateway (IGW)

An **Internet Gateway** is a **horizontally scaled, redundant, highly available** VPC component that allows communication between your VPC and the internet.

### Key Facts

| Fact | Detail |
|------|--------|
| One IGW per VPC | You attach one IGW to one VPC |
| Region-specific | IGW exists in a specific region |
| Not in a subnet | IGW is a VPC-level resource |
| Required for public access | Without IGW, no internet in/out |
| Free | No charge for the IGW itself |

### What IGW Enables

| Direction | Example |
|-----------|---------|
| Inbound | Users accessing your website |
| Outbound | EC2 downloading packages from internet |

### IGW Attachment Flow

```
1. Create VPC (10.0.0.0/16)
2. Create Internet Gateway
3. Attach IGW to VPC
4. Create public subnet
5. Create route: 0.0.0.0/0 → IGW
6. Associate route table with public subnet
7. Launch EC2 with public IP
8. Internet ↔ EC2 works
```

### Real-World Analogy

IGW = **Main entrance gate** of your office building.

- Visitors (internet users) enter through the gate
- Employees can exit through the gate to go outside
- Without a gate, the building is completely closed off

---

## 10. NAT Gateway & NAT Instance

### NAT Gateway

**NAT** = Network Address Translation

A **NAT Gateway** allows instances in a **private subnet** to connect to the internet **without allowing the internet to initiate connections** to those instances.

| Feature | NAT Gateway |
|---------|-------------|
| Managed by | AWS (fully managed) |
| Availability | Highly available in each AZ |
| Bandwidth | Up to 100 Gbps |
| Cost | ~$0.045/hr + data processing |
| Placement | Must be in a **public subnet** |
| Elastic IP | Required (one per NAT GW) |

### NAT Gateway Traffic Flow

```
Private EC2 (10.0.11.50)
    │  "I need to reach 52.94.123.45 (npm registry)"
    ▼
Private Route Table (0.0.0.0/0 → NAT)
    ▼
NAT Gateway (10.0.1.100) in Public Subnet
    │  Replaces private IP with NAT's public IP
    ▼
Internet Gateway
    ▼
Internet
    │
    ▼ Response comes back to NAT's public IP
NAT Gateway forwards to 10.0.11.50
```

### NAT Gateway vs NAT Instance

| | NAT Gateway | NAT Instance |
|---|------------|--------------|
| Managed | AWS | You |
| HA | Built-in per AZ | You configure |
| Bandwidth | Up to 100 Gbps | Depends on instance type |
| Maintenance | None | OS patches, scaling |
| Cost | Higher | Lower (but ops cost) |
| Production | Recommended | Legacy / learning |

### Real-World Example — Why NAT?

**Scenario:** Your backend API servers in a private subnet need to:
- Download Python packages (`pip install`)
- Call external payment API (Razorpay)
- Pull Docker images

They need **outbound** internet but must **not** be reachable from outside.

**Solution:** NAT Gateway in public subnet + private route table pointing `0.0.0.0/0` to NAT.

> 💡 **Hinglish Insight**
>
> NAT Gateway = private subnet ke liye one-way window. Andar se bahar dekh sakte ho, bahar se andar nahi.

### Cost Optimization Tip

- **One NAT GW per AZ** = High availability (recommended for production)
- **One NAT GW total** = Cheaper, but single point of failure
- **VPC Endpoints for S3/DynamoDB** = Avoid NAT data charges for AWS service traffic

---

## 11. Security Groups

A **Security Group** is a **virtual firewall** at the **instance level** (ENI level).

### Key Characteristics

| Feature | Detail |
|---------|--------|
| Level | Instance (ENI) |
| Stateful | Yes — return traffic automatically allowed |
| Rules | Allow only (no deny rules) |
| Default | Deny all inbound, allow all outbound |
| Evaluation | All rules evaluated before decision |

### Stateful Explained

If you allow **inbound** port 443:
- User sends HTTPS request → **allowed**
- Server sends HTTPS response back → **automatically allowed** (no outbound rule needed)

### Example — Web Server Security Group

```
Security Group: web-sg

Inbound Rules:
┌──────────┬──────────┬─────────────┬──────────────────┐
│ Type     │ Protocol │ Port        │ Source           │
├──────────┼──────────┼─────────────┼──────────────────┤
│ HTTP     │ TCP      │ 80          │ 0.0.0.0/0        │
│ HTTPS    │ TCP      │ 443         │ 0.0.0.0/0        │
│ SSH      │ TCP      │ 22          │ 10.0.1.0/24      │  ← Bastion only
└──────────┴──────────┴─────────────┴──────────────────┘

Outbound Rules:
┌──────────┬──────────┬─────────────┬──────────────────┐
│ All traffic │ All   │ All         │ 0.0.0.0/0        │
└──────────┴──────────┴─────────────┴──────────────────┘
```

### Example — Database Security Group

```
Security Group: db-sg

Inbound Rules:
┌──────────┬──────────┬─────────────┬──────────────────┐
│ MySQL    │ TCP      │ 3306        │ sg-app-sg        │  ← Only app SG
└──────────┴──────────┴─────────────┴──────────────────┘
```

**Best practice:** Reference security group IDs, not IP addresses, for internal traffic.

### Real-World — 3-Tier Security Group Chain

```
Internet → ALB (alb-sg: allow 80,443 from 0.0.0.0/0)
              │
              ▼
         Web EC2 (web-sg: allow 80 from alb-sg only)
              │
              ▼
         App EC2 (app-sg: allow 8080 from web-sg only)
              │
              ▼
         RDS (db-sg: allow 3306 from app-sg only)
```

Each tier only accepts traffic from the tier above it.

---

## 12. Network ACL (NACL)

A **Network ACL (NACL)** is a **subnet-level** firewall — an optional layer of security.

### Key Characteristics

| Feature | Detail |
|---------|--------|
| Level | Subnet |
| Stateful | **No** — must allow inbound AND outbound separately |
| Rules | Allow **and** Deny |
| Rule order | Evaluated by rule number (lowest first) |
| Default | Allow all inbound and outbound |

### NACL Example — Block Specific IP

```
NACL: public-nacl

Inbound:
┌──────┬─────────┬──────────┬───────────────┬──────────┐
│ Rule │ Type    │ Protocol │ Source        │ Action   │
├──────┼─────────┼──────────┼───────────────┼──────────┤
│ 100  │ All     │ All      │ 203.0.113.99  │ DENY     │  ← Block attacker
│ 110  │ HTTP    │ TCP      │ 0.0.0.0/0     │ ALLOW    │
│ 120  │ HTTPS   │ TCP      │ 0.0.0.0/0     │ ALLOW    │
│ *    │ All     │ All      │ 0.0.0.0/0     │ DENY     │  ← Default deny
└──────┴─────────┴──────────┴───────────────┴──────────┘

Outbound:
┌──────┬─────────┬──────────┬───────────────┬──────────┐
│ 100  │ All     │ All      │ 0.0.0.0/0     │ ALLOW    │
│ *    │ All     │ All      │ 0.0.0.0/0     │ DENY     │
└──────┴─────────┴──────────┴───────────────┴──────────┘
```

### Security Group vs NACL — Complete Comparison

| Feature | Security Group | NACL |
|---------|---------------|------|
| Level | Instance (ENI) | Subnet |
| Stateful | Yes | No |
| Allow rules | Yes | Yes |
| Deny rules | No | Yes |
| Rule evaluation | All rules | By rule number (order matters) |
| Default | Deny inbound, allow outbound | Allow all |
| Applies to | Instance launch | Entire subnet |
| Return traffic | Auto-allowed | Must explicitly allow |

### When to Use Which?

| Use Security Group for | Use NACL for |
|------------------------|--------------|
| Everyday firewall rules | Blocking specific IP ranges |
| Per-service access control | Subnet-wide deny rules |
| Referencing other SGs | Compliance requirements |
| 99% of use cases | Extra layer when needed |

> **Interview Note:** Security Groups are usually sufficient. NACLs add complexity because they're stateless — you must configure both inbound and outbound for responses.

---

## 13. Availability Zones & Multi-AZ Design

### Rules

- Each subnet = **one AZ only**
- For high availability, **duplicate subnets across AZs**
- RDS Multi-AZ = primary in one AZ, standby in another

### Multi-AZ Architecture

```
Region: ap-south-1 (Mumbai)

┌──────────────────── AZ: ap-south-1a ────────────────────┐
│  Public:  10.0.1.0/24   → ALB, NAT GW                 │
│  Private: 10.0.11.0/24  → App Server 1                │
│  Private: 10.0.21.0/24  → RDS Primary                 │
└───────────────────────────────────────────────────────┘

┌──────────────────── AZ: ap-south-1b ────────────────────┐
│  Public:  10.0.2.0/24   → ALB, NAT GW                 │
│  Private: 10.0.12.0/24  → App Server 2                │
│  Private: 10.0.22.0/24  → RDS Standby                 │
└───────────────────────────────────────────────────────┘
```

### Real-World — What Happens When AZ Fails?

**Scenario:** `ap-south-1a` goes down (power failure).

| Component | Behavior |
|-----------|----------|
| ALB | Routes traffic to `ap-south-1b` targets only |
| App Server 1 | Down — ALB stops sending traffic |
| App Server 2 | Continues serving requests |
| RDS | Automatic failover to standby in `1b` |

**Result:** Application stays online (if designed correctly).

---

## 14. VPC Endpoints

**VPC Endpoints** allow private connectivity to AWS services **without** going through the internet or NAT Gateway.

### Types

| Type | Services | How |
|------|----------|-----|
| **Gateway Endpoint** | S3, DynamoDB | Route table entry |
| **Interface Endpoint** | Most other AWS services | ENI with private IP in subnet |

### Why Use VPC Endpoints?

| Without Endpoint | With Endpoint |
|-----------------|---------------|
| Traffic → NAT GW → Internet → S3 | Traffic → VPC Endpoint → S3 (stays in AWS network) |
| NAT data processing charges | No NAT charges |
| Internet exposure | Private only |

### Real-World Example

Your app servers upload user photos to S3:

**Without VPC Endpoint:**
```
EC2 (private) → NAT GW → IGW → Internet → S3
Cost: NAT processing + slower
```

**With VPC Endpoint (Gateway):**
```
EC2 (private) → VPC Endpoint → S3
Cost: Free (gateway endpoints have no charge)
Faster, more secure
```

---

## 15. VPC Peering & Transit Gateway

### VPC Peering

Connects two VPCs **privately** using AWS network.

| Feature | Detail |
|---------|--------|
| Scope | Two VPCs only |
| CIDR overlap | **Not allowed** — CIDRs must not overlap |
| Transitive | **No** — A↔B and B↔C does NOT mean A↔C |
| Cross-region | Possible (inter-region peering) |
| Cross-account | Possible |

**Use case:** Dev VPC needs to access shared services VPC (logging, monitoring).

### Transit Gateway

Central hub connecting **multiple VPCs**, VPNs, and Direct Connect.

```
                    Transit Gateway
                   /       |        \
              VPC-Dev   VPC-Prod   VPC-Shared
                                      |
                                   VPN
                                      |
                              On-Premises Office
```

**Use case:** Enterprise with 20+ VPCs — managing peering mesh becomes nightmare; Transit Gateway simplifies.

| | VPC Peering | Transit Gateway |
|---|------------|-----------------|
| Connections | 1:1 | Hub and spoke (many) |
| Transitive routing | No | Yes |
| Cost | Data transfer only | Per attachment + data |
| Complexity | Low (2 VPCs) | Better for many VPCs |

---

## 16. VPC Flow Logs

Captures **IP traffic** information going to/from network interfaces in your VPC.

### What Gets Logged

- Source IP, destination IP
- Source port, destination port
- Protocol
- Accept or reject
- Bytes transferred

### Use Cases

| Use Case | How Flow Logs Help |
|----------|-------------------|
| Security audit | Detect unauthorized access attempts |
| Troubleshooting | "Why can't app reach database?" — check rejected flows |
| Compliance | Prove network isolation for auditors |
| Forensics | Investigate security incidents |

### Example Log Entry (simplified)

```
srcAddr=10.0.11.50  dstAddr=10.0.21.30  dstPort=3306  action=ACCEPT
srcAddr=203.0.113.99  dstAddr=10.0.1.50  dstPort=22  action=REJECT
```

Second line: attacker tried SSH to public instance — rejected by Security Group.

---

# Part 2 — Real-World Architectures

---

## 17. Real-World Architecture — E-Commerce App

**Company:** Online fashion store (like Myntra)
**Region:** ap-south-1
**Requirement:** Handle festival sales, secure payments, PCI compliance

### Architecture

```
                         Internet (Customers)
                                │
                       Internet Gateway
                                │
              ┌─────────────────┴─────────────────┐
              │         Public Subnets           │
              │  ap-south-1a    ap-south-1b      │
              │  10.0.1.0/24    10.0.2.0/24      │
              │       ALB (Multi-AZ)              │
              │       NAT Gateway (per AZ)          │
              └─────────────────┬─────────────────┘
                                │
              ┌─────────────────┴─────────────────┐
              │        Private Subnets (App)       │
              │  10.0.11.0/24   10.0.12.0/24      │
              │  EC2 API × 3    EC2 API × 3        │
              │  Auto Scaling Group (min 2, max 20) │
              └─────────────────┬─────────────────┘
                                │
              ┌─────────────────┴─────────────────┐
              │      Private Subnets (Database)    │
              │  10.0.21.0/24   10.0.22.0/24      │
              │  RDS MySQL (Multi-AZ)              │
              │  ElastiCache Redis                 │
              └───────────────────────────────────┘

              VPC Endpoint → S3 (product images)
              VPC Endpoint → Secrets Manager (DB credentials)
```

### Security Group Chain

| Resource | SG | Inbound |
|----------|-----|---------|
| ALB | alb-sg | 443 from 0.0.0.0/0 |
| API EC2 | api-sg | 8080 from alb-sg |
| RDS | db-sg | 3306 from api-sg |
| Redis | cache-sg | 6379 from api-sg |

### Festival Sale Scaling

| Normal Day | Festival Sale |
|------------|---------------|
| 2 API servers | Auto Scale to 20 |
| 1 NAT GW per AZ | Same (sufficient) |
| RDS db.r5.large | RDS read replicas added |
| ALB handles routing | ALB distributes across all AZs |

---

## 18. Real-World Architecture — Banking App

**Company:** Digital banking startup
**Requirement:** Strictest security, no public database access, audit logging

### Architecture

```
Internet
    │
Internet Gateway
    │
Public Subnet (10.0.1.0/24)
    └── ALB only (no direct EC2 public access)
    │
Private Subnet (10.0.11.0/24)
    └── Application servers
    └── WAF on ALB blocks SQL injection, XSS
    │
Private Subnet (10.0.21.0/24)
    └── RDS (encrypted at rest, Multi-AZ)
    └── NO internet route at all
    │
Private Subnet (10.0.31.0/24)
    └── AWS Systems Manager Session Manager
    └── NO Bastion host needed (no SSH port open)
    │
VPC Flow Logs → CloudWatch → Security team alerts
CloudTrail → All API calls logged
```

### Key Differences from E-Commerce

| Aspect | E-Commerce | Banking |
|--------|-----------|---------|
| Bastion host | Optional | Avoided — use SSM Session Manager |
| DB internet | None | None (stricter SG rules) |
| WAF | Optional | Mandatory on ALB |
| Encryption | Recommended | Required (at rest + in transit) |
| Audit | Flow Logs | Flow Logs + CloudTrail + Config |

---

## 19. Step-by-Step: Build a Custom VPC

### Step 1 — Create VPC

```
Name: production-vpc
CIDR: 10.0.0.0/16
```

### Step 2 — Create Internet Gateway

```
Create IGW → Attach to production-vpc
```

### Step 3 — Create Subnets

| Name | CIDR | AZ |
|------|------|-----|
| public-1a | 10.0.1.0/24 | ap-south-1a |
| public-1b | 10.0.2.0/24 | ap-south-1b |
| private-app-1a | 10.0.11.0/24 | ap-south-1a |
| private-app-1b | 10.0.12.0/24 | ap-south-1b |
| private-db-1a | 10.0.21.0/24 | ap-south-1a |
| private-db-1b | 10.0.22.0/24 | ap-south-1b |

### Step 4 — Create NAT Gateways

```
NAT-GW-1a in public-1a (allocate Elastic IP)
NAT-GW-1b in public-1b (allocate Elastic IP)
```

### Step 5 — Create Route Tables

**public-rt:**
```
10.0.0.0/16 → local
0.0.0.0/0   → igw-xxx
```
Associate: public-1a, public-1b

**private-app-rt:**
```
10.0.0.0/16 → local
0.0.0.0/0   → nat-1a (in 1a subnets) or nat-1b (in 1b subnets)
```
Associate: private-app-1a, private-app-1b

**private-db-rt:**
```
10.0.0.0/16 → local
(no 0.0.0.0/0 route — database has NO internet access)
```
Associate: private-db-1a, private-db-1b

### Step 6 — Launch Resources

```
ALB → public subnets
EC2 → private-app subnets
RDS → private-db subnets
```

### Step 7 — Configure Security Groups

Chain: alb-sg → app-sg → db-sg (reference SG IDs, not IPs)

---

## 20. Common Mistakes & Troubleshooting

| Problem | Likely Cause | Fix |
|---------|-------------|-----|
| Can't SSH to EC2 | No public IP, wrong SG, wrong subnet | Check public IP, SG port 22, public subnet + IGW route |
| EC2 can't reach internet | Private subnet without NAT route | Add NAT GW + route `0.0.0.0/0 → NAT` |
| App can't reach RDS | SG not allowing app SG on DB port | Add inbound rule: db-sg allows port 3306 from app-sg |
| Website not loading | ALB in private subnet | Move ALB to public subnet |
| NAT GW not working | NAT in private subnet (wrong!) | NAT must be in **public** subnet |
| Subnet has no internet but should | Wrong route table association | Associate subnet with correct route table |
| VPC Peering not working | CIDR overlap or missing route | Ensure non-overlapping CIDRs, add routes in both VPCs |
| High NAT costs | All S3/DynamoDB traffic through NAT | Add VPC Gateway Endpoints |

### Troubleshooting Checklist

When connectivity fails, check in this order:

```
1. Security Group rules (most common issue)
2. NACL rules (if customized)
3. Route table (correct association + routes)
4. Internet Gateway attached?
5. NAT Gateway in public subnet?
6. Instance has correct IP (public vs private)?
7. VPC Flow Logs for ACCEPT/REJECT
```

---

## 21. Key Takeaways & Quick Revision

| Concept | One-Line Summary |
|---------|-----------------|
| **VPC** | Your private network inside AWS |
| **CIDR** | IP address range for VPC/subnet |
| **Subnet** | Smaller network in one AZ |
| **Public Subnet** | Route to IGW — internet accessible |
| **Private Subnet** | No IGW route — protected |
| **Route Table** | Rules directing traffic |
| **IGW** | VPC ↔ Internet gateway |
| **NAT Gateway** | Outbound-only internet for private subnets |
| **Security Group** | Stateful instance firewall (allow only) |
| **NACL** | Stateless subnet firewall (allow + deny) |
| **VPC Endpoint** | Private access to AWS services |
| **VPC Peering** | Connect two VPCs (non-transitive) |
| **Transit Gateway** | Hub for many VPCs |
| **Flow Logs** | Network traffic audit logs |

### Architecture Pattern to Remember

```
Internet → IGW → Public Subnet (ALB, NAT)
                    │
              Private Subnet (App)
                    │
              Private Subnet (DB)
```

---

## Resources

- [AWS VPC Documentation](https://docs.aws.amazon.com/vpc/)
- [VPC with Public and Private Subnets (NAT)](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-example-private-subnets-nat.html)
- [Security Groups vs NACLs](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_Security.html)
- [VPC Flow Logs](https://docs.aws.amazon.com/vpc/latest/userguide/flow-logs.html)
- [AWS VPC Workshop](https://catalog.workshops.aws/vpc/en-US)

---

*Practice with `InterviewQuestions.md` in this folder. Last updated: July 2026*
