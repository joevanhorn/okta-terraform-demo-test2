# Documentation Guide

Welcome to the Okta Terraform GitOps Template documentation!

## 🗺️ Documentation Map

### Just Getting Started?

**Start here:**
1. **[QUICKSTART.md](../QUICKSTART.md)** ← Absolute beginner? Start here! (10 minutes)
2. **[01-GETTING-STARTED.md](./01-GETTING-STARTED.md)** ← After initial setup (30 minutes)
3. **[03-WORKFLOWS-GUIDE.md](./03-WORKFLOWS-GUIDE.md)** ← Learn which workflow to use when

### By User Type

#### 🎯 Sales Engineers / Demo Builders
**Goal:** Quickly create demo environments

**Path:**
1. [QUICKSTART.md](../QUICKSTART.md) - Import existing org
2. [DEMO_BUILD_GUIDE.md](../DEMO_BUILD_GUIDE.md) - Build demo scenarios
3. [ai-assisted/README.md](../ai-assisted/README.md) - AI-assisted code generation

#### 👨‍💻 Developers / Engineers
**Goal:** Understand architecture and make changes

**Path:**
1. [01-GETTING-STARTED.md](./01-GETTING-STARTED.md) - First steps
2. [02-ARCHITECTURE.md](./02-ARCHITECTURE.md) - How everything works
3. [GITOPS_WORKFLOW.md](./GITOPS_WORKFLOW.md) - GitOps patterns
4. [API_MANAGEMENT.md](./API_MANAGEMENT.md) - Python scripts reference

#### 🔧 Operations / DevOps
**Goal:** Deploy and maintain in production

**Path:**
1. [AWS_BACKEND_SETUP.md](./AWS_BACKEND_SETUP.md) - State backend setup
2. [BACKEND_SETUP_WIZARD.md](./BACKEND_SETUP_WIZARD.md) - Choose backend type
3. [05-TROUBLESHOOTING.md](./05-TROUBLESHOOTING.md) - Fix issues
4. [ROLLBACK_GUIDE.md](./ROLLBACK_GUIDE.md) - Disaster recovery

---

## 📚 Core Documentation

### Getting Started (Read First)

| Document | Purpose | Time | Audience |
|----------|---------|------|----------|
| [QUICKSTART.md](../QUICKSTART.md) | 10-minute first success | 10 min | Everyone |
| [01-GETTING-STARTED.md](./01-GETTING-STARTED.md) | First steps after setup | 30 min | Everyone |
| [TEMPLATE_SETUP.md](../TEMPLATE_SETUP.md) | Detailed setup guide | 1 hour | New users |

### Understanding the System

| Document | Purpose | Time | Audience |
|----------|---------|------|----------|
| [02-ARCHITECTURE.md](./02-ARCHITECTURE.md) | How it all works | 20 min | Developers |
| [DIRECTORY_GUIDE.md](../DIRECTORY_GUIDE.md) | Environment structure | 10 min | Everyone |
| [../CLAUDE.md](../CLAUDE.md) | AI assistant guidance | 15 min | Developers |

### Working with the System

| Document | Purpose | Time | Audience |
|----------|---------|------|----------|
| [03-WORKFLOWS-GUIDE.md](./03-WORKFLOWS-GUIDE.md) | Which workflow when | 15 min | Everyone |
| [GITOPS_WORKFLOW.md](./GITOPS_WORKFLOW.md) | GitOps patterns | 30 min | Developers |
| [WORKFLOWS.md](./WORKFLOWS.md) | Workflow reference | 20 min | Advanced |

### OIG Features

| Document | Purpose | Time | Audience |
|----------|---------|------|----------|
| [04-OIG-FEATURES.md](./04-OIG-FEATURES.md) | OIG overview | 20 min | Everyone |
| [../OIG_PREREQUISITES.md](../OIG_PREREQUISITES.md) | OIG setup requirements | 10 min | Admins |
| [LABEL_WORKFLOW_GUIDE.md](./LABEL_WORKFLOW_GUIDE.md) | Label management | 15 min | Operators |
| [TERRAFORMER_OIG_FAQ.md](./TERRAFORMER_OIG_FAQ.md) | Import limitations | 10 min | Developers |

