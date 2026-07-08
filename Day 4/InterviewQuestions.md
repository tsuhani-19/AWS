# Amazon VPC — Interview Questions & Answers

Practice questions covering VPC, Subnets, Route Tables, Internet Gateway, NAT Gateway, Security Groups, NACLs, and real-world architecture scenarios.

---

# Beginner Level

---

## Q1. What is Amazon VPC?

### Answer

Amazon **Virtual Private Cloud (VPC)** is a logically isolated virtual network inside AWS where you launch and manage cloud resources such as EC2, RDS, Lambda, and Load Balancers.

It gives you complete control over:

- IP address ranges (CIDR)
- Subnets and Availability Zones
- Route tables and internet connectivity
- Security (Security Groups, NACLs)

### Real-World Example

When Flipkart runs on AWS, their entire application infrastructure — web servers, APIs, databases — lives inside their own VPC. No other AWS customer can access their network.

### 💡 Hinglish

VPC matlab AWS ke andar tumhara apna private network. Jaise apartment building mein har flat alag hota hai, waise har company ka VPC alag hota hai.

### 🎯 Interview Tip

Don't just say "VPC is a network." Mention **isolation**, **control over IP ranges**, and **security layers**.

---

## Q2. What is a Subnet?

### Answer

A **subnet** is a **segment of your VPC's IP address range** that resides in **exactly one Availability Zone**.

Key points:

- Subnet CIDR must be a subset of VPC CIDR
- One subnet = one AZ (cannot span multiple AZs)
- Used to isolate resources by tier (web, app, database)

### Example

```
VPC: 10.0.0.0/16

Subnet 1: 10.0.1.0/24  → ap-south-1a (Public)
Subnet 2: 10.0.2.0/24  → ap-south-1b (Public)
Subnet 3: 10.0.11.0/24 → ap-south-1a (Private - App)
Subnet 4: 10.0.21.0/24 → ap-south-1a (Private - Database)
```

### 💡 Hinglish

Subnet = VPC ke andar ek chhota network block. Har subnet ek hi Availability Zone mein hota hai.

---

## Q3. What is the difference between a Public Subnet and a Private Subnet?

### Answer

| | Public Subnet | Private Subnet |
|---|--------------|----------------|
| **Route to IGW** | Yes (`0.0.0.0/0 → IGW`) | No |
| **Inbound from internet** | Possible (if SG allows) | Blocked |
| **Outbound to internet** | Direct via IGW | Via NAT Gateway |
| **Public IP** | Usually assigned | Usually not |
| **Typical resources** | ALB, Bastion, NAT GW | App servers, RDS, Redis |

### Real-World Example

**Public:** Your restaurant's front door area — customers can walk in.

**Private:** The kitchen — only staff (internal services) can access it. Customers never enter directly.

### 🎯 Interview Tip

A subnet is **not** public or private by name — it's determined by the **route table** associated with it.

---

## Q4. What is CIDR? Explain with an example.

### Answer

**CIDR (Classless Inter-Domain Routing)** defines the IP address range of a VPC or subnet.

Format: `IP_Address/Prefix`

| CIDR | Total IPs | Usable in AWS | Use |
|------|-----------|---------------|-----|
| `10.0.0.0/16` | 65,536 | 65,531 | Entire VPC |
| `10.0.1.0/24` | 256 | 251 | One subnet |
| `10.0.1.0/28` | 16 | 11 | Small subnet |

AWS reserves **5 IP addresses** per subnet (network, router, DNS, future, broadcast).

### Example

```
VPC:     10.0.0.0/16   → 65,536 addresses
Subnet:  10.0.1.0/24   → 256 addresses (251 usable)
EC2 IP:  10.0.1.50     → One instance in that subnet
```

### 💡 Hinglish

`/16` matlab bada area, `/24` matlab usme ek chhota block. Jaise city aur usme colony.

---

## Q5. What is a Route Table?

### Answer

A **Route Table** contains rules that determine **where network traffic is directed**.

Each subnet must be associated with exactly one route table.

### Example Routes

| Destination | Target | Meaning |
|-------------|--------|---------|
| `10.0.0.0/16` | `local` | Traffic within VPC stays local |
| `0.0.0.0/0` | `igw-xxx` | All other traffic → Internet Gateway |
| `0.0.0.0/0` | `nat-xxx` | All other traffic → NAT Gateway |

