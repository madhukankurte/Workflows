# 🚀 GitHub Actions CI/CD Pipeline Library

A centralized collection of reusable **GitHub Actions workflows** for Continuous Integration (CI), Continuous Delivery (CD), and DevOps automation.

This repository serves as a reference library for building, maintaining, and reusing CI/CD pipelines across multiple applications, environments, and infrastructure platforms.

The goal is to standardize pipeline configurations, reduce duplication, improve deployment reliability, and follow DevOps best practices.

## 📌 Repository Objectives

- 📦 Maintain reusable GitHub Actions workflows.
- 🔄 Automate Continuous Integration and Continuous Delivery.
- 🧪 Automate build, test, validation, and quality checks.
- 🐳 Build, tag, and push Docker images to container registries.
- ☸️ Automate application deployments to Kubernetes environments.
- ☁️ Support cloud-based deployments and infrastructure automation.
- 🏗️ Automate Terraform formatting, validation, planning, and deployment workflows.
- 🔐 Follow security best practices for secrets, credentials, and permissions.
- 🌍 Support multiple environments, including Development, QA, UAT, Staging, and Production.
- 📚 Provide standardized pipeline examples that can be adapted for different projects.

## 🗂️ Repository Structure

The repository will be organized by technology and pipeline use case as new workflows are added.

```text
github-actions-cicd/
│
├── README.md
│
├── workflows/
│   ├── ci/
│   │   ├── nodejs-ci.yml
│   │   ├── python-ci.yml
│   │   └── docker-build.yml
│   │
│   ├── cd/
│   │   ├── kubernetes-deploy.yml
│   │   └── docker-deploy.yml
│   │
│   ├── terraform/
│   │   ├── terraform-validate.yml
│   │   └── terraform-plan-apply.yml
│   │
│   └── security/
│       └── dependency-scan.yml
│
├── examples/
│   ├── application-deployment/
│   └── infrastructure-deployment/
│
└── docs/
    ├── setup.md
    └── troubleshooting.md
```

*Note: This is a suggested structure. GitHub only automatically discovers workflows placed in `.github/workflows/` within a repository. The folders above are for organizing reusable workflow files, examples, and documentation; workflows must be placed in the appropriate location to run automatically.*

## 🛠️ Technologies Covered

This repository can include CI/CD implementations for the following technologies:

| Category | Technologies |
|---|---|
| CI/CD | GitHub Actions |
| Version Control | Git, GitHub |
| Containers | Docker |
| Container Orchestration | Kubernetes |
| Infrastructure as Code | Terraform |
| Cloud Platforms | AWS, Microsoft Azure, Google Cloud Platform (GCP) |
| Container Registries | Amazon ECR, Azure Container Registry, Google Artifact Registry, Docker Hub |
| Deployment | Kubernetes, Helm |
| Scripting | Bash, Shell |
| Security | Dependency Scanning, Secret Management, Workflow Permissions |
| Monitoring | Prometheus, Grafana |

The technologies listed above represent the intended scope of the repository. Individual implementations will be added as they are developed and tested.

## 🔄 CI/CD Pipeline Categories

### 1. Continuous Integration (CI)

CI workflows automate application validation whenever code changes are pushed or pull requests are opened.

Typical stages include:

- Source code checkout
- Dependency installation
- Code linting and formatting
- Unit and integration testing
- Static code analysis
- Application build
- Docker image build
- Artifact generation

### 2. Continuous Delivery and Deployment (CD)

CD workflows automate the delivery of validated application builds to target environments.

Typical stages include:

- Downloading build artifacts or building container images
- Authenticating with the target platform
- Pushing images to a container registry
- Deploying applications
- Updating Kubernetes manifests or Helm releases
- Running deployment verification
- Supporting rollback procedures where configured

Production deployments should use appropriate approval gates, environment protection rules, and restricted deployment permissions.

### 3. Infrastructure Automation

Infrastructure workflows can automate Terraform operations, including:

- `terraform fmt`
- `terraform init`
- `terraform validate`
- `terraform plan`
- `terraform apply`

Production infrastructure changes should be reviewed and approved before execution. State management, cloud authentication, and environment-specific configuration must be handled securely.

## ⚙️ How to Use This Repository

### Step 1: Browse the Workflows

Explore the available pipeline files and identify the workflow that matches your application, infrastructure, or deployment requirements.

### Step 2: Configure Repository Variables and Secrets

Configure the required variables, credentials, and environment-specific settings in your GitHub repository.

Navigate to:

**Repository → Settings → Secrets and variables → Actions**

Use:
- **Secrets** for sensitive values.
- **Variables** for non-sensitive configuration.
- **GitHub Environments** for environment-specific settings and deployment protection.

Where possible, prefer OpenID Connect (OIDC) or workload identity federation over long-lived cloud credentials.

Never commit passwords, access tokens, private keys, or other sensitive information to the repository.

### Step 3: Customize the Workflow

