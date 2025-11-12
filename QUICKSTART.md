# Quick Start - Your First Success in 10 Minutes

**Goal:** Import your Okta organization into code and see the results.

**No prior knowledge required** - Just follow these steps!

---

## ⏱️ Time Breakdown

- Prerequisites check: 2 minutes
- GitHub setup: 3 minutes
- Run import: 2 minutes
- View results: 3 minutes

**Total: ~10 minutes**

---

## Step 1: Prerequisites Check (2 minutes)

Make sure you have these ready:

### ✅ Checklist

- [ ] **GitHub account** with Actions enabled (free tier is fine)
- [ ] **Okta organization** (any of these):
  - Okta developer account (free: https://developer.okta.com/signup/)
  - Preview organization
  - Production organization
- [ ] **Okta API token** with these permissions:
  - `okta.groups.manage`
  - `okta.users.manage`
  - `okta.apps.manage`
  - `okta.governance.*` (if using OIG features)

### 🔑 How to Get Your Okta API Token

If you don't have one yet:

1. Log into your Okta Admin Console
2. Go to **Security → API → Tokens**
3. Click **Create Token**
4. Name it: `Terraform GitOps`
5. Click **Create Token**
6. **⚠️ IMPORTANT:** Copy the token immediately - you won't see it again!

**Save these values** (you'll need them in Step 2):
- Your API token
- Your org name (e.g., `dev-12345678` from `dev-12345678.okta.com`)
- Your base URL (e.g., `okta.com` or `oktapreview.com`)

---

## Step 2: GitHub Setup (3 minutes)

### 2.1 Use This Template

Click the green **"Use this template"** button at the top of this page.

**Settings:**
- **Owner:** Your GitHub username
- **Repository name:** `my-okta-gitops` (or your choice)
- **Visibility:** Private (recommended)

Click **"Create repository from template"**

### 2.2 Create GitHub Environment

In your new repository:

1. Go to **Settings** (top menu)
2. In left sidebar, click **Environments**
3. Click **New environment**
4. Name it: `MyFirstEnvironment` (you can rename later)
5. Click **Configure environment**

### 2.3 Add Secrets

Now add your Okta credentials as secrets:

**Secret 1:**
- Click **Add secret**
- Name: `OKTA_API_TOKEN`
- Value: [Paste your API token from Step 1]
- Click **Add secret**

**Secret 2:**
- Click **Add secret**
- Name: `OKTA_ORG_NAME`
- Value: [Your org name, e.g., `dev-12345678`]
- Click **Add secret**

**Secret 3:**
- Click **Add secret**
- Name: `OKTA_BASE_URL`
- Value: [Your base URL]
  - `okta.com` for production orgs
  - `oktapreview.com` for preview orgs
  - `okta-emea.com` for EMEA orgs
- Click **Add secret**

**✅ Verification:** You should see 3 secrets listed under your environment.

---

## Step 3: Run Your First Import (2 minutes)

Now let's import your Okta resources into code!

### 3.1 Navigate to Actions

1. Click **Actions** tab (top of repository)
2. You'll see a list of workflows on the left

### 3.2 Run Import Workflow

1. In left sidebar, click **Import All OIG Resources**
2. On the right, click **Run workflow** dropdown button
3. Fill in the form:
   - **Branch:** `main` (default)
   - **Tenant Environment:** `MyFirstEnvironment` (must match your environment name)
   - **Update Terraform:** `true` (checkbox checked)
   - **Commit Changes:** `false` (leave unchecked for first run)
4. Click green **Run workflow** button

### 3.3 Watch Progress

1. A new workflow run appears at the top
2. Click on it to watch progress
3. You'll see steps executing:
   - Set up job
   - Import entitlement bundles
   - Import access reviews
   - Sync resource owners
   - Sync governance labels
   - Upload artifacts

**Expected time:** 2-5 minutes depending on your org size

**✅ Success looks like:** All steps show green checkmarks

---

## Step 4: View Your Results (3 minutes)

Your Okta resources are now in code! Let's see what was created.

### 4.1 Download Artifacts (Optional)

If you set `commit_changes=false`:

1. Scroll to bottom of workflow run page
2. Under **Artifacts** section, click **terraform-files**
3. Extract the downloaded zip file
4. You'll see:
   - `oig_entitlements.tf` - Your entitlement bundles
   - `oig_reviews.tf` - Your access review campaigns
   - `owner_mappings.json` - Resource owners
   - `label_mappings.json` - Governance labels

### 4.2 View in Repository (If Committed)

If you used `commit_changes=true`:

1. Go to **Code** tab
2. Navigate to `environments/myfirstenvironment/`
3. Explore the generated files:

```
environments/myfirstenvironment/
├── terraform/
│   ├── oig_entitlements.tf    # Your entitlement bundles as Terraform
│   └── oig_reviews.tf          # Your access reviews as Terraform
├── config/
│   ├── owner_mappings.json     # Resource ownership assignments
│   └── label_mappings.json     # Governance labels
└── imports/
    ├── entitlements.json       # Raw API data (for reference)
    └── reviews.json            # Raw API data (for reference)
```

### 4.3 Understand What You Have

**Terraform Files** (`terraform/*.tf`):
- Infrastructure as Code representation of your Okta resources
- Can be version controlled, reviewed, and managed via GitOps
- Can be applied to recreate or update resources

**Config Files** (`config/*.json`):
- API-managed resources (not yet in Terraform provider)
- Resource owners and governance labels
- Applied via Python scripts and workflows

**Import Files** (`imports/*.json`):
- Raw API responses for reference
- Used for troubleshooting and audit trail

---

## 🎉 Success! What You Just Did

1. ✅ Created your own GitOps repository from template
2. ✅ Configured GitHub Environment with Okta credentials
3. ✅ Imported your entire Okta organization into code
4. ✅ Generated Terraform configurations for OIG resources
5. ✅ Created configuration files for governance features

**Your Okta organization is now managed as code!**

---

## What's Next?

### Immediate Next Steps

1. **Review Generated Files**
   - Look at the Terraform code generated
   - Understand what resources you have
   - Check for any TODOs that need attention

2. **Set Up Terraform State Backend**
   - Choose between AWS S3, Terraform Cloud, or local
   - See [Backend Setup Wizard](docs/BACKEND_SETUP_WIZARD.md)
   - Required before applying changes

3. **Initialize Terraform**
   ```bash
   cd environments/myfirstenvironment/terraform
   terraform init
   terraform plan
   ```

### Learning Path

**New to this repo?** Follow this path:

1. **[Getting Started Guide](docs/01-GETTING-STARTED.md)** ← Start here
   - Understanding the environment structure
   - Making your first change
   - GitOps workflow basics

2. **[Architecture Overview](docs/02-ARCHITECTURE.md)**
   - How everything fits together
   - Three-layer resource management model
   - State management

3. **[Workflows Guide](docs/03-WORKFLOWS-GUIDE.md)**
   - Which workflow to use when
   - Workflow decision tree
   - Common operations

4. **[OIG Features](docs/04-OIG-FEATURES.md)**
   - Entitlement bundles
   - Access reviews
   - Governance labels and owners

### Common Next Actions

**Want to make changes?**
→ [Making Your First Change](docs/01-GETTING-STARTED.md#making-your-first-change)

**Want to understand workflows?**
→ [Workflows Guide](docs/03-WORKFLOWS-GUIDE.md)

**Want to apply changes to Okta?**
→ [Terraform Operations](docs/01-GETTING-STARTED.md#applying-changes)

**Want to manage governance features?**
→ [Label Management](docs/LABEL_WORKFLOW_GUIDE.md)

**Having issues?**
→ [Troubleshooting Guide](docs/05-TROUBLESHOOTING.md)

---

## Troubleshooting

### Issue: Workflow failed with "Environment not found"

**Cause:** Environment name doesn't match

**Solution:**
1. Check environment name in Settings → Environments
2. Must match exactly (case-insensitive)
3. Re-run workflow with correct name

### Issue: Workflow failed with "401 Unauthorized"

**Cause:** Invalid API token or wrong secrets

**Solution:**
1. Verify token is still valid in Okta Admin Console
2. Check you added secrets to ENVIRONMENT (not repository)
3. Regenerate token if expired
4. Update GitHub Environment secret

### Issue: Import succeeded but no files generated

**Cause:** No OIG resources in your Okta org

**Solution:**
- This is normal if OIG is not enabled
- Check Okta Admin Console for Identity Governance features
- See [OIG Prerequisites](OIG_PREREQUISITES.md) for setup

### Issue: Can't find downloaded artifacts

**Cause:** Artifacts expire after 90 days, or you need to scroll down

**Solution:**
1. Scroll to bottom of workflow run page
2. Look for "Artifacts" section
3. If not there, re-run import workflow

---

## Quick Reference

### Re-run Import Anytime

```bash
# Via GitHub CLI
gh workflow run import-all-resources.yml \
  -f tenant_environment=MyFirstEnvironment \
  -f update_terraform=true \
  -f commit_changes=true

# Or use GitHub web UI:
# Actions → Import All OIG Resources → Run workflow
```

### Check Secrets Are Configured

```bash
# Via GitHub CLI
gh secret list -e MyFirstEnvironment

# Should show:
# OKTA_API_TOKEN
# OKTA_ORG_NAME
# OKTA_BASE_URL
```

### Import Different Environment

Create a new GitHub Environment and run import again with different environment name.

---

## Support

**Questions?**
- Check [Troubleshooting Guide](docs/05-TROUBLESHOOTING.md)
- Review [Getting Started](docs/01-GETTING-STARTED.md)
- See [GitHub Discussions](https://github.com/joevanhorn/okta-terraform-demo-template/discussions)
- Create an [Issue](https://github.com/joevanhorn/okta-terraform-demo-template/issues)

**See it in action:**
- [Working Example Repository](https://github.com/joevanhorn/okta-terraform-complete-demo) - Fully configured demo environment

---

## Detailed Setup

Need more details? See [TEMPLATE_SETUP.md](TEMPLATE_SETUP.md) for comprehensive setup guide with:
- Detailed prerequisites
- AWS backend setup
- Complete configuration steps
- Advanced options
- Testing and validation

---

**Ready?** Let's go! → [Step 1: Prerequisites Check](#step-1-prerequisites-check-2-minutes)
