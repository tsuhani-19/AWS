# AWS IAM Interview Questions & Answers


# Beginner Level

## 1. What is AWS IAM?

**Answer:**

AWS IAM (Identity and Access Management) is a global AWS service used to securely manage access to AWS resources.

It helps answer two questions:

- **Who are you?** (Authentication)
- **What are you allowed to do?** (Authorization)

IAM allows you to create users, groups, roles, and policies to control access to AWS services.

---

## 2. Why is IAM important?

**Answer:**

Without IAM, every person would need to use the AWS Root Account, giving them unrestricted access.

IAM allows organizations to:

- Secure AWS resources
- Grant only required permissions
- Separate responsibilities
- Track user activity
- Improve overall security

---

## 3. What is the difference between Authentication and Authorization?

| Authentication | Authorization |
|---------------|---------------|
| Verifies identity | Determines permissions |
| "Who are you?" | "What can you do?" |
| Username, Password, Access Keys | IAM Policies |

**Example**

You log into AWS Console using your username and password (**Authentication**).

IAM then checks whether you're allowed to launch an EC2 instance (**Authorization**).

---

## 4. What is an IAM User?

**Answer:**

An IAM User is a permanent identity created for a person or application.

An IAM user may have:

- Console Password
- Access Key
- Secret Access Key
- MFA

**Real-world Example**

Every developer in your company gets their own IAM User instead of sharing one account.

---

## 5. What is an IAM Group?

**Answer:**

A Group is a collection of IAM Users.

Permissions are attached to the group instead of individual users.

**Example**

```
Developers Group
├── Alice
├── Bob
└── Charlie

Policy:
EC2 Full Access
S3 Read Only
```

When a new developer joins, simply add them to the Developers group.

---

## 6. What is an IAM Role?

**Answer:**

An IAM Role is a temporary identity that provides temporary AWS credentials.

Unlike IAM Users, Roles do not have:

- Password
- Access Keys

Roles are assumed by:

- EC2
- Lambda
- ECS
- EKS
- Other AWS Accounts
- Federated Users

---

## 7. Why should Roles be preferred over Access Keys?

**Answer:**

Access Keys are long-term credentials.

If leaked, attackers can use them until they are rotated.

IAM Roles provide:

- Temporary credentials
- Automatic credential rotation
- Better security
- No hardcoded secrets

**Interview Tip**

Whenever an AWS service needs another AWS service, use an IAM Role instead of storing Access Keys.

---

## 8. What is an IAM Policy?

**Answer:**

A Policy is a JSON document that defines permissions.

It specifies:

- Effect (Allow/Deny)
- Action
- Resource
- Optional Conditions

Example:

```json
{
  "Effect": "Allow",
  "Action": "s3:GetObject",
  "Resource": "arn:aws:s3:::my-bucket/*"
}
```

---

## 9. What are AWS Managed Policies?

**Answer:**

Policies created and maintained by AWS.

Examples:

- AdministratorAccess
- AmazonS3ReadOnlyAccess
- AmazonEC2FullAccess

They are easy to use and automatically updated by AWS.

---

## 10. What are Customer Managed Policies?

**Answer:**

Policies created by your organization.

Advantages:

- Fully customizable
- Reusable
- Better control over permissions

---

# Intermediate Level

## 11. What is the Principle of Least Privilege?

**Answer:**

Users should receive only the permissions required to perform their tasks.

Instead of:


AdministratorAccess


Grant:


Read access to one S3 bucket


This minimizes the impact of compromised credentials.

---

## 12. What is MFA?

**Answer:**

Multi-Factor Authentication requires an additional verification method besides a password.

Examples:

- Google Authenticator
- Authy
- Hardware Security Keys

AWS strongly recommends enabling MFA for:

- Root User
- Administrator Users

---

## 13. What is the difference between the Root User and an IAM User?

| Root User | IAM User |
|-----------|----------|
| Created during AWS account creation | Created manually |
| Full unrestricted access | Permissions controlled by IAM |
| Cannot be restricted | Fully manageable |
| One per account | Multiple allowed |

Best Practice:

Never use the Root User for daily work.

---

## 14. Can a User belong to multiple Groups?

**Answer:**

Yes.

A single IAM User can belong to multiple groups.

Example:

```
Alice

Developers
QA
DevOps
```

Alice inherits permissions from all groups.

---

## 15. What happens if multiple policies are attached?

**Answer:**

AWS combines all permissions.

However,

**Explicit Deny always overrides Allow.**

---

## 16. Explain IAM Policy Evaluation Logic.

**Answer:**

AWS evaluates requests in the following order:

```
Default Deny
↓

Explicit Allow
↓

Explicit Deny (Highest Priority)
```

Final result:

```
Explicit Deny > Allow > Default Deny
```

---

## 17. What are Inline Policies?

**Answer:**

Inline Policies are directly attached to one specific user, group, or role.

Characteristics:

- Cannot be reused
- Deleted with the identity
- Best for unique permissions

---

## 18. What is Cross-Account Access?

**Answer:**

IAM Roles allow one AWS Account to securely access resources in another AWS Account.

