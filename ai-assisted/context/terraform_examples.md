# Terraform Examples for AI Generation

This document provides example patterns for generating Okta Terraform resources.

## User Examples

### Basic User
```hcl
resource "okta_user" "john_doe" {
  email      = "john.doe@example.com"
  first_name = "John"
  last_name  = "Doe"
  login      = "john.doe@example.com"
  status     = "ACTIVE"
}
```

### User with Department and Manager
```hcl
resource "okta_user" "jane_smith" {
  email        = "jane.smith@example.com"
  first_name   = "Jane"
  last_name    = "Smith"
  login        = "jane.smith@example.com"
  department   = "Engineering"
  title        = "Senior Engineer"
  manager_id   = okta_user.john_doe.id
  status       = "ACTIVE"
}
```

### User with Custom Attributes
```hcl
resource "okta_user" "bob_jones" {
  email      = "bob.jones@example.com"
  first_name = "Bob"
  last_name  = "Jones"
  login      = "bob.jones@example.com"
  department = "Sales"

  custom_profile_attributes = jsonencode({
    "employeeNumber" = "12345"
    "costCenter"     = "SALES-001"
  })

  status = "ACTIVE"
}
```

## Group Examples

### Basic Group
```hcl
resource "okta_group" "engineering" {
  name        = "Engineering Team"
  description = "All engineering department members"
}
```

### Group with Members
```hcl
resource "okta_group" "marketing" {
  name        = "Marketing Team"
  description = "Marketing department members"
}

resource "okta_group_memberships" "marketing_members" {
  group_id = okta_group.marketing.id
  users = [
    okta_user.jane_smith.id,
    okta_user.bob_jones.id,
  ]
}
```

## Application Examples

### OAuth 2.0 Web Application
```hcl
resource "okta_app_oauth" "example_web_app" {
  label                      = "Example Web Application"
  type                       = "web"
  grant_types                = ["authorization_code", "refresh_token"]
  redirect_uris              = ["https://app.example.com/callback"]
  post_logout_redirect_uris  = ["https://app.example.com/logout"]
  response_types             = ["code"]

  # Client authentication
  token_endpoint_auth_method = "client_secret_post"

  # PKCE (recommended for security)
  pkce_required              = true

  # Additional settings
  client_uri                 = "https://app.example.com"
  logo_uri                   = "https://app.example.com/logo.png"

  # Login settings
  login_mode                 = "DISABLED"

  # Hide from end user dashboard
  hide_ios                   = true
  hide_web                   = true

  # User name template (IMPORTANT: Use $$ to escape)
  user_name_template         = "$${source.login}"
  user_name_template_type    = "BUILT_IN"
}
```

### Single Page Application (SPA)
```hcl
resource "okta_app_oauth" "example_spa" {
  label                      = "Example SPA"
  type                       = "browser"
  grant_types                = ["authorization_code"]
  redirect_uris              = ["http://localhost:3000/callback"]
  response_types             = ["code"]

  # SPAs must use PKCE
  pkce_required              = true

  # Public client (no client secret)
  token_endpoint_auth_method = "none"

  client_uri                 = "http://localhost:3000"
  login_mode                 = "DISABLED"
  hide_ios                   = true
  hide_web                   = true

  user_name_template         = "$${source.login}"
  user_name_template_type    = "BUILT_IN"
}
```

### Application with Group Assignment
```hcl
resource "okta_app_oauth" "salesforce" {
  label          = "Salesforce"
  type           = "web"
  grant_types    = ["authorization_code", "refresh_token"]
  redirect_uris  = ["https://login.salesforce.com/services/oauth2/callback"]
  response_types = ["code"]

  pkce_required              = true
  token_endpoint_auth_method = "client_secret_post"
  login_mode                 = "DISABLED"
  hide_ios                   = true
  hide_web                   = true

  user_name_template         = "$${source.login}"
  user_name_template_type    = "BUILT_IN"
}

resource "okta_app_group_assignment" "salesforce_marketing" {
  app_id   = okta_app_oauth.salesforce.id
  group_id = okta_group.marketing.id
}
```

