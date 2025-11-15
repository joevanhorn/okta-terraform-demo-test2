# SCIM Server Deployment Plan

**Environment:** myorg
**Domain:** demo-entitlements-lowerdecks.com
**SCIM Server URL:** scim.demo-entitlements-lowerdecks.com
**Route53 Zone ID:** Z0726191Q5GQ53YU4EOR
**AWS Region:** us-east-1
**Deployment Date:** 2025-11-14

---

## Overview

This deployment will create a custom SCIM 2.0 server on AWS to demonstrate:
- API-only entitlements and custom provisioning
- SCIM provisioning from Okta to custom applications
- Custom roles that don't map to standard Okta app resources

## Infrastructure Components

**AWS Resources to be created:**
- EC2 instance (t3.micro) running Flask SCIM 2.0 server
- Elastic IP for stable addressing
- Route53 DNS record: scim.demo-entitlements-lowerdecks.com → Elastic IP
- Security groups (HTTPS only, SSH disabled)
- Automatic HTTPS via Caddy + Let's Encrypt

**SCIM Server Features:**
- Flask-based SCIM 2.0 implementation
- Web dashboard at https://scim.demo-entitlements-lowerdecks.com/
- SCIM endpoint at https://scim.demo-entitlements-lowerdecks.com/scim/v2
- Bearer token authentication
- Custom entitlements (5 default roles)

**Custom Entitlements/Roles:**
1. Administrator - Full system access
2. Standard User - Basic application access
3. Read Only - View-only access
4. Support Agent - Customer support access
5. Billing Admin - Financial/billing access

---

## Prerequisites Verified

- ✅ AWS Account with credentials configured
- ✅ Route53 hosted zone for demo-entitlements-lowerdecks.com (Z0726191Q5GQ53YU4EOR)
- ✅ Okta org with OIG features enabled
- ✅ S3 backend configured (okta-terraform-demo bucket)
- ✅ Auth token generated (stored securely in terraform.tfvars)

---

## Deployment Steps

### Phase 1: Deploy SCIM Server Infrastructure (AWS)

```bash
# Navigate to SCIM server directory
cd environments/myorg/infrastructure/scim-server

# Create terraform.tfvars from example (ALREADY DONE - contains secrets)
# cp terraform.tfvars.example terraform.tfvars
# (Edit with actual values - domain, zone ID, auth token)

# Initialize Terraform
terraform init

# Review the deployment plan
terraform plan

# Deploy infrastructure
terraform apply

# Wait 5-10 minutes for server initialization
# - Caddy installs and provisions Let's Encrypt SSL certificate
# - Python dependencies install
# - SCIM server starts
```

**Expected Outputs:**
```
scim_base_url = "https://scim.demo-entitlements-lowerdecks.com/scim/v2"
dashboard_url = "https://scim.demo-entitlements-lowerdecks.com"
domain_name = "scim.demo-entitlements-lowerdecks.com"
```

### Phase 2: Verify SCIM Server Health

```bash
# Health check
SCIM_URL=$(terraform output -raw scim_base_url | sed 's|/scim/v2||')
curl $SCIM_URL/health
# Expected: {"status":"healthy"}

# View dashboard in browser
open $(terraform output -raw dashboard_url)
```

### Phase 3: Create Okta SCIM Application

```bash
# Navigate to Okta Terraform directory
cd ../../terraform

# Verify SCIM server state is readable
terraform init

# Review the plan (should show SCIM app creation)
terraform plan

# Create Okta app
terraform apply

# Capture app ID for configuration
APP_ID=$(terraform output -raw scim_app_id)
echo "App ID: $APP_ID"
```

### Phase 4: Configure SCIM Connection (Python Script)

The Okta Terraform provider **cannot** configure SCIM connections. Use the Python script:

```bash
# Get SCIM credentials from infrastructure state
cd ../infrastructure/scim-server
SCIM_URL=$(terraform output -raw scim_base_url)
SCIM_TOKEN=$(terraform output -json okta_configuration | jq -r '.header_auth_token')

# Return to terraform directory
cd ../../terraform

# Set Okta environment variables (if not already set)
export OKTA_ORG_NAME="your-org"
export OKTA_BASE_URL="okta.com"
export OKTA_API_TOKEN="your-token"

# Run configuration script
python3 ../../scripts/configure_scim_app.py \
  --app-id "$APP_ID" \
  --scim-url "$SCIM_URL" \
  --scim-token "$SCIM_TOKEN" \
  --test-connection
```

