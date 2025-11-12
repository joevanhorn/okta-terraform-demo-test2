# Repository Structure Context

This document provides context about the Okta Terraform Complete Demo repository structure for AI assistants.

## Directory Layout

```
okta-terraform-complete-demo/
├── environments/
│   ├── lowerdecklabs/          # Example tenant environment
│   │   ├── terraform/          # Terraform configurations
│   │   │   ├── provider.tf     # Okta provider configuration
│   │   │   ├── variables.tf    # Variable definitions
│   │   │   ├── users.tf        # User resources
│   │   │   ├── groups.tf       # Group resources
│   │   │   ├── apps.tf         # Application resources
│   │   │   ├── oig_entitlements.tf  # OIG entitlement bundles
│   │   │   └── oig_reviews.tf  # OIG access reviews
│   │   ├── imports/            # Imported JSON data
│   │   └── config/             # Configuration files
│   ├── production/             # Production environment (template)
│   ├── staging/                # Staging environment (template)
│   └── development/            # Development environment (template)
├── scripts/                    # Python automation scripts
├── docs/                       # Documentation
├── testing/                    # Testing and validation guides
└── .github/workflows/          # GitHub Actions workflows
```

## Key Files to Know

### Provider Configuration
- **File:** `environments/{env}/terraform/provider.tf`
- **Purpose:** Configures the Okta Terraform provider
- **Important:** Uses variables for credentials (never hardcode secrets)

### Variables
- **File:** `environments/{env}/terraform/variables.tf`
- **Purpose:** Defines input variables for Okta connection
- **Variables:**
  - `okta_org_name` - Okta organization name
  - `okta_base_url` - Base URL (okta.com, oktapreview.com, etc.)
  - `okta_api_token` - API token (from terraform.tfvars)

### Resource Files
Each resource type has its own file:
- `users.tf` - Okta users
- `groups.tf` - Okta groups
- `group_memberships.tf` - Group member assignments
- `apps.tf` - OAuth applications
- `auth_servers.tf` - Authorization servers
- `policies.tf` - MFA and other policies
- `oig_entitlements.tf` - OIG entitlement bundles
- `oig_reviews.tf` - OIG access review campaigns

## Naming Conventions

### Resource Names (in Terraform)
- Use snake_case: `okta_user.john_doe`
- Be descriptive: `okta_group.engineering_team`
- Use prefixes for imported resources: `tfer--user_00u1234567890`

### File Organization
- One resource type per file
- Group related resources together
- Use comments to explain complex configurations

## Important Patterns

### User Name Template
Always escape template strings with `$$`:
```hcl
user_name_template = "$${source.login}"
```

### Resource Dependencies
Use explicit depends_on when needed:
```hcl
resource "okta_group_memberships" "example" {
  depends_on = [okta_user.john_doe]
  # ...
}
```

### OIG Resources
- Entitlement bundles: Manage bundle definitions only
- Principal assignments: Manage via Okta Admin Console (not Terraform)
- Resource owners: Manage via Python scripts (API-only)
- Governance labels: Manage via Python scripts (API-only)

## Environment-Specific Paths

When generating code for a specific environment, use:
- Terraform files: `environments/{env}/terraform/`
- Imports: `environments/{env}/imports/`
- Config: `environments/{env}/config/`

Example for lowerdecklabs:
- `environments/lowerdecklabs/terraform/users.tf`
