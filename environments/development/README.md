# Development Environment (Template)

This is a template directory for your development Okta tenant. Follow the setup guide below to configure it for your organization.

## ⚠️ Setup Required

This directory contains template files only. Before using:

1. Configure GitHub Environment with secrets
2. Update Terraform provider configuration
3. Import resources from your Okta tenant

## Quick Setup Guide

### 1. Configure GitHub Environment

Go to **Settings > Environments** and create:

**Environment Name:** `Development`

**Required Secrets:**
- `OKTA_API_TOKEN` - Development Okta API token with governance scopes
- `OKTA_ORG_NAME` - Your Okta org name (e.g., `dev-12345678`)
- `OKTA_BASE_URL` - Base URL (typically `oktapreview.com` for dev orgs)

**Protection Rules:**
- ⚠️ Optional: Less restrictive than production/staging
- ✅ Can allow self-approval for faster iteration

### 2. Update Terraform Configuration

Edit `terraform/provider.tf`:

```hcl
terraform {
  backend "s3" {
    bucket         = "okta-terraform-demo"
    key            = "Okta-GitOps/development/terraform.tfstate"  # ✓ Already correct
    region         = "us-east-1"
    encrypt        = true
    dynamodb_table = "okta-terraform-state-lock"
  }
}
```

The backend configuration is already set for development. No changes needed unless using a different S3 bucket.

### 3. Initialize Terraform

```bash
cd environments/development/terraform
terraform init
```

### 4. Import Resources from Okta

```bash
gh workflow run import-all-resources.yml \
  -f tenant_environment=Development \
  -f update_terraform=true \
  -f commit_changes=false
```

### 5. Review and Apply

```bash
cd environments/development/terraform
terraform plan
terraform apply
```

## Directory Structure

```
development/
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
  -f tenant_environment=Development \
  -f update_terraform=true
```

### Apply Changes
```bash
gh workflow run terraform-apply-with-approval.yml \
  -f environment=development
```

### Manage Resource Owners
```bash
gh workflow run apply-owners.yml \
  -f environment=development \
  -f dry_run=false
```

### Manage Labels
```bash
gh workflow run apply-labels-from-config.yml \
  -f environment=development \
  -f dry_run=false
```

## Development Environment Purpose

Development is typically used for:
- ✅ Rapid experimentation and testing
- ✅ Learning OIG features
- ✅ Developing new configurations
- ✅ Testing workflows before staging
- ✅ Sandbox for breaking changes

## Development Best Practices

### Experimentation
- ✅ Safe to test breaking changes
- ✅ Can be destroyed and recreated
- ✅ Use for learning Terraform patterns
- ✅ Test new OIG features here first

### Cleanup
- ✅ Regularly clean up unused resources
- ✅ Reset to baseline periodically
- ✅ Don't let it become production-like
- ✅ Document experiments in PRs

### Promotion Path
```
Development → Staging → Production
    ↓            ↓          ↓
  Test        Validate   Deploy
```

## Related Documentation

- [Main Environments README](../README.md)
- [GitOps Workflow Guide](../../docs/GITOPS_WORKFLOW.md)
- [Workflow Documentation](../../docs/WORKFLOWS.md)
