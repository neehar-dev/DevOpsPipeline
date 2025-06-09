# DevOpsPipeline

This repository provides a reusable GitHub Actions workflow for common CI/CD tasks. It supports infrastructure provisioning with Terraform or Ansible, building applications in popular languages with linting and tests, running security scans with CodeQL and Trivy, and deploying through staged environments with an approval gate for production.

## Workflow Overview

The workflow resides at `.github/workflows/centralized-ci-cd.yml` and is triggered by `workflow_call`. It exposes the following components through the `component` input:

- **infra** – Provision infrastructure using Terraform and/or Ansible.
- **build** – Build applications for **.NET**, **Java**, **Python**, or **Node.js**.
- **scan** – Perform static and vulnerability scans.
- **deploy** – Deploy to a staging environment, run tests, then wait for approval before deploying to production.

Optional inputs include:

- `language` – Programming language for the build component.
- `terraform_version` – Version of Terraform to install.
- `terraform_dir` – Path to your Terraform configuration.

## Using the Centralized Pipeline

Reference this workflow from any repository that needs CI/CD automation. Replace `ORG/DevOpsPipeline` with the correct organization and repository name.

### Provision Infrastructure Example
```yaml
jobs:
  provision:
    uses: ORG/DevOpsPipeline/.github/workflows/centralized-ci-cd.yml@main
    with:
      component: infra
      terraform_version: 1.6.3
      terraform_dir: infra
```

### Build Application Example
```yaml
jobs:
  build:
    uses: ORG/DevOpsPipeline/.github/workflows/centralized-ci-cd.yml@main
    with:
      component: build
      language: python
```
The build component automatically runs linting and unit tests for the selected language.

### Security Scan Example
```yaml
jobs:
  scan:
    uses: ORG/DevOpsPipeline/.github/workflows/centralized-ci-cd.yml@main
    with:
      component: scan
      language: python
```

### Deploy from Stage to Production Example
```yaml
jobs:
  deploy:
    uses: ORG/DevOpsPipeline/.github/workflows/centralized-ci-cd.yml@main
    with:
      component: deploy
```
The deploy component automatically pushes to the staging environment, runs tests, and then pauses for approval before deploying to production. Configure the `production` environment in your repository with the required reviewers to enable this gate.

## Notes
- Store any required secrets (cloud provider credentials, etc.) in the repository that calls this workflow.
- You can fork this repository and modify the workflow to fit your specific needs.
- CodeQL and Trivy require network access to download databases the first time they run.
