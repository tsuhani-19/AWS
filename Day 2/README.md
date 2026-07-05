# AWS IAM (Identity and Access Management)

AWS Identity and Access Management (IAM) is a global AWS service that allows you to securely manage **who can access your AWS account**, **what resources they can access**, and **what actions they can perform**.

Think of IAM as the authentication and authorization system of AWS.

- **Authentication** → Who are you?
- **Authorization** → What are you allowed to do?

Without IAM, anyone with AWS account credentials would have unrestricted access to every resource. IAM helps enforce security by granting only the permissions that are actually needed.

---

# Why IAM Matters

Imagine a company with different teams:

- Developers should deploy applications.
- Database administrators should manage databases.
- Finance should only view billing information.
- Interns should have read-only access.

Giving everyone Administrator access is dangerous.

IAM solves this by allowing you to define exactly who can do what.

---

# Core Features

- Secure access management for AWS resources
- Fine-grained permission control
- Centralized identity management
- Temporary credential support
- Multi-Factor Authentication (MFA)
- Identity Federation (SSO)
- Cross-account access
- Auditability with AWS CloudTrail
- Principle of Least Privilege

---

# How IAM Works

Whenever someone tries to perform an action:

```
User/Application
        │
        ▼
Authenticates using credentials
        │
        ▼
IAM evaluates attached policies
        │
        ▼
Allow or Deny Request
        │
        ▼
AWS Service
```

IAM first verifies **who** is making the request and then checks whether the request is allowed based on the attached policies.

---

# IAM Components

## 1. Users

An IAM User represents a person or application that requires long-term access to AWS resources.

Each IAM user has its own credentials such as:

- Username
- Password (for AWS Console)
- Access Key ID
- Secret Access Key

### Example

A backend developer who needs access to:

- Amazon S3
- Amazon EC2
- Amazon CloudWatch

Instead of sharing the AWS root account, create a dedicated IAM user with only the required permissions.

---

## 2. Groups

Groups are collections of IAM users.

Instead of assigning permissions individually to every user, permissions can be attached to a group.

Every member of the group automatically inherits those permissions.

### Example

```
Developers Group
├── Alice
├── Bob
└── Charlie

Policy:
- EC2 Full Access
- S3 Read Only
```

Adding a new developer only requires adding them to the Developers group.

---

## 3. Roles

IAM Roles provide **temporary credentials** instead of permanent credentials.

Unlike users, roles do not have:

- Passwords
- Access Keys

A role is assumed by:

- AWS Services
- EC2 instances
- Lambda functions
- ECS Tasks
- External AWS Accounts
- Federated Users

### Example

An EC2 instance needs to upload files to S3.

❌ Bad Practice

Store AWS Access Keys inside the EC2 instance.

✅ Best Practice

Attach an IAM Role to the EC2 instance.

AWS automatically provides temporary credentials.

---

## 4. Policies

Policies define permissions.

A policy is simply a JSON document that specifies:

- Who
- Can perform which actions
- On which resources
- Under what conditions

Policies can be attached to:

- Users
- Groups
- Roles

---

# IAM Policy Structure

A policy consists of one or more statements.

Example:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": "s3:GetObject",
      "Resource": "arn:aws:s3:::my-bucket/*"
    }
  ]
}
```

### Important Fields

| Field | Description |
|--------|-------------|
| Version | Policy language version |
| Statement | One or more permission rules |
| Effect | Allow or Deny |
| Action | AWS API actions |
| Resource | AWS resource ARN |
| Condition | Optional conditions for access |

---

# Policy Types

## AWS Managed Policies

Pre-built policies maintained by AWS.

Examples:

- AmazonS3ReadOnlyAccess
- AmazonEC2FullAccess
- AdministratorAccess

Advantages:

- Easy to use
- Automatically updated by AWS

---

## Customer Managed Policies

Policies created and maintained by you.

Advantages:

- Fully customizable
- Can be reused across multiple users and roles
- Easier to manage than inline policies

---

## Inline Policies

Policies embedded directly inside a single user, group, or role.

Characteristics:

- One-to-one relationship
- Cannot be reused
- Deleted when the IAM identity is deleted

Use only when permissions are unique to one identity.

---

# Authentication vs Authorization

Authentication answers:

> Who are you?

Examples:

- Username & Password
- Access Keys
- MFA

Authorization answers:

> What are you allowed to do?

IAM Policies determine authorization.

---

# IAM Credentials

IAM users can authenticate using:

- Console Password
- Access Key ID
- Secret Access Key
- MFA Device

Never share credentials between users.

---

# Multi-Factor Authentication (MFA)

MFA adds an extra security layer.

Instead of relying only on a password, users must also provide a second verification factor.

Examples:

- Google Authenticator
- Authy
- Hardware Security Keys

Benefits:

- Protects against password theft
- Prevents unauthorized console access

AWS strongly recommends enabling MFA for:

- Root Account
- Privileged IAM Users

---

# Root User vs IAM User

| Root User | IAM User |
|------------|----------|
| Full access to everything | Limited permissions |
| Created during AWS account creation | Created manually |
| Cannot be restricted | Fully controllable |
| One per AWS account | Multiple allowed |
| Use only for account-level tasks | Daily operations |

Best Practice:

Never use the Root User for everyday work.

---

# Principle of Least Privilege

One of IAM's most important security principles.

Grant only the permissions required to perform a task.

Example:

Instead of:

```
AdministratorAccess
```

Use:

```
Read only access to one S3 bucket
```

This minimizes the impact if credentials are compromised.

---

# Explicit Deny vs Allow

IAM always evaluates permissions in this order:

1. Explicit Deny
2. Explicit Allow
3. Default Deny

Example:

```
Policy A:
Allow S3 Access

