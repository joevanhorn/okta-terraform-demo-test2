# Okta Resource Quick Reference

Quick reference for common Okta Terraform resources.

## Core Resources

### okta_user
Manages Okta users
- **Docs:** https://registry.terraform.io/providers/okta/okta/latest/docs/resources/user
- **Use for:** Creating users for demos, testing, employee onboarding

### okta_group
Manages Okta groups
- **Docs:** https://registry.terraform.io/providers/okta/okta/latest/docs/resources/group
- **Use for:** Organizing users by department, role, or access level

### okta_group_memberships
Assigns users to groups
- **Docs:** https://registry.terraform.io/providers/okta/okta/latest/docs/resources/group_memberships
- **Use for:** Adding users to one or more groups

### okta_app_oauth
Creates OAuth 2.0 / OIDC applications
- **Docs:** https://registry.terraform.io/providers/okta/okta/latest/docs/resources/app_oauth
- **Use for:** Integrating web apps, SPAs, mobile apps, APIs
- **Types:** web, browser, native, service

### okta_app_saml
Creates SAML applications
- **Docs:** https://registry.terraform.io/providers/okta/okta/latest/docs/resources/app_saml
- **Use for:** Enterprise app integrations (Salesforce, Workday, etc.)

### okta_app_group_assignment
Assigns groups to applications
- **Docs:** https://registry.terraform.io/providers/okta/okta/latest/docs/resources/app_group_assignment
- **Use for:** Controlling which groups can access which apps

## OIG (Identity Governance) Resources

### okta_entitlement_bundle
Manages entitlement bundles (packages of access)
- **Docs:** https://registry.terraform.io/providers/okta/okta/latest/docs/resources/entitlement_bundle
- **Use for:** Creating access bundles (e.g., "Marketing Tools", "Engineering Access")
- **Note:** Manage bundle definitions only; assign principals via Okta UI

### okta_reviews
Manages access review campaigns
- **Docs:** https://registry.terraform.io/providers/okta/okta/latest/docs/resources/reviews
- **Use for:** Setting up periodic access reviews for compliance

### okta_resource_set
Manages resource sets for OIG
- **Docs:** https://registry.terraform.io/providers/okta/okta/latest/docs/resources/resource_set
- **Use for:** Grouping resources for governance workflows

## Authorization & Security

### okta_auth_server
Custom authorization servers
- **Docs:** https://registry.terraform.io/providers/okta/okta/latest/docs/resources/auth_server
- **Use for:** Creating custom OAuth 2.0 authorization servers for APIs

### okta_auth_server_scope
OAuth scopes for authorization servers
- **Docs:** https://registry.terraform.io/providers/okta/okta/latest/docs/resources/auth_server_scope
- **Use for:** Defining granular permissions (read:data, write:data, etc.)

### okta_auth_server_claim
Custom claims in tokens
- **Docs:** https://registry.terraform.io/providers/okta/okta/latest/docs/resources/auth_server_claim
- **Use for:** Adding user attributes to ID tokens and access tokens

### okta_policy_mfa
Multi-factor authentication policies
- **Docs:** https://registry.terraform.io/providers/okta/okta/latest/docs/resources/policy_mfa
- **Use for:** Enforcing MFA requirements for users/groups

### okta_policy_password
Password policies
- **Docs:** https://registry.terraform.io/providers/okta/okta/latest/docs/resources/policy_password
- **Use for:** Setting password complexity, expiration, history rules

## Common Data Sources

### data "okta_user"
Look up existing users
- **Docs:** https://registry.terraform.io/providers/okta/okta/latest/docs/data-sources/user
- **Use for:** Referencing users created outside Terraform

### data "okta_group"
Look up existing groups
- **Docs:** https://registry.terraform.io/providers/okta/okta/latest/docs/data-sources/group
- **Use for:** Referencing default Okta groups (Everyone, etc.)

### data "okta_app"
Look up existing applications
- **Docs:** https://registry.terraform.io/providers/okta/okta/latest/docs/data-sources/app
- **Use for:** Referencing Okta system apps

## Resource Relationships

```
okta_user ──┐
            ├──> okta_group_memberships ──> okta_group ──> okta_app_group_assignment ──> okta_app_oauth
okta_user ──┘

okta_auth_server ──┬──> okta_auth_server_scope
                   └──> okta_auth_server_claim

okta_entitlement_bundle (manage via Terraform)
    └──> Principal assignments (manage via Okta UI)
```

## Typical Demo Scenarios

### Basic User Onboarding
1. Create users (`okta_user`)
2. Create department groups (`okta_group`)
3. Assign users to groups (`okta_group_memberships`)
4. Create applications (`okta_app_oauth`)
5. Assign groups to apps (`okta_app_group_assignment`)

### API Access Control
1. Create auth server (`okta_auth_server`)
2. Define scopes (`okta_auth_server_scope`)
3. Add custom claims (`okta_auth_server_claim`)
4. Create service app (`okta_app_oauth` type=service)
5. Grant scopes to app

### Identity Governance Demo
1. Create users and groups (basic setup)
2. Create entitlement bundles (`okta_entitlement_bundle`)
3. Assign apps to bundles (via Okta UI)
4. Create access reviews (`okta_reviews`)
5. Demonstrate access certification workflow

## Important Notes

### Template String Escaping
Always use `$$` to escape Okta template expressions:
```hcl
user_name_template = "$${source.login}"  # Correct
```

### Status Values
Most resources support `status` attribute:
- `"ACTIVE"` - Resource is active
- `"INACTIVE"` - Resource is disabled
- `"STAGED"` - User not yet activated (users only)

### OIG License Required
These resources require Okta Identity Governance license:
- `okta_entitlement_bundle`
- `okta_reviews`
- `okta_resource_set`
- Related OIG resources

### API-Only Features
Some OIG features are API-only (not in Terraform):
- Resource owners (use Python scripts: `scripts/apply_resource_owners.py`)
- Governance labels (use Python scripts: `scripts/apply_governance_labels.py`)
- Principal assignments to bundles (manage via Okta Admin Console)

## Additional Resources

- **Full Resource Catalog:** `docs/TERRAFORM_RESOURCES.md`
- **Provider Documentation:** https://registry.terraform.io/providers/okta/okta/latest/docs
- **Okta Developer Docs:** https://developer.okta.com/
