# 23. Security / DevSecOps 🔐

Security is one of the most important areas in Cloud Computing and DevOps.

When applications are deployed to the cloud, we need to protect:

* Users
* Applications
* Servers
* Databases
* Networks
* APIs
* Source code
* Credentials
* Passwords
* Encryption keys
* Containers
* Cloud resources

**DevSecOps** integrates security into the software development and operations lifecycle.

Instead of treating security as something that is checked only after an application is developed, DevSecOps tries to include security throughout the entire development and deployment process.

```text
              DEVSECOPS

Developer
    ↓
Source Code
    ↓
Security Checks
    ↓
Dependency Scanning
    ↓
Secret Scanning
    ↓
Build
    ↓
Container Security
    ↓
Testing
    ↓
Deployment
    ↓
AWS / Cloud
    ↓
Monitoring
```

---

# Table of Contents

1. [What is Security?](#1-what-is-security)
2. [What is DevSecOps?](#2-what-is-devsecops)
3. [IAM — Identity and Access Management](#3-iam--identity-and-access-management)
4. [Least Privilege](#4-least-privilege)
5. [Secrets Management](#5-secrets-management)
6. [SSH Keys](#6-ssh-keys)
7. [Security Groups](#7-security-groups)
8. [Network ACLs](#8-network-acls)
9. [Security Groups vs Network ACLs](#9-security-groups-vs-network-acls)
10. [Encryption](#10-encryption)
11. [Encryption at Rest](#11-encryption-at-rest)
12. [Encryption in Transit](#12-encryption-in-transit)
13. [AWS KMS](#13-aws-kms)
14. [HTTPS](#14-https)
15. [TLS / SSL](#15-tls--ssl)
16. [HTTPS vs HTTP](#16-https-vs-http)
17. [Vulnerability Scanning](#17-vulnerability-scanning)
18. [Dependency Scanning](#18-dependency-scanning)
19. [Container Security](#19-container-security)
20. [Secret Scanning](#20-secret-scanning)
21. [AWS Secrets Manager](#21-aws-secrets-manager)
22. [Secrets Manager Architecture](#22-secrets-manager-architecture)
23. [Security in CI/CD](#23-security-in-cicd)
24. [Security Best Practices](#24-security-best-practices)
25. [Real-World AWS Security Architecture](#25-real-world-aws-security-architecture)
26. [Security Checklist](#26-security-checklist)
27. [Important Interview Questions](#27-important-interview-questions)
28. [Key Concepts to Remember](#28-key-concepts-to-remember)

---

# 1. What is Security?

Security means protecting systems, applications, data, and infrastructure from unauthorized access, misuse, modification, destruction, or theft.

In Cloud Computing, security applies to multiple layers.

```text
                 SECURITY
                    |
       +------------+------------+
       |            |            |
     IAM         Network       Data
       |            |            |
   Permissions   SG/NACL     Encryption
       |
   Authentication
```

For example, imagine an application running on AWS:

```text
User
  ↓
Internet
  ↓
Load Balancer
  ↓
EC2
  ↓
RDS
```

Security must be applied at every layer.

### User Layer

We need to authenticate users.

### Network Layer

We need to control which traffic can reach servers.

### Server Layer

We need to secure the operating system and applications.

### Database Layer

We need to protect database credentials and data.

### Data Layer

We need to encrypt sensitive information.

---

# 2. What is DevSecOps?

## Definition

**DevSecOps = Development + Security + Operations**

DevSecOps means integrating security practices into the DevOps lifecycle.

Traditional approach:

```text
Development
     ↓
Testing
     ↓
Deployment
     ↓
Security Check
```

DevSecOps approach:

```text
Development
     ↓
Security
     ↓
Testing
     ↓
Build
     ↓
Security Scan
     ↓
Deployment
     ↓
Monitoring
```

Security becomes part of the entire development process.

---

## DevSecOps Lifecycle

```text
Plan
 ↓
Code
 ↓
Build
 ↓
Test
 ↓
Scan
 ↓
Release
 ↓
Deploy
 ↓
Operate
 ↓
Monitor
```

Security checks can be added throughout this lifecycle.

For example:

```text
Developer
    ↓
GitHub
    ↓
Secret Scanning
    ↓
Dependency Scanning
    ↓
Vulnerability Scanning
    ↓
Docker Build
    ↓
Container Image Scan
    ↓
CI/CD
    ↓
AWS
```

---

## Why DevSecOps is Important

Security problems discovered very late can be expensive and difficult to fix.

For example:

```text
Developer writes insecure code
          ↓
Application deployed
          ↓
Security problem discovered
          ↓
Application must be fixed
          ↓
Deployment may need to be repeated
```

With DevSecOps:

```text
Developer writes code
          ↓
Automated security scan
          ↓
Problem detected early
          ↓
Developer fixes problem
          ↓
Secure build
```

---

# 3. IAM — Identity and Access Management

## What is IAM?

**IAM stands for Identity and Access Management.**

AWS IAM controls:

* Who can access AWS
* What resources they can access
* What actions they can perform
* Which AWS services can access other AWS services

IAM is one of the most important AWS security services.

---

# IAM Components

The major IAM concepts are:

```text
IAM
 |
 +---- Users
 |
 +---- Groups
 |
 +---- Roles
 |
 +---- Policies
 |
 +---- MFA
 |
 +---- Access Keys
```

---

# 3.1 IAM User

An IAM User represents an identity, commonly a person or application identity requiring long-term credentials.

Example:

```text
AWS Account
    |
    +---- Developer
    +---- Tester
    +---- Administrator
```

Each user can have specific permissions.

### Important Practice

Avoid using the AWS root account for normal daily operations.

Instead:

```text
Root Account
     ↓
Secure / Limited Usage

IAM User / IAM Role
     ↓
Daily AWS Operations
```

---

# 3.2 IAM Group

An IAM Group is a collection of IAM users.

Example:

```text
Developers
    |
    +---- User A
    +---- User B
    +---- User C
```

A policy can be attached to the group.

All users in the group can then receive the permissions defined by that policy.

---

# 3.3 IAM Role

An IAM Role is an identity that can be assumed by a trusted entity.

Roles are very important in AWS.

For example, an EC2 instance may need to access S3.

Bad approach:

```text
EC2
 ↓
Access Key
 ↓
S3
```

Better approach:

```text
EC2
 ↓
IAM Role
 ↓
S3 Permissions
 ↓
S3
```

The application does not need to contain long-term AWS credentials.

---

# 3.4 IAM Policy

An IAM Policy defines permissions.

A policy can specify:

```text
Effect
Action
Resource
```

Example concept:

```text
Effect: Allow
Action: s3:GetObject
Resource: Specific S3 Bucket
```

This means the identity is allowed to perform the specified action on the specified resource.

---

# 3.5 MFA

**MFA = Multi-Factor Authentication**

MFA provides an additional authentication factor.

Instead of:

```text
Password
```

authentication becomes:

```text
Password
   +
MFA Code
```

This provides additional protection if the password is compromised.

---

# 3.6 Access Keys

Access keys are used for programmatic access to AWS.

They commonly consist of:

```text
Access Key ID
Secret Access Key
```

Example:

```text
Application / AWS CLI
        ↓
Access Key
        ↓
AWS API
```

Access keys are sensitive credentials.

Never commit them to GitHub.

Do not place them directly inside source code.

---

# 4. Least Privilege

## What is Least Privilege?

**Least Privilege means giving only the minimum permissions required to perform a task.**

This is one of the most important security principles.

---

## Example

Suppose an application needs to read objects from S3.

Bad:

```text
Application
     ↓
AdministratorAccess
     ↓
Entire AWS Account
```

The application does not need administrator permissions.

Better:

```text
Application
     ↓
IAM Role
     ↓
s3:GetObject
     ↓
Specific S3 Bucket
```

---

## Why Least Privilege is Important

It reduces the impact of:

* Stolen credentials
* Compromised applications
* Human mistakes
* Unauthorized access
* Malware
* Security incidents

---

## Least Privilege Example

Suppose:

```text
Application A
```

needs:

```text
S3 read access
```

Do not give:

```text
EC2 Full Access
RDS Full Access
IAM Full Access
S3 Full Access
AdministratorAccess
```

Instead give:

```text
S3 Read-Only
```

and preferably restrict it to the required resources.

---

# 5. Secrets Management

## What are Secrets?

Secrets are sensitive pieces of information used by applications and infrastructure.

Examples include:

```text
Database Password
API Key
Access Token
AWS Credentials
Private Key
Application Secret
Encryption Secret
```

Example:

```text
DB_USERNAME = admin
DB_PASSWORD = password123
```

These values must be protected.

---

# Why Secrets Should Not Be Hard-Coded

Bad example:

```python
DB_PASSWORD = "password123"
API_KEY = "abcdef123456"
```

Problems:

* The password can be exposed in Git.
* Developers can accidentally share it.
* Logs may expose it.
* Docker images may contain it.
* Anyone with repository access may see it.

---

# Bad Secret Management

```text
Application Code
      |
      +---- Password
      |
      +---- API Key
      |
      +---- Access Token
```

This is dangerous.

---

# Better Secret Management

```text
Application
     ↓
Secret Management System
     ↓
Secret
```

The application retrieves the secret when required.

AWS provides:

**AWS Secrets Manager**

for this purpose.

---

# 6. SSH Keys

## What is SSH?

**SSH = Secure Shell**

SSH is a secure protocol used to connect to remote systems.

A common Cloud/DevOps use case is connecting to an EC2 Linux server.

```text
Developer Laptop
       |
       | SSH
       ↓
      EC2
```

Default SSH port:

```text
TCP 22
```

---

# SSH Authentication

AWS EC2 commonly uses SSH key pairs for Linux instances.

An SSH key pair contains:

```text
Public Key
Private Key
```

---

# Public Key

The public key can be stored on the server.

It is used as part of the authentication process.

---

# Private Key

The private key remains with the administrator/user.

Example:

```text
my-key.pem
```

The private key must be protected.

Never share it publicly.

---

# SSH Authentication Architecture

```text
Your Mac
   |
   | Private Key
   |
   ↓
 SSH Authentication
   |
   ↓
EC2
   |
   | Public Key
   ↓
Authentication Verification
```

---

# SSH Command Example

```bash
ssh -i my-key.pem ec2-user@<PUBLIC-IP>
```

For Ubuntu:

```bash
ssh -i my-key.pem ubuntu@<PUBLIC-IP>
```

The exact username depends on the operating system image.

---

# Protecting the Private Key

On Linux/macOS:

```bash
chmod 400 my-key.pem
```

This restricts permissions on the private key.

---

# Important SSH Security Practices

Do not:

```text
Upload private key to GitHub
```

Do not:

```text
Send private key through public communication channels
```

Do not:

```text
Allow SSH from the entire internet unnecessarily
```

Avoid:

```text
0.0.0.0/0 → TCP 22
```

Prefer:

```text
Trusted IP → TCP 22
```

or a controlled administrative access mechanism.

---

# Bastion Host Example

A secure architecture can use a bastion host.

```text
Internet
   |
   ↓
Bastion Host
Public Subnet
   |
   ↓
Private EC2
Private Subnet
```

The bastion host can provide controlled SSH access to private servers.

Example security groups:

```text
Bastion-SG
TCP 22
Source: Trusted Admin IP
```

and:

```text
Private-EC2-SG
TCP 22
Source: Bastion-SG
```

This avoids exposing the private EC2 server directly to the internet.

---

# 7. Security Groups

## What is a Security Group?

A **Security Group (SG)** acts as a virtual firewall for AWS resources such as EC2 instances.

It controls network traffic.

For example:

```text
Internet
   |
   ↓
Security Group
   |
   ↓
EC2
```

---

# Security Group Rules

A Security Group can contain:

### Inbound Rules

Control incoming traffic.

Example:

```text
HTTP
TCP
Port 80
Source: 0.0.0.0/0
```

### SSH

```text
TCP
Port 22
Source: My IP
```

### HTTPS

```text
TCP
Port 443
Source: 0.0.0.0/0
```

---

# Example Security Group

```text
Inbound Rules

Type       Port       Source
-----------------------------------
SSH        22         My IP
HTTP       80         0.0.0.0/0
HTTPS      443        0.0.0.0/0
```

---

# Security Group is Stateful

Security Groups are **stateful**.

If inbound traffic is allowed and the response is generated by the instance, the return traffic is automatically allowed as part of the established connection.

---

# Security Groups are Allow-Based

Security Groups support allow rules.

They do not work like a traditional ordered deny-rule firewall.

---

# Security Group Example

Suppose:

```text
EC2 Web Server
```

needs:

```text
HTTP → 80
HTTPS → 443
SSH → 22
```

A reasonable configuration could be:

```text
HTTP   80   → Internet
HTTPS  443  → Internet
SSH    22   → Administrator IP
```

Do not unnecessarily expose SSH to:

```text
0.0.0.0/0
```

---

# 8. Network ACLs

## What is a Network ACL?

**NACL = Network Access Control List**

A NACL is a network-level security control for a VPC subnet.

Architecture:

```text
VPC
 |
 +---- Public Subnet
 |          |
 |        NACL
 |          |
 |         EC2
 |
 +---- Private Subnet
            |
          NACL
            |
           EC2
```

---

# NACL Rules

NACLs can contain:

* Inbound rules
* Outbound rules

Example:

```text
Inbound
Allow TCP 80
Allow TCP 443
Deny other traffic
```

---

# NACL is Stateless

NACLs are **stateless**.

This means inbound and outbound traffic are evaluated independently.

For example, if you allow incoming traffic, you may also need to explicitly allow the corresponding return traffic in the outbound rules.

---

# NACL Rule Numbers

NACL rules are evaluated in order.

Example:

```text
Rule 100 → Allow
Rule 110 → Allow
Rule 120 → Deny
```

The first matching rule is applied.

Therefore rule ordering is important.

---

# NACL vs Security Group

A simple way to remember:

```text
Security Group
       ↓
Instance / ENI level

NACL
       ↓
Subnet level
```

---

# 9. Security Groups vs Network ACLs

| Feature        | Security Group                                    | Network ACL                        |
| -------------- | ------------------------------------------------- | ---------------------------------- |
| Level          | Instance / network interface                      | Subnet                             |
| Stateful       | Yes                                               | No                                 |
| Rules          | Allow                                             | Allow and Deny                     |
| Rule order     | Not traditional numbered evaluation               | Numbered order                     |
| Return traffic | Automatically allowed for established connections | Must be explicitly allowed         |
| Main purpose   | Control traffic to resources                      | Control traffic at subnet boundary |

---

# Example

Consider:

```text
Internet
   |
   ↓
Public Subnet
   |
  NACL
   |
  EC2
   |
 Security Group
```

Both controls can participate in protecting the workload.

---

# 10. Encryption

## What is Encryption?

Encryption converts readable data into protected ciphertext.

```text
Plaintext
    ↓
Encryption
    ↓
Ciphertext
```

Only an authorized party with the appropriate key can decrypt the data.

```text
Ciphertext
    ↓
Decryption
    ↓
Plaintext
```

---

# Example

Without encryption:

```text
Password
   ↓
Readable Data
```

With encryption:

```text
Password
   ↓
Encryption
   ↓
Encrypted Data
```

---

# Why Encryption is Important

Encryption helps protect data if unauthorized parties gain access to the underlying storage or network traffic.

Important areas include:

* Databases
* EBS volumes
* S3 objects
* Backups
* Network traffic
* Application secrets

---

# Two Major Types of Encryption

```text
Encryption
   |
   +---- Encryption at Rest
   |
   +---- Encryption in Transit
```

---

# 11. Encryption at Rest

## What does "at rest" mean?

Data at rest means data stored somewhere.

Examples:

```text
EBS
S3
RDS
Database
Backup
Snapshot
```

Example:

```text
Application
     ↓
Database
     ↓
Encrypted Storage
```

---

# AWS Examples

### EBS

EBS volumes can be encrypted.

```text
EC2
 ↓
Encrypted EBS
```

### S3

S3 objects can be encrypted.

```text
Application
     ↓
S3
     ↓
Encrypted Object
```

### RDS

RDS databases can use encryption at rest.

```text
Application
     ↓
RDS
     ↓
Encrypted Database Storage
```

---

# 12. Encryption in Transit

## What does "in transit" mean?

Data in transit is data moving across a network.

Example:

```text
Browser
   |
   | Encrypted Traffic
   ↓
Server
```

Protocols such as TLS are used to protect network communication.

Example:

```text
Browser
   |
 HTTPS / TLS
   |
   ↓
Web Server
```

---

# 13. AWS KMS

## What is AWS KMS?

**AWS KMS = AWS Key Management Service**

KMS is used to create and manage cryptographic keys and perform cryptographic operations used by AWS services and applications.

Conceptually:

```text
AWS Resource
     ↓
Encryption
     ↓
KMS Key
```

---

# Example

An encrypted EBS volume can use an AWS KMS key.

```text
EC2
 ↓
EBS
 ↓
Encryption
 ↓
KMS
```

Similarly, encryption can be used with other AWS services.

---

# KMS and Encryption

```text
Data
 ↓
Encryption
 ↓
KMS Key
 ↓
Encrypted Data
```

KMS helps organizations manage cryptographic keys and control access to them.

---

# 14. HTTPS

## What is HTTPS?

**HTTPS = Hypertext Transfer Protocol Secure**

HTTPS is HTTP communication protected using TLS.

HTTP normally uses:

```text
Port 80
```

HTTPS normally uses:

```text
Port 443
```

---

# HTTP

```text
Browser
   |
 HTTP
   |
   ↓
Server
```

---

# HTTPS

```text
Browser
   |
 HTTPS
   |
 TLS Encryption
   |
   ↓
Server
```

HTTPS protects data while it travels between the client and server.

---

# Why HTTPS is Important

HTTPS helps protect against attackers intercepting or modifying network communication.

Examples of information that should be protected:

* Passwords
* Login credentials
* Payment information
* API requests
* Personal information
* Session information

---

# HTTPS Example

When you visit:

```text
https://example.com
```

the browser establishes a secure TLS connection with the server.

---

# 15. TLS / SSL

## What is TLS?

**TLS = Transport Layer Security**

TLS is a cryptographic protocol used to secure communication over networks.

It provides important security properties such as:

* Encryption
* Authentication
* Integrity

---

# What is SSL?

**SSL = Secure Sockets Layer**

SSL is the older predecessor to TLS.

Modern secure systems use TLS rather than obsolete SSL versions.

The term **SSL certificate** is still commonly used even though modern connections generally use TLS.

---

# TLS Concept

```text
Client
  |
  | TLS Handshake
  |
  ↓
Server
  |
  ↓
Secure Encrypted Connection
```

---

# TLS Certificate

A website normally uses a digital certificate to establish the identity of the server.

Example:

```text
Browser
   ↓
HTTPS Request
   ↓
TLS Certificate
   ↓
Server Authentication
   ↓
Encrypted Connection
```

---

# HTTPS and TLS Relationship

Remember:

```text
HTTPS
  =
HTTP
  +
TLS
```

HTTPS is HTTP transmitted over a secure TLS connection.

---

# 16. HTTPS vs HTTP

| Feature        | HTTP                         | HTTPS                             |
| -------------- | ---------------------------- | --------------------------------- |
| Security       | No encryption by itself      | Protected using TLS               |
| Port           | 80                           | 443                               |
| Encryption     | No                           | Yes                               |
| Authentication | No TLS server authentication | TLS certificate                   |
| Data integrity | Not protected by HTTP itself | TLS provides integrity protection |

---

# 17. Vulnerability Scanning

## What is a Vulnerability?

A vulnerability is a weakness that could potentially be exploited by an attacker.

Examples:

```text
Outdated Software
Weak Configuration
Known Security Bug
Unpatched Package
Insecure Service
```

---

# What is Vulnerability Scanning?

Vulnerability scanning means automatically checking systems, applications, dependencies, or container images for known security weaknesses.

Conceptually:

```text
Application
     ↓
Vulnerability Scanner
     ↓
Security Findings
     ↓
Fix Vulnerabilities
```

---

# Example

Suppose your server is running:

```text
Apache version X
```

If that version contains a known security vulnerability, a vulnerability scanner may identify it.

---

# Vulnerability Scanning in DevSecOps

A CI/CD pipeline can perform security scanning before deployment.

```text
GitHub
   ↓
Build
   ↓
Security Scan
   ↓
Vulnerability Found?
   |
   +---- Yes → Stop / Fix
   |
   +---- No → Continue
```

---

# Why Vulnerability Scanning is Important

It helps identify:

* Known vulnerabilities
* Outdated software
* Insecure packages
* Vulnerable container images
* Configuration problems

---

# 18. Dependency Scanning

## What is a Dependency?

A dependency is a library or package that an application uses.

For example, a Python application might use:

```text
Flask
NumPy
Requests
TensorFlow
```

A Node.js application might use:

```text
Express
React
Axios
```

---

# What is Dependency Scanning?

Dependency scanning checks application dependencies for known security vulnerabilities.

Example:

```text
Application
    |
    +---- Flask
    +---- Requests
    +---- Other Packages
             |
             ↓
     Dependency Scanner
             |
             ↓
     Vulnerability Found
```

---

# Example

Suppose:

```text
Application
     ↓
requests version 1.x
```

A scanner may detect that the package version has a known vulnerability.

The developer can then upgrade it.

```text
Old Version
    ↓
Vulnerability
    ↓
Upgrade Package
    ↓
New Version
```

---

# Dependency Scanning in CI/CD

```text
Developer
    ↓
Git Push
    ↓
CI/CD Pipeline
    ↓
Install Dependencies
    ↓
Dependency Scan
    ↓
Security Result
```

If the configured security policy fails, the pipeline can prevent deployment until the issue is addressed.

---

# 19. Container Security

## What is Container Security?

Container security means protecting:

* Container images
* Containers
* Container runtime
* Container configuration
* Container secrets
* Container network
* Host system

Docker containers are widely used in DevOps.

```text
Application
    ↓
Dockerfile
    ↓
Docker Image
    ↓
Container
```

Security must be considered at each stage.

---

# Container Security Areas

```text
Container Security
       |
       +---- Base Image
       |
       +---- Packages
       |
       +---- Vulnerabilities
       |
       +---- Secrets
       |
       +---- User Permissions
       |
       +---- Network
       |
       +---- Runtime
```

---

# 19.1 Use Trusted Base Images

Example:

```dockerfile
FROM ubuntu:latest
```

Using arbitrary or unknown images can introduce security risks.

Use trusted and maintained base images where possible.

---

# 19.2 Keep Images Updated

Container images may contain outdated packages.

Regularly update the base image and dependencies.

```text
Old Image
   ↓
Security Updates
   ↓
Rebuild
   ↓
New Image
```

---

# 19.3 Scan Container Images

Before deploying an image:

```text
Docker Image
      ↓
Image Scanner
      ↓
Vulnerabilities
```

If serious vulnerabilities are detected, investigate and remediate them before deployment.

---

# 19.4 Do Not Store Secrets in Dockerfiles

Bad:

```dockerfile
ENV DB_PASSWORD="password123"
```

The secret may become part of the image configuration/history.

Better:

```text
Container
    ↓
Secret Management
    ↓
Runtime Secret
```

---

# 19.5 Run Containers with Appropriate Permissions

Avoid giving containers unnecessary privileges.

For example, applications generally should not require unrestricted root privileges.

Use a non-root user where appropriate.

Example:

```dockerfile
USER appuser
```

---

# 19.6 Minimize Container Images

A smaller image generally contains fewer packages and components.

Conceptually:

```text
Large Image
   ↓
More Packages
   ↓
More Components
   ↓
Larger Attack Surface
```

A minimal image can reduce unnecessary components.

---

# 20. Secret Scanning

## What is Secret Scanning?

Secret scanning searches source code and repositories for exposed credentials and sensitive information.

Examples:

```text
AWS Access Key
API Key
Password
Private Key
GitHub Token
Database Credential
```

---

# Example

Suppose a developer accidentally commits:

```python
AWS_SECRET_ACCESS_KEY = "xxxxxxxx"
```

A secret scanning tool may detect it.

---

# Secret Scanning Flow

```text
Developer
    ↓
Git Push
    ↓
Secret Scanner
    ↓
Secret Detected?
    |
    +---- Yes → Block / Alert / Revoke
    |
    +---- No → Continue
```

---

# Why Secret Scanning is Important

Credentials can accidentally enter:

* Git repositories
* GitHub
* Docker images
* Configuration files
* CI/CD logs
* Source code
* Documentation

Once a secret is exposed, simply deleting it from the latest file may not be enough because it can remain in Git history or other systems.

A compromised credential should be treated seriously and may need to be revoked or rotated.

---

# Bad Example

```text
project/
 |
 +---- app.py
 |
 +---- config.py
          |
          +---- API_KEY="123456"
```

---

# Better Approach

```text
project/
 |
 +---- app.py
 |
 +---- config.py
          |
          +---- Read secret from environment / secret manager
```

For production applications, a dedicated secrets-management system is preferred.

---

# 21. AWS Secrets Manager

## What is AWS Secrets Manager?

**AWS Secrets Manager** is an AWS service used to securely store, manage, and retrieve secrets.

Examples:

```text
Database Password
API Key
Application Credential
Database Connection Secret
```

---

# Why Use Secrets Manager?

Instead of storing:

```text
Password
```

inside:

```text
Source Code
```

we can store it in:

```text
AWS Secrets Manager
```

and retrieve it when needed.

---

# Basic Architecture

```text
Application
     |
     ↓
AWS Secrets Manager
     |
     ↓
Secret
     |
     ↓
Application
```

---

# Example

Suppose an application connects to an RDS database.

Without Secrets Manager:

```text
Application
    ↓
Hard-coded DB password
    ↓
RDS
```

This is not a good security practice.

With Secrets Manager:

```text
Application
    ↓
IAM Role
    ↓
Secrets Manager
    ↓
Database Credentials
    ↓
RDS
```

---

# IAM + Secrets Manager

The application should not automatically have permission to read every secret.

Use IAM to control access.

Example:

```text
EC2
 ↓
IAM Role
 ↓
Permission
 ↓
Secrets Manager
 ↓
Specific Secret
```

This follows the principle of least privilege.

---

# Secrets Manager Example

Suppose we have:

```text
Secret Name:
production/database
```

The secret might contain:

```text
username
password
host
port
database
```

The application can retrieve the secret when it needs to connect to the database.

---

# Secrets Manager and RDS

A common architecture is:

```text
              AWS

Application
    |
    ↓
IAM Role
    |
    ↓
Secrets Manager
    |
    ↓
Database Credentials
    |
    ↓
RDS
```

This prevents the application from needing to hard-code the database password.

---

# Secret Rotation

Secret rotation means changing a secret periodically or when required.

Example:

```text
Old Password
     ↓
Rotation
     ↓
New Password
```

Rotation reduces the amount of time a compromised credential remains valid.

Secrets Manager can support automated rotation workflows for supported scenarios.

---

# 22. Secrets Manager Architecture

A more complete architecture:

```text
                       AWS
                        |
                 IAM Role / Policy
                        |
                        ↓
                 Secrets Manager
                        |
                 +------+------+
                 |             |
             Secret A       Secret B
                 |             |
                 ↓             ↓
             Database       API Credential
```

The application gets permission to retrieve only the secret it requires.

---

# 23. Security in CI/CD

Security should be integrated into CI/CD pipelines.

A secure pipeline can look like:

```text
Developer
    ↓
GitHub
    ↓
Source Code Scan
    ↓
Secret Scan
    ↓
Dependency Scan
    ↓
Build
    ↓
Docker Image
    ↓
Container Vulnerability Scan
    ↓
Tests
    ↓
Deployment
    ↓
AWS
```

---

# Example CI/CD Security Checks

## Step 1 — Source Code

Check source code for security problems.

```text
Source Code
    ↓
Static Analysis
```

---

## Step 2 — Secret Scanning

Check for:

```text
API Keys
Passwords
Tokens
Private Keys
AWS Credentials
```

---

## Step 3 — Dependency Scanning

Check:

```text
Python Packages
Node Packages
Java Dependencies
Other Libraries
```

for known vulnerabilities.

---

## Step 4 — Container Scanning

If Docker is used:

```text
Dockerfile
   ↓
Docker Image
   ↓
Vulnerability Scanner
```

---

## Step 5 — Deploy

Only after the security checks pass should the pipeline continue according to the organization's security policy.

```text
Security Checks
      |
      +---- Failed → Investigate / Fix
      |
      +---- Passed → Deployment
```

---

# 24. Security Best Practices

## 1. Follow Least Privilege

Give users and applications only the permissions they need.

```text
Minimum Required Permissions
```

---

## 2. Use IAM Roles

For AWS workloads such as EC2, prefer IAM roles rather than embedding long-term AWS access keys in applications.

```text
EC2
 ↓
IAM Role
 ↓
AWS Service
```

---

## 3. Protect SSH Keys

Never expose private keys.

Do not commit:

```text
*.pem
```

to GitHub.

---

## 4. Restrict SSH Access

Avoid:

```text
0.0.0.0/0 → Port 22
```

when it is not required.

Prefer a trusted source or controlled administrative access.

---

## 5. Use Security Groups Carefully

Only open the required ports.

Example:

```text
HTTP  → 80
HTTPS → 443
SSH   → 22 from trusted source
```

---

## 6. Use Private Subnets

Sensitive backend resources such as databases generally should not need direct internet exposure.

Example:

```text
Internet
    |
    ↓
Load Balancer
    |
    ↓
Private EC2
    |
    ↓
Private RDS
```

---

## 7. Encrypt Data

Use encryption for sensitive data at rest.

Examples:

```text
EBS
S3
RDS
Backups
```

---

## 8. Encrypt Network Traffic

Use:

```text
HTTPS
TLS
```

for appropriate application communication.

---

## 9. Do Not Hard-Code Secrets

Avoid:

```text
Password in Source Code
API Key in Source Code
AWS Credentials in Source Code
```

Use:

```text
Secrets Manager
Environment / runtime secret mechanisms
IAM Roles
```

according to the workload.

---

## 10. Scan Dependencies

Keep application libraries updated.

```text
Dependencies
     ↓
Security Scan
     ↓
Vulnerabilities
     ↓
Update / Remediate
```

---

## 11. Scan Container Images

Before deploying:

```text
Docker Image
     ↓
Security Scanner
```

---

## 12. Enable Monitoring and Logging

Security events should be monitored.

AWS security-related services and controls can include:

```text
CloudWatch
CloudTrail
VPC Flow Logs
IAM
GuardDuty
Security Hub
```

The exact services used depend on the architecture and security requirements.

---

## 13. Use MFA

Enable MFA for important identities, especially privileged access.

```text
Password
   +
MFA
```

---

## 14. Regularly Review Permissions

Permissions should be reviewed periodically.

Remove permissions that are no longer required.

---

# 25. Real-World AWS Security Architecture

Consider a web application.

```text
                         USERS
                           |
                           ↓
                       Internet
                           |
                           ↓
                    HTTPS / TLS
                           |
                           ↓
                    Load Balancer
                           |
                           ↓
                 +---------+---------+
                 |                   |
              EC2-1               EC2-2
                 |                   |
                 +---------+---------+
                           |
                           ↓
                          RDS
```

Now add security.

```text
                         USERS
                           |
                           ↓
                       HTTPS/TLS
                           |
                           ↓
                    Application LB
                     [ALB-SG]
                           |
                           ↓
                    Private EC2
                    [EC2-SG]
                           |
                           ↓
                     Private RDS
                    [RDS-SG]
```

Security Group relationships:

```text
ALB-SG
  ↓
Allow HTTP/HTTPS
  ↓
Internet

EC2-SG
  ↓
Allow Application Traffic
  ↓
Source: ALB-SG

RDS-SG
  ↓
Allow Database Port
  ↓
Source: EC2-SG
```

This is better than allowing the database to accept traffic directly from the internet.

---

# Secrets in the Same Architecture

```text
                       Application
                           |
                           ↓
                       EC2 Role
                           |
                           ↓
                  AWS Secrets Manager
                           |
                           ↓
                    DB Credentials
                           |
                           ↓
                          RDS
```

---

# Monitoring

```text
EC2
 |
 ↓
CloudWatch
 |
 +---- Metrics
 |
 +---- Logs
 |
 +---- Alarms
```

Additional security auditing can use:

```text
CloudTrail
VPC Flow Logs
Security Services
```

---

# Complete Architecture

```text
                         USERS
                           |
                           ↓
                     HTTPS / TLS
                           |
                           ↓
                    Application ALB
                           |
                    +------+------+
                    |             |
                    ↓             ↓
                  EC2-1         EC2-2
                    |             |
                    +------+------+
                           |
                           ↓
                          RDS
                           |
                    Encrypted Storage


              SECURITY CONTROLS

       IAM
        |
        ↓
   Least Privilege
        |
        ↓
     EC2 Role
        |
        ↓
Secrets Manager
        |
        ↓
  Database Secret


       NETWORK SECURITY

    Security Groups
          +
        NACLs
          +
   Private Subnets


       DATA SECURITY

     Encryption
          +
      AWS KMS


       APPLICATION SECURITY

 Vulnerability Scanning
 Dependency Scanning
 Container Scanning
 Secret Scanning


       MONITORING

 CloudWatch
 CloudTrail
 VPC Flow Logs
```

---

# 26. Security Checklist

Use this checklist when deploying a Cloud/DevOps application.

## IAM

* [ ] Avoid using root for normal operations
* [ ] Use IAM users/roles appropriately
* [ ] Use IAM roles for AWS workloads
* [ ] Apply least privilege
* [ ] Enable MFA where appropriate
* [ ] Review permissions regularly
* [ ] Avoid unnecessary access keys

---

## Secrets

* [ ] Do not hard-code passwords
* [ ] Do not hard-code API keys
* [ ] Do not commit credentials to GitHub
* [ ] Use AWS Secrets Manager where appropriate
* [ ] Rotate sensitive credentials when required
* [ ] Restrict access to secrets

---

## SSH

* [ ] Protect private SSH keys
* [ ] Never upload `.pem` files to GitHub
* [ ] Restrict SSH access
* [ ] Avoid unnecessary `0.0.0.0/0` access on port 22
* [ ] Use controlled administrative access

---

## Network

* [ ] Configure Security Groups carefully
* [ ] Configure NACLs appropriately
* [ ] Use private subnets for sensitive resources
* [ ] Avoid unnecessary open ports
* [ ] Use HTTPS for web applications

---

## Encryption

* [ ] Encrypt sensitive data at rest
* [ ] Encrypt network traffic in transit
* [ ] Use TLS
* [ ] Use AWS KMS where appropriate
* [ ] Protect encryption keys

---

## Application

* [ ] Perform vulnerability scanning
* [ ] Perform dependency scanning
* [ ] Perform secret scanning
* [ ] Scan container images
* [ ] Keep dependencies updated
* [ ] Use trusted container base images

---

## Monitoring

* [ ] Enable CloudWatch monitoring
* [ ] Collect relevant logs
* [ ] Monitor important metrics
* [ ] Configure alarms
* [ ] Use CloudTrail for AWS API auditing where appropriate
* [ ] Consider VPC Flow Logs for network visibility

---

# 27. Important Interview Questions

## Q1. What is IAM?

IAM stands for Identity and Access Management. It controls who can access AWS resources and what actions they are allowed to perform.

---

## Q2. What is least privilege?

Least privilege means giving an identity only the minimum permissions required to perform its task.

---

## Q3. What is the difference between IAM User and IAM Role?

An IAM User represents an identity, commonly a person or long-term identity.

An IAM Role is an identity that can be assumed by trusted entities such as EC2, Lambda, or users.

Example:

```text
EC2
 ↓
IAM Role
 ↓
S3
```

---

## Q4. Why should you use an IAM Role for EC2?

Because the application can obtain temporary AWS credentials through the role instead of storing long-term AWS access keys inside the application.

---

## Q5. What is a Security Group?

A Security Group is a virtual firewall associated with AWS resources such as EC2 network interfaces. It controls network traffic using allow rules.

---

## Q6. What is a NACL?

A Network ACL is a subnet-level network access control mechanism in a VPC.

---

## Q7. What is the difference between Security Group and NACL?

```text
Security Group
    ↓
Resource / ENI level
    ↓
Stateful
    ↓
Allow rules

NACL
    ↓
Subnet level
    ↓
Stateless
    ↓
Allow and Deny
    ↓
Rules evaluated in order
```

---

## Q8. What is encryption?

Encryption converts readable plaintext into protected ciphertext so that unauthorized parties cannot easily read the data.

---

## Q9. What is encryption at rest?

Encryption applied to stored data.

Examples:

```text
EBS
S3
RDS
Backups
```

---

## Q10. What is encryption in transit?

Encryption applied to data while it travels over a network.

Example:

```text
Browser
   ↓
HTTPS / TLS
   ↓
Server
```

---

## Q11. What is AWS KMS?

AWS KMS is a managed AWS service used to create and manage cryptographic keys and perform cryptographic operations.

---

## Q12. What is HTTPS?

HTTPS is HTTP protected using TLS.

```text
HTTPS = HTTP + TLS
```

HTTPS commonly uses port:

```text
443
```

---

## Q13. What is TLS?

TLS stands for Transport Layer Security. It provides secure communication over a network using encryption, authentication, and integrity protection.

---

## Q14. What is SSL?

SSL stands for Secure Sockets Layer. It is an older predecessor to TLS.

Modern secure systems use TLS rather than obsolete SSL versions.

---

## Q15. What is vulnerability scanning?

Vulnerability scanning is the process of checking systems, applications, packages, or images for known security vulnerabilities.

---

## Q16. What is dependency scanning?

Dependency scanning checks third-party libraries and packages used by an application for known vulnerabilities.

---

## Q17. What is container security?

Container security means protecting container images, containers, runtime environments, configurations, secrets, networks, and the underlying host.

---

## Q18. What is secret scanning?

Secret scanning detects sensitive credentials such as:

```text
API Keys
Passwords
Tokens
Private Keys
Cloud Credentials
```

inside source code and repositories.

---

## Q19. What is AWS Secrets Manager?

AWS Secrets Manager is an AWS service used to securely store, manage, retrieve, and, where configured, rotate secrets such as database credentials and API keys.

---

## Q20. Why should passwords not be stored in source code?

Because source code can be shared, committed to Git, copied into logs, or exposed through repositories.

Instead, use an appropriate secrets-management mechanism.

---

## Q21. What is DevSecOps?

DevSecOps integrates security into the DevOps lifecycle.

```text
Development
      ↓
Security
      ↓
Testing
      ↓
CI/CD
      ↓
Deployment
      ↓
Monitoring
```

---

# 28. Key Concepts to Remember

The most important concepts from this topic are:

```text
IAM
 ↓
Controls access

Least Privilege
 ↓
Minimum required permissions

Secrets Management
 ↓
Protect sensitive credentials

SSH Keys
 ↓
Secure server authentication

Security Groups
 ↓
Resource-level network control

NACL
 ↓
Subnet-level network control

Encryption
 ↓
Protect stored and transmitted data

KMS
 ↓
Manage cryptographic keys

HTTPS
 ↓
Secure HTTP communication

TLS
 ↓
Protect network communication

Vulnerability Scanning
 ↓
Find known security weaknesses

Dependency Scanning
 ↓
Find vulnerabilities in packages

Container Security
 ↓
Secure container images and runtime

Secret Scanning
 ↓
Detect exposed credentials

AWS Secrets Manager
 ↓
Securely store and retrieve secrets
```

---

# Complete Security / DevSecOps Flow

A Cloud/DevOps engineer should think about security throughout the entire lifecycle.

```text
                    DEVELOPER
                        |
                        ↓
                    GitHub
                        |
              +---------+---------+
              |                   |
              ↓                   ↓
        Secret Scanning     Code Security
              |                   |
              +---------+---------+
                        |
                        ↓
                 Dependency Scan
                        |
                        ↓
                     Build
                        |
                        ↓
                 Docker Image
                        |
                        ↓
              Container Scan
                        |
                        ↓
                    CI/CD
                        |
                        ↓
                       AWS
                        |
             +----------+----------+
             |                     |
             ↓                     ↓
           IAM                Networking
             |                     |
       Least Privilege       SG + NACL
             |                     |
             +----------+----------+
                        |
                        ↓
                   Application
                        |
                        ↓
                    Database
                        |
                        ↓
                   Encryption
                        |
                        ↓
                    KMS / Keys
                        |
                        ↓
                  Secrets Manager
                        |
                        ↓
                  CloudWatch /
                  CloudTrail /
                  Logs
```

---

# Final Summary

Security in Cloud and DevOps is not a single service.

It is a combination of multiple security controls.

```text
              CLOUD SECURITY
                    |
    +---------------+----------------+
    |               |                |
   IAM           NETWORK            DATA
    |               |                |
Least Privilege   SG / NACL       Encryption
Roles             HTTPS           KMS
MFA               TLS
                    |
                    |
              APPLICATION
                    |
        +-----------+-----------+
        |           |           |
   Vulnerability  Dependency  Secret
     Scanning      Scanning   Scanning
        |
        ↓
     CONTAINER
        |
        ↓
 Container Security
        |
        ↓
      SECRETS
        |
        ↓
 AWS Secrets Manager
```

The main principle to remember is:

> **Security should be built into the system from the beginning, not added only after deployment.**

A good Cloud/DevOps engineer should continuously think about:

```text
Who can access it?
        ↓
What can they access?
        ↓
What permissions do they need?
        ↓
Is the network protected?
        ↓
Is the data encrypted?
        ↓
Are secrets protected?
        ↓
Are dependencies secure?
        ↓
Are containers secure?
        ↓
Are vulnerabilities being scanned?
        ↓
Are security events being monitored?
```

This is the foundation of **Security and DevSecOps**.

