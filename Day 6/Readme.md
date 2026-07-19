# Amazon Route 53 -- Detailed Study Notes (Part 1)

## Introduction

Amazon Route 53 is AWS's managed Domain Name System (DNS) service. It
allows users to access applications using easy-to-remember domain names
instead of numerical IP addresses.

Imagine you build a shopping website on AWS. Your EC2 instance receives
an IP address such as `13.232.10.20`. While computers can communicate
using this IP address, humans would find it difficult to remember.
Instead, users type `myshop.com`. Route 53 translates that domain name
into the correct destination.

Route 53 is much more than a service that maps names to IP addresses. It
also provides domain registration, DNS record management, health checks,
and intelligent traffic routing.

------------------------------------------------------------------------

# What is DNS?

DNS stands for **Domain Name System**. It is often described as the
phonebook of the Internet.

When you save a contact in your phone, you remember the person's name
instead of their phone number. Your phone looks up the stored number
whenever you call them.

DNS works in the same way.

    myshop.com
            │
            ▼
    13.232.10.20

Instead of remembering numbers, users remember names.

DNS performs the translation from a domain name into an IP address. This
process is called **DNS Resolution**.

------------------------------------------------------------------------

# Why DNS is Needed

Without DNS, every website would need to be accessed by its IP address.

Problems include:

-   IP addresses are difficult for humans to remember.
-   IP addresses may change when infrastructure changes.
-   A single application can consist of multiple servers behind a load
    balancer.

With DNS, users continue using the same domain name even if the
underlying infrastructure changes.

------------------------------------------------------------------------

# What is Amazon Route 53?

Amazon Route 53 is AWS's globally distributed DNS service.

It provides four major capabilities:

1.  Domain Registration
2.  DNS Record Management
3.  Health Checks
4.  Traffic Routing

It is called **Route 53** because DNS uses port **53** for both UDP and
TCP communication.

------------------------------------------------------------------------

# How Route 53 Fits into AWS

    User
      │
      ▼
    Route 53 (DNS)
      │
      ▼
    Application Load Balancer
      │
      ▼
    EC2 Application Servers
      │
      ▼
    Database

Notice that Route 53 sits **outside** your VPC. It answers DNS queries
before traffic reaches your AWS infrastructure.

------------------------------------------------------------------------

# Example Request Flow

Suppose a user enters `myshop.com` in a browser.

1.  The browser checks its local DNS cache.
2.  If no cached record exists, it asks a DNS resolver.
3.  The resolver eventually reaches the authoritative DNS server for the
    domain.
4.  If the domain is hosted in Route 53, Route 53 returns the correct
    destination.
5.  The browser connects to the returned endpoint (for example, an
    Application Load Balancer).
6.  The application processes the request and returns the response.

------------------------------------------------------------------------

# Key Takeaways

-   Route 53 is a managed DNS service.
-   DNS converts names into network destinations.
-   Users access domains, not IP addresses.
-   Route 53 integrates with AWS services such as ALB, CloudFront, S3
    Static Websites, API Gateway, ECS, EKS and EC2.
-   Route 53 also supports health checks and advanced routing policies.

> This document is Part 1 of a complete Route 53 study guide. Later
> parts can cover Hosted Zones, DNS Records, Routing Policies, Health
> Checks, Route 53 Resolver, Hybrid DNS, integrations, troubleshooting,
> and interview questions in depth.

# Amazon Route 53 -- Detailed Study Notes (Part 2)

# Hosted Zones

A Hosted Zone is a logical container in Route 53 that stores all DNS
records for a domain. Think of it as a database dedicated to one domain.

For example, if you own `myshop.com`, every DNS record related to that
domain---such as the IP address for the website, the mail server, or API
subdomains---is stored inside its hosted zone.

## Public Hosted Zone

A Public Hosted Zone answers DNS queries from anywhere on the Internet.
If users anywhere in the world type `myshop.com`, Route 53 checks the
public hosted zone and returns the configured answer.

Typical use cases include: - Company websites - APIs - Public
applications - Blogs

## Private Hosted Zone

A Private Hosted Zone is associated with one or more VPCs. Only
resources inside those VPCs can resolve the records.

Example:

    api.internal
    db.internal
    redis.internal

These names are invisible to the public Internet but convenient for
communication between internal AWS resources.

# DNS Records

DNS records define where traffic should go.

## A Record

Maps a domain directly to an IPv4 address.

Example:

    myshop.com
        │
        ▼
    13.232.10.20

Whenever someone asks for `myshop.com`, Route 53 returns the IPv4
address.

## AAAA Record

Similar to an A record, but returns an IPv6 address instead of IPv4.

## CNAME Record

A Canonical Name (CNAME) record maps one hostname to another hostname
instead of to an IP address.

Example:

    blog.myshop.com
            │
            ▼
    hosting-provider.example.com

The DNS resolver performs another lookup to find the IP address of the
target hostname.

Limitations: - Cannot be used for the root domain (`myshop.com`) -
Standard DNS feature supported across providers

## Alias Record

Alias records are an AWS extension to DNS.

Instead of pointing to an IP address, an Alias record can point directly
to supported AWS resources such as:

-   Application Load Balancer
-   Network Load Balancer
-   CloudFront
-   API Gateway
-   S3 Static Website
-   Global Accelerator

Unlike CNAME, Alias records can also be used for the root domain.

# TTL (Time To Live)

TTL defines how long DNS resolvers cache a DNS record.

Suppose an A record has:

    TTL = 300 seconds

After the resolver receives the answer, it stores it for five minutes.
During that period it does not contact Route 53 again for the same
record.

A lower TTL allows infrastructure changes to propagate more quickly but
increases DNS queries. A higher TTL reduces DNS traffic but delays
updates.

# Name Servers (NS)

Every hosted zone receives four Route 53 authoritative name servers.

When you register a domain with another registrar such as GoDaddy or
Namecheap, you update the domain's nameservers to these Route 53 values.
This delegates DNS authority to Route 53.

# SOA Record

The Start of Authority (SOA) record contains administrative information
about the hosted zone, including the primary name server, contact
information, serial number, and timing values used by DNS.

# Summary

Hosted Zones are where Route 53 stores DNS records. Public Hosted Zones
serve Internet users, while Private Hosted Zones serve VPCs. Records
such as A, AAAA, CNAME, and Alias determine how domain names are
resolved, while TTL controls caching behavior.
