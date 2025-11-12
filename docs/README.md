# Production-Ready Okta Terraform Configuration

**✅ VALIDATED:** This configuration has been tested end-to-end with importing existing resources and applying changes.

This directory contains a **production-ready** Terraform setup that demonstrates the complete workflow:
1. Import existing Okta resources using Terraformer
2. Manage imported resources with Terraform
3. Add new resources and apply changes

## 🎯 What This Proves

This configuration successfully demonstrates:

✅ **Import existing infrastructure** - Terraformer import works
✅ **Manage imported resources** - Resources are under Terraform control
✅ **Make changes** - Added 3 users + 1 app successfully
✅ **No drift** - `terraform plan` shows "No changes" after apply
✅ **Production patterns** - Proper state management, resource organization

## 📦 Complete Terraform Provider Support

This directory now supports **ALL ~100 Okta Terraform resources** available in provider v6.1.0+:

### ✅ Terraformer Auto-Import (~90 resources)
- Users, Groups, Apps, Policies
- Authorization Servers
- Identity Providers
- Network Zones, Trusted Origins
- Hooks, Templates

### ✅ Manual Terraform Creation (~100 resources total)
- All basic resources PLUS:
- **OIG Governance Resources** (9 new in v6.1.0):
  - `okta_reviews` - Access certification campaigns
  - `okta_principal_entitlements` - Manual entitlements ONLY
  - `okta_request_*` - Access request workflows
  - `okta_catalog_*` - Resource catalog management
- **Device Assurance Policies** (Windows, macOS, iOS, Android, ChromeOS)
- **Brands & Themes** (5 resources)
- **Custom Admin Roles** (5 resources)
- **Linking & Profile Mappings** (3 resources)

### ⚠️ API-Only Resources (2 resources)
These are NOT supported by Terraform and must use API scripts:
- ❌ **Governance Labels** - Use `scripts/okta_api_manager.py`
- ❌ **Resource Owners** - Use `scripts/okta_api_manager.py`
- ✅ **App-Managed Entitlements** - Read-only (exported for documentation)

## 📚 Quick Reference Files

- **[RESOURCE_EXAMPLES.tf](./RESOURCE_EXAMPLES.tf)** - Commented examples for ALL ~100 resources
- **[TERRAFORM_RESOURCES.md](./TERRAFORM_RESOURCES.md)** - Complete resource guide (catalog + detailed attributes)
- **[OIG_PREREQUISITES.md](../OIG_PREREQUISITES.md)** - Prerequisites for OIG features
- **[oig-exports/README.md](../oig-exports/README.md)** - API-managed resource documentation

**⚠️ Important Entitlement Distinction:**
- ✅ **Manual/Custom Entitlements** - Can be managed in Terraform (`okta_principal_entitlements`)
- ❌ **App-Managed Entitlements** - READ-ONLY (Salesforce, Workday, etc.) - exported via API for documentation

## 📁 Current Structure

```
production-ready/
├── provider.tf                      # Terraform & Okta provider config (v6.1.0+)
├── variables.tf                     # Input variables
├── terraform.tfvars                 # Actual credentials (gitignored)
├── terraform.tfstate                # Root state file (consolidated)
├── RESOURCE_EXAMPLES.tf             # ⭐ Commented examples for ALL ~100 resources
├── TERRAFORM_RESOURCES.md           # ⭐ Complete resource guide (catalog + attributes)
├── app_oauth.tf                     # OAuth applications (3 existing + 1 new)
├── user.tf                          # Users (3 existing + 3 new)
├── group.tf                         # Groups (4 total)
├── auth_server_default.tf           # Default auth server
├── policy_mfa_default.tf            # MFA policy
├── okta_system_apps.tf.excluded     # Okta-managed apps (reference only)
└── subdirectories/                  # Original Terraformer imports (for reference)
```

## 📊 Managed Resources

| Resource Type | Count | Details |
|--------------|-------|---------|
| **Users** | 6 | john.doe, jane.smith, bob.johnson, alice.williams, david.chen, emma.rodriguez |
| **OAuth Apps** | 4 | Demo Platform Management, Internal CRM, Project Management Tool, Team Collaboration Tool |
| **Groups** | 4 | All Employees, Engineering Team, Sales Team, Security Team |
| **Auth Servers** | 1 | Default authorization server |
| **MFA Policies** | 1 | Default MFA policy |

### 🚫 Excluded Resources

**Okta System Apps** (in `okta_system_apps.tf.excluded`):
- Okta Workflows
- Okta Access Requests
- Okta Identity Governance
- Okta Access Certification Reviews
- Okta Workflows OAuth

