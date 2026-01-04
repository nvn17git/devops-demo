# Simple DevOps Workflow Demo Implementation Plan

This plan outlines the creation of a standard DevOps pipeline to demonstrate infrastructure as code (Terraform), containerization (Docker/Node.js), and automated CI/CD (GitHub Actions).

## User Review Required

> [!IMPORTANT]
> This demo uses **IAM Roles for GitHub Actions via OIDC**. This is more secure than long-lived Access Keys. You will need to provide your AWS Account ID as a GitHub Secret (`AWS_ACCOUNT_ID`).

## Proposed Changes

### Infrastructure (Terraform)
We will create a `terraform` directory to manage AWS resources.

#### [MODIFY] [provider.tf](file:///Users/naveensharma/brandlive/demo-devops/terraform/provider.tf)
Added S3 backend configuration for state persistence.

#### [NEW] [oidc.tf](file:///Users/naveensharma/brandlive/demo-devops/terraform/oidc.tf)
Configures the AWS OIDC Identity Provider and the IAM Role that GitHub Actions will assume.

#### [NEW] [main.tf](file:///Users/naveensharma/brandlive/demo-devops/terraform/main.tf)
- ECR Repository for Docker images.
- VPC Data Source (using default VPC for simplicity).
- Security Group allowing ports 22 (SSH) and 80 (App).
- EC2 Instance (Ubuntu) with User Data to install Docker.

#### [NEW] [outputs.tf](file:///Users/naveensharma/brandlive/demo-devops/terraform/outputs.tf)
Outputs for ECR URL and EC2 Public IP.

---

### Application (Node.js)
A minimal Express.js application.

#### [NEW] [app.js](file:///Users/naveensharma/brandlive/demo-devops/app/app.js)
Simple "Hello World" style API.

#### [NEW] [package.json](file:///Users/naveensharma/brandlive/demo-devops/app/package.json)
Dependencies and scripts.

#### [NEW] [Dockerfile](file:///Users/naveensharma/brandlive/demo-devops/app/Dockerfile)
Container definition for the Node.js app.

---

### CI/CD (GitHub Actions)
Automated build and deploy.

#### [NEW] [deploy.yml](file:///Users/naveensharma/brandlive/demo-devops/.github/workflows/deploy.yml)
- **Build**: Docker build and push to ECR.
- **Deploy**: SSH into EC2, pull the new image, and restart the container.

## Verification Plan

### Automated Tests
- Syntax check for Terraform (`terraform validate`).
- Local Docker build test.

### Manual Verification
1. Run `terraform apply` to provision AWS infra.
2. Push code to GitHub to trigger the workflow.
3. Access the EC2 instance's public IP in a browser to see the Node.js app running.
