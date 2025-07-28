# Infrastructure Deployment with Multi-Environment
## Environments

- **dev** – used for development and automated testing (includes Terratest after apply).
- **prod** – production environment.

Each environment has its own `*.tfvars` file:
- `dev.tfvars`
- `prod.tfvars`

## Workflows

There are three main GitHub Actions workflows:

1. **CI (Validation)** – Validates Terraform code and runs plan for `feature` changes.  
   File: `.github/workflows/ci.yaml`

2. **CD Apply** – Applies infrastructure for the selected environment.  
   File: `.github/workflows/cd-apply.yaml`

3. **CD Destroy** – Destroys infrastructure for the selected environment.  
   File: `.github/workflows/cd-destroy.yaml`

## How to Deploy a New Environment

1. Ensure the required `*.tfvars` file exists for the new environment (e.g. `dev.tfvars`, `prod.tfvars`).
2. Push your Terraform changes to the `main` branch.
3. Go to **Actions** → select **CD apply infra** workflow → click **Run workflow**.
4. Choose the desired environment (`dev` or `prod`) and run the workflow.
5. The workflow will:
    - Initialize Terraform
    - Apply infrastructure with `terraform apply -var-file="<env>.tfvars"`
    - Run Terratest (only for `dev`)

## How to Destroy an Environment

1. Go to **Actions** → select **CD destroy infra** workflow → click **Run workflow**.
2. Choose the desired environment (`dev` or `prod`) and run the workflow.
3. The workflow will:
    - Initialize Terraform
    - Destroy infrastructure with `terraform destroy -var-file="<env>.tfvars"`

## Adding a New Environment

1. Create a new `*.tfvars` file with all required variables.
2. Update the `options` section in the `workflow_dispatch` inputs of both `cd-apply.yaml` and `cd-destroy.yaml` to include the new environment.
3. Commit changes to `main`.

## Requirements

- AWS credentials configured in repository secrets:
    - `AWS_ACCESS_KEY_ID`
    - `AWS_SECRET_ACCESS_KEY`
