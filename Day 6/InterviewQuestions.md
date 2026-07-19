
# Q1. What is DNS?

## Answer

DNS (Domain Name System) is a globally distributed, hierarchical naming
system that translates human-readable domain names into IP addresses.

Humans prefer names such as `amazon.com`, `google.com`, or `github.com`,
whereas computers communicate using numerical IP addresses such as
`54.239.28.85` or `142.250.183.14`.

Without DNS, every user would have to remember the IP address of every
website they wanted to visit. DNS eliminates this problem by acting as a
directory service that maps names to network destinations.

DNS is not a single server or a single database. It is a distributed
system consisting of millions of DNS servers around the world that work
together to answer queries.

### Example

When a user types `amazon.com` into a browser:

1.  The browser checks its local DNS cache.
2.  If the address is not cached, the operating system asks a recursive
    DNS resolver.
3.  The resolver contacts the DNS hierarchy until it reaches Amazon's
    authoritative DNS servers.
4.  The authoritative server returns the correct record.
5.  The browser connects to the returned destination.

### Interview Tip

Avoid saying "DNS is just a database."

A stronger answer is:

> DNS is a hierarchical, distributed naming system that translates
> domain names into IP addresses so clients can locate resources on the
> Internet.

------------------------------------------------------------------------

# Q2. Why do we need DNS?

## Answer

DNS exists because computers and humans identify systems differently.

Computers route packets using IP addresses. Humans remember meaningful
names.

DNS provides an abstraction layer between those two worlds.

It also decouples users from infrastructure changes. Suppose your
application moves from one EC2 instance to another or your load balancer
changes. Instead of asking millions of users to use a new IP address,
you simply update the DNS record while users continue accessing the same
domain.

DNS therefore improves usability, maintainability, scalability, and
availability.

### Real-world analogy

Think of your mobile phone contacts.

You search for "Mom" instead of memorizing a 10-digit number.

DNS performs the same function for websites.

------------------------------------------------------------------------

# Q3. What is Amazon Route 53?

## Answer

Amazon Route 53 is AWS's fully managed Domain Name System (DNS) service.

It provides four major capabilities:

-   Domain registration
-   DNS hosting
-   Health checks
-   Intelligent DNS-based traffic routing

Route 53 is globally distributed and highly available. It integrates
tightly with AWS services such as Application Load Balancers, CloudFront
distributions, API Gateway, S3 static websites, Global Accelerator, and
more.

Unlike a traditional DNS provider, Route 53 can make routing decisions
based on latency, endpoint health, user location, or weighted
percentages.

### Why is it called Route 53?

DNS uses port 53 over UDP and TCP. AWS named the service after the
standard DNS port.

------------------------------------------------------------------------

# Q4. Explain the complete DNS resolution process.

## Answer

The DNS resolution process is the sequence of steps required to convert
a domain name into the network destination needed to establish a
connection.

Typical flow:

1.  Browser cache
2.  Operating system cache
3.  Recursive DNS resolver (often provided by the ISP or a public
    resolver)
4.  Root DNS server
5.  Top-Level Domain (TLD) server
6.  Authoritative name server (for example, Route 53)
7.  DNS response returned to the resolver
8.  Resolver caches the answer according to the TTL
9.  Browser connects to the destination

Only after DNS resolution completes can the browser begin the TCP/TLS
connection and send the HTTP request.

------------------------------------------------------------------------

# Q5. What is the difference between Route 53 and an Application Load Balancer?

## Answer

This is one of the most common interview questions.

Route 53 is a DNS service.

Its responsibility is to answer the question:

> "Where should the client connect?"

An Application Load Balancer (ALB) receives client traffic after the
client has already connected. Its responsibility is to distribute
HTTP/HTTPS requests among backend targets.

Example:

User ↓ Route 53 (returns ALB DNS name) ↓ Application Load Balancer ↓ EC2
instances

Therefore:

-   Route 53 performs DNS-based routing.
-   ALB performs request-level load balancing.

A common mistake is saying that Route 53 balances HTTP requests. It does
not.

------------------------------------------------------------------------

# Common Follow-up Questions

-   Why does DNS mainly use UDP?
-   What is TTL?
-   What is an authoritative name server?
-   What is a hosted zone?
-   Difference between Alias and CNAME?
-   Can Route 53 work without a VPC?
-   Why is Route 53 considered a global service?