**Why excluded?** These are Okta-managed internal apps that don't follow standard OAuth patterns. Attempting to manage them via Terraform causes compatibility issues with required attributes like `type`.

**Admin Users** (filtered during import):
- joe.vanhorn@okta.com (super admin)
- demo.platform@okta.com (super admin)

**Why excluded?** Managing super admin users in Terraform risks account lockout if `terraform destroy` is run.

## 🚀 Quick Start

### For Users Forking This Repo

See **[FORKING_GUIDE.md](./FORKING_GUIDE.md)** for complete setup instructions.

Quick version:

```bash
# 1. Clone and navigate
git clone <your-fork>
cd production-ready

# 2. Configure credentials
cp terraform.tfvars.example terraform.tfvars
nano terraform.tfvars  # Add your Okta credentials

# 3. Initialize
terraform init

# 4. Plan (should show existing resources managed, new resources to add)
terraform plan

# 5. Apply changes (optional - creates the 3 new users + 1 app)
terraform apply
```

### For Users Importing Fresh

```bash
# 1. Run Terraformer import
cd ..
terraformer import okta --resources=user,group,app_oauth,auth_server,policy \
  --organizations=<your-org-name> \
  --okta-base-url=<okta-domain> \
  --okta-api-token=<your-token>

# 2. Move to production-ready directory
cd production-ready

# 3. Consolidate state (see LESSONS_LEARNED.md for details)
# Import each resource type into root state

# 4. Exclude Okta system apps (see okta_system_apps.tf.excluded for list)

# 5. Test
terraform plan
terraform apply
```

## 🔧 Validated Workflow

This configuration has been validated through this workflow:

1. **Terraformer Import** ✅
   - Imported users, groups, apps, policies, auth servers
   - Each resource type gets its own subdirectory with state

2. **Consolidation** ✅
   - Moved `.tf` files to root directory
   - Imported resources into root `terraform.tfstate`
   - Fixed template string interpolation (`${source.login}` → `$${source.login}`)

3. **Excluded Problematic Resources** ✅
   - Removed 5 Okta system apps from management
   - Documented in `okta_system_apps.tf.excluded`

4. **Added New Resources** ✅
   - Created 3 new users (alice, david, emma)
   - Created 1 new app (Team Collaboration Tool)

5. **Applied Successfully** ✅
   ```
   Apply complete! Resources: 4 added, 0 changed, 0 destroyed.
   ```

6. **Verified No Drift** ✅
   ```
   No changes. Your infrastructure matches the configuration.
   ```

## 🛠️ Common Tasks

### Adding a New User

```hcl
resource "okta_user" "new_employee" {
  email      = "new.employee@example.com"
  first_name = "New"
  last_name  = "Employee"
  login      = "new.employee@example.com"
  status     = "ACTIVE"
  department = "Engineering"
}
```

```bash
terraform plan   # Review
terraform apply  # Create
```

### Adding a New OAuth App

```hcl
resource "okta_app_oauth" "my_new_app" {
  label                      = "My New Application"
  type                       = "web"
  grant_types                = ["authorization_code", "refresh_token"]
  redirect_uris              = ["https://myapp.example.com/callback"]
  post_logout_redirect_uris  = ["https://myapp.example.com/logout"]
  response_types             = ["code"]
  client_uri                 = "https://myapp.example.com"
  pkce_required              = true
  token_endpoint_auth_method = "client_secret_post"
  login_mode                 = "DISABLED"
  hide_ios                   = true
  hide_web                   = true
  user_name_template         = "$${source.login}"
  user_name_template_type    = "BUILT_IN"
}
```

### Importing an Existing Resource

```bash
# Get the resource ID from Okta
# Then import into state:
terraform import okta_user.john_doe 00u1234567890abcdef

# Add the resource definition to your .tf file
# Run plan to verify
terraform plan
```

## ⚠️ Important Lessons Learned

See **[LESSONS_LEARNED.md](./LESSONS_LEARNED.md)** for detailed issues and solutions.

Key takeaways:

1. **Template Strings**: Use `$${source.login}` not `${source.login}` to prevent Terraform interpolation
2. **Okta System Apps**: Cannot be managed via Terraform (exclude them)
3. **State Consolidation**: Import resources into root state, don't use subdirectory states
4. **Login Mode**: When `hide_ios` and `hide_web` are false, `login_mode` cannot be "DISABLED"
5. **Refresh Token Rotation**: Use "STATIC" not "ROTATE_ON_USE" for compatibility