Policy B:
Deny DeleteObject

Result:

✓ Upload Object

✓ Download Object

✗ Delete Object
```

Explicit Deny always wins.

---

# IAM Roles in AWS Services

Roles are heavily used across AWS.

Examples:

| Service | Role Usage |
|----------|------------|
| EC2 | Access S3 without Access Keys |
| Lambda | Access DynamoDB |
| ECS | Access Secrets Manager |
| EKS | Access AWS Services |
| CloudFormation | Create AWS Resources |
| CodePipeline | Deploy Applications |

---

# Cross-Account Access

IAM Roles allow one AWS account to securely access resources in another account.

Example:

```
AWS Account A
        │
Assume Role
        ▼
AWS Account B
        │
Access Resources
```

No permanent credentials need to be shared.

---

# IAM Best Practices

- Never use the root user for daily work.
- Enable MFA for the root account and privileged users.
- Follow the Principle of Least Privilege.
- Prefer IAM Roles over long-term Access Keys.
- Rotate access keys regularly.
- Remove unused users and permissions.
- Use groups to simplify permission management.
- Audit IAM activity using AWS CloudTrail.
- Avoid using wildcard (`*`) permissions whenever possible.
- Review permissions periodically.

---

# Real-World Example

Consider an e-commerce application.

### Developer

Permissions:

- Deploy EC2 instances
- Upload code to S3

Cannot:

- View billing
- Delete databases

---

### Finance Team

Permissions:

- View AWS Billing
- Download Cost Reports

Cannot:

- Launch EC2
- Delete resources

---

### Lambda Function

Needs to:

- Read from DynamoDB
- Write logs to CloudWatch

Instead of embedding AWS credentials inside the function, attach an IAM Role with only those permissions.

---

# Common Interview Questions

### What is IAM?

IAM is AWS's identity and access management service that controls authentication and authorization for AWS resources.

---

### What is the difference between a User and a Role?

| User | Role |
|------|------|
| Permanent identity | Temporary identity |
| Has credentials | No permanent credentials |
| Used by people/applications | Assumed by users or AWS services |

---

### What is the Principle of Least Privilege?

Grant only the minimum permissions required to complete a task.

---

### Why should IAM Roles be preferred over Access Keys?

Roles provide temporary credentials that are automatically rotated, reducing the risk of credential leakage and improving security.

---

### What happens if one policy allows an action and another denies it?

Explicit **Deny** always overrides **Allow**.

---

# Key Takeaways

- IAM controls authentication and authorization in AWS.
- Users represent permanent identities.
- Groups simplify permission management.
- Roles provide secure temporary access.
- Policies define permissions using JSON.
- Always follow the Principle of Least Privilege.
- Prefer IAM Roles over long-term Access Keys.
- Enable MFA for enhanced security.
- Avoid using the Root User for daily activities.
- Use CloudTrail to audit IAM activities.

---

## Summary

AWS IAM is the foundation of AWS security. It enables organizations to securely manage identities, enforce least-privilege access, and control permissions across all AWS services. Mastering IAM is essential before working with services like EC2, S3, Lambda, RDS, or EKS because every AWS resource ultimately depends on IAM for secure access control.