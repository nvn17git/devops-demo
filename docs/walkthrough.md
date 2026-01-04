# DevOps Workflow Walkthrough

This demo showcases a full DevOps lifecycle: **Infrastructure as Code (IaC)**, **Containerization**, and **Automated CI/CD**.

## Phase 1: Infrastructure Provisioning (Terraform)
We use Terraform to define our AWS resources. This process is now fully automated via GitHub Actions, but it requires a one-time manual setup for state persistence.

1. **S3 Backend Setup**: Create an S3 bucket in AWS (e.g., `my-terraform-state`) and update the `bucket` name in `terraform/provider.tf`.
2. **First Run**: Since you are using OIDC to create the OIDC provider itself, you might need to run `terraform apply` **locally once** with your admin credentials to bootstrap the IAM Role and Provider.
3. **Automated Flow**: Once bootstrapped, any push to the `terraform/` directory will trigger the [terraform.yml](file:///Users/naveensharma/brandlive/demo-devops/.github/workflows/terraform.yml) workflow.

> [!TIP]
> This automation ensures that your infrastructure is always in sync with your code without manual CLI interventions.

## Phase 2: GitHub Actions Configuration
To automate the deployment securely, you need to set up the following secrets in your GitHub repository:

| Secret Name | Description |
| :--- | :--- |
| `AWS_ACCOUNT_ID` | Your 12-digit AWS Account ID |
| `EC2_HOST` | The public IP of your EC2 instance (from Terraform) |
| `EC2_SSH_KEY` | Your private SSH key (PEM) to access the instance |

> [!IMPORTANT]
> We are using **OIDC**, so you **do not** need to store `AWS_ACCESS_KEY_ID` or `AWS_SECRET_ACCESS_KEY` in GitHub.

## Phase 3: The CI/CD Pipeline
The workflow in `.github/workflows/deploy.yml` triggers on every push to the `main` branch:

1. **Build**: It builds the Docker image from `/app` and pushes it to Amazon ECR.
2. **Deploy**: It SSHs into the EC2 instance, pulls the latest image from ECR, and restarts the container.

## Phase 4: Verification
Once the GitHub Action completes successfully:
1. Open your browser.
2. Navigate to `http://<EC2_PUBLIC_IP>`.
3. You should see the "DevOps Workflow Demo" app running!

---

## Project Structure
- [terraform/](file:///Users/naveensharma/brandlive/demo-devops/terraform/): Infrastructure as Code files.
- [app/](file:///Users/naveensharma/brandlive/demo-devops/app/): Node.js source code and Dockerfile.
- [.github/workflows/](file:///Users/naveensharma/brandlive/demo-devops/.github/workflows/): CI/CD pipeline definition.