### Troubleshooting

| Document | Purpose | Time | Audience |
|----------|---------|------|----------|
| [05-TROUBLESHOOTING.md](./05-TROUBLESHOOTING.md) | Common issues | Ref | Everyone |
| [TROUBLESHOOTING_ENTITLEMENT_BUNDLES.md](./TROUBLESHOOTING_ENTITLEMENT_BUNDLES.md) | Bundle-specific | Ref | Developers |
| [ROLLBACK_GUIDE.md](./ROLLBACK_GUIDE.md) | Disaster recovery | Ref | Operators |
| [LESSONS_LEARNED.md](./LESSONS_LEARNED.md) | Known issues | Ref | Developers |

---

## 🔧 Reference Documentation

### Backend & Infrastructure

- [AWS_BACKEND_SETUP.md](./AWS_BACKEND_SETUP.md) - S3/DynamoDB state backend
- [BACKEND_SETUP_WIZARD.md](./BACKEND_SETUP_WIZARD.md) - Choose backend type
- [VERSION_COMPATIBILITY.md](./VERSION_COMPATIBILITY.md) - Version matrix

### API & Scripts

- [API_MANAGEMENT.md](./API_MANAGEMENT.md) - Python scripts (1190+ lines)
- [LABELS_API_VALIDATION.md](./LABELS_API_VALIDATION.md) - Labels API investigation
- [../scripts/README.md](../scripts/README.md) - Scripts reference

### Advanced Topics

- [TERRAFORM_RESOURCES.md](./TERRAFORM_RESOURCES.md) - Provider resources
- [COMPLETE_SOLUTION.md](./COMPLETE_SOLUTION.md) - Full architecture
- [PROJECT_STRUCTURE.md](./PROJECT_STRUCTURE.md) - Repository layout

### Contributing

- [CONTRIBUTING.md](./CONTRIBUTING.md) - Contribution guide
- [FORKING_GUIDE.md](./FORKING_GUIDE.md) - How to fork
- [TESTING.md](./TESTING.md) - Testing approach
- [../SECURITY.md](../SECURITY.md) - Security best practices

---

## 🔍 Find Documentation By Topic

### "I want to..."