# Route53 Interview Handbook - Part 2

# Q6. Why can't we directly use IP addresses?

## Answer

Technically, we can access any Internet host by its IP address if the
service is configured to respond directly to that IP. However, using IP
addresses for applications at scale is impractical.

First, IP addresses are difficult for humans to remember. Remembering
`54.239.28.85` is much harder than remembering `amazon.com`.

Second, infrastructure changes over time. An application may move from
one EC2 instance to another, migrate to a different Region, or sit
behind an Application Load Balancer. If users relied on IP addresses,
every infrastructure change could require them to learn a new address.

DNS solves this by providing a stable, human-readable name while
allowing administrators to change the underlying destination
transparently.

### Interview Tip

Emphasize that DNS decouples application identity (the domain name) from
infrastructure (the IP or endpoint).

------------------------------------------------------------------------

# Q7. Is Route 53 a Regional or Global Service?

## Answer

Route 53 is a **global AWS service**.

Unlike EC2 or Amazon RDS, which are launched in specific AWS Regions,
Route 53 operates through a globally distributed network of
authoritative DNS servers.

This design ensures:

-   High availability
-   Low latency DNS responses
-   Fault tolerance
-   Global reach

Although the DNS service is global, it can route traffic to resources
deployed in any AWS Region or even outside AWS.

### Follow-up

**Does Route 53 exist inside a VPC?**

No. Route 53's authoritative DNS service is outside the VPC. Private
Hosted Zones and the Route 53 Resolver integrate with VPCs, but the
service itself is global.

------------------------------------------------------------------------

# Q8. What is a Hosted Zone?

## Answer

A Hosted Zone is a logical container for DNS records belonging to a
domain.

Suppose you own `example.com`.

Every DNS record---such as the website, API, email server, or
verification records---is stored in the Hosted Zone for that domain.

Think of the Hosted Zone as a folder.

    example.com
        │
        ▼
    Hosted Zone
     ├── A Record
     ├── AAAA Record
     ├── MX Record
     ├── TXT Record
     ├── NS Record
     └── SOA Record

Route 53 consults these records whenever it receives a DNS query for the
domain.

------------------------------------------------------------------------

# Q9. What is the difference between a Public and Private Hosted Zone?

## Answer

A **Public Hosted Zone** answers DNS queries from the public Internet.

Example:

    www.example.com
    api.example.com

Anyone can resolve these names.

A **Private Hosted Zone** is associated with one or more Amazon VPCs.
Only resources inside associated VPCs can resolve those names.

Example:

    db.internal
    redis.internal
    payments.private

These names are intentionally hidden from the public Internet and are
commonly used for internal microservices and databases.

### Interview Tip

Private Hosted Zones improve security because internal service names are
not publicly resolvable.

------------------------------------------------------------------------

# Q10. Explain A Record, CNAME and Alias Record.

## Answer

### A Record

An A (Address) record maps a hostname directly to an IPv4 address.

    example.com
          │
          ▼
    192.0.2.10

### CNAME Record

A CNAME (Canonical Name) maps one hostname to another hostname.

    blog.example.com
            │
            ▼
    hosting.example.net

The resolver must then look up the second hostname to obtain its IP
address.

A CNAME cannot be used for the root domain.

### Alias Record

Alias is an AWS-specific feature offered by Route 53.

Instead of pointing to an IP address, it points directly to supported
AWS resources such as:

-   Application Load Balancer
-   CloudFront
-   API Gateway
-   S3 Static Website
-   Global Accelerator

Unlike CNAME, Alias records can be used at the root domain (zone apex).

### Comparison

  Feature               A     CNAME   Alias
  --------------------- ----- ------- --------------------
  Points to IP          Yes   No      AWS resource
  Root domain support   Yes   No      Yes
  Standard DNS          Yes   Yes     No (AWS extension)

------------------------------------------------------------------------

# Common Follow-up Questions

-   Why can't CNAME be used at the root domain?
-   When should you choose Alias over CNAME?
-   Can an Alias point to an EC2 instance directly?
-   How does a Private Hosted Zone interact with a VPC?

# Route53 Interview Handbook - Part 3

# Q11. Explain the complete DNS resolution process in detail.

## Answer