## OIG Examples

### Entitlement Bundle
```hcl
resource "okta_entitlement_bundle" "marketing_tools" {
  name        = "Marketing Tools Bundle"
  description = "Access to all marketing applications and resources"
}
```

### Access Review Campaign
```hcl
resource "okta_reviews" "quarterly_access_review" {
  name        = "Quarterly Access Review - Q1 2025"
  description = "Review user access to all applications"

  # Review schedule
  start_date  = "2025-01-01T00:00:00Z"
  end_date    = "2025-01-31T23:59:59Z"

  # Review type
  review_type = "USER_ACCESS_REVIEW"

  # Reviewers
  reviewer_type = "MANAGER"
}
```

## Authorization Server Examples

### Custom Authorization Server
```hcl
resource "okta_auth_server" "example" {
  name        = "Example Auth Server"
  description = "Authorization server for example APIs"
  audiences   = ["api://example"]

  # Optional: Set as issuer
  issuer_mode = "CUSTOM_URL"
}

resource "okta_auth_server_scope" "read" {
  auth_server_id = okta_auth_server.example.id
  name           = "read:data"
  description    = "Read access to data"
  consent        = "REQUIRED"
}

resource "okta_auth_server_claim" "email" {
  auth_server_id = okta_auth_server.example.id
  name           = "email"
  value          = "user.email"
  claim_type     = "RESOURCE"

  scopes = [
    okta_auth_server_scope.read.name,
  ]
}
```

## Policy Examples

### MFA Policy
```hcl
resource "okta_policy_mfa" "example" {
  name        = "Example MFA Policy"
  status      = "ACTIVE"
  description = "Require MFA for all users"

  groups_included = [
    okta_group.engineering.id,
  ]
}

resource "okta_policy_rule_mfa" "require_mfa" {
  policy_id = okta_policy_mfa.example.id
  name      = "Require MFA"
  status    = "ACTIVE"

  enroll = "REQUIRED"
}
```

## Best Practices

### 1. Always Use Status = "ACTIVE"
For production-ready resources, set status to "ACTIVE".

### 2. Escape Template Strings
Always use `$${...}` for Okta template expressions:
```hcl
user_name_template = "$${source.login}"  # Correct
user_name_template = "${source.login}"   # WRONG - Terraform will try to interpolate
```

### 3. Use Descriptive Resource Names
```hcl
# Good
resource "okta_user" "engineering_manager_jane" { ... }

# Less clear
resource "okta_user" "user1" { ... }
```

### 4. Add Comments
```hcl
# Marketing team Salesforce integration
resource "okta_app_oauth" "marketing_salesforce" {
  label = "Salesforce - Marketing"
  # ... configuration
}
```

### 5. Group Related Resources
Keep users, groups, and their memberships together in the same file or adjacent files.

### 6. Use Dependencies When Needed
```hcl
resource "okta_group_memberships" "example" {
  depends_on = [
    okta_user.john_doe,
    okta_user.jane_smith,
  ]
  # ...
}
```

## Common Attributes Reference

### User Attributes
- `email` (required)
- `first_name` (required)
- `last_name` (required)
- `login` (required)
- `status` (optional: "ACTIVE", "STAGED", "DEPROVISIONED")
- `department`
- `title`
- `manager_id`
- `city`
- `state`
- `country_code`
- `mobile_phone`
- `custom_profile_attributes` (JSON string)

### Group Attributes
- `name` (required)
- `description`
- `skip_users` (optional: set to true if managing memberships separately)

### OAuth App Attributes
- `label` (required)
- `type` (required: "web", "browser", "native", "service")
- `grant_types` (required: array)
- `redirect_uris` (required for most types)
- `response_types` (required: array)
- `token_endpoint_auth_method`
- `pkce_required` (boolean)
- `client_uri`
- `logo_uri`
- `login_mode`
- `hide_ios`
- `hide_web`
- `user_name_template`
- `user_name_template_type`
