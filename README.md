# Contentstack CI Workflows

This repository contains reusable GitHub Actions workflows for Contentstack projects. These workflows can be easily integrated into any repository to automate CI/CD processes.

## How to Use Workflows in Your Repository

These workflows are designed to be used as **reusable workflows** using the `uses:` syntax. You don't need to copy the workflow files - simply reference them from this repository.

### Step 1: Create a Workflow File in Your Repository

Create a new workflow file in your repository at `.github/workflows/<your-workflow-name>.yml`:

```yaml
name: Code Quality Pipeline

on:
    push:
        branches: [main, master, feature/*]
    pull_request:
        branches: [main, master]
    workflow_dispatch:

jobs:
    security-scan:
        name: Security Scan
        uses: contentstack/contentstack-ci-workflows/.github/workflows/snyk-sca-scan.yml@main
        secrets: inherit
```

### Step 2: Configure Required Secrets

Go to your repository's **Settings → Secrets and variables → Actions** and add:

- **Required Secrets**: 
  - `SNYK_TOKEN`: Your Snyk authentication token


### Step 3: Reference the Workflow

Use the `uses:` syntax to reference the workflow from this repository:

```yaml
jobs:
    my-job:
        uses: contentstack/contentstack-ci-workflows/.github/workflows/<workflow-name>.yml@main
        secrets: inherit
```

### Example: Complete Workflow File

Here's a complete example of how to use the Snyk SCA scan workflow:

```yaml
name: Security and Quality Checks

on:
    push:
        branches: [main, master]
    pull_request:
        branches: [main, master]
    workflow_dispatch:

jobs:
    security-scan:
        name: Snyk Security Scan
        uses: contentstack/contentstack-ci-workflows/.github/workflows/snyk-sca-scan.yml@main
        secrets: inherit
```


## Available Workflows

### [Source Composition Analysis Scan](.github/workflows/snyk-sca-scan.yml)

A comprehensive security scanning workflow that uses Snyk to detect vulnerabilities in your project dependencies. This workflow:

- **Scans all projects** in your repository for open-source vulnerabilities
- **Enforces severity thresholds** for critical, high, medium, and low severity issues
- **Tracks SLA breaches** based on days since vulnerability publication
- **Detects wildcard/latest versions** in dependencies
- **Posts detailed results** as PR comments
- **Fails builds** when security thresholds are exceeded

**Prerequisites:**
- `package.json` (and preferably `package-lock.json`) must be present in the root directory of your repository for the wildcard version check to function properly

**Required Secrets:**
- `SNYK_TOKEN`: Your Snyk authentication token

**Optional Repository Variables:**
- `MAX_CRITICAL_ISSUES`: Maximum allowed critical issues (default: 1)
- `MAX_HIGH_ISSUES`: Maximum allowed high issues (default: 1)
- `MAX_MEDIUM_ISSUES`: Maximum allowed medium issues (default: 500)
- `MAX_LOW_ISSUES`: Maximum allowed low issues (default: 1000)
- `SLA_CRITICAL_WITH_FIX`: SLA threshold in days for critical issues with fixes (default: 15)
- `SLA_HIGH_WITH_FIX`: SLA threshold in days for high issues with fixes (default: 30)
- `SLA_MEDIUM_WITH_FIX`: SLA threshold in days for medium issues with fixes (default: 90)
- `SLA_LOW_WITH_FIX`: SLA threshold in days for low issues with fixes (default: 180)
- `SLA_CRITICAL_NO_FIX`: SLA threshold in days for critical issues without fixes (default: 30)
- `SLA_HIGH_NO_FIX`: SLA threshold in days for high issues without fixes (default: 120)
- `SLA_MEDIUM_NO_FIX`: SLA threshold in days for medium issues without fixes (default: 365)
- `SLA_LOW_NO_FIX`: SLA threshold in days for low issues without fixes (default: 365)

**Triggers:**
- Pull requests (opened, synchronize, reopened)
- Manual workflow dispatch

---