DNS resolution is the process of converting a human-readable domain name
into the network destination required to communicate with a server.

Suppose a user enters:

    www.example.com

The following sequence occurs.

### Step 1: Browser Cache

Modern browsers maintain a DNS cache. If the browser recently resolved
the domain and the cached entry has not expired (based on TTL), it
immediately uses the cached answer.

### Step 2: Operating System Cache

If the browser has no cached entry, the operating system checks its own
DNS cache.

### Step 3: Recursive Resolver

If the answer is still not available, the operating system sends the
query to a recursive DNS resolver, typically provided by the ISP or a
public provider such as Google Public DNS or Cloudflare DNS.

The recursive resolver is responsible for finding the final answer on
behalf of the client.

### Step 4: Root Name Server

If the resolver has no cached answer, it contacts one of the Internet's
root name servers.

The root server does not know the IP address of the website. Instead, it
knows which Top-Level Domain (TLD) servers are responsible for domains
such as:

-   .com
-   .org
-   .net
-   .in

The root server replies with the address of the correct TLD server.

### Step 5: Top-Level Domain Server

The resolver contacts the appropriate TLD server.

For `www.example.com`, it contacts the `.com` TLD servers.

The TLD server replies with the authoritative name server responsible
for `example.com`.

### Step 6: Authoritative Name Server

The resolver now contacts the authoritative name server.

If the domain is hosted in Amazon Route 53, Route 53 is the
authoritative DNS server.

It returns the requested DNS record, such as an A Record or Alias
Record.

### Step 7: Cache the Answer

The recursive resolver caches the response according to the record's
TTL.

The operating system and browser may also cache the answer.

### Step 8: Connect to the Application

Once the IP address or endpoint has been obtained, the browser
establishes the TCP/TLS connection and sends the HTTP request.

------------------------------------------------------------------------

# Q12. What is a Recursive Resolver?

## Answer

A recursive resolver is a DNS server that performs all lookups on behalf
of a client.

Instead of asking the user to contact root servers, TLD servers, and
authoritative servers individually, the recursive resolver performs
every step and returns the final answer.

Its responsibilities include:

-   Performing DNS lookups
-   Caching results
-   Reducing latency
-   Reducing DNS traffic

Examples include ISP resolvers, Google Public DNS (8.8.8.8), and
Cloudflare (1.1.1.1).

------------------------------------------------------------------------

# Q13. What is an Authoritative Name Server?

## Answer

An authoritative name server stores the official DNS records for a
domain.

Unlike a recursive resolver, it does not search for answers elsewhere.

It simply returns the records configured for its hosted zone.

If Route 53 hosts `example.com`, then Route 53 is the authoritative name
server for that domain.

------------------------------------------------------------------------

# Q14. What is TTL?

## Answer

TTL (Time To Live) specifies how long DNS resolvers are allowed to cache
a DNS record before requesting a fresh copy.

For example:

    TTL = 300 seconds

A resolver caches the answer for five minutes.

A shorter TTL allows DNS changes to propagate faster but increases DNS
query volume.

A longer TTL reduces DNS traffic but delays infrastructure changes
becoming visible.

During migrations or blue-green deployments, administrators often reduce
the TTL beforehand to speed up propagation.

------------------------------------------------------------------------

# Q15. Why does DNS mainly use UDP instead of TCP?

## Answer

DNS primarily uses UDP because DNS requests and responses are usually
very small.

UDP offers:

-   Lower latency
-   No connection establishment
-   Lower overhead
-   Better performance for high query volumes

However, DNS switches to TCP in situations such as:

-   Zone transfers between authoritative servers
-   Responses too large for UDP
-   Some DNSSEC scenarios

Therefore, DNS supports both UDP and TCP, although UDP is the default.

------------------------------------------------------------------------

# Interview Tips

-   Clearly distinguish recursive resolvers from authoritative name
    servers.
-   Remember that Route 53 is usually the authoritative server, not the
    recursive resolver.
-   Mention browser cache, OS cache, and recursive resolver before
    discussing root and TLD servers.

# Route53 Interview Handbook - Part 4

# Q16. What are Route 53 Routing Policies?

## Answer

A routing policy defines **how Route 53 answers a DNS query**. When a
client asks for the IP address or endpoint of a domain, Route 53
evaluates the configured routing policy and decides which record should
be returned.