### Real-World Analogy

Route table = **GPS for network traffic**. It decides which road (gateway) traffic takes to reach its destination.

---

## Q6. What is an Internet Gateway (IGW)?

### Answer

An **Internet Gateway** is a horizontally scaled, highly available VPC component that allows bidirectional communication between your VPC and the internet.

| Fact | Detail |
|------|--------|
| Scope | One IGW per VPC |
| Cost | Free |
| Required for | Public internet access |
| Attached to | VPC (not a subnet) |

### Real-World Example

IGW = Main gate of your office building. Without it, nobody from outside can enter, and nobody inside can go out.

---

## Q7. What is a NAT Gateway?

### Answer

A **NAT (Network Address Translation) Gateway** allows instances in a **private subnet** to initiate outbound connections to the internet while **preventing** the internet from initiating connections to those instances.

| Feature | Detail |
|---------|--------|
| Placement | Must be in a **public subnet** |
| Managed by | AWS |
| Requires | Elastic IP |
| Direction | Outbound only |

### Real-World Example

Your backend servers need to download security patches from the internet. NAT Gateway lets them reach out, but hackers on the internet cannot reach the servers directly.

### 💡 Hinglish

NAT = one-way mirror. Andar se bahar dekh sakte ho, bahar se andar nahi.

---

## Q8. What is the difference between a Security Group and a NACL?

### Answer

| Feature | Security Group | NACL |
|---------|---------------|------|
| **Level** | Instance (ENI) | Subnet |
| **Stateful** | Yes | No |
| **Allow rules** | Yes | Yes |
| **Deny rules** | No | Yes |
| **Rule evaluation** | All rules | By rule number (order matters) |
| **Default** | Deny inbound, allow outbound | Allow all |

### Real-World Analogy

- **Security Group** = Bouncer at each room's door (knows who came in, lets them out automatically)
- **NACL** = Security checkpoint at the building floor entrance (checks both entry and exit separately)

### 🎯 Interview Tip

For 99% of use cases, Security Groups are enough. NACLs add complexity because they're stateless.

---

## Q9. What is the difference between Default VPC and Custom VPC?

### Answer

| | Default VPC | Custom VPC |
|---|------------|------------|
| **Created by** | AWS automatically | You manually |
| **CIDR** | `172.31.0.0/16` (pre-set) | You choose |
| **Subnets** | Public per AZ | You design |
| **IGW** | Pre-attached | You attach |
| **Use case** | Learning, quick tests | Production |

---

## Q10. Can a subnet span multiple Availability Zones?

### Answer

**No.** A subnet exists in exactly **one** Availability Zone.

For high availability, you create **duplicate subnets** in different AZs:

```
ap-south-1a → 10.0.1.0/24 (Public)
ap-south-1b → 10.0.2.0/24 (Public)
ap-south-1c → 10.0.3.0/24 (Public)
```

---

# Intermediate Level

---

## Q11. Explain the complete traffic flow when a user visits your website hosted on AWS.

### Answer

```
1. User types www.myapp.com in browser
2. DNS (Route 53) resolves to ALB public DNS
3. Request hits Internet
4. Internet Gateway receives request
5. Route Table (public): 0.0.0.0/0 → IGW routes it in
6. ALB in Public Subnet receives request (Security Group allows 443)
7. ALB forwards to EC2 in Private Subnet (app-sg allows traffic from alb-sg)
8. App queries RDS in Private DB Subnet (db-sg allows 3306 from app-sg)
9. Response travels back: RDS → App → ALB → IGW → Internet → User
```

### 🎯 Interview Tip

Draw this flow on a whiteboard. Interviewers love seeing you trace the full path.

---

## Q12. Why is a NAT Gateway placed in a Public Subnet?

### Answer

NAT Gateway needs **outbound internet access** to forward traffic from private instances.

Public subnet has route: `0.0.0.0/0 → IGW`

Flow:
```
Private EC2 → NAT Gateway (public subnet) → IGW → Internet
```

If NAT were in a private subnet without IGW route, it couldn't reach the internet itself.

### Common Mistake

Placing NAT Gateway in a private subnet — it won't work.

---

## Q13. How many IP addresses does AWS reserve in a subnet?

### Answer

AWS reserves **5 IP addresses** in every subnet:

| IP Position | Purpose |
|-------------|---------|
| 1st (network) | Network address |
| 2nd | VPC router |
| 3rd | DNS server (if enabled) |
| 4th | Reserved for future use |
| Last (broadcast) | Broadcast address |

**Example:** `10.0.1.0/24` = 256 total, **251 usable**.

---

## Q14. What is a VPC Endpoint? Why use it?

### Answer

A **VPC Endpoint** enables **private connectivity** to AWS services without using the internet, NAT Gateway, or VPN.

| Type | Services | Cost |
|------|----------|------|
| **Gateway Endpoint** | S3, DynamoDB | Free |
| **Interface Endpoint** | Most AWS services | Per hour + data |

### Real-World Example

Your app uploads images to S3. Without endpoint: traffic goes through NAT (charges + latency). With Gateway Endpoint: traffic stays in AWS network (free + faster).

---

## Q15. What is VPC Peering?

### Answer

**VPC Peering** connects two VPCs privately using AWS infrastructure.

| Rule | Detail |
|------|--------|
| Scope | Two VPCs only |
| CIDR overlap | **Not allowed** |
| Transitive | **No** — A↔B + B↔C ≠ A↔C |
| Cross-region | Supported |
| Cross-account | Supported |

### Use Case

Development VPC needs to access a Shared Services VPC (centralized logging, monitoring).

---

## Q16. What is Transit Gateway?

### Answer

**Transit Gateway** is a central hub that connects multiple VPCs, VPN connections, and Direct Connect gateways.

```
Transit Gateway
  ├── VPC-Dev
  ├── VPC-Staging
  ├── VPC-Production
  └── VPN → On-Premises Office
```

### VPC Peering vs Transit Gateway

| | VPC Peering | Transit Gateway |
|---|------------|-----------------|
| Connections | 1:1 | Many (hub and spoke) |
| Transitive | No | Yes |
| Best for | 2 VPCs | Enterprise (many VPCs) |

---

## Q17. What are VPC Flow Logs?

### Answer

**VPC Flow Logs** capture information about IP traffic going to and from network interfaces in your VPC.

Logged data includes:

- Source/destination IP and port
- Protocol
- Accept or reject
- Bytes transferred

### Use Cases

- Security auditing ("Who tried to access our database?")
- Troubleshooting ("Why is traffic being rejected?")
- Compliance reporting

---

## Q18. Explain Stateful vs Stateless firewalls with examples.

### Answer

### Stateful (Security Group)

```
Inbound rule: Allow port 443 from 0.0.0.0/0

User sends HTTPS request → ALLOWED (inbound rule matches)
Server sends HTTPS response → AUTOMATICALLY ALLOWED (stateful)
```

You don't need an outbound rule for the response.

### Stateless (NACL)

```
Inbound rule: Allow port 443
Outbound rule: Must ALSO allow ephemeral ports (1024-65535)

User sends HTTPS request → ALLOWED (inbound rule)
Server sends response on port 45678 → BLOCKED unless outbound rule allows it
```

You must configure **both directions** explicitly.

---

## Q19. What happens if two VPCs have overlapping CIDR blocks and you try to peer them?

### Answer

**VPC Peering will fail.** AWS does not allow peering between VPCs with overlapping CIDR ranges.

### Example

```
VPC-A: 10.0.0.0/16
VPC-B: 10.0.0.0/16  ← OVERLAP — peering rejected
```

### Solution

Plan CIDR blocks before creating VPCs. Use non-overlapping ranges:

```
VPC-Dev:  10.0.0.0/16
VPC-Prod: 10.1.0.0/16
VPC-Shared: 10.2.0.0/16
```

---

## Q20. How do you make an existing private subnet instance accessible from the internet temporarily?

### Answer

**Best practices (in order):**

1. **AWS Systems Manager Session Manager** — No SSH port needed, no bastion required
2. **Bastion Host** — EC2 in public subnet, SSH to bastion, then SSH to private instance
3. **Elastic IP + move to public subnet** — Not recommended for production
4. **VPN connection** — Secure tunnel from office to VPC

### Never Do in Production

- Open SSH (port 22) to `0.0.0.0/0` on a private instance
- Assign public IP to database servers

---

# Advanced / Scenario-Based

---

## Q21. Design a 3-tier VPC architecture for a production e-commerce application.

### Answer

