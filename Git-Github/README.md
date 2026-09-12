# 🔐 Security in Git & GitHub

> **Your repository is part of your security boundary.**
>
> Git security is not only about protecting code. It is about preventing secret leaks, controlling access, enforcing reviews, securing dependencies, and protecting the path from a developer's machine to production.

## 📚 Contents

1. `.gitignore`
2. Native Git Pre-Commit Hooks
3. Block Commits with Gitleaks
4. Gitleaks — Repository & History Scanning
5. Gitleaks in GitHub Actions
6. Branch Protection Rules
7. RBAC — Least Privilege
8. Mandatory Reviews
9. CODEOWNERS
10. Dependabot
11. Secure Git & GitHub Architecture
12. Key Takeaways

---

# 1. `.gitignore` — First Line of Defense

`.gitignore` tells Git which files and directories should not be tracked.

It helps prevent accidental commits of:

- Environment files
- Credentials
- Private keys
- Local configuration
- Build artifacts
- IDE/OS files
- Dependency directories

### Common entries

```gitignore
# Environment / secrets
.env
.env.*
*.pem
*.key
id_rsa

# Terraform
terraform.tfstate
terraform.tfstate.*
.terraform/

# Dependencies / build output
node_modules/
dist/

# IDE / OS
.vscode/
.idea/
.DS_Store
```

### Demo

```bash
echo "AWS_SECRET_ACCESS_KEY=123" > .env
git status
```

Add it to `.gitignore`:

```bash
echo ".env" >> .gitignore
git status
```

### ⚠️ Important

`.gitignore` does **not** remove a file that is already tracked.

If a real secret was committed:

```text
Secret committed
      ↓
Adding it to .gitignore
      ↓
Does NOT erase Git history
```

Treat exposed credentials as compromised and rotate/revoke them.

> **`.gitignore` prevents accidental tracking; it is not a secret scanner.**

---

# 2. Native Git Pre-Commit Hooks

A **pre-commit hook** is a local script executed automatically before Git creates a commit.

Location:

```text
.git/hooks/pre-commit
```

### Flow

```text
git commit
     ↓
pre-commit hook
     ↓
Security / Quality checks
     ↓
 ┌───────────────┐
 PASS            FAIL
 ↓               ↓
Commit        Commit blocked
```

### Exit Codes

| Exit Code | Result |
|---|---|
| `0` | Commit allowed |
| Non-zero | Commit blocked |

### Demo

```bash
cat > .git/hooks/pre-commit << 'EOF'
#!/bin/bash

echo "🔍 Running native pre-commit hook..."

if git diff --cached | grep -Ei "secret|password|api_key"; then
  echo "❌ Potential secret detected. Commit blocked."
  exit 1
fi

echo "✅ Commit passed security checks."
exit 0
EOF
```

Make it executable:

```bash
chmod +x .git/hooks/pre-commit
```

Test:

```bash
echo "my_secret=123" > test.txt
git add test.txt
git commit -m "test commit"
```

### Limitation

Native hooks are **local**. They are useful for fast feedback but should not be the only security control because they can be bypassed, removed, or never installed.

---

# 3. Block Commits with Gitleaks

**Gitleaks** is a secret-detection tool for Git repositories.

It can detect potential:

- API keys
- Tokens
- Passwords
- Private keys
- Cloud credentials

Instead of relying only on a simple custom script, Gitleaks provides purpose-built secret detection.

### Pre-Commit integration

Create:

```text
.pre-commit-config.yaml
```

Example:

```yaml
repos:
  - repo: https://github.com/gitleaks/gitleaks
    rev: v8.24.2
    hooks:
      - id: gitleaks
```

Install:

```bash
pre-commit install
```

Update configured repositories when needed:

```bash
pre-commit autoupdate
```

### Demo

```bash
echo "AWS_SECRET_ACCESS_KEY=AKIA123456789" > secrets.env
git add secrets.env
git commit -m "adding secrets"
```

Expected result:

```text
Secret detected
→ Commit blocked
```

### Flow

```text
Developer
    ↓
git commit
    ↓
Pre-Commit Hook
    ↓
Gitleaks
    ↓
Secret?
 ┌──┴──┐
No     Yes
 ↓      ↓
Commit  Block
```