A common misconception is that Route 53 forwards or load balances HTTP
requests. It does not. Route 53 only answers DNS queries. After the
client receives the answer, it connects directly to the returned
resource.

Routing policies help solve problems such as: - Sending users to the
closest AWS Region. - Performing disaster recovery. - Gradually rolling
out a new application version. - Serving different content to users in
different countries.

------------------------------------------------------------------------

# Q17. Explain Simple Routing.

## Answer

Simple Routing is the default policy. One DNS name maps to one resource.

Example:

    app.example.com
          │
          ▼
    Application Load Balancer

This is suitable for small applications where there is only one
destination.

Use cases: - Single EC2 instance - Single Application Load Balancer -
Development environments

Limitation: Simple routing cannot intelligently distribute traffic
between multiple resources.

------------------------------------------------------------------------

# Q18. Explain Weighted Routing.

## Answer

Weighted Routing distributes DNS responses according to assigned
weights.

Suppose two application versions exist.

    Version A → Weight 90
    Version B → Weight 10

Approximately 90% of DNS responses return Version A and 10% return
Version B.

Important: This is **DNS-level distribution**, not request-level
balancing. Because DNS responses are cached, the split is approximate
rather than exact.

Typical use cases: - Canary deployments - A/B testing - Gradual
migration - Feature rollout

Interview Tip: Mention that Weighted Routing works best when clients
frequently refresh DNS.

------------------------------------------------------------------------

# Q19. Explain Latency-Based Routing.

## Answer

Latency-Based Routing sends users to the AWS Region that provides the
lowest network latency.

Example:

    User in India
          │
          ▼
    Mumbai Region

    User in Germany
          │
          ▼
    Frankfurt Region

Route 53 measures network latency between users and AWS Regions and
returns the endpoint expected to provide the fastest response.

It is ideal for global applications where user experience is important.

Remember: The nearest Region is not always the fastest. Route 53 chooses
based on measured latency, not geographical distance.

------------------------------------------------------------------------

# Q20. Explain Failover Routing.

## Answer

Failover Routing supports high availability.

It uses Route 53 Health Checks to monitor a primary endpoint.

    Primary Endpoint
          │
     Healthy?
     ┌────┴────┐
     │         │
    Yes        No
     │         │
     ▼         ▼
    Primary  Secondary

If the primary endpoint becomes unhealthy, Route 53 automatically
answers DNS queries with the secondary endpoint.

Common use cases: - Disaster recovery - Active-passive architectures -
Business continuity

------------------------------------------------------------------------

# Q21. Explain Geolocation Routing.

## Answer

Geolocation Routing chooses endpoints based on the user's geographic
location.

Example:

    India → Mumbai
    Japan → Tokyo
    USA → Virginia

It is useful when content differs by language, legal regulations, or
regional requirements.

Unlike Latency Routing, Geolocation ignores network performance and
relies on the user's location.

------------------------------------------------------------------------

# Q22. Explain Geoproximity and Multi-Value Answer Routing.

## Geoproximity Routing

Geoproximity Routing directs traffic according to the geographic
location of AWS resources and users. Administrators can also apply a
**bias** to increase or decrease the area served by a Region.

Example: A company wants most European users to reach Frankfurt, but
some traffic should shift toward London. A bias can adjust this
distribution.

## Multi-Value Answer Routing

Instead of returning one IP address, Route 53 returns multiple healthy
IP addresses.

    example.com

    ↓

    10.0.0.1
    10.0.0.2
    10.0.0.3

If one endpoint becomes unhealthy, Route 53 removes it from responses.

This provides basic redundancy but is **not a replacement for an
Application Load Balancer**.

------------------------------------------------------------------------

# Q23. Which routing policy should you choose?

  Scenario                                 Recommended Policy
  ---------------------------------------- --------------------
  One application                          Simple
  Canary deployment                        Weighted
  Disaster Recovery                        Failover
  Lowest response time                     Latency
  Country-specific content                 Geolocation
  Geographic traffic shaping               Geoproximity
  Multiple healthy endpoints without ALB   Multi-Value

------------------------------------------------------------------------

# Interview Tips

-   Route 53 answers DNS queries; it does not proxy application traffic.
-   Health Checks are commonly used with Failover and Multi-Value
    routing.