**Expected Output:**
```
================================================================================
CONFIGURING SCIM APPLICATION
================================================================================

📋 Getting app details...
   App: Custom SCIM Demo App
   ID: 0oa...

🔧 Enabling SCIM provisioning...
✅ Enabled SCIM provisioning

🔗 Configuring SCIM connection...
✅ Configured SCIM connection

🧪 Testing SCIM connection...
✅ SCIM connection test succeeded!

⚙️  Enabling provisioning features...
✅ Enabled provisioning features

================================================================================
✅ SCIM CONFIGURATION COMPLETE
================================================================================
```

### Phase 5: Test Provisioning

1. **Assign users in Okta:**
   - Open Okta Admin Console
   - Navigate to Applications → Custom SCIM Demo App
   - Go to Assignments tab
   - Assign users or groups

2. **Verify provisioning:**
   ```bash
   # Check SCIM server dashboard
   open https://scim.demo-entitlements-lowerdecks.com
   ```

3. **View provisioned users and roles** in the dashboard

---

## Security Configuration

**Network Access:**
- HTTPS: Public (0.0.0.0/0) - can restrict to Okta IP ranges if needed
- SSH: Disabled (use AWS SSM Session Manager)
- HTTP: Open for Let's Encrypt validation (redirects to HTTPS)

**Authentication:**
- Bearer token authentication required for all SCIM endpoints
- Token stored securely (not in git)
- Dashboard is public but read-only

**SSL/TLS:**
- Automatic Let's Encrypt certificate
- Auto-renewal via Caddy
- HTTPS enforced

---

## Monitoring and Troubleshooting

**View Logs:**
```bash
# Via SSM Session Manager (no SSH key needed)
cd environments/myorg/infrastructure/scim-server
aws ssm start-session --target $(terraform output -raw instance_id)

# Once connected:
scim-logs       # View SCIM server logs
scim-status     # Check service status
```

**Common Issues:**

1. **Server not responding after 10 minutes:**
   - Check instance status: `aws ec2 describe-instance-status --instance-id <id>`
   - View init logs: `tail -f /var/log/user-data.log`

2. **SSL certificate not provisioning:**
   - Verify DNS propagation: `dig scim.demo-entitlements-lowerdecks.com`
   - Ensure port 80 is accessible for Let's Encrypt validation
   - Wait 5-10 more minutes

3. **SCIM connection test fails:**
   - Test server directly: `curl -H "Authorization: Bearer <token>" <scim_url>/ServiceProviderConfig`
   - Verify token matches between Terraform output and Okta configuration
   - Check security group allows HTTPS from Okta IP ranges

---

## Cleanup

To destroy all resources:

```bash
# Destroy Okta app first
cd environments/myorg/terraform
terraform destroy -target=okta_app_auto_login.scim_demo

# Destroy SCIM server infrastructure
cd ../infrastructure/scim-server
terraform destroy
```

---

## References

- **SCIM Server README:** `environments/myorg/infrastructure/scim-server/README.md`
- **Automation Guide:** `docs/SCIM_OKTA_AUTOMATION.md`
- **Deployment Prompt:** `ai-assisted/prompts/deploy_scim_server.md`
- **Repository Structure:** `ai-assisted/context/repository_structure.md`

---

## Success Criteria

- ✅ SCIM server deployed and accessible via HTTPS
- ✅ Health endpoint returns `{"status":"healthy"}`
- ✅ Dashboard shows UI with entitlements list
- ✅ Okta SCIM app created
- ✅ SCIM connection configured and tested successfully
- ✅ Users provisioned from Okta appear in SCIM server dashboard
- ✅ Custom entitlements visible and assignable

---

**Deployment Status:** Ready for execution
**Next Step:** Create terraform.tfvars with actual values and run Phase 1