Update the workflow according to your requirements:

- Application language and build commands
- Branch and tag triggers
- Runtime and dependency versions
- Container image names and tags
- Container registry configuration
- Cloud provider and authentication method
- Deployment target and namespace
- Environment-specific variables

### Step 4: Configure Workflow Triggers

GitHub Actions workflows can be triggered by events such as:

```yaml
on:
  push:
    branches:
      - main

  pull_request:
    branches:
      - main

  workflow_dispatch:
```

These triggers allow workflows to run on pushes, pull requests, or manual execution. Adjust them according to your release strategy.

### Step 5: Execute and Validate

1. Commit the workflow to the appropriate repository location.
2. Push the changes to GitHub.
3. Open the **Actions** tab.
4. Review workflow execution and logs.
5. Validate the build, tests, and deployment results.
6. Fix any issues before promoting changes to higher environments.

## ♻️ Reusable Workflows

Where appropriate, workflows can be designed for reuse across multiple repositories using GitHub's `workflow_call` trigger.

Example:

```yaml
name: Reusable CI Workflow

on:
  workflow_call:
    inputs:
      node-version:
        description: "Node.js version"
        required: false
        type: string
        default: "22"

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout Repository
        uses: actions/checkout@v4

      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: ${{ inputs.node-version }}

      - name: Install Dependencies
        run: npm ci

      - name: Build Application
        run: npm run build
```

*This is an illustrative example. Update action versions and runtime versions according to your organization's supported versions and security requirements before using it in production.*

A consuming repository can call the reusable workflow from its own workflow file.

```yaml
name: Application CI

on:
  push:
    branches:
      - main

jobs:
  ci:
    uses: YOUR_GITHUB_USERNAME/github-actions-cicd/.github/workflows/nodejs-ci.yml@main
    with:
      node-version: "22"
```

Replace `YOUR_GITHUB_USERNAME` with your GitHub username and adjust the repository path to match the actual location of the reusable workflow.

For production use, prefer a reviewed, immutable commit SHA instead of a mutable branch reference.

## 🔐 Security Best Practices

Security is an important part of every CI/CD pipeline.

- Never hardcode secrets or credentials in workflow files.
- Use GitHub Actions secrets or a supported federated authentication mechanism.
- Prefer OIDC for cloud authentication when supported.
- Apply the principle of least privilege to `GITHUB_TOKEN` permissions.
- Pin third-party actions to verified commit SHAs for production workflows.
- Use protected branches and pull request reviews.
- Configure GitHub Environments for production deployment approvals.
- Avoid printing secrets or sensitive environment variables in logs.
- Validate external inputs before using them in shell commands.
- Scan application dependencies and container images where appropriate.
- Keep actions, runners, and dependencies updated.

## 🌍 Multi-Environment Deployment Strategy

The workflows can be adapted to support multiple deployment environments.

| Environment | Purpose | Deployment Approach |
|---|---|---|
| Development | Initial integration and testing | Automated |
| QA | Functional and integration testing | Automated after CI validation |
| UAT | Business acceptance testing | Controlled deployment |
| Staging | Pre-production validation | Controlled deployment |
| Production | Live application deployment | Approval-gated deployment |

The actual deployment sequence and approval requirements should be defined by each project's release strategy.

## 📋 Workflow Documentation Standards

Each workflow should document the following information:

- **Workflow Name:** Name of the pipeline.
- **Purpose:** What the workflow automates.
- **Trigger:** Events that start the workflow.
- **Prerequisites:** Required tools, permissions, secrets, and variables.
- **Inputs:** Configurable parameters.
- **Execution Steps:** Build, test, validation, and deployment stages.
- **Outputs:** Generated artifacts or deployment results.
- **Security Considerations:** Permissions and credential requirements.
- **Rollback Strategy:** Recovery procedure, if applicable.
- **Usage Example:** Instructions for integrating the workflow into another repository.

## 🤝 Contributions

Contributions and improvements are welcome.

When adding a new workflow:

1. Follow the existing directory structure.
2. Use descriptive workflow and job names.
3. Document required inputs, secrets, and variables.
4. Follow security best practices.
5. Validate YAML syntax and workflow configuration.
6. Test the workflow in a non-production environment.
7. Update the README or relevant documentation.

## 📈 Future Enhancements

Planned areas for expansion include:

- Reusable CI/CD workflows
- Docker build and publishing automation
- Kubernetes and Helm deployments
- Terraform automation
- AWS, Azure, and GCP deployment workflows
- Multi-environment release pipelines
- Security and dependency scanning
- Automated rollback and deployment verification
- Release tagging and version management
- Pipeline optimization and caching
- CI/CD monitoring and notifications

## 📄 License

Unless otherwise specified, the licensing terms for this repository have not yet been defined. Add a `LICENSE` file if you intend to distribute the workflows for public or third-party use.

---

**Maintained as a centralized library of GitHub Actions workflows for CI/CD automation and DevOps best practices.**