-   Weighted routing is approximate because DNS responses are cached.
-   Latency Routing is based on measured network latency, not physical
    distance.


# Route53 Interview Handbook -- Part 5

# Q24. What are Route 53 Health Checks?

## Answer

Health Checks allow Route 53 to determine whether an endpoint is healthy
before returning it in a DNS response.

A health check periodically sends requests to an endpoint from multiple
AWS health checker locations around the world. Based on the configured
success criteria, Route 53 marks the endpoint as **Healthy** or
**Unhealthy**.

Health checks are commonly combined with Failover Routing and
Multi-Value Answer Routing.

### Why are Health Checks important?

Without health checks, Route 53 would continue returning an endpoint
even if the application had crashed.

With health checks enabled:

-   Healthy endpoints remain in DNS responses.
-   Unhealthy endpoints can be removed or replaced.

### Example

User ↓ Route 53 ↓ Health Check ↓ Healthy? → Yes → Return Primary

Healthy? → No → Return Secondary

------------------------------------------------------------------------

# Q25. How do Health Checks work internally?

## Answer

1.  You create a health check in Route 53.
2.  Configure the endpoint (IP address or domain).
3.  Select HTTP, HTTPS, or TCP.
4.  Route 53 health checkers send requests from multiple AWS edge
    locations.
5.  Responses are evaluated.
6.  If enough checkers report success, the endpoint is healthy.
7.  If the failure threshold is reached, Route 53 marks it unhealthy.

Using multiple health checker locations helps avoid false positives
caused by temporary network issues.

------------------------------------------------------------------------

# Q26. Difference between HTTP, HTTPS and TCP Health Checks

## HTTP

Checks whether the endpoint responds over HTTP with an expected status
code.

## HTTPS

Performs the same verification over an encrypted TLS connection.

## TCP

Only verifies that a TCP connection can be established. It does not
validate application content.

### When to use which?

-   HTTP → Public web applications.
-   HTTPS → Secure production websites.
-   TCP → Databases, custom services, or applications without HTTP.

------------------------------------------------------------------------

# Q27. Can Route 53 use CloudWatch alarms?

## Answer

Yes.

Instead of probing an endpoint directly, Route 53 can evaluate the state
of a CloudWatch alarm.

Example:

A Lambda function publishes a custom metric.

If the metric crosses a threshold, CloudWatch enters the ALARM state.

Route 53 treats that endpoint as unhealthy and updates DNS answers
accordingly.

This is useful when application health depends on business metrics
rather than simple HTTP responses.

------------------------------------------------------------------------

# Q28. What is Route 53 Resolver?

## Answer

Route 53 Resolver is the DNS resolution service available inside every
Amazon VPC.

When an EC2 instance performs a DNS lookup, it automatically queries the
VPC Resolver.

The resolver can resolve:

-   Public DNS names
-   Private Hosted Zone records
-   AWS service endpoints
-   Forwarded on-premises domains

Remember that the Resolver answers queries; it is different from Route
53 authoritative name servers that host DNS records.

------------------------------------------------------------------------

# Q29. What are Inbound Resolver Endpoints?

## Answer

Inbound Resolver Endpoints allow DNS queries originating outside AWS
(such as an on-premises data center) to resolve records stored in AWS
Private Hosted Zones.

Architecture:

On-Prem DNS ↓ Inbound Endpoint ↓ Private Hosted Zone ↓ Private IP
returned

Use this when on-premises applications need to resolve private AWS
domain names.

------------------------------------------------------------------------

# Q30. What are Outbound Resolver Endpoints?

## Answer

Outbound Resolver Endpoints allow AWS resources to resolve domains
hosted outside AWS.

Architecture:

EC2 ↓ Route 53 Resolver ↓ Outbound Endpoint ↓ Corporate DNS Server

Typical use case:

An EC2 application needs to resolve `hr.corp.local`, which exists only
in the company's on-premises DNS infrastructure.

------------------------------------------------------------------------

# Interview Tips

-   Health checks influence DNS answers---they do not restart servers.
-   TCP checks verify connectivity only.
-   Route 53 Resolver operates inside VPCs.
-   Inbound endpoints bring DNS queries **into AWS**.
-   Outbound endpoints send DNS queries **out of AWS**.
