# 🔐 DevSecOps

> **DevSecOps = Development + Security + Operations**

DevSecOps is an approach that integrates **security throughout the DevOps lifecycle** instead of treating security as a final step before production.

The goal is simple:

> **Build fast, deliver continuously, and make security part of the process from the beginning.**

---

## 📖 Table of Contents

- [Why DevSecOps?](#-why-devsecops)
- [DevSecOps Mindset](#-devsecops-mindset)
- [Shift Left Security](#️-shift-left-security)
- [Security Fundamentals](#️-security-fundamentals)
- [Threat Modeling](#-threat-modeling)
- [OWASP](#-owasp)
- [Threat & Vulnerability Scanning](#-threat--vulnerability-scanning)
- [Security in the DevSecOps Lifecycle](#-security-in-the-devsecops-lifecycle)
- [Key Principles](#-key-principles)
- [DevSecOps Security Areas](#️-devsecops-security-areas)
- [Quick Revision](#-quick-revision)
- [Key Takeaways](#-key-takeaways)
- [Learning Journey](#-learning-journey)

---

## 📌 Why DevSecOps?

In a traditional approach, security may happen after the application or infrastructure has already been built.

```text
Development → Build → Test → Deploy → Security Check
```

If a vulnerability is discovered at the end, fixing it can require rework and may introduce production risk.

DevSecOps moves security into the development and delivery process:

```text
Plan → Code → Build → Test → Release → Deploy → Operate
  ↑      ↑      ↑       ↑       ↑        ↑        ↑
  └──────────── Security Throughout ──────────────┘
```

### Main objectives

- Detect security issues early
- Reduce security risk
- Automate security checks
- Reduce human error
- Secure infrastructure and applications
- Make security a shared responsibility
- Continuously monitor and improve security

---

## 🧠 DevSecOps Mindset

The biggest change is not a tool. It is a mindset.

Instead of:

> "Build everything first and secure it later."

Think:

> "How can we build this securely from the beginning?"

At every stage, ask:

- What can go wrong?
- What are we exposing?
- What is the attack surface?
- Who has access?
- What happens if this component is compromised?
- Can we detect the problem early?
- Can we automate the security check?

---

## ⬅️ Shift Left Security

Shift Left means moving security activities earlier in the Software Development Life Cycle (SDLC).

**Traditional approach**

```text
Code → Build → Deploy → Production → Security Finding
```

**Shift Left approach**

```text
Plan
 ↓
Threat Modeling
 ↓
Code
 ↓
Security Checks
 ↓
Build
 ↓
Test
 ↓
Deploy
```

The idea is:

> Find security problems as close as possible to the point where they are introduced.

**Examples**

Security activities that can be shifted left:

- Threat modeling
- Secure coding
- Secret scanning
- SAST
- Dependency scanning
- IaC scanning
- Container scanning

**Important**

Shift Left does not mean security ends before production.

Runtime security, monitoring and incident response are still required.

```text
Shift Left
     +
Runtime Security
     +
Continuous Monitoring
```

---

## 🛡️ Security Fundamentals

DevSecOps security is not limited to application code.

A modern application may depend on:

```text
Application
    ↓
Dependencies
    ↓
Container
    ↓
Kubernetes
    ↓
Cloud Infrastructure
    ↓
CI/CD Pipeline
```

Every layer can introduce security risks.

### Infrastructure Security

Infrastructure includes resources such as:

- Servers
- Networks
- VPCs
- Subnets
- Security Groups
- IAM
- Databases
- Load Balancers
- Kubernetes clusters
- Cloud resources

**Common infrastructure risks**

- Publicly exposed resources
- Open ports
- Weak access controls
- Excessive permissions
- Unencrypted data
- Insecure configurations
- Exposed credentials
- Missing logging and monitoring

**Security principle**

> Infrastructure should be secure by design, not secured only after deployment.

---

## 🎯 Threat Modeling

Threat modeling is a structured process for identifying potential security threats before they become real vulnerabilities or incidents.

**Basic process**

```text
Understand the System
        ↓
Identify Assets
        ↓
Identify Threats
        ↓
Analyze Risk
        ↓
Define Mitigations
        ↓
Validate
```

**Questions to ask**

- What are we protecting?
- Who can access it?
- What are the trust boundaries?
- What can an attacker do?
- Which components are exposed?
- What happens if one component is compromised?
- How can the risk be reduced?

**Example**

```text
User
  ↓
Load Balancer
  ↓
Application
  ↓
Database
```

Possible threats:

- Unauthorized access
- Injection attacks
- Credential exposure
- Database exposure
- Weak authentication
- Excessive application permissions

Threat modeling helps identify these risks before implementation or deployment.

---

## 🌐 OWASP

### What is OWASP?

OWASP (Open Worldwide Application Security Project) is a nonprofit organization focused on improving software and application security.

OWASP provides:

- Security guidance
- Security standards
- Testing methodologies
- Educational resources
- Security tools
- Risk awareness

One of its most well-known resources is the OWASP Top 10.

### OWASP Top 10

The OWASP Top 10 is a regularly updated awareness document covering major categories of web application security risks.

It helps developers and security professionals understand common application security problems.

Examples of common categories include:

- Broken Access Control
- Cryptographic Failures
- Injection
- Security Misconfiguration
- Identification and Authentication Failures
- Vulnerable and Outdated Components
- Security Logging and Monitoring Failures

> OWASP Top 10 is an awareness resource, not a complete security checklist.
>
> An application should not be considered secure simply because it has no OWASP Top 10 findings.

---

## 🔍 Threat & Vulnerability Scanning

Scanning is an important part of DevSecOps because manually checking every component is difficult and inconsistent.

### What is Vulnerability Scanning?

Vulnerability scanning attempts to identify known security weaknesses in systems, applications, dependencies, containers, or infrastructure.

Examples:

```text
Source Code
    ↓
SAST

Dependencies
    ↓
SCA

Infrastructure
    ↓
IaC Scan

Container Image
    ↓
Container Scan

Running Application
    ↓
DAST
```

### Threat Scanning

Threat scanning can be used to identify potential security threats, suspicious configurations, weaknesses, or attack opportunities depending on the scanning technology and environment.

The important concept is not simply:

> "Run a scanner."

It is:

```text
Scan
 ↓
Finding
 ↓
Risk Analysis
 ↓
Prioritize
 ↓
Remediate
 ↓
Rescan
```

---

## 🔄 Security in the DevSecOps Lifecycle

A simplified secure delivery lifecycle:

```text
PLAN
 ↓
Threat Modeling
 ↓
CODE
 ↓
Secret / Code Checks
 ↓
BUILD
 ↓
Dependency / Container Checks
 ↓
TEST
 ↓
Security Testing
 ↓
RELEASE
 ↓
Security Validation
 ↓
DEPLOY
 ↓
OPERATE
 ↓
MONITOR
 ↓
Feedback
 ↺
```

Security is therefore a continuous feedback loop.

---

## 🔑 Key Principles

**1. Shift Left**

Detect security issues early.

**2. Secure by Default**

Prefer secure configurations from the beginning.

**3. Least Privilege**

Give only the permissions that are actually required.

**4. Defense in Depth**

Do not depend on a single security control.

**5. Automation**

Automate repeatable security checks.

**6. Continuous Monitoring**

Security does not stop after deployment.

**7. Shared Responsibility**

Development, security and operations all contribute to security.

---

## 🛠️ DevSecOps Security Areas

As this learning journey progresses, I will cover:

- Git & Secret Security
- Secure Coding
- Dependency Security
- Infrastructure as Code Security
- Docker / Container Security
- Kubernetes Security
- CI/CD Security
- SAST
- SCA
- DAST
- Cloud Security
- Software Supply Chain Security
- SBOM
- Artifact Security
- Runtime Security

These are part of the roadmap and will be documented as I learn and implement them.

---

## 📚 Quick Revision

| Concept | Main Idea |
|---|---|
| DevSecOps | Integrate security into DevOps |
| Shift Left | Move security earlier |
| Threat Modeling | Identify threats before they become incidents |
| OWASP | Application security knowledge and guidance |
| OWASP Top 10 | Common web application security risks |
| Vulnerability Scanning | Identify security weaknesses |
| Infrastructure Security | Secure infrastructure and configurations |
| Least Privilege | Give minimum required access |
| Continuous Security | Security throughout the lifecycle |

---

## 🎯 Key Takeaways

- DevSecOps integrates security into the DevOps lifecycle.
- Security should not be treated as a final checkpoint.
- Shift Left helps detect problems earlier.
- Infrastructure itself can introduce significant security risks.
- Threat modeling helps identify risks before implementation.
- OWASP provides important application security guidance.
- Scanning helps discover vulnerabilities and misconfigurations.
- Security requires continuous monitoring and improvement.

> **Learn → Implement → Break → Secure → Document → Repeat.**

---

## 🚀 Learning Journey

This is the beginning of my DevSecOps learning journey.

I will continue adding practical knowledge, tools, labs and real-world implementation as I progress.
