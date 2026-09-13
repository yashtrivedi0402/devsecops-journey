# 🔐 DevSecOps — Security-First DevOps Notes

My practical DevSecOps knowledge base — concepts, security practices, tools, commands, interview notes, labs, and real-world implementation patterns.

DevSecOps is not simply DevOps + a security tool. It is the practice of integrating security into the software delivery lifecycle so that security becomes a continuous responsibility across development, security , and operations.

The core idea is:

> **Build fast → build securely → detect early → automate → continuously improve.**

---

## 📖 Table of Contents

1. [Why This Repository Exists](#-why-this-repository-exists)
2. [What is DevSecOps?](#1-what-is-devsecops)
3. [DevSecOps Mindset](#2-devsecops-mindset)
4. [Shift Left Security](#3-shift-left-security)
5. [Security From Scratch](#4-security-from-scratch)
6. [Threat Modeling](#5-threat-modeling)
7. [Secure Git Practices](#6-secure-git-practices)
8. [Secrets Management](#7-secrets-management)
9. [Infrastructure as Code Security](#8-infrastructure-as-code-security)
10. [Secure Python / Scripting](#9-secure-python--scripting)
11. [Container Security](#10-container-security)
12. [Multi-Stage Builds](#11-multi-stage-builds)
13. [Distroless Images](#12-distroless-images)
14. [Kubernetes Security](#13-kubernetes-security)
15. [AWS + Kubernetes](#14-aws--kubernetes)
16. [CI/CD Security](#15-cicd-security)
17. [Security Testing in CI/CD](#16-security-testing-in-cicd)
18. [Common DevSecOps Pipeline Controls](#17-common-devsecops-pipeline-controls)
19. [Software Supply Chain Security](#18-software-supply-chain-security)
20. [Artifact Integrity](#19-artifact-integrity)
21. [Security as Code](#20-security-as-code)
22. [Least Privilege](#21-least-privilege)
23. [Zero Trust](#22-zero-trust)
24. [Continuous Monitoring & Feedback](#23-continuous-monitoring--feedback)
25. [DevSecOps Lifecycle](#24-devsecops-lifecycle)
26. [DevSecOps vs DevOps](#25-devsecops-vs-devops)
27. [Key Principles to Remember](#26-key-principles-to-remember)
28. [Practical DevSecOps Tool Map](#27-practical-devsecops-tool-map)
29. [Example End-to-End DevSecOps Architecture](#28-example-end-to-end-devsecops-architecture)
30. [Interview Questions to Prepare](#29-interview-questions-to-prepare)
31. [My DevSecOps Learning Roadmap](#30-my-devsecops-learning-roadmap)
32. [Practical Projects I Want to Build](#31-practical-projects-i-want-to-build)
33. [My Golden Rules](#32-my-golden-rules)
34. [References](#-references)
35. [Repository Philosophy](#-repository-philosophy)

---

## 📌 Why This Repository Exists

These notes are designed to be my go-to DevSecOps reference while learning and building projects.

This repository will focus on:

- Understanding the *why* behind DevSecOps
- Learning security practices instead of memorizing tools
- Implementing security in CI/CD pipelines
- Securing source code, dependencies, containers, Kubernetes and cloud infrastructure
- Building practical labs and documenting what I actually implement
- Preparing for DevSecOps / DevOps / Cloud interviews

---

## 1. What is DevSecOps?

**Definition**

DevSecOps = Development + Security + Operations

DevSecOps extends the DevOps model by making security a continuous part of the software development and delivery lifecycle.

In a traditional model:

```text
Development → Testing → Operations → Security Review
                                      ↓
                                Security comes late
```

In DevSecOps:

```text
        SECURITY THROUGHOUT THE LIFECYCLE
                     ↓
Plan → Code → Build → Test → Release → Deploy → Operate
  ↑      ↑      ↑       ↑       ↑        ↑        ↑
  └──────┴──────┴───────┴───────┴────────┴────────┘
                    Feedback
```

Security is not a final gate owned by a separate team. It is progressively automated and integrated into the delivery process.

---

## 2. DevSecOps Mindset

The most important shift is:

> Security should be considered from the beginning, not after the application is built.

**Traditional mindset**

```text
Build first
   ↓
Deploy
   ↓
Find security issues
   ↓
Fix later
```

**DevSecOps mindset**

```text
Plan securely
   ↓
Code securely
   ↓
Scan early
   ↓
Build securely
   ↓
Test continuously
   ↓
Deploy with controls
   ↓
Monitor continuously
   ↓
Improve
```

This is commonly described as **Shift Left Security**.

---

## 3. Shift Left Security

**What does "Shift Left" mean?**

Security testing and security decisions are moved earlier in the SDLC.

Instead of discovering a vulnerability after production deployment, try to detect it during:

- Planning
- Coding
- Pull requests
- Pre-commit checks
- CI builds
- Dependency installation
- Container image creation
- Infrastructure validation

**Example**

Bad:

```text
Developer → Code → Build → Deploy → Production → Vulnerability discovered
```

Better:

```text
Developer
   ↓
Pre-commit security checks
   ↓
Pull Request
   ↓
SAST + SCA + Secret Scan
   ↓
Build
   ↓
Container Scan
   ↓
IaC Scan
   ↓
Deploy
   ↓
Runtime Monitoring
```

**Why shift left?**

Finding a vulnerability earlier generally makes it easier and cheaper to fix because fewer downstream artifacts and environments are affected.

---

## 4. Security From Scratch

A DevSecOps engineer should not think:

> "The application is finished. Now let's add security."

Instead:

> "What security requirements should exist before we start building?"

Security should be considered across:

- Source code
- Git workflow
- Dependencies
- Build system
- CI/CD
- Secrets
- Infrastructure as Code
- Containers
- Kubernetes
- Cloud IAM
- Network security
- Runtime
- Monitoring
- Incident response

---

## 5. Threat Modeling

**What is Threat Modeling?**

Threat modeling is a structured process for identifying and evaluating possible security threats before they become incidents.

A simple workflow is:

```text
Identify assets
      ↓
Understand architecture/data flows
      ↓
Identify threats
      ↓
Analyze risk
      ↓
Choose mitigations
      ↓
Validate and monitor
```

**Questions to ask**

- What are we protecting?
- What are the important assets?
- Who can access them?
- What can an attacker do?
- Where can trust boundaries be crossed?
- What happens if a component is compromised?
- How can the risk be reduced?

**Example**

For an application:

```text
User
  ↓
Load Balancer
  ↓
Application
  ↓
Database
```

Threat-modeling questions:

- Can an attacker bypass authentication?
- Can the application expose database credentials?
- Can user input cause injection?
- Is the database publicly accessible?
- Does the application have excessive IAM permissions?
- What happens if the application container is compromised?

---

## 6. Secure Git Practices

Git is part of the security boundary.

**Pre-commit Security**

Security checks can run before code is committed.

Example:

```text
Developer
   ↓
git commit
   ↓
Pre-commit hooks
   ↓
Secret / lint / security checks
   ↓
Commit allowed or blocked
```

**What should we prevent?**

Never commit:

```text
AWS_ACCESS_KEY_ID
AWS_SECRET_ACCESS_KEY
DATABASE_PASSWORD
API_KEY
PRIVATE_KEY
TOKEN
```

**Important rule**

> A secret committed to Git should be treated as compromised.

Removing the secret from the latest file does not necessarily remove it from Git history.

If a real credential is exposed:

1. Revoke/rotate it immediately.
2. Remove it from the repository/history where appropriate.
3. Investigate where it was exposed.
4. Add prevention mechanisms.

---

## 7. Secrets Management

**What are secrets?**

Sensitive values such as:

- Passwords
- API keys
- Access tokens
- Private keys
- Database credentials
- Cloud credentials

**Never do this**

```text
AWS_SECRET = "my-real-secret"
```

or:

```text
password: my-real-password
```

**Better approach**

```text
Application
     ↓
Secrets Manager / Vault
     ↓
Secret retrieved securely
```

Examples of secret-management technologies:

- HashiCorp Vault
- AWS Secrets Manager
- AWS Systems Manager Parameter Store
- Kubernetes Secrets (with appropriate protection)

**Important distinction**

> Vault is not an IaC tool. Vault is primarily a secrets management and identity-based access system.

---

## 8. Infrastructure as Code Security

Infrastructure as Code (IaC) means defining infrastructure using code.

Examples:

- Terraform
- CloudFormation
- Pulumi
- Ansible (configuration automation)

Security should be applied before infrastructure reaches production.

Example:

```text
Terraform Code
      ↓
Format / Validate
      ↓
IaC Security Scan
      ↓
Policy Check
      ↓
Plan Review
      ↓
Apply
```

**What can IaC scanning detect?**

- Public storage buckets
- Open security groups
- Excessive permissions
- Unencrypted resources
- Publicly exposed databases
- Weak network configurations
- Missing logging/monitoring

---

## 9. Secure Python / Scripting

Python is useful for automation, but the script and its dependencies are also part of the attack surface.

**Security practices**

- Keep dependencies updated
- Pin or constrain dependencies appropriately
- Avoid unnecessary packages
- Scan dependencies for known vulnerabilities
- Validate untrusted input
- Avoid executing untrusted input
- Do not hardcode secrets
- Use virtual environments
- Use dependency lock files where appropriate

Example:

```text
requirements.txt
       ↓
Dependency analysis
       ↓
Known vulnerability check
       ↓
Build / test
```

**Important correction**

It is not realistic to say:

> "Use Python so there are no vulnerabilities."

Python applications can absolutely contain vulnerabilities. Security comes from secure coding + dependency management + testing + runtime controls.

---

## 10. Container Security

Containers should be treated as production security boundaries, not just packaging formats.

**Never run as root unnecessarily**

Bad:

```dockerfile
USER root
```

Better:

```dockerfile
RUN useradd -m appuser
USER appuser
```

The exact implementation depends on the base image and application.

**Other container security practices**

- Use minimal trusted base images
- Keep images updated
- Scan images for vulnerabilities
- Remove unnecessary packages
- Use multi-stage builds
- Do not put secrets inside images
- Pin trusted image versions/digests where appropriate
- Use read-only filesystems where practical
- Drop unnecessary Linux capabilities
- Avoid privileged containers
- Sign/verify images and artifacts where appropriate

---

## 11. Multi-Stage Builds

Multi-stage builds separate the build environment from the runtime environment.

Example:

```text
Builder Image
   ↓
Compile / Build
   ↓
Application Artifact
   ↓
Small Runtime Image
```

Benefits:

- Smaller image
- Smaller attack surface
- Fewer unnecessary packages
- Better separation between build and runtime

---

## 12. Distroless Images

Distroless images contain only the components required to run an application rather than a full general-purpose Linux userspace.

Conceptually:

```text
Full OS Image
├── Application
├── Runtime
├── Package Manager
├── Shell
├── Extra Utilities
└── Other Packages

        ↓

Distroless-style Runtime
├── Application
└── Required Runtime Components
```

Fewer unnecessary components can reduce the attack surface.

However:

> Distroless does not automatically make an application secure.

Application vulnerabilities, dependency vulnerabilities, bad permissions and runtime misconfiguration can still exist.

---

## 13. Kubernetes Security

Kubernetes introduces another security layer.

A secure Kubernetes environment should consider:

- Authentication
- Authorization / RBAC
- Network policies
- Secrets
- Pod Security
- Image security
- Admission controls
- Resource limits
- Logging and monitoring
- Cluster/node security

Example architecture:

```text
Developer
    ↓
Git
    ↓
CI/CD
    ↓
Container Registry
    ↓
Kubernetes / EKS
    ↓
Pods
    ↓
Application
```

---

## 14. AWS + Kubernetes

When using Kubernetes on AWS, a common managed option is:

**Amazon EKS (Elastic Kubernetes Service)**

A simplified network model:

```text
AWS Account
    ↓
VPC
    ↓
Subnets
    ↓
EKS Cluster
    ↓
Worker Nodes / Compute
    ↓
Pods
```

Security must be considered at every layer:

```text
IAM
 ↓
VPC
 ↓
Security Groups
 ↓
Subnets / Routing
 ↓
EKS
 ↓
RBAC
 ↓
Pods
 ↓
Application
```

**Important correction**

Do not think:

> "DevSecOps = VPC + subnet + EKS."

Those are infrastructure components.

DevSecOps is the security methodology and set of practices applied across the lifecycle, including the security of those infrastructure components.

---

## 15. CI/CD Security

CI/CD is one of the most important areas in DevSecOps.

A secure pipeline can look like:

```text
        ┌──────────────┐
        │    PLAN      │
        └──────┬───────┘
               ↓
        ┌──────────────┐
        │     CODE     │
        └──────┬───────┘
               ↓
     Secret / SAST / Lint
               ↓
        ┌──────────────┐
        │    BUILD     │
        └──────┬───────┘
               ↓
       Dependency Scan
               ↓
        ┌──────────────┐
        │     TEST     │
        └──────┬───────┘
               ↓
      DAST / Integration
               ↓
        Container Scan
               ↓
        ┌──────────────┐
        │   RELEASE    │
        └──────┬───────┘
               ↓
       Sign / Verify Artifact
               ↓
        ┌──────────────┐
        │    DEPLOY    │
        └──────┬───────┘
               ↓
        ┌──────────────┐
        │   OPERATE    │
        └──────┬───────┘
               ↓
       Monitor + Feedback
               ↺
```

---

## 16. Security Testing in CI/CD

Common security checks include:

| Check | Purpose |
|---|---|
| SAST | Find vulnerabilities in source code |
| SCA | Find vulnerable/outdated dependencies |
| Secret Scanning | Detect exposed credentials/secrets |
| IaC Scanning | Detect insecure infrastructure configurations |
| Container Scanning | Detect vulnerable container components |
| DAST | Test the running application from the outside |
| IAST | Analyze application behavior during execution |
| Linting | Detect code/configuration problems |
| Policy Checks | Enforce organizational security rules |

A mature pipeline does not necessarily run every tool on every commit. Checks should be selected based on risk, speed, architecture and deployment requirements.

---

## 17. Common DevSecOps Pipeline Controls

**Before Commit**

```text
Pre-commit hooks
├── Secret detection
├── Formatting
└── Basic security checks
```

**Pull Request**

```text
PR
├── Code review
├── SAST
├── SCA
├── Secret scan
└── IaC scan
```

**Build**

```text
Build
├── Dependency verification
├── Unit tests
├── Artifact creation
└── Container image creation
```

**Before Deployment**

```text
Release
├── Container scan
├── DAST where appropriate
├── Policy checks
├── Artifact verification
└── Approval / deployment policy
```

**Runtime**

```text
Production
├── Logs
├── Metrics
├── Security monitoring
├── Vulnerability management
└── Incident response
```

---

## 18. Software Supply Chain Security

Modern applications rarely contain only code written by the organization.

They depend on:

- Open-source libraries
- Container base images
- Build tools
- CI/CD actions/plugins
- Packages
- Third-party services

Therefore, the software supply chain itself must be secured.

**Important concepts:**

- Dependency scanning
- SBOM (Software Bill of Materials)
- Artifact integrity
- Artifact signing
- Provenance
- Trusted registries
- Dependency pinning
- Build isolation
- Verification

A useful mental model:

```text
Source
  ↓
Dependencies
  ↓
Build
  ↓
Artifact
  ↓
Registry
  ↓
Deployment
```

Security should follow the artifact through the entire chain.

---

## 19. Artifact Integrity

An artifact can be:

- Container image
- Binary
- Package
- Deployment bundle
- Other build output

Security mechanisms can establish:

```text
WHO produced it?
WHAT was built?
HOW was it built?
WERE dependencies involved?
HAS it been modified?
```

Common concepts:

- Hashes
- Digital signatures
- Provenance
- Attestations
- SBOM

---

## 20. Security as Code

Security policies and configurations should increasingly be represented as code so that they can be:

- Version controlled
- Reviewed
- Tested
- Automated
- Reproduced
- Audited

Example:

```text
Security Policy
      ↓
Code
      ↓
Git
      ↓
Review
      ↓
CI Validation
      ↓
Automated Enforcement
```

---

## 21. Least Privilege

Every identity, application, container and pipeline should receive only the permissions it actually needs.

Bad:

```text
CI/CD → AdministratorAccess
```

Better:

```text
CI/CD → Only required permissions
```

Questions to ask:

- Does this user need this permission?
- Does this service need this IAM action?
- Does this pod need access to the host?
- Does this pipeline need production credentials?
- Can short-lived credentials be used instead?

---

## 22. Zero Trust

A useful security principle is:

> Do not automatically trust a user, workload, network or device simply because it is inside the environment.

Access should be based on:

- Identity
- Authentication
- Authorization
- Context
- Policy
- Least privilege

For DevSecOps, this matters particularly around:

- CI/CD systems
- Cloud IAM
- Kubernetes
- Secrets
- Service-to-service communication
- Production environments

---

## 23. Continuous Monitoring & Feedback

DevSecOps does not end when deployment succeeds.

After deployment:

```text
Deploy
  ↓
Monitor
  ↓
Detect
  ↓
Investigate
  ↓
Remediate
  ↓
Improve
  ↓
Update Code / Pipeline / Infrastructure
  ↺
```

Security is therefore a continuous feedback loop, not a one-time activity.

---

## 24. DevSecOps Lifecycle

A practical lifecycle:

```text
PLAN
 ↓
DEVELOP
 ↓
BUILD
 ↓
TEST
 ↓
RELEASE
 ↓
DEPLOY
 ↓
OPERATE
 ↓
MONITOR / FEEDBACK
 ↺
```

Security controls should be mapped across all of these phases.

---

## 25. DevSecOps vs DevOps

| DevOps | DevSecOps |
|---|---|
| Development + Operations | Development + Security + Operations |
| Focus on speed, reliability, automation | Speed + reliability + security |
| Security may be handled separately | Security is integrated |
| Testing often focuses on functionality | Functional + security testing |
| Security may come late | Security starts early |
| Operations owns many runtime concerns | Security responsibility is shared |

DevSecOps is not about slowing DevOps down.

The objective is to make security checks repeatable, automated and integrated so secure delivery can scale.

---

## 26. Key Principles to Remember

1. **Shift Left** — Find security issues as early as practical.
2. **Automate** — If a security check can be automated reliably, integrate it into the workflow.
3. **Security as Code** — Version and review security policies/configurations.
4. **Least Privilege** — Give only the access that is required.
5. **Never Hardcode Secrets** — Use a proper secrets-management mechanism.
6. **Secure the Supply Chain** — Protect dependencies, builds and artifacts.
7. **Secure Containers** — Use minimal images, non-root users, scanning and strong runtime controls.
8. **Secure Kubernetes** — Protect IAM, RBAC, networking, workloads, images and secrets.
9. **Monitor Continuously** — Security does not stop after deployment.
10. **Shared Responsibility** — Developers, security engineers and operations engineers all contribute to security.

---

## 27. Practical DevSecOps Tool Map

| Area | Example Tools |
|---|---|
| Source Control | Git, GitHub, GitLab |
| Secret Scanning | Gitleaks, TruffleHog |
| Secrets Management | HashiCorp Vault, AWS Secrets Manager |
| SAST | Semgrep, SonarQube |
| SCA | OWASP Dependency-Check, Snyk |
| IaC Security | Checkov, tfsec/OpenTofu-compatible scanners |
| Container Scanning | Trivy |
| DAST | OWASP ZAP |
| CI/CD | GitHub Actions, GitLab CI, Jenkins |
| Containers | Docker |
| Orchestration | Kubernetes |
| Managed Kubernetes | Amazon EKS |
| IaC | Terraform |
| Policy | OPA / Gatekeeper, Kyverno |
| Monitoring | Prometheus, Grafana |
| Cloud | AWS |

> Tools are implementation choices. Do not memorize the tool list and call yourself DevSecOps. Understand what security problem each tool solves.

---

## 28. Example End-to-End DevSecOps Architecture

```text
Developer
    │
    ▼
Git Repository
    │
    ├── Pre-commit Secret Scan
    │
    ▼
Pull Request
    │
    ├── Code Review
    ├── SAST
    ├── SCA
    ├── Secret Scan
    └── IaC Scan
    │
    ▼
CI Pipeline
    │
    ├── Build
    ├── Test
    ├── Container Build
    └── Container Scan
    │
    ▼
Artifact / Container Registry
    │
    ├── Signing
    └── Verification
    │
    ▼
Kubernetes / EKS
    │
    ├── IAM
    ├── RBAC
    ├── Network Policies
    ├── Secrets
    └── Pod Security
    │
    ▼
Production
    │
    ├── Logs
    ├── Metrics
    ├── Security Monitoring
    └── Vulnerability Management
    │
    └──────────────► Feedback ► Code / Infrastructure / Pipeline
```

---

## 29. Interview Questions to Prepare

**Fundamentals**

- What is DevSecOps?
- Why is DevSecOps needed?
- What is Shift Left?
- How is DevSecOps different from DevOps?
- What is Security as Code?
- What is Threat Modeling?

**Git & Secrets**

- Why should secrets never be committed to Git?
- What are pre-commit hooks?
- What happens if a secret is committed?
- How would you prevent secrets from reaching Git?

**CI/CD**

- Where would you place SAST?
- What is SCA?
- What is DAST?
- How do you secure a CI/CD pipeline?
- How do you prevent insecure artifacts from being deployed?

**Containers**

- Why should containers not run as root?
- What is a multi-stage build?
- What are distroless images?
- How do you scan a Docker image?
- How do you reduce container attack surface?

**Kubernetes / Cloud**

- How do you secure Kubernetes?
- What is RBAC?
- What is least privilege?
- How does IAM fit into DevSecOps?
- How would you secure an EKS environment?

---

## 30. My DevSecOps Learning Roadmap

```text
01. DevSecOps Fundamentals
        ↓
02. Linux & Networking Security Basics
        ↓
03. Git Security & Secret Management
        ↓
04. Secure Coding
        ↓
05. Threat Modeling
        ↓
06. SAST / SCA / DAST
        ↓
07. Docker Security
        ↓
08. Kubernetes Security
        ↓
09. AWS Security / IAM
        ↓
10. IaC Security
        ↓
11. CI/CD Pipeline Security
        ↓
12. Supply Chain Security
        ↓
13. SBOM / Signing / Provenance
        ↓
14. Monitoring & Incident Response
        ↓
15. End-to-End DevSecOps Projects
```

---

## 31. Practical Projects I Want to Build

- [ ] Secure Git workflow with pre-commit secret scanning
- [ ] Terraform security scanning pipeline
- [ ] Secure Dockerfile + image scanning
- [ ] Multi-stage / non-root container project
- [ ] SAST + SCA GitHub Actions pipeline
- [ ] DAST with OWASP ZAP
- [ ] Secure Kubernetes deployment
- [ ] Kubernetes RBAC + NetworkPolicy lab
- [ ] AWS IAM least-privilege lab
- [ ] EKS security lab
- [ ] Secrets management with Vault
- [ ] SBOM generation and vulnerability scanning
- [ ] Artifact signing and verification
- [ ] Complete end-to-end DevSecOps pipeline

---

## 32. My Golden Rules

> Security is not a final step.
> Security is part of every step.

- Never hardcode secrets.
- Never assume internal traffic is automatically trusted.
- Never give more privileges than required.
- Never blindly trust dependencies or base images.
- Never treat a successful deployment as the end of security.
- Automate what can be automated.
- Detect early.
- Fix at the source.
- Monitor continuously.

---

## 📚 References

- NIST Secure Software Development Framework (SSDF)
- NIST DevSecOps Practices
- NIST SP 800-204D — Software Supply Chain Security in DevSecOps CI/CD Pipelines
- OWASP DevSecOps Guideline
- OWASP Top 10
- CIS Benchmarks
- SLSA

---

## 🚀 Repository Philosophy

> **Learn → Implement → Break → Secure → Automate → Document → Repeat**

This repository is not meant to be a collection of copied definitions.

Every major topic should eventually contain:

```text
Concept
   ↓
Why it matters
   ↓
How it works
   ↓
Security risks
   ↓
Best practices
   ↓
Tools
   ↓
Hands-on lab
   ↓
Commands / Configuration
   ↓
Real-world example
   ↓
Interview questions
```

The goal is not to know DevSecOps terminology.
The goal is to be able to secure a real delivery pipeline.