No permanent credentials are shared.

---

## 19. Why is IAM considered a Global Service?

**Answer:**

IAM is not tied to a specific AWS Region.

Users, roles, and policies are available across all AWS Regions within the same AWS account.

---

## 20. How does IAM integrate with CloudTrail?

**Answer:**

CloudTrail records IAM-related events such as:

- User logins
- Policy changes
- Role assumptions
- Failed authentication attempts
- API calls

This helps with auditing and security investigations.

---

# Scenario-Based Questions

## 21. Your EC2 instance needs to upload files to S3. How would you configure it?

### Wrong Approach

Store AWS Access Keys inside the EC2 instance.

Problems:

- Keys can leak.
- Manual rotation is required.
- Security risk.

### Correct Solution

- Create an IAM Role.
- Grant the role S3 permissions.
- Attach the role to the EC2 instance.

AWS automatically provides temporary credentials.

**Why this is better**

- No hardcoded credentials
- Automatic credential rotation
- More secure

---

## 22. A developer accidentally deleted an S3 bucket. How would you prevent this?

### Solution

- Follow the Principle of Least Privilege.
- Do not grant `s3:*`.
- Explicitly deny `DeleteBucket`.
- Enable bucket versioning.
- Enable MFA for privileged users.

---

## 23. A company has 500 developers. How would you manage permissions?

### Solution

Instead of assigning permissions individually:

```
Developers Group
↓

Attach Developer Policy

↓

Add users to the group
```

Benefits:

- Easier management
- Consistent permissions
- Less administrative effort

---

## 24. An application running on Lambda needs access to DynamoDB. How should it authenticate?

### Solution

Attach an IAM Role to the Lambda function with only the required DynamoDB permissions.

Avoid storing AWS Access Keys in environment variables or code.

---

## 25. A third-party vendor needs temporary access to your AWS account. What would you do?

### Solution

- Create an IAM Role with limited permissions.
- Configure a trust policy for the vendor's AWS account.
- Allow the vendor to assume the role using AWS STS.

This avoids sharing long-term credentials.

---

# Advanced Questions

## 26. What is AWS STS?

**Answer:**

AWS Security Token Service (STS) generates temporary security credentials.

It is commonly used with:

- IAM Roles
- Cross-account access
- Federated users
- Temporary sessions

---

## 27. What is an ARN?

**Answer:**

ARN stands for Amazon Resource Name.

It uniquely identifies AWS resources.

Example:

```
arn:aws:s3:::my-bucket
```

---

## 28. What is a Trust Policy?

**Answer:**

A Trust Policy defines **who is allowed to assume an IAM Role**.

Permission policies define **what the role can do**, while trust policies define **who can use the role**.

---

## 29. What are Policy Conditions?

**Answer:**

Conditions allow permissions only when specific criteria are met.

Examples:

- Source IP address
- MFA enabled
- Request time
- AWS Region
- Resource tags

This enables fine-grained access control.

---

## 30. What is the difference between Identity-Based Policies and Resource-Based Policies?

| Identity-Based Policy | Resource-Based Policy |
|------------------------|-----------------------|
| Attached to Users, Groups, or Roles | Attached directly to a resource |
| Controls what an identity can access | Controls who can access a resource |
| Common in IAM | Common in S3 Buckets, SNS, SQS, KMS |

---

# Frequently Asked Interview Traps

## Q. Can an IAM User exist without any policies?

**Answer:**

Yes.

The user can authenticate successfully but cannot perform any AWS actions because all requests are denied by default.

---

## Q. Does Allow override Deny?

**Answer:**

No.

Explicit **Deny** always overrides **Allow**.

---

## Q. Can an IAM Role have Access Keys?

**Answer:**

No.

Roles use temporary credentials generated by AWS STS and do not have permanent access keys.

---

## Q. Can a Role assume another Role?

**Answer:**

Yes.

This is known as **Role Chaining**, although AWS imposes limits on chained role session durations.

---

## Q. Can multiple users assume the same Role?

**Answer:**

Yes.

Any principal trusted by the role's trust policy can assume the same role.

---

# Best Practices

- Never use the Root User for daily tasks.
- Enable MFA for the Root User and privileged IAM Users.
- Follow the Principle of Least Privilege.
- Prefer IAM Roles over Access Keys.
- Rotate long-term credentials regularly.
- Remove unused users and permissions.
- Avoid wildcard (`*`) permissions whenever possible.
- Use Groups for permission management.
- Monitor IAM activity using AWS CloudTrail.
- Review IAM policies periodically and remove unnecessary access.

---

# Quick Revision

| Topic | Key Point |
|--------|-----------|
| IAM | Authentication & Authorization |
| User | Permanent identity |
| Group | Collection of users |
| Role | Temporary identity |
| Policy | JSON permission document |
| STS | Temporary credentials |
| ARN | Unique resource identifier |
| MFA | Extra authentication layer |
| Least Privilege | Minimum required permissions |
| CloudTrail | Auditing and logging |
| Explicit Deny | Overrides Allow |
| Root User | Avoid for daily use |