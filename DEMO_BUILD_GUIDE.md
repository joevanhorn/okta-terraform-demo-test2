# Demo Build Guide for Sales Engineers

**Build impressive Okta demos in minutes, not hours!**

This guide shows you how to use this template to rapidly create demo environments for customer presentations.

---

## 🎯 What You'll Learn

- **Quick Method:** Use AI to generate demo environments in 5 minutes
- **Manual Method:** Build custom demos step-by-step
- **Demo Scenarios:** Pre-built patterns for common use cases
- **Live Demo Tips:** Best practices for presenting

**Time Required:**
- Quick method: 5-10 minutes
- Manual method: 30-60 minutes
- Learning: 2-3 hours (first time)

---

## 🚀 Prerequisites

Before building demos, complete the [QUICKSTART.md](./QUICKSTART.md):
- ✅ Repository created from template
- ✅ GitHub Environment configured
- ✅ Initial import completed
- ✅ Terraform backend set up

**New to Terraform?** That's okay! This guide assumes no prior knowledge.

---

## Table of Contents

1. [Quick Demo Builder (AI-Assisted)](#quick-demo-builder-ai-assisted)
2. [Manual Demo Building](#manual-demo-building)
3. [Common Demo Scenarios](#common-demo-scenarios)
4. [Demo Presentation Tips](#demo-presentation-tips)
5. [Troubleshooting Demos](#troubleshooting-demos)

---

## Quick Demo Builder (AI-Assisted)

**Fastest way to create demos!** Use AI to generate Terraform code in minutes.

### Prerequisites

- Python 3.9+ installed
- AI API key (Gemini, OpenAI, or Anthropic)
- Completed [QUICKSTART.md](./QUICKSTART.md)

### Step 1: Install AI-Assisted Tool

```bash
cd ai-assisted

# Install requirements
pip install -r requirements.txt

# Set your API key (choose one)
export GEMINI_API_KEY="your-key"
export OPENAI_API_KEY="your-key"
export ANTHROPIC_API_KEY="your-key"
```

### Step 2: Generate Demo Environment

**Example: Marketing team with Salesforce app**

```bash
python generate.py \
  --prompt "Create a marketing team demo with 5 users, 2 groups (Marketing-Team and Marketing-Managers), and a Salesforce app with SSO. Include entitlement bundles for user and admin access." \
  --provider gemini \
  --output ../environments/demo/terraform/marketing-demo.tf \
  --validate
```

**What this does:**
1. Sends your prompt to AI
2. AI generates complete Terraform code
3. Validates syntax automatically
4. Saves to your environment

**Time:** 2-5 minutes

### Step 3: Review and Apply

```bash
cd ../environments/demo/terraform

# Review generated code
cat marketing-demo.tf

# Plan what will be created
terraform plan

# Create resources in Okta
terraform apply
```

**Time:** 3-5 minutes

### Step 4: Assign Entitlements (Manual)

Terraform creates bundle **definitions**, but you assign them manually:

1. Log into Okta Admin Console
2. Go to **Identity Governance → Entitlement Bundles**
3. Find your generated bundles
4. Click **Assign** → Select users/groups
5. Click **Save**

**Time:** 2-3 minutes

### Total Time: ~10 minutes for complete demo!

---

## Manual Demo Building

**For custom scenarios or learning Terraform.**

### Understanding Terraform Files

Terraform uses `.tf` files with this structure:

```hcl
# This is a resource block
resource "okta_user" "john_doe" {
  first_name = "John"
  last_name  = "Doe"
  login      = "john.doe@company.com"
  email      = "john.doe@company.com"
}
```

**Components:**
- `resource` - Keyword meaning "create this"
- `"okta_user"` - Type of resource
- `"john_doe"` - Name you give it (for reference)
- `first_name`, etc. - Configuration properties

### Step 1: Create Demo File

```bash
cd environments/demo/terraform

# Create new file for your demo
vim sales-team-demo.tf
```

### Step 2: Add Users

```hcl
# Sales Representative
resource "okta_user" "alice_sales" {
  first_name = "Alice"
  last_name  = "Johnson"
  login      = "alice.johnson@company.com"
  email      = "alice.johnson@company.com"
}

# Sales Manager
resource "okta_user" "bob_manager" {
  first_name = "Bob"
  last_name  = "Smith"
  login      = "bob.smith@company.com"
  email      = "bob.smith@company.com"
}
```

### Step 3: Create Groups

```hcl
resource "okta_group" "sales_team" {
  name        = "Sales Team"
  description = "All sales representatives"
}

resource "okta_group" "sales_managers" {
  name        = "Sales Managers"
  description = "Sales team managers"
}
```

### Step 4: Assign Users to Groups

```hcl
resource "okta_group_memberships" "sales_team_members" {
  group_id = okta_group.sales_team.id
  users = [
    okta_user.alice_sales.id,
    okta_user.bob_manager.id,
  ]
}

resource "okta_group_memberships" "sales_managers_members" {
  group_id = okta_group.sales_managers.id
  users = [
    okta_user.bob_manager.id,
  ]
}
```

### Step 5: Add Application

```hcl
resource "okta_app_oauth" "salesforce" {
  label                    = "Salesforce"
  type                     = "web"
  grant_types              = ["authorization_code"]
  redirect_uris            = ["https://login.salesforce.com/services/oauth2/callback"]
  response_types           = ["code"]

  # Make visible to users
  hide_ios = false
  hide_web = false
}

# Assign Sales Team to app
resource "okta_app_group_assignment" "salesforce_sales_team" {
  app_id   = okta_app_oauth.salesforce.id
  group_id = okta_group.sales_team.id
}
```

### Step 6: Apply Demo

```bash
# Format code
terraform fmt

# Validate syntax
terraform validate

# Preview what will be created
terraform plan

# Create everything!
terraform apply
```

Type `yes` when prompted.

**Time:** 20-30 minutes for custom demo

---

## Common Demo Scenarios

### Scenario 1: Basic User Lifecycle

**Use case:** Show user provisioning, group management, app assignment

**What to build:**
- 3-5 users (various roles)
- 2-3 groups (department-based)
- 1-2 apps with group assignments
- Demonstrate: Create user → Add to group → Gets app access

**Time:** 15 minutes

**AI Prompt:**
```
Create a user lifecycle demo with:
- 5 users (2 marketing, 2 sales, 1 IT admin)
- 3 groups (Marketing, Sales, IT-Admins)
- 2 apps (Salesforce for sales, HubSpot for marketing)
- Assign appropriate groups to each app
```

### Scenario 2: Entitlement Bundles

**Use case:** Show OIG entitlement management

**What to build:**
- Users and groups (from Scenario 1)
- 2-3 entitlement bundles:
  - "Salesforce User Access"
  - "Salesforce Admin Access"
  - "Marketing Tools Bundle"
- Demonstrate: Request → Approve → Get access

**Time:** 20 minutes

**AI Prompt:**
```
Create entitlement bundles demo:
- 5 users across sales and marketing
- 2 groups (Sales, Marketing)
- Salesforce app with 2 entitlement bundles (user and admin access)
- HubSpot app with 1 entitlement bundle (marketing access)
- Make bundles ACTIVE status
```

**Note:** After terraform apply, assign bundles manually in Okta Admin UI.

### Scenario 3: Access Reviews

**Use case:** Show governance and compliance

**What to build:**
- Complete Scenario 2 first
- Add access review campaigns for:
  - Quarterly app access review
  - Admin access review (more frequent)
- Demonstrate: Review launches → Reviewer approves/revokes → Access updated

**Time:** 25 minutes

**AI Prompt:**
```
Create access review demo:
- Build on previous entitlement bundles
- Create quarterly access review campaign for all apps
- Create monthly access review campaign for admin entitlements only
- Configure reviewers as group owners
```

### Scenario 4: Full Governance Stack

**Use case:** Complete OIG feature showcase

**What to build:**
- All of the above
- Resource owners assigned
- Governance labels applied
- Catalog entries configured
- Approval workflows

**Time:** 45-60 minutes

**Best approach:** Use AI + manual tuning

```bash
# Generate base structure with AI
python generate.py --interactive --provider gemini

# Fine-tune manually
vim environments/demo/terraform/governance-demo.tf

# Apply in stages
terraform apply -target=okta_user.all
terraform apply -target=okta_group.all
terraform apply  # Everything else
```

---

## Demo Presentation Tips

### Before the Demo

**Preparation (10 minutes before):**

1. **Verify demo is running:**
   ```bash
   cd environments/demo/terraform
   terraform plan
   # Should show "No changes"
   ```

2. **Open relevant tabs:**
   - Okta Admin Console (logged in)
   - GitHub repository (on demo branch)
   - Terminal (in terraform directory)

3. **Have backup ready:**
   - Screenshots of expected results
   - Pre-built demo if live demo fails

### During the Demo

**DO:**
- ✅ Start with "why" (business problem)
- ✅ Show code briefly, explain what it does
- ✅ Focus on outcomes, not syntax
- ✅ Use `terraform plan` to show "what if"
- ✅ Highlight ease of replication
- ✅ Demonstrate rollback capability

**DON'T:**
- ❌ Debug live (have backup ready)
- ❌ Get lost in technical details
- ❌ Assume audience knows Terraform
- ❌ Run destructive commands without confirmation
- ❌ Show sensitive tokens or credentials

### Demo Flow Template

**5-Minute Version:**
1. **Problem** (1 min): "Manual user provisioning takes hours..."
2. **Solution** (1 min): "With Terraform, we automate..."
3. **Demo** (2 min): Show `terraform apply` creating resources
4. **Verify** (1 min): Show created resources in Okta Admin Console

**15-Minute Version:**
1. **Problem** (2 min): Business context
2. **Architecture** (3 min): How it works (diagram)
3. **Demo - Create** (3 min): Show terraform apply
4. **Demo - Change** (3 min): Modify code, re-apply
5. **Demo - GitOps** (2 min): Show PR workflow
6. **Cleanup** (2 min): Show terraform destroy

### Handling Questions

**Common Questions & Answers:**

**Q: "Is this only for Okta?"**
A: No! Terraform works with 3000+ providers - AWS, Azure, databases, etc.

**Q: "What if I make a mistake?"**
A: `terraform plan` shows changes before applying. Plus, everything is in git - you can rollback.

**Q: "Can non-technical people use this?"**
A: Yes! Teams can use GitOps workflow - create PR, review, approve, deploy.

**Q: "How do we learn this?"**
A: This template is ready to fork. Documentation included. Plus, AI can generate code.

**Q: "What about state files?"**
A: Stored securely in S3/Terraform Cloud. Encrypted, versioned, locked for team collaboration.

---

## Demo Scenarios Library

### Pre-Built Scenarios

See `ai-assisted/prompts/` for ready-to-use prompts:

1. **`create_demo_environment.md`**
   - Complete multi-app demo
   - Users, groups, apps, bundles
   - ~10 minutes to generate

2. **`add_users.md`**
   - Add users to existing demo
   - Quick user provisioning
   - ~2 minutes

3. **`create_app.md`**
   - Add new application
   - With group assignments
   - ~3 minutes

4. **`oig_setup.md`**
   - Full OIG configuration
   - Governance features
   - ~15 minutes

### Customizing Scenarios

Edit prompt files to match your customer:

```bash
cd ai-assisted/prompts

# Copy template
cp create_demo_environment.md custom-financial-services.md

# Edit with customer details
vim custom-financial-services.md

# Generate
cd ..
python generate.py --file prompts/custom-financial-services.md
```

---

## Troubleshooting Demos

### Demo Won't Apply

**Issue:** `terraform apply` fails

**Solutions:**

1. **Check authentication:**
   ```bash
   # Verify environment variables set
   echo $TF_VAR_okta_api_token
   echo $TF_VAR_okta_org_name
   ```

2. **Validate syntax:**
   ```bash
   terraform fmt
   terraform validate
   ```

3. **Check for conflicts:**
   ```bash
   # User already exists?
   # Group name taken?
   # Review error message carefully
   ```

### Demo Shows Wrong Org

**Issue:** Resources appear in wrong Okta org

**Cause:** Wrong environment secrets used

**Solution:**
```bash
# Verify environment
cd environments/demo/terraform
terraform init
# Check backend configuration

# Verify workflow used correct environment
# Check GitHub Actions logs
```

### Can't Find Created Resources

**Issue:** Applied successfully but can't see in Okta

**Solutions:**

1. **Check correct org:**
   - Verify you're logged into right Okta org
   - URL should match `OKTA_ORG_NAME`

2. **Refresh page:**
   - Sometimes takes a few seconds to appear

3. **Check terraform state:**
   ```bash
   terraform state list
   # Shows what Terraform created
   ```

### Demo is Slow

**Issue:** `terraform apply` takes too long

**Solutions:**

1. **Use targeted applies:**
   ```bash
   # Apply users first
   terraform apply -target=okta_user.all

   # Then groups
   terraform apply -target=okta_group.all

   # Then apps
   terraform apply
   ```

2. **Reduce scope:**
   - Fewer users for demo (5 instead of 50)
   - Fewer resources overall
   - Focus on key features

3. **Pre-build demos:**
   - Build ahead of time
   - Demo is just showing existing environment

---

## Cleaning Up Demos

### Destroy Everything

```bash
cd environments/demo/terraform

# Preview what will be deleted
terraform plan -destroy

# Delete all resources
terraform destroy
```

Type `yes` when prompted.

**Time:** 2-5 minutes

### Selective Cleanup

```bash
# Delete specific resources
terraform destroy -target=okta_user.demo_user1

# Delete all users but keep apps
terraform destroy -target=okta_user
```

### Reset for Next Demo

```bash
# Destroy and re-create
terraform destroy
terraform apply

# Or, if using git:
git checkout demo-v1  # Previous demo state
terraform apply
```

---

## Quick Reference

### Demo Building Checklist

Before customer demo:
- [ ] Demo environment exists and works
- [ ] Tested terraform apply (no errors)
- [ ] Resources visible in Okta Admin Console
- [ ] Backup plan ready (screenshots)
- [ ] Terminal and browser tabs open
- [ ] Authentication tokens valid
- [ ] Talking points prepared

### Command Cheat Sheet

```bash
# Plan changes (preview)
terraform plan

# Apply changes (create resources)
terraform apply

# Destroy everything
terraform destroy

# Format code
terraform fmt

# Validate syntax
terraform validate

# Show current state
terraform show

# List resources
terraform state list

# Generate with AI
python ai-assisted/generate.py --interactive
```

---

## Next Steps

**After building your first demo:**

1. **Learn GitOps workflow:**
   → [01-GETTING-STARTED.md](./docs/01-GETTING-STARTED.md#making-your-first-change)

2. **Understand architecture:**
   → [02-ARCHITECTURE.md](./docs/02-ARCHITECTURE.md)

3. **Explore OIG features:**
   → [04-OIG-FEATURES.md](./docs/04-OIG-FEATURES.md)

4. **Master workflows:**
   → [03-WORKFLOWS-GUIDE.md](./docs/03-WORKFLOWS-GUIDE.md)

5. **Build advanced demos:**
   → [ai-assisted/README.md](./ai-assisted/README.md)

---

## Resources

**Example Demos:**
- [Working Repository](https://github.com/joevanhorn/okta-terraform-complete-demo) - See real demos in action

**Documentation:**
- [QUICKSTART.md](./QUICKSTART.md) - First-time setup
- [docs/01-GETTING-STARTED.md](./docs/01-GETTING-STARTED.md) - Making changes
- [docs/03-WORKFLOWS-GUIDE.md](./docs/03-WORKFLOWS-GUIDE.md) - Workflow reference

**Learning Resources:**
- [Terraform Okta Provider Docs](https://registry.terraform.io/providers/okta/okta/latest/docs)
- [Okta Developer Docs](https://developer.okta.com/)
- [Terraform Learn](https://learn.hashicorp.com/terraform)

**Community:**
- [GitHub Discussions](https://github.com/joevanhorn/okta-terraform-demo-template/discussions)
- [GitHub Issues](https://github.com/joevanhorn/okta-terraform-demo-template/issues)

---

## Summary

**Three ways to build demos:**

1. **AI-Assisted (Fastest):** 10 minutes
   - Use AI to generate code
   - Best for common scenarios
   - Minimal Terraform knowledge needed

2. **Manual (Custom):** 30-60 minutes
   - Write Terraform yourself
   - Full control over demo
   - Great for learning

3. **Hybrid (Balanced):** 20 minutes
   - Generate base with AI
   - Customize manually
   - Best of both worlds

**Choose based on:**
- Time available
- Customization needed
- Terraform experience
- Demo complexity

**Start here:** [Quick Demo Builder](#quick-demo-builder-ai-assisted) for fastest results!
