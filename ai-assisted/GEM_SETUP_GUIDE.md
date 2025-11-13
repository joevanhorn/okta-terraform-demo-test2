# Gemini Gem Setup Guide: Okta Terraform Code Generator

This guide walks you through creating your own personalized Gemini Gem for generating Okta Terraform code. Once set up, you'll have a custom AI assistant that knows all the patterns and rules for this repository.

---

## Table of Contents

1. [What is a Gemini Gem?](#what-is-a-gemini-gem)
2. [Why Use a Gem vs Other Methods?](#why-use-a-gem-vs-other-methods)
3. [Prerequisites](#prerequisites)
4. [Step-by-Step Setup](#step-by-step-setup)
5. [Using Your Gem](#using-your-gem)
6. [Example Prompts](#example-prompts)
7. [Troubleshooting](#troubleshooting)
8. [Updating Your Gem](#updating-your-gem)

---

## What is a Gemini Gem?

**Google Gems** are customized versions of Gemini that you create for specific tasks. Think of them as your personal AI experts that:

- Remember specialized instructions permanently
- Have context about your specific use case
- Can be accessed from any device via gemini.google.com
- Can be shared with your team
- Don't require any local software installation

**For this repository:** Your Gem will be an expert Terraform code generator that knows all the Okta patterns, naming conventions, and gotchas.

---

## Why Use a Gem vs Other Methods?

### Comparison of Three Tiers

| Feature | Tier 1: Manual | Tier 2: CLI Tool | Tier 3: Gemini Gem |
|---------|----------------|------------------|--------------------|
| **Setup Time** | 0 min (just copy/paste) | 20 min (Python install) | 15 min (one-time Gem setup) |
| **Per-Task Time** | 10-15 min | 2 min | 1 min |
| **Local Software** | None | Python, pip packages | None |
| **API Key Needed** | No (use free AI) | Yes | Yes |
| **Context Memory** | Manual paste each time | Automatic | Automatic (Gem remembers) |
| **Team Sharing** | Copy docs | Share scripts | Share Gem link |
| **Best For** | Learning, one-offs | Automation, bulk tasks | Quick demos, non-technical users |

**Gem is ideal if you:**
- Generate code frequently (multiple times per week)
- Don't want to install Python locally
- Want to share with Solutions Engineers who aren't developers
- Need access from any device (laptop, tablet, phone)

---

## Prerequisites

1. **Google Account** - Free or paid (Gemini Advanced recommended but not required)
2. **Gemini API Key** - Get from [Google AI Studio](https://aistudio.google.com/app/apikey)
   - Free tier: 60 requests/minute
   - Cost: ~$3-10/month for typical SE usage
3. **Basic Okta knowledge** - Understand users, groups, apps
4. **5-10 minutes** for setup

---

## Step-by-Step Setup

### Step 1: Create the Gem

1. **Go to Gemini:**
   - Navigate to [gemini.google.com](https://gemini.google.com)
   - Sign in with your Google account

2. **Open Gem Manager:**
   - Look for "Gem manager" in the top right (may be under profile menu)
   - Or visit: [gemini.google.com/gems](https://gemini.google.com/gems)

3. **Create New Gem:**
   - Click "Create new Gem" or "+ New Gem"
   - You'll see a configuration screen

### Step 2: Configure Basic Settings

1. **Name Your Gem:**
   ```
   Okta Terraform Generator
   ```

2. **Description (optional):**
   ```
   Expert Terraform code generator for Okta Identity and Governance resources.
   Generates production-ready HCL code following GitOps best practices.
   ```

3. **Choose Icon:**
   - Select an icon (e.g., gear, code, or settings icon)

### Step 3: Add Instructions

1. **Copy the instruction file:**
   - Open: `ai-assisted/GEM_INSTRUCTIONS.md`
   - Copy the ENTIRE file contents

2. **Paste into "Instructions" field:**
   - In the Gem configuration, find the "Instructions" text box
   - Paste all content from `GEM_INSTRUCTIONS.md`
   - This tells the Gem how to generate Terraform code

**Note:** The instructions field may have a character limit. If it's too long, use a condensed version (see Troubleshooting section).

### Step 4: Add Knowledge Files (Optional but Recommended)

Google Gems can accept knowledge files to provide additional context.

**Option A: Upload Quick Reference (Recommended)**
1. Find "Add knowledge" or "Upload files" section
2. Upload: `ai-assisted/GEM_QUICK_REFERENCE.md`
3. This gives the Gem condensed examples and patterns

**Option B: Upload Full Context (If space allows)**
If the Gem allows multiple files or larger uploads:
1. Upload: `ai-assisted/context/repository_structure.md`
2. Upload: `ai-assisted/context/terraform_examples.md`
3. Upload: `ai-assisted/context/okta_resource_guide.md`

**Option C: No uploads (Instructions only)**
If you can't upload files, just the instructions are sufficient for most use cases.

### Step 5: Configure Settings

1. **Temperature/Creativity (if available):**
   - Set to **Low** or **Deterministic**
   - We want consistent, predictable code output

2. **Response Length (if available):**
   - Set to **Medium** or **Long**
   - Terraform code can be verbose

3. **Model (if available):**
   - Use **Gemini 1.5 Pro** or **Gemini 2.0** (latest available)

### Step 6: Test Your Gem

1. **Save the Gem**
   - Click "Save" or "Create Gem"

2. **Send a test prompt:**
   ```
   Create 3 engineering users and an Engineering Team group
   ```

3. **Verify output:**
   - Should generate valid Terraform code
   - Should use `status = "ACTIVE"`
   - Should use `$$` for template strings
   - Should include comments

**Expected output:**
```hcl
# Engineering team members
resource "okta_user" "engineer1" {
  email      = "engineer1@example.com"
  first_name = "Alice"
  last_name  = "Engineer"
  login      = "engineer1@example.com"
  department = "Engineering"
  title      = "Software Engineer"
  status     = "ACTIVE"
}
# ... more users and group ...
```

### Step 7: Bookmark Your Gem

1. **Find your Gem in Gem Manager**
2. **Bookmark or pin** for quick access
3. **Share link** with team members (if sharing is enabled)

---

## Using Your Gem

### Basic Usage

1. **Access your Gem:**
   - Go to [gemini.google.com/gems](https://gemini.google.com/gems)
   - Click on "Okta Terraform Generator"

2. **Enter a natural language prompt:**
   ```
   Create 5 marketing users, a Marketing Team group, and a Salesforce OAuth app
   ```

3. **Copy the generated code:**
   - Gem outputs Terraform HCL code
   - Copy and paste into appropriate `.tf` files

4. **Apply the code:**
   ```bash
   cd environments/mycompany/terraform
   # Paste code into users.tf, groups.tf, apps.tf
   terraform fmt
   terraform validate
   terraform plan
   terraform apply
   ```

### Advanced Usage

**Specify environment and file:**
```
Create 3 users for the production environment.
Save to: environments/production/terraform/users.tf
```

**Request specific patterns:**
```
Create a SPA OAuth app for a React dashboard with PKCE enabled
```

**Ask for explanations:**
```
Create a GitHub OAuth app and explain the security settings
```

**Multi-resource requests:**
```
Create a complete demo with:
- 5 users (3 engineering, 2 marketing)
- 2 groups (Engineering, Marketing)
- 1 GitHub app for engineering
- 1 Salesforce app for marketing
```

---

## Example Prompts

### Users

```
Create 3 users in the sales department
```

```
Add a new user: John Doe, john.doe@example.com, Engineering, Senior Engineer
```

### Groups

```
Create an Administrators group and add 2 admin users
```

```
Create a Contractors group for temporary employees
```

### OAuth Applications

```
Create a Single Page Application for our React admin dashboard
```

```
Create a Salesforce OAuth integration for the marketing team
```

```
Create a backend service app for our payment processing API
```

### OIG Features

```
Create an entitlement bundle called "Engineering Tools Access"
```

```
Create a quarterly access review campaign for Q1 2025
```

```
Create 3 department bundles: Marketing, Sales, Engineering
```

### Complete Demos

```
Create a complete demo environment with:
- 10 users across 3 departments
- Department groups
- A GitHub OAuth app
- Access review campaigns
```

---

## Troubleshooting

### Problem: Instructions Too Long for Gem

**Solution:** Use condensed version
1. Create a new file: `GEM_INSTRUCTIONS_CONDENSED.md`
2. Copy only the "Critical Rules" and "Core Directive" sections from `GEM_INSTRUCTIONS.md`
3. Add: "Refer to uploaded knowledge file for detailed patterns"
4. Upload `GEM_QUICK_REFERENCE.md` as knowledge file

### Problem: Gem Generates Invalid Code

**Common issues:**

1. **Template strings using single `$`:**
   - Remind Gem: "Always use double dollar signs: `$$`"
   - Regenerate: "Fix the template strings to use $$"

2. **Missing `status = "ACTIVE"`:**
   - Regenerate: "Add status = ACTIVE to all resources"

3. **Wrong OAuth visibility settings:**
   - Regenerate: "Fix OAuth app to use hide_ios=true and login_mode=DISABLED"

### Problem: Gem Output Includes Explanations

**Solution:** Modify prompt
```
Create 3 users. Output only code, no explanations.
```

Or update Gem instructions to emphasize:
```
Output ONLY Terraform code unless explicitly asked for explanations.
```

### Problem: Can't Upload Knowledge Files

**Solution:** Paste key patterns into instructions
1. Copy critical patterns from `GEM_QUICK_REFERENCE.md`
2. Add to bottom of instructions field
3. Focus on most common patterns (users, groups, apps)

### Problem: Gem Generates Placeholder Values

**Example:** `redirect_uris = ["https://YOUR-DOMAIN.com/callback"]`

**Solution:** Be specific in prompts
```
Create Salesforce app with redirect URI: https://login.salesforce.com/services/oauth2/callback
```

Or regenerate:
```
Replace placeholder values with realistic example.com URLs
```

### Problem: Gem Doesn't Remember Context Across Sessions

**This is expected:** Gems remember instructions, not conversation history.

**Workaround:** Include context in each prompt:
```
For environment: production
Create 3 users in Engineering department
```

---

## Updating Your Gem

As this repository evolves, you may need to update your Gem.

### When to Update

- New Terraform patterns added
- New resource types supported
- Critical rule changes
- Bug fixes in instructions

### How to Update

1. **Go to Gem Manager:**
   - [gemini.google.com/gems](https://gemini.google.com/gems)

2. **Edit your Gem:**
   - Find "Okta Terraform Generator"
   - Click "Edit" or settings icon

3. **Update instructions:**
   - Replace with new `GEM_INSTRUCTIONS.md` content
   - Or add incremental changes

4. **Update knowledge files:**
   - Remove old files
   - Upload new versions

5. **Test changes:**
   - Send test prompts
   - Verify output still valid

---

## Sharing Your Gem with Team

### Option 1: Direct Sharing (Google Workspace)

If using Google Workspace:
1. Open Gem settings
2. Click "Share"
3. Add team members by email
4. They get instant access

### Option 2: Export Instructions

For non-Google Workspace users:
1. Share the `GEM_INSTRUCTIONS.md` file
2. Share the `GEM_QUICK_REFERENCE.md` file
3. Team members create their own Gems using these files

### Option 3: Team Gem Template

Create a shared document with:
- Link to this setup guide
- Your customizations or tips
- Team-specific examples
- Common prompts for your org

---

## Cost Considerations

### Gemini API Pricing (as of 2025)

**Free Tier:**
- 60 requests per minute
- Sufficient for most individual SE usage

**Paid Tier (Gemini Advanced):**
- Higher rate limits
- Priority access to latest models
- ~$20/month for Google One AI Premium

**Typical Usage Costs:**
- Casual use (5-10 generations/week): **Free tier sufficient**
- Heavy use (50+ generations/week): **~$3-10/month in API costs**
- Team use (5 people): **~$15-50/month total**

**Compare to:**
- Manual time saved: 2-3 hours/week = **$200-400/week** in SE time
- Python automation: Free but requires setup/maintenance

**ROI:** Even with paid tier, Gem pays for itself in first hour of use.

---

## Tips for Best Results

### 1. Be Specific in Prompts

```
# ❌ Vague
Create some users

# ✅ Specific
Create 5 users in the Marketing department with realistic names and titles
```

### 2. Specify File Targets

```
Create 3 users for environments/demo/terraform/users.tf
```

### 3. Request Validation

```
Create a GitHub app and validate OAuth settings
```

### 4. Iterate on Output

```
[After getting code]
Add 2 more users to the Engineering group
```

### 5. Use Context from Previous Output

```
[After creating users]
Now create group memberships for those users in an Engineering Team group
```

---

## Next Steps

1. ✅ **Set up your Gem** (15 minutes)
2. ✅ **Test with simple prompts** (5 minutes)
3. ✅ **Generate your first demo** (10 minutes)
4. ✅ **Share with team** (optional)
5. ✅ **Provide feedback** (file issues in repo)

**Ready to create your Gem?** Follow [Step-by-Step Setup](#step-by-step-setup) above!

---

## Additional Resources

- **Google Gems Documentation:** [blog.google/products/gemini/google-gems-tips/](https://blog.google/products/gemini/google-gems-tips/)
- **Gemini API Pricing:** [ai.google.dev/pricing](https://ai.google.dev/pricing)
- **Repository Documentation:** `../docs/` folder
- **Prompt Templates:** `../prompts/` folder (for manual reference)

---

## Support

**Questions or issues?**
1. Check [Troubleshooting](#troubleshooting) section above
2. Review `ai-assisted/README.md` for Tier 1 and Tier 2 alternatives
3. File an issue in the repository
4. Share learnings with team

**Happy Terraform generating!** 🎉