## 📋 Configuration Details

### Template String Escaping

Okta uses `${source.login}` as a template variable. Terraform also uses `${}` for interpolation. To prevent Terraform from trying to interpolate, escape with double `$$`:

```hcl
# CORRECT
user_name_template = "$${source.login}"

# WRONG (Terraform will try to interpolate)
user_name_template = "${source.login}"
```

### OAuth App Visibility Settings

Okta enforces this rule:
- If `hide_ios = false` OR `hide_web = false`, then `login_mode` cannot be "DISABLED"
- Solution: Either set both hide flags to `true`, or use a different login_mode

```hcl
# Option 1: Hide the app
hide_ios   = true
hide_web   = true
login_mode = "DISABLED"

# Option 2: Make it visible with proper login mode
hide_ios   = false
hide_web   = false
login_mode = "SPEC"
login_uri  = "https://myapp.example.com/login"  # Required!
```

## 🔐 Security Best Practices

### Never Commit Secrets

```bash
# Add to .gitignore
echo "terraform.tfvars" >> .gitignore
echo "*.tfstate" >> .gitignore
echo "*.tfstate.backup" >> .gitignore
echo ".terraform/" >> .gitignore
```

### Use Remote State

For production, configure remote state backend:

```hcl
# In provider.tf
terraform {
  backend "s3" {
    bucket         = "my-terraform-state"
    key            = "okta/production/terraform.tfstate"
    region         = "us-east-1"
    encrypt        = true
    dynamodb_table = "terraform-lock"
  }
}
```

### Least Privilege API Token

Create an Okta API token with only required scopes:
- `okta.users.manage`
- `okta.groups.manage`
- `okta.apps.manage`
- `okta.authorizationServers.manage`
- `okta.policies.manage`

## 🧪 Testing

```bash
# Validate syntax
terraform validate

# Check formatting
terraform fmt -check

# Review plan (should show no changes)
terraform plan

# Test in non-production first!
```

## 📚 Additional Documentation

### Resource References
- **[RESOURCE_EXAMPLES.tf](./RESOURCE_EXAMPLES.tf)** - ⭐ Commented examples for ALL ~100 Terraform resources
- **[TERRAFORM_RESOURCES.md](./TERRAFORM_RESOURCES.md)** - ⭐ Complete resource guide (catalog + detailed attributes for key resources)

### Setup & Usage Guides
- **[FORKING_GUIDE.md](./FORKING_GUIDE.md)** - Complete guide for users forking this repo
- **[LESSONS_LEARNED.md](./LESSONS_LEARNED.md)** - Detailed issues and solutions encountered
- **[../OIG_PREREQUISITES.md](../OIG_PREREQUISITES.md)** - Prerequisites for OIG governance features
- **[../oig-exports/README.md](../oig-exports/README.md)** - API-managed resources (Labels, Resource Owners)

### External Documentation
- **[Parent README](../README.md)** - Overall project documentation
- **[Okta Provider Docs](https://registry.terraform.io/providers/okta/okta/latest/docs)** - Official provider documentation

## 🆘 Troubleshooting

### "Missing required argument 'type'" on OAuth apps

Some Okta system apps don't have a standard `type`. These should be excluded from Terraform management. See `okta_system_apps.tf.excluded` for the list.

### "Reference to undeclared resource" error with ${source.login}

You need to escape the template string: `$${source.login}`

### "login_uri required" error

When using `login_mode = "SPEC"`, you must also provide `login_uri`. Either add the URI or change to `login_mode = "DISABLED"`.

### State conflicts

Enable state locking with a remote backend (S3 + DynamoDB, Azure Blob, etc.)

## 📝 Changelog

- **2025-11-04**: Production validation completed
  - ✅ Tested full import → manage → modify → apply workflow
  - ✅ Created 3 new users and 1 new app successfully
  - ✅ Verified no drift after apply
  - ✅ Documented lessons learned and best practices
  - ✅ Excluded Okta system apps from management

- **2025-10-31**: Initial import
  - Imported resources via Terraformer
  - Filtered super admin users
  - Organized by resource type

## 👥 Support

For questions or issues:
1. Check [LESSONS_LEARNED.md](./LESSONS_LEARNED.md) for common problems
2. Review [FORKING_GUIDE.md](./FORKING_GUIDE.md) for setup help
3. Consult [Okta Provider docs](https://registry.terraform.io/providers/okta/okta/latest/docs)
4. Open an issue in the GitHub repository
