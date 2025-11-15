# GitHub Environment Secrets Migration Guide

**Date:** 2025-11-14
**Purpose:** Document the migration from terraform.tfvars to GitHub Environment secrets for SCIM server deployment

---

## Summary

The SCIM server deployment now supports **GitHub Actions workflows with environment secrets**, following the same GitOps pattern as the rest of this repository.

## What Changed

### Before (terraform.tfvars approach)
- Secrets stored in local `terraform.tfvars` file
- Manual `terraform apply` commands
- Risk of committing secrets to git
- No audit trail
- No environment protection

### After (GitHub Actions + Environment Secrets)
- Secrets stored securely in GitHub Environment
- Workflow-based deployment with approval gates
- Audit trail of all deployments
- AWS OIDC authentication (no long-lived credentials)
- Follows GitOps best practices

---

## GitHub Environment Secrets Required

Add these secrets to your GitHub Environment (e.g., `MyOrg`):

| Secret Name | Description | Example Value |
|-------------|-------------|---------------|
| `SCIM_AUTH_TOKEN` | Bearer token for SCIM authentication | `python3 -c 'import secrets; print(secrets.token_urlsafe(32))'` |
| `AWS_REGION` | AWS region | `us-east-1` |
| `AWS_ROLE_ARN` | AWS OIDC role ARN | `arn:aws:iam::123456789012:role/GitHubActions-OktaTerraform` |

### Setup Instructions

1. Navigate to **GitHub Repository → Settings → Environments**
2. Select your environment (e.g., `MyOrg`)
3. Click **Add secret**
4. Add each secret from the table above

---

## New Workflow

### File Created
`.github/workflows/deploy-scim-server.yml`

### Usage

```bash
# Plan deployment
gh workflow run deploy-scim-server.yml \
  -f environment=myorg \
  -f domain_name=scim.demo-myorg.example.com \
  -f route53_zone_id=Z1234567890ABC \
  -f instance_type=t3.micro \
  -f action=plan

# Apply deployment (after reviewing plan)
gh workflow run deploy-scim-server.yml \
  -f environment=myorg \
  -f domain_name=scim.demo-myorg.example.com \
  -f route53_zone_id=Z1234567890ABC \
  -f instance_type=t3.micro \
  -f action=apply

# Destroy infrastructure
gh workflow run deploy-scim-server.yml \
  -f environment=myorg \
  -f domain_name=scim.demo-myorg.example.com \
  -f route53_zone_id=Z1234567890ABC \
  -f action=destroy
```

### Workflow Features

**Inputs:**
- `environment` - Environment to deploy (matches directory name)
- `domain_name` - SCIM server FQDN
- `route53_zone_id` - Route53 hosted zone ID
- `instance_type` - EC2 instance type (default: t3.micro)
- `action` - Terraform action: plan, apply, or destroy

**Outputs:**
- Infrastructure outputs (URLs, IDs, etc.)
- Next-step instructions in workflow summary
- SCIM configuration commands for Python script

**Security:**
- AWS OIDC authentication (no access keys)
- Environment protection with approval gates
- Secrets never exposed in logs or outputs

---

## Documentation Updates

### Files Updated in This PR

1. **`.github/workflows/deploy-scim-server.yml`** (NEW)
   - Complete GitHub Actions workflow
   - Environment secrets integration
   - Terraform plan/apply/destroy support

2. **`environments/myorg/infrastructure/scim-server/README.md`**
   - Added "Deployment Options" section
   - Option A: GitHub Actions Workflow (Recommended)
   - Option B: Manual Terraform Deployment
   - GitHub Environment secrets setup

3. **`environments/myorg/infrastructure/scim-server/DEPLOYMENT_PLAN.md`**
   - Added "Deployment Approaches" section
   - GitHub Actions deployment workflow
   - Manual Terraform alternative

4. **`environments/myorg/infrastructure/scim-server/terraform.tfvars.example`**
   - Example template for manual deployments
   - Comments indicating GitHub Actions is preferred method

---

## Documentation Updates Still Needed

These files reference terraform.tfvars and should be updated to mention GitHub Actions as the preferred method:

### 1. `docs/SCIM_OKTA_AUTOMATION.md`
**Section to update:** Quick Start / Step-by-Step Guide
**Add:**
- GitHub Actions workflow option as primary method
- terraform.tfvars as alternative method
- Environment secrets setup instructions

**Location:** After "Prerequisites" section, before "Step-by-Step Guide"

### 2. `ai-assisted/prompts/deploy_scim_server.md`
**Section to update:** PART 1: SCIM Server Infrastructure
**Add:**
- GitHub Actions workflow deployment option
- Environment secrets configuration
- Workflow input parameters

**Location:** Lines 86-117 (Configuration section)