---

# 4. Gitleaks — Repository & History Scanning

Blocking new secrets is only one part of repository security.

Secrets may already exist in:

- Current files
- Previous commits
- Old branches
- Git history

### Custom rules

Gitleaks supports custom rules for organization-specific patterns.

Example:

```toml
[[rules]]
id = "generic-password"
description = "Detect password assignments"
regex = '''(?i)password\s*=\s*["'][^"']+["']'''
tags = ["password", "custom"]
```

Save as:

```text
custom-rules.toml
```

Run:

```bash
gitleaks detect --config custom-rules.toml
```

### Why history matters

```text
Commit 1 → Application
Commit 2 → Secret accidentally committed
Commit 3 → Secret removed
```

The current file may look clean, but the secret can still exist in history.

```text
Current Code
     +
Git History
     ↓
Security Scan
```

> **If a real credential is discovered in Git, rotate/revoke it. Do not rely only on deleting the line from the latest version.**

---

# 5. Gitleaks in GitHub Actions

Local hooks can be bypassed.

GitHub Actions adds a centralized CI security layer.

### Flow

```text
Developer
    ↓
Push / Pull Request
    ↓
GitHub Actions
    ↓
Gitleaks
    ↓
 ┌───────┴───────┐
 PASS            FAIL
 ↓               ↓
Continue       Workflow fails
```

Example:

```yaml
name: Gitleaks

on:
  push:
  pull_request:
  workflow_dispatch:

jobs:
  scan:
    name: Gitleaks
    runs-on: ubuntu-latest

    steps:
      - name: Checkout
        uses: actions/checkout@v4
        with:
          fetch-depth: 0

      - name: Run Gitleaks
        uses: gitleaks/gitleaks-action@v2
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
```

`fetch-depth: 0` makes the full repository history available to the workflow.

> Always verify action versions and configuration against the current official documentation before using them in production.

### Defense in depth

```text
Local Pre-Commit
       +
GitHub Actions
       +
Repository Controls
```

---

# 6. Branch Protection Rules

Important branches such as `main` should not be changed without appropriate controls.

### Common controls

- No direct pushes to `main`
- Require pull requests
- Require approvals
- Require status checks
- Prevent force pushes
- Require branches to be up to date where appropriate

### Secure workflow

```text
Developer
    ↓
Feature Branch
    ↓
Pull Request
    ↓
Automated Checks
    ↓
Required Review
    ↓
Merge
    ↓
Protected Main
```

Without protection:

```text
Developer → Direct Push → main
```

With protection:

```text
Developer → PR → Checks → Review → main
```

---

# 7. RBAC — Least Privilege

**RBAC = Role-Based Access Control**

Access is assigned according to a user's role and responsibilities.

The security principle is:

> **Give the minimum access required to perform the job.**

Example:

| Role | Typical Access |
|---|---|
| Admin | Repository settings + administrative operations |
| Maintainer | Manage repository and merge changes |
| Developer | Contribute code and create PRs |
| Auditor | Read-only / review access |

The exact permissions depend on the platform and organization.

### Why least privilege?

```text
Compromised Account
       ↓
Administrator Access
       ↓
Large Blast Radius
```

versus:

```text
Compromised Account
       ↓
Limited Permissions
       ↓
Reduced Blast Radius
```

---

# 8. Mandatory Reviews

Pull request reviews are both a collaboration mechanism and a security control.

### Good practices

- Require at least one appropriate reviewer
- Use additional reviewers for sensitive changes
- Require security review for high-risk areas
- Protect authentication, infrastructure and CI/CD changes
- Combine human review with automated checks

### Flow

```text
Pull Request
     ↓
Automated Checks
     ↓
Human Review
     ↓
Approval?
 ┌───┴───┐
No      Yes
 ↓       ↓
Changes  Merge
```

### Human + Automation

```text
Automation
→ Fast and repeatable

Human Review
→ Context and judgment
```

---

# 9. CODEOWNERS

`CODEOWNERS` defines which users or teams should review changes to particular files or directories.

Typical location:

```text
.github/CODEOWNERS
```

Example:

```text
/.github/       @security-team
/terraform/     @cloud-team
/src/           @backend-team
```

### Flow