```
VPC: 10.0.0.0/16 | Region: ap-south-1

┌─── AZ: ap-south-1a ───────────────────────────────┐
│ Public:  10.0.1.0/24  → ALB, NAT Gateway         │
│ Private: 10.0.11.0/24 → API Servers (ASG)        │
│ Private: 10.0.21.0/24 → RDS Primary, Redis      │
└──────────────────────────────────────────────────┘

┌─── AZ: ap-south-1b ───────────────────────────────┐
│ Public:  10.0.2.0/24  → ALB, NAT Gateway         │
│ Private: 10.0.12.0/24 → API Servers (ASG)        │
│ Private: 10.0.22.0/24 → RDS Standby, Redis       │
└──────────────────────────────────────────────────┘

Route Tables:
  public-rt:    0.0.0.0/0 → IGW
  private-rt:   0.0.0.0/0 → NAT (per AZ)
  db-rt:        local only (no internet)

Security Groups:
  alb-sg → 443 from 0.0.0.0/0
  app-sg → 8080 from alb-sg
  db-sg  → 3306 from app-sg

VPC Endpoints: S3 (product images), Secrets Manager
```

---

## Q22. Your EC2 instance in a private subnet cannot download packages from the internet. How do you troubleshoot?

### Answer

Check in this order:

| Step | Check | Fix |
|------|-------|-----|
| 1 | Route table associated with subnet | Must have `0.0.0.0/0 → NAT Gateway` |
| 2 | NAT Gateway exists and is in **public** subnet | Create/move NAT to public subnet |
| 3 | NAT Gateway's public subnet has `0.0.0.0/0 → IGW` | Fix public route table |
| 4 | NAT Gateway has Elastic IP | Allocate and attach EIP |
| 5 | Security Group outbound rules | Default allows all outbound — check if modified |
| 6 | NACL outbound rules | Ensure ephemeral ports allowed if NACL is custom |
| 7 | NAT Gateway status | Must be "Available" |

### VPC Flow Logs

Enable flow logs and check for `REJECT` entries from the EC2's IP.

---

## Q23. How would you reduce NAT Gateway costs in a VPC?

### Answer

| Strategy | How |
|----------|-----|
| **VPC Gateway Endpoints** | Route S3/DynamoDB traffic directly (free) |
| **Interface Endpoints** | For other AWS services (cheaper than NAT for high volume) |
| **Single NAT Gateway** | One NAT instead of per-AZ (saves cost, loses HA) |
| **NAT Instance** | Cheaper but self-managed (not recommended for production) |
| **Optimize traffic** | Cache dependencies, use private package mirrors |
| **Right-size** | Monitor data processing charges in Cost Explorer |

### Real-World

A company spent $2,000/month on NAT Gateway. After adding S3 Gateway Endpoint, cost dropped to $400/month because 80% of NAT traffic was S3 uploads.

---

## Q24. Explain the difference between NACL and Security Group with a scenario.

### Answer

**Scenario:** Block IP `203.0.113.99` (known attacker) from accessing your web servers.

### Using Security Group

**Cannot do it.** Security Groups only support ALLOW rules, not DENY.

### Using NACL

```
NACL Inbound:
Rule 100: DENY all from 203.0.113.99
Rule 110: ALLOW HTTP from 0.0.0.0/0
Rule 120: ALLOW HTTPS from 0.0.0.0/0
```

NACL blocks the attacker at the subnet boundary before traffic even reaches the Security Group.

### Best Practice

Use **both layers**: NACL for broad subnet-level blocks, Security Group for instance-level allow rules.

---

## Q25. You have 15 VPCs in your organization. Should you use VPC Peering or Transit Gateway?

### Answer

**Transit Gateway.**

With 15 VPCs, full mesh peering requires `n(n-1)/2 = 105` peering connections. Managing routes in each VPC becomes a nightmare.

```
Transit Gateway:
  15 VPC attachments
  1 VPN attachment (on-premises)
  Centralized route management
```

| VPCs | Recommended |
|------|-------------|
| 2 | VPC Peering |
| 3–5 | VPC Peering (manageable) |
| 6+ | Transit Gateway |

---

## Q26. How do you connect your on-premises office network to AWS VPC?

### Answer

| Method | Speed | Cost | Use Case |
|--------|-------|------|----------|
| **Site-to-Site VPN** | Up to 1.25 Gbps | Low | Small/medium offices |
| **AWS Direct Connect** | 1–100 Gbps | High | Enterprise, high bandwidth |
| **VPN over Direct Connect** | High + encrypted | Highest | Enterprise + encryption |

