# Staging Environment (Template)

This is a template directory for your staging Okta tenant. Follow the setup guide below to configure it for your organization.

## ⚠️ Setup Required

This directory contains template files only. Before using:

1. Configure GitHub Environment with secrets
2. Update Terraform provider configuration
3. Import resources from your Okta tenant

## Quick Setup Guide

### 1. Configure GitHub Environment

Go to **Settings > Environments** and create:

**Environment Name:** `Staging`

**Required Secrets:**
- `OKTA_API_TOKEN` - Staging Okta API token with governance scopes
- `OKTA_ORG_NAME` - Your Okta org name (e.g., `your-company-staging`)
- `OKTA_BASE_URL` - Base URL (e.g., `okta.com` or `oktapreview.com`)

**Recommended Protection Rules:**
- ✅ Required reviewers (1+ for staging)
- ⚠️ Optional: Wait timer for safety

### 2. Update Terraform Configuration

Edit `terraform/provider.tf`:

```hcl
terraform {
  backend "s3" {
    bucket         = "okta-terraform-demo"
    key            = "Okta-GitOps/staging/terraform.tfstate"  # ✓ Already correct
    region         = "us-east-1"
    encrypt        = true
    dynamodb_table = "okta-terraform-state-lock"
  }
}
```

The backend configuration is already set for staging. No changes needed unless using a different S3 bucket.

### 3. Initialize Terraform

```bash
cd environments/staging/terraform
terraform init
```

### 4. Import Resources from Okta

```bash
gh workflow run import-all-resources.yml \
  -f tenant_environment=Staging \
  -f update_terraform=true \
  -f commit_changes=false
```

### 5. Review and Apply

```bash
cd environments/staging/terraform
terraform plan
terraform apply
```

## Directory Structure

```
staging/
├── terraform/              # Terraform configurations
│   ├── provider.tf        # Provider and backend config
│   └── variables.tf       # Variable definitions
├── config/                # API-managed resources
│   ├── owner_mappings.json   # Resource owners (empty template)
│   └── label_mappings.json   # Governance labels (empty template)
├── imports/               # Raw API import data
│   └── README.md         # Import data documentation
└── README.md             # This file
```

## Current Status

| Component | Status | Action Needed |
|-----------|--------|---------------|
| GitHub Environment | ❌ Not configured | Create with secrets |
| Terraform State | ❌ Not initialized | Run `terraform init` |
| Resources | ❌ Empty | Import from Okta |
| Config Files | ⚠️ Templates only | Populate after import |

## Workflows

Once configured, use these workflows:

### Import Resources
```bash
gh workflow run import-all-resources.yml \
  -f tenant_environment=Staging \
  -f update_terraform=true
```

### Apply Changes
```bash
gh workflow run terraform-apply-with-approval.yml \
  -f environment=staging
```

### Manage Resource Owners
```bash
gh workflow run apply-owners.yml \
  -f environment=staging \
  -f dry_run=false
```

### Manage Labels
```bash
gh workflow run apply-labels-from-config.yml \
  -f environment=staging \
  -f dry_run=false
```

## Staging Environment Purpose

Staging is typically used for:
- ✅ Testing changes before production
- ✅ QA and integration testing
- ✅ Demo and training environments
- ✅ Customer acceptance testing
- ✅ Performance and load testing

## Related Documentation

- [Main Environments README](../README.md)
- [GitOps Workflow Guide](../../docs/GITOPS_WORKFLOW.md)
- [Workflow Documentation](../../docs/WORKFLOWS.md)