```text
Developer
    ↓
Pull Request
    ↓
Changed Files
    ↓
CODEOWNERS
    ↓
Relevant Reviewers
```

This is especially useful for sensitive areas such as:

- Infrastructure
- Authentication
- Security configuration
- CI/CD workflows

---

# 10. Dependabot

Modern applications depend on third-party packages and libraries.

Dependencies can become:

- Outdated
- Vulnerable
- Unsupported

Dependabot helps automate dependency maintenance by checking supported ecosystems and creating update pull requests.

### Example

```yaml
version: 2

updates:
  - package-ecosystem: "npm"
    directory: "/"
    schedule:
      interval: "weekly"
```

### Workflow

```text
Repository
    ↓
Dependencies
    ↓
Dependabot
    ↓
Update Available
    ↓
Pull Request
    ↓
Review + CI Checks
    ↓
Merge
```

> **Automated update ≠ automatically safe update.**

Updates should still pass appropriate testing, security checks and review.

---

# 11. Secure Git & GitHub Architecture

These controls are most effective when they work together.

```text
                         ┌──────────────────┐
                         │    DEVELOPER     │
                         └────────┬─────────┘
                                  │
                            git commit
                                  │
                                  ▼
                      ┌───────────────────────┐
                      │   PRE-COMMIT HOOK     │
                      │                       │
                      │   Gitleaks            │
                      │   Custom Checks       │
                      └──────────┬────────────┘
                                 │
                           Secret found?
                           ┌─────┴─────┐
                          YES           NO
                           │             │
                         BLOCK         COMMIT
                                         │
                                         ▼
                                      PUSH / PR
                                         │
                                         ▼
              ┌─────────────────────────────────────────┐
              │              GITHUB REPOSITORY           │
              │                                         │
              │  .gitignore                             │
              │  RBAC                                   │
              │  Branch Protection                       │
              │  CODEOWNERS                              │
              └───────────────────┬─────────────────────┘
                                  │
                                  ▼
                       ┌──────────────────────┐
                       │    GITHUB ACTIONS    │
                       │                      │
                       │    Gitleaks          │
                       │    Tests / Checks    │
                       └──────────┬───────────┘
                                  │
                            Checks pass?
                            ┌─────┴─────┐
                           NO           YES
                           │             │
                         BLOCK          REVIEW
                                         │
                              ┌──────────┴──────────┐
                              │                     │
                         CODEOWNERS          Mandatory Review
                              │                     │
                              └──────────┬──────────┘
                                         │
                                      APPROVED
                                         │
                                         ▼
                                  PROTECTED MAIN
                                         │
                                         ▼
                                    DEPLOYMENT


             ┌─────────────────────────────────────┐
             │             DEPENDABOT               │
             │                                     │
             │ Dependency → PR → CI → Review       │
             └─────────────────────────────────────┘
```

## Defense in Depth

```text
.gitignore
   ↓
Pre-Commit Hook
   ↓
Gitleaks
   ↓
GitHub Actions
   ↓
Branch Protection
   ↓
RBAC
   ↓
Mandatory Reviews
   ↓
CODEOWNERS
   ↓
Dependabot
```

If one control is bypassed, another can still reduce the risk.

---

# 12. Key Takeaways

| Control | Purpose |
|---|---|
| `.gitignore` | Prevent accidental tracking |
| Pre-Commit Hook | Catch problems before commit |
| Gitleaks | Detect exposed secrets |
| GitHub Actions | Centralize automated checks |
| Branch Protection | Protect important branches |
| RBAC | Control repository access |
| Mandatory Reviews | Add human verification |
| CODEOWNERS | Route sensitive changes to responsible reviewers |
| Dependabot | Maintain dependency security |

## 🧠 Bigger Picture

Git security is not one tool.

It is a chain of controls:

```text
Prevent
   ↓
Detect
   ↓
Block
   ↓
Review
   ↓
Control Access
   ↓
Maintain Dependencies
   ↓
Continuously Improve
```

> **Secure Repository → Safer Code → Safer Delivery**

## 🚀 Learning Takeaway

The biggest lesson from this section:

> **Security starts before code reaches production — and in many cases, before it even reaches the repository.**

A secure Git workflow combines **developer-side prevention, automated scanning, repository controls, access control, human review, and dependency management**.