**Import resources from Okta**
→ [QUICKSTART.md Step 3](../QUICKSTART.md#step-3-run-your-first-import-2-minutes)
→ [03-WORKFLOWS-GUIDE.md](./03-WORKFLOWS-GUIDE.md#import-resources)

**Make my first change**
→ [01-GETTING-STARTED.md#making-your-first-change](./01-GETTING-STARTED.md#making-your-first-change)

**Understand the architecture**
→ [02-ARCHITECTURE.md](./02-ARCHITECTURE.md)

**Set up AWS backend**
→ [BACKEND_SETUP_WIZARD.md](./BACKEND_SETUP_WIZARD.md)
→ [AWS_BACKEND_SETUP.md](./AWS_BACKEND_SETUP.md)

**Manage governance labels**
→ [LABEL_WORKFLOW_GUIDE.md](./LABEL_WORKFLOW_GUIDE.md)

**Fix a problem**
→ [05-TROUBLESHOOTING.md](./05-TROUBLESHOOTING.md)

**Recover from a mistake**
→ [ROLLBACK_GUIDE.md](./ROLLBACK_GUIDE.md)

**Build a demo**
→ [DEMO_BUILD_GUIDE.md](../DEMO_BUILD_GUIDE.md)

**Use AI to generate code**
→ [ai-assisted/README.md](../ai-assisted/README.md)

---

## 📖 Reading Order Recommendations

### Path 1: Quick Start (30 minutes)
Best for: Getting started fast

1. [QUICKSTART.md](../QUICKSTART.md) - 10 min
2. [01-GETTING-STARTED.md](./01-GETTING-STARTED.md) - 20 min
3. Done! Come back when you need more

### Path 2: Comprehensive (2 hours)
Best for: Understanding everything

1. [QUICKSTART.md](../QUICKSTART.md) - 10 min
2. [TEMPLATE_SETUP.md](../TEMPLATE_SETUP.md) - 30 min
3. [02-ARCHITECTURE.md](./02-ARCHITECTURE.md) - 20 min
4. [03-WORKFLOWS-GUIDE.md](./03-WORKFLOWS-GUIDE.md) - 15 min
5. [GITOPS_WORKFLOW.md](./GITOPS_WORKFLOW.md) - 30 min
6. [04-OIG-FEATURES.md](./04-OIG-FEATURES.md) - 20 min

### Path 3: Demo Builder (1 hour)
Best for: Creating demos quickly

1. [QUICKSTART.md](../QUICKSTART.md) - 10 min
2. [DEMO_BUILD_GUIDE.md](../DEMO_BUILD_GUIDE.md) - 30 min
3. [ai-assisted/README.md](../ai-assisted/README.md) - 20 min

### Path 4: Production Deployment (3 hours)
Best for: Deploying to production

1. [TEMPLATE_SETUP.md](../TEMPLATE_SETUP.md) - 30 min
2. [BACKEND_SETUP_WIZARD.md](./BACKEND_SETUP_WIZARD.md) - 15 min
3. [AWS_BACKEND_SETUP.md](./AWS_BACKEND_SETUP.md) - 45 min
4. [02-ARCHITECTURE.md](./02-ARCHITECTURE.md) - 20 min
5. [GITOPS_WORKFLOW.md](./GITOPS_WORKFLOW.md) - 30 min
6. [../SECURITY.md](../SECURITY.md) - 20 min
7. [05-TROUBLESHOOTING.md](./05-TROUBLESHOOTING.md) - 20 min

---

## 🆘 Quick Help

**Something not working?**
1. Check [05-TROUBLESHOOTING.md](./05-TROUBLESHOOTING.md)
2. Search [GitHub Issues](https://github.com/joevanhorn/okta-terraform-demo-template/issues)
3. Ask in [GitHub Discussions](https://github.com/joevanhorn/okta-terraform-demo-template/discussions)

**Want to see it working?**
Visit the [working example repository](https://github.com/joevanhorn/okta-terraform-complete-demo) with fully configured MyOrg environment.

**Need help getting started?**
Follow [QUICKSTART.md](../QUICKSTART.md) - it's designed for absolute beginners!

---

## 📝 Documentation Status

| Status | Meaning |
|--------|---------|
| ✅ Complete | Ready to use |
| 🚧 In Progress | Being written |
| 📝 Planned | On roadmap |

### Current Status

- ✅ QUICKSTART.md
- ✅ TEMPLATE_SETUP.md
- ✅ DIRECTORY_GUIDE.md
- ✅ OIG_PREREQUISITES.md
- 🚧 01-GETTING-STARTED.md (in progress)
- 🚧 02-ARCHITECTURE.md (in progress)
- 🚧 03-WORKFLOWS-GUIDE.md (in progress)
- 📝 04-OIG-FEATURES.md (planned)
- 📝 05-TROUBLESHOOTING.md (planned)
- 📝 BACKEND_SETUP_WIZARD.md (planned)
- 📝 LABEL_WORKFLOW_GUIDE.md (planned)
- 📝 ROLLBACK_GUIDE.md (planned)
- 📝 VERSION_COMPATIBILITY.md (planned)

---

## Contributing to Documentation

See [CONTRIBUTING.md](./CONTRIBUTING.md) for:
- Documentation standards
- How to add new docs
- Style guide
- Review process

**Found something unclear?** Please [create an issue](https://github.com/joevanhorn/okta-terraform-demo-template/issues) or submit a PR!