### Hybrid Architecture

```
Office Network (192.168.0.0/16)
        │
   VPN / Direct Connect
        │
   AWS VPC (10.0.0.0/16)
        │
   Private Subnets (App + DB)
```

**Important:** Office CIDR and VPC CIDR must **not overlap**.

---

## Q27. What is the Shared Responsibility Model for VPC security?

### Answer

| AWS Responsibility | Customer Responsibility |
|---------------------|------------------------|
| Physical network security | Security Group configuration |
| Infrastructure protection | NACL configuration |
| VPC infrastructure | Route table design |
| Managed services (NAT GW, IGW) | Subnet architecture |
| | VPC Flow Logs setup |
| | Encryption in transit |
| | Network monitoring |

---

## Q28. Design VPC architecture for a banking application with strict compliance.

### Answer

```
VPC: 10.0.0.0/16

Public Subnet (minimal):
  └── ALB only (with WAF attached)
  └── NO direct EC2 public access

Private Subnet (Application):
  └── API servers
  └── Security Groups: least privilege

Private Subnet (Database):
  └── RDS with encryption at rest
  └── NO route to internet (no NAT route in db route table)
  └── SG: only app-sg on port 3306

Management:
  └── AWS Systems Manager Session Manager (no bastion, no SSH)
  └── VPC Flow Logs → CloudWatch → alerts
  └── CloudTrail for API audit
  └── AWS Config for compliance rules

VPC Endpoints:
  └── S3, Secrets Manager, KMS (no internet exposure)
```

---

# Quick-Fire Round

| # | Question | Short Answer |
|---|----------|-------------|
| 1 | What makes a subnet public? | Route `0.0.0.0/0 → IGW` in its route table |
| 2 | Can one subnet span two AZs? | No |
| 3 | Is Security Group stateful? | Yes |
| 4 | Is NACL stateful? | No |
| 5 | Where does NAT Gateway go? | Public subnet |
| 6 | How many IGW per VPC? | One |
| 7 | Reserved IPs per subnet? | 5 |
| 8 | CIDR for 256 IPs? | `/24` |
| 9 | VPC Peering transitive? | No |
| 10 | Free VPC Endpoint type? | Gateway (S3, DynamoDB) |
| 11 | Default SG inbound? | Deny all |
| 12 | Default SG outbound? | Allow all |
| 13 | NACL can deny? | Yes |
| 14 | SG can deny? | No |
| 15 | Flow Logs capture? | IP traffic metadata |

---

# Troubleshooting Scenarios

---

## S1. Website works from ALB but EC2 public IP doesn't respond.

**Answer:** ALB is in public subnet with correct SG. EC2 might be in private subnet, or its SG doesn't allow direct traffic, or no public IP assigned. ALB routes internally — it doesn't need EC2 to have a public IP.

---

## S2. Two EC2 instances in the same VPC cannot ping each other.

**Answer:** Check: (1) Security Groups — must allow ICMP or the protocol used, (2) Different subnets? Route table `local` route should handle it, (3) NACL blocking traffic, (4) OS-level firewall on instances.

---

## S3. RDS in private subnet — app server can't connect.

**Answer:** Most common: RDS Security Group doesn't allow inbound from app server's Security Group on the database port. Fix: Add inbound rule to db-sg allowing port 3306/5432 from app-sg.

---

## S4. NAT Gateway charges are very high.

**Answer:** Check if S3/DynamoDB traffic is going through NAT. Add Gateway Endpoints. Check for misconfigured routes sending internal traffic through NAT.

---

# Self-Assessment

| Section | Questions | Your Score |
|---------|-----------|------------|
| Beginner | Q1–Q10 | /10 |
| Intermediate | Q11–Q20 | /10 |
| Advanced / Scenario | Q21–Q28 | /8 |
| Quick-Fire | 15 items | /15 |
| Troubleshooting | S1–S4 | /4 |
| **Total** | | **/47** |

**Scoring guide:**

- 40–47: Excellent — production-ready VPC knowledge
- 30–39: Good — review route tables and NAT flow
- Below 30: Re-read `README.md` and retry

---

*Practice explaining answers out loud and drawing architecture diagrams on paper. VPC interviews often include whiteboard design questions.*