**Updated prompt should include:**
```
════════════════════════════════════════════════════════════════════════════
PART 1: SCIM Server Infrastructure (AWS)
════════════════════════════════════════════════════════════════════════════

Deployment Options:
- Option A: GitHub Actions Workflow (Recommended)
  - Uses environment secrets
  - No local terraform.tfvars
  - Workflow: .github/workflows/deploy-scim-server.yml

- Option B: Manual Terraform
  - Uses terraform.tfvars file
  - Direct terraform commands
  - For local development/testing

GitHub Environment Secrets (for Option A):
- SCIM_AUTH_TOKEN - [Generated token]
- AWS_REGION - us-east-1
- AWS_ROLE_ARN - [Already configured]

Workflow Deployment:
gh workflow run deploy-scim-server.yml \
  -f environment=myorg \
  -f domain_name=scim.demo-myorg.example.com \
  -f route53_zone_id=[Your Zone ID] \
  -f action=apply
```

### 3. `ai-assisted/context/repository_structure.md`
**Section to update:** SCIM Server Integration (Lines 218-263)
**Add:**
- Mention of deploy-scim-server.yml workflow
- GitHub Environment secrets requirement
- Workflow-based deployment as primary path

**Updated workflow section:**
```
**Complete Workflow (GitHub Actions):**
# Step 1: Add secrets to GitHub Environment
# Settings → Environments → MyOrg → Add secrets

# Step 2: Deploy SCIM server via workflow
gh workflow run deploy-scim-server.yml \
  -f environment=myorg \
  -f domain_name=scim.demo-myorg.example.com \
  -f route53_zone_id=Z... \
  -f action=apply

# Step 3: Create Okta SCIM app
cd environments/myorg/terraform
terraform apply

# Step 4: Configure SCIM connection (Python - API only)
python3 ../../scripts/configure_scim_app.py \
  --app-id $(terraform output -raw scim_app_id) \
  --scim-url $(terraform output -raw scim_server_url) \
  --scim-token <from-github-secret> \
  --test-connection
```

---

## Migration Path for Existing Deployments

If you already deployed using terraform.tfvars:

### Step 1: Extract Current Values
```bash
cd environments/myorg/infrastructure/scim-server
SCIM_TOKEN=$(terraform output -json okta_configuration | jq -r '.header_auth_token')
echo "SCIM_AUTH_TOKEN=$SCIM_TOKEN"
```

### Step 2: Add to GitHub Environment
1. Copy the token value
2. Go to GitHub → Settings → Environments → MyOrg
3. Add secret: `SCIM_AUTH_TOKEN` with the token value

### Step 3: Future Updates via Workflow
Use the GitHub Actions workflow for all future changes:
```bash
# Update SCIM server
gh workflow run deploy-scim-server.yml \
  -f environment=myorg \
  -f domain_name=scim.demo-entitlements-lowerdecks.com \
  -f route53_zone_id=Z0726191Q5GQ53YU4EOR \
  -f action=apply
```

### Step 4: Remove Local terraform.tfvars
```bash
# Backup first (optional)
cp terraform.tfvars terraform.tfvars.backup

# Remove from working directory
rm terraform.tfvars

# Verify it's in .gitignore
grep terraform.tfvars .gitignore
```

---

## Benefits of GitHub Actions Approach

✅ **Security:** Secrets never stored in local files
✅ **Audit Trail:** All deployments logged in GitHub Actions
✅ **Environment Protection:** Approval gates for production
✅ **Team Collaboration:** No need to share secrets
✅ **GitOps Workflow:** Matches Okta Terraform pattern
✅ **AWS OIDC:** No long-lived AWS credentials
✅ **Automated Instructions:** Workflow provides next steps

---

## Testing the Workflow

### Test Plan Checklist

- [ ] Add SCIM_AUTH_TOKEN to GitHub Environment secrets
- [ ] Run workflow with action=plan
- [ ] Review plan output in workflow summary
- [ ] Run workflow with action=apply
- [ ] Verify infrastructure created (EC2, Elastic IP, Route53)
- [ ] Wait 5-10 minutes for server initialization
- [ ] Check health endpoint
- [ ] Create Okta SCIM app via Terraform
- [ ] Configure SCIM connection via Python script
- [ ] Test provisioning (assign user in Okta)
- [ ] Verify user appears in SCIM dashboard

---

## Rollback Plan

If the workflow has issues, you can still use manual Terraform:

```bash
cd environments/myorg/infrastructure/scim-server

# Create terraform.tfvars from example
cp terraform.tfvars.example terraform.tfvars

# Add your values
vim terraform.tfvars

# Deploy manually
terraform init
terraform plan
terraform apply
```

---

## Support

- **Workflow File:** `.github/workflows/deploy-scim-server.yml`
- **README:** `environments/myorg/infrastructure/scim-server/README.md`
- **Deployment Plan:** `environments/myorg/infrastructure/scim-server/DEPLOYMENT_PLAN.md`
- **Automation Guide:** `docs/SCIM_OKTA_AUTOMATION.md`

---

**Status:** ✅ GitHub Actions workflow implemented and documented
**Next:** Update AI-assisted prompt templates to reference workflow approach
