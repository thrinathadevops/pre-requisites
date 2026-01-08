# The Complete Git Repository Management & Branching Strategy Guide

**A Comprehensive Resource for DevOps Engineers, Database Administrators & Team Leads**

---

## Quick Navigation

- **Start Here**: [Introduction & Scope](#introduction--scope)
- **Choose Strategy**: [Real-World Scenarios & Strategy Selection](#real-world-scenarios--strategy-selection)
- **Implement**: [Your Repository Implementation](#your-repository-implementation-master--integration-model)
- **Commands**: [Git Commands Reference](#git-commands-reference--complete)
- **Troubleshoot**: [Common Issues](#common-issues--troubleshooting-systematic-approach)

---

## Introduction & Scope

This guide provides **enterprise-grade** guidance on:

- **Selecting appropriate branching strategies** based on organizational context
- **Implementing robust CI/CD pipelines** with branch protection and automated testing
- **Managing complex workflows** (environment progression: QA → Pre-Prod → Prod)
- **Recovering from common mistakes** and understanding Git internals
- **Team adoption and change management** for Git workflows

### Who This Guide Is For

- **DevOps Engineers** building and maintaining CI/CD infrastructure
- **Database Administrators** managing schema changes and database migrations via Git
- **Development Teams** seeking structured collaboration and release management
- **Team Leads & Architects** designing development processes
- **Site Reliability Engineers (SREs)** ensuring system stability and recovery

---

## Key Concepts & Terminology

### Branch Types & Lifecycle

| Branch Type | Definition | Lifetime | Purpose |
|---|---|---|---|
| **Main / Master** | Production-ready, stable code | Long-lived | Direct production deployments; sacred branch |
| **Develop / Integration** | Staging/integration branch | Long-lived | Aggregates features; testing before release |
| **Feature** | Individual work branches | Short-lived (days-weeks) | Isolates single feature/fix; enables parallel work |
| **Release** | Pre-release stabilization | Short-lived (weeks) | Version prep, bug fixes, no new features |
| **Hotfix** | Emergency production fix | Short-lived (hours-days) | Urgent patches; never blocks release cycle |

### CI/CD Definitions

**Continuous Integration (CI)**: Automated process that validates code changes:
- Run unit tests, linting, security scans
- Fail fast on code quality issues
- Integrate changes multiple times per day (ideal)

**Continuous Deployment (CD)**: Automated process that pushes validated code to environments:
- Automatic push to staging/testing environments
- Automatic or manual push to production

---

## Branching Strategies Comparison

### Strategy at a Glance

| Strategy | Best For | Release Freq | Team Size | Complexity |
|----------|----------|---|---|---|
| **GitFlow** | Enterprise, scheduled releases | Monthly/Quarterly | 10-50+ | High |
| **GitHub Flow** | Startups, rapid iteration | Daily/Multiple per day | 2-30 | Low |
| **GitLab Flow** | Mid-market, staged deployments | Weekly/Daily | 5-50 | Medium |
| **Trunk-Based** | High-scale, continuous deploy | Multiple per day | 5-100+ | Hard (discipline) |
| **Feature-Branch** | All teams (component pattern) | N/A | All sizes | Easy |

---

## GitFlow Workflow — Complete Guide

### Theory: The Structure Model

GitFlow is designed for projects with:
- Scheduled releases (monthly, quarterly)
- Multiple production versions supported simultaneously
- Need for stability and careful change management

### Architecture

```
main (Production)
    ↑ (merge when ready)
    |
develop (Staging)
    ↑ (merge features)
    |
feature/* branches
release/* branches
hotfix/* branches
```

### Feature Development Workflow

```bash
# Start from latest develop
git checkout develop
git pull origin develop

# Create feature branch
git checkout -b feature/TICKET-123-add-payments

# Work on feature (multiple commits encouraged)
git add .
git commit -m "TICKET-123: Implement payment API integration"
git push -u origin feature/TICKET-123-add-payments

# Create Pull Request
# After approval and CI passes, merge to develop
gh pr merge <PR-NUMBER> --merge --delete-branch
```

### Release Preparation

```bash
# When ready for release
git checkout develop
git pull origin develop
git checkout -b release/v1.2.0

# On release branch: stabilize only
git commit -am "Bump version to v1.2.0"
git commit -am "Update CHANGELOG for v1.2.0"

# Test thoroughly, fix critical bugs only
git commit -am "Fix critical bug #456"

# Push and create PR to main
git push -u origin release/v1.2.0
```

### Release to Production

```bash
# Merge release to main
git checkout main
git pull origin main
git merge --no-ff release/v1.2.0 -m "Merge release v1.2.0"

# Tag the release
git tag -a v1.2.0 -m "Release v1.2.0"
git push origin main v1.2.0

# Merge release fixes back to develop
git checkout develop
git merge --no-ff release/v1.2.0
git push origin develop
```

### Emergency Hotfix

```bash
# Create hotfix from main (not develop)
git checkout main
git pull origin main
git checkout -b hotfix/v1.2.1-payment-crash

# Fix the bug
git commit -am "HOTFIX: Payment service crash"
git push -u origin hotfix/v1.2.1-payment-crash

# Merge to BOTH main AND develop
git checkout main
git merge --no-ff hotfix/v1.2.1-payment-crash
git tag -a v1.2.1 -m "Hotfix v1.2.1"
git push origin main v1.2.1

git checkout develop
git merge --no-ff hotfix/v1.2.1-payment-crash
git push origin develop
```

### Advantages

✅ Clear release process with testing time
✅ Stability management (main always production-ready)
✅ Multiple version support (v1.x, v2.x)
✅ Hotfix handling without blocking releases
✅ Good for large teams and enterprise environments

### Disadvantages

❌ Complex with many branch types
❌ Frequent merges can cause conflicts
❌ Not ideal for continuous deployment
❌ Release coordination overhead
❌ Features wait in develop until release (slow feedback)

### When to Use

✅ **Ideal for:**
- Enterprise software with quarterly/monthly releases
- Projects supporting multiple customer versions
- Regulated industries requiring change control
- Large teams (20+ developers)
- Projects with long release cycles

❌ **Not ideal for:**
- Continuous deployment (10+ deploys per day)
- Tiny teams (2-3 people)
- Rapid prototyping
- Early-stage startups

---

## GitHub Flow Workflow — Complete Guide

### Theory: The Simplicity Model

GitHub Flow is optimized for:
- Continuous deployment (multiple times per day)
- Small, focused changes
- Strong automation (CI/CD must be excellent)
- Web applications and services

### Architecture

```
main (always deployable)
  ↑ (merge via PR when ready)
  |
feature/* branches (hours to days)
```

### Daily Workflow

```bash
# Always start from latest main
git checkout main
git pull origin main

# Create feature branch
git checkout -b feature/add-login-page

# Make changes
git add .
git commit -m "Add login form component"
git push -u origin feature/add-login-page

# Create PR
gh pr create --base main --title "Add login page"

# After CI passes and review approved:
gh pr merge <PR-NUMBER> --merge --delete-branch

# Auto-deploys to production
```

### Advantages

✅ Simplicity (one main branch)
✅ Speed (no intermediate branches)
✅ Rapid feedback (users see changes within hours)
✅ Reduced conflicts (frequent integration)
✅ Low overhead
✅ Great for continuous deployment

### Disadvantages

❌ Requires strong testing infrastructure
❌ No version management
❌ Deployment risk (main always deploying)
❌ Requires feature flags for incomplete work
❌ Not suitable for regulated environments

### When to Use

✅ **Ideal for:**
- Web applications & SaaS
- Startups with rapid iteration
- Continuous deployment (5+ deploys per day)
- Small teams (2-20 people)
- Mature CI/CD pipelines

❌ **Not ideal for:**
- Multiple production versions
- Regulated/enterprise with change control
- Large teams needing clear process
- Infrequent, high-coordination releases

---

## GitLab Flow Workflow — Complete Guide

### Theory: The Hybrid Approach

GitLab Flow combines:
- **Simplicity of GitHub Flow** (feature branches to main)
- **Environment management** of staged deployments (dev → staging → production)

### Architecture

```
feature → main → staging → production
         (auto)  (manual)  (manual)
```

### Simple Variant (Single Environment)

```bash
# Create feature off main
git checkout main
git pull origin main
git checkout -b feature/TICKET-123

# Work on feature
git add .
git commit -m "TICKET-123: Add functionality"
git push -u origin feature/TICKET-123

# Create PR to main
gh pr create --base main

# After approval and CI: merge
# Auto-deploys to staging → production (after approval)
```

### Complex Variant (Multiple Environments)

```bash
# Merge to main (triggers staging deployment)
# After staging validation: manual approval → production deployment

# Or use release branches:
git checkout main
git checkout -b release/v2.1.0

git commit -am "Bump version to v2.1.0"
git push -u origin release/v2.1.0

# PR to production after testing
```

### Advantages

✅ Flexibility (scales simple to complex)
✅ Environment staging (validation before prod)
✅ Less ceremony than GitFlow
✅ Works with feature flags
✅ Suitable for mid-sized teams

### Disadvantages

❌ Complexity for simple projects
❌ Environment drift if not automated
❌ Manual approval can slow iteration
❌ More branches to manage

### When to Use

✅ **Ideal for:**
- Medium-sized teams (5-30 people)
- Projects with staging/pre-prod environments
- Regulated industries
- Multiple deployment targets
- Product teams balancing velocity and stability

---

## Trunk-Based Development (TBD) — Complete Guide

### Theory: The Extreme Simplicity Model

Single primary branch where developers commit frequently:

```
main (THE ONLY BRANCH)
  ↑ (multiple commits per day)
  |
  ├─ c1: feature-a (tested immediately)
  ├─ c2: feature-b (tested immediately)
  ├─ c3: bugfix-c (tested immediately)
  └─ c4: feature-a (deployed immediately)
```

### Workflow

```bash
# Create ultra-short feature branch
git checkout main
git pull origin main
git checkout -b feature/cache-optimization

# Work for 1-4 hours only
git add .
git commit -m "TICKET-123: Add caching layer"
git push -u origin feature/cache-optimization

# Create PR immediately (even if WIP)
gh pr create --base main

# CI must be VERY FAST (< 10 minutes)
# Once CI passes and 1 review approved: MERGE
gh pr merge <PR-NUMBER> --squash --delete-branch

# Main is immediately deployed to production
```

### Feature Flags for Incomplete Features

```python
if feature_flags.is_enabled('CACHE_OPTIMIZATION'):
    result = cached_result or fetch_from_db()
else:
    result = fetch_from_db()  # Old code path
```

### Advantages

✅ Minimal merge overhead
✅ Rapid integration feedback
✅ Simplest mental model
✅ Highest productivity
✅ Scales to 100+ developers
✅ Excellent for continuous deployment
✅ Better for distributed teams

### Disadvantages

❌ Requires excellent testing
❌ High discipline required
❌ Feature flags infrastructure needed
❌ Harder on large teams
❌ Deployment risk if testing weak

### When to Use

✅ **Ideal for:**
- Startups with small, high-trust teams
- Continuous deployment (10+ times per day)
- Cloud-native services
- Teams with mature testing
- Feature flag infrastructure in place

❌ **Not ideal for:**
- Regulated environments
- Teams with weak testing
- Infrequent releases
- Systems where downtime is extremely costly

---

## Feature-Branch Workflow — Complete Guide

### Theory: The Foundational Pattern

**Not a complete strategy by itself** but a **component pattern** used in all other strategies.

**Key principle**: Each piece of work gets its own isolated branch, reducing conflicts and enabling parallel development.

### Naming Conventions

```bash
feature/TICKET-123-add-payment-gateway
bugfix/TICKET-456-fix-email-validation
hotfix/TICKET-789-security-patch
chore/update-dependencies
docs/api-reference-v2
```

### Standard Workflow

```bash
# 1. Update stable branch
git checkout main
git pull origin main

# 2. Create feature branch
git checkout -b feature/TICKET-123-add-auth

# 3. Work on feature (small, logical commits)
git add .
git commit -m "TICKET-123: Implement auth API"
git add .
git commit -m "TICKET-123: Add unit tests"

# 4. Push to remote
git push -u origin feature/TICKET-123-add-auth

# 5. Create Pull Request
gh pr create --base main --title "TICKET-123: Add auth"

# 6. Address review feedback
git commit -am "TICKET-123: Address review feedback"
git push origin feature/TICKET-123-add-auth

# 7. Merge after approval
gh pr merge <PR-NUMBER> --squash --delete-branch
```

### Advantages

✅ Isolation (each feature developed independently)
✅ Parallel development
✅ Code review before merge
✅ Rollback capability
✅ Clear tracking of who did what

### Disadvantages

❌ Merge overhead if branches live too long
❌ Integration delay (not tested until merge)
❌ Branch drift

### Best Practices

- Keep branches short-lived (max 3-5 days)
- Push frequently (daily minimum)
- Rebase on base branch before merge
- Delete branches after merge
- Write descriptive commit messages

---

## Real-World Scenarios & Strategy Selection

### Scenario 1: Startup Building SaaS

**Context:**
- 5 people
- Daily deployments
- No regulations
- Strong testing culture

**Recommended:** **GitHub Flow**

**Why:**
- Simplicity matches team size
- Daily deployments supported
- Minimal ceremony for rapid iteration
- Can scale to 20+ people

### Scenario 2: Enterprise Financial Application

**Context:**
- 40+ developers
- Quarterly releases
- Multiple versions in support (v2.5, v3.0, v3.1)
- Strict change control required

**Recommended:** **GitFlow** (strictly enforced)

**Why:**
- Designed for scheduled releases
- Multiple version support built-in
- Clear separation for auditing
- Deliberate process suits regulations

### Scenario 3: E-Commerce Platform

**Context:**
- 20 developers
- 2-3 releases per week
- Single production version
- Some regulations (PCI, privacy)

**Recommended:** **GitLab Flow** (with release branches)

**Why:**
- Balances velocity with structure
- Staging environment validation
- Approval gates for compliance
- Scales well for team size

### Scenario 4: Microservices Infrastructure (Google/Meta-scale)

**Context:**
- 100+ developers
- Thousands of deployments per day
- Excellent monitoring & auto-rollback
- Comprehensive feature flags

**Recommended:** **Trunk-Based Development**

**Why:**
- Scales to hundreds of developers
- Supports continuous deployment
- Feature flags provide release control
- Less conflict risk with short branches

### Scenario 5: Open-Source Library

**Context:**
- 20-30 volunteer maintainers
- Release every 3-6 months
- Multiple versions (v1.x, v2.x)
- Community contributions

**Recommended:** **GitFlow + Feature-Branch**

**Why:**
- Clear structure for async collaboration
- Release coordination point
- Multiple version support
- Contributors understand standard model

---

## Your Repository Implementation (master + integration model)

### Your Context

- **Branches**: `master` (production), `integration` (staging)
- **Deployment path**: feature → integration → QA → Pre-Prod → master → Prod
- **Goal**: Staged validation before production

### Recommended Strategy

**Hybrid GitFlow + GitHub Flow** with GitLab Flow environment progression

### Architecture

```
master (Production)
    ↑ (merge after prod success)
    |
integration (Staging)
    ↑ (merge features here first)
    |
feature/* branches (QA, Pre-Prod testing before master)
```

### Step 1: Branch Protection Setup (GitHub)

**For `integration`:**

1. Go to repo → **Settings** → **Branches** → **Add rule**
2. Branch pattern: `integration`
3. Enable:
   - ✅ Require pull request reviews (1+ reviewers)
   - ✅ Require status checks to pass (feature-ci workflow)
   - ✅ Require branches up to date
   - ✅ Require linear history
   - ✅ Dismiss stale approvals

**For `master`:**

1. Branch pattern: `master`
2. Enable:
   - ✅ Require pull request reviews (2+ reviewers)
   - ✅ Require status checks to pass (deploy-prod workflow)
   - ✅ Require branches up to date
   - ✅ Restrict who can push (Admins only)

### Step 2: GitHub Actions Workflows

**File: `.github/workflows/feature-ci.yml`**

```yaml
name: feature-ci

on:
  push:
    branches:
      - 'feature/**'
      - 'bugfix/**'
      - 'hotfix/**'
  pull_request:
    branches:
      - integration
      - master

jobs:
  lint-and-test:
    runs-on: ubuntu-latest
    timeout-minutes: 15
    
    steps:
      - uses: actions/checkout@v4
      
      - name: Set up runtime environment
        uses: actions/setup-node@v4
        with:
          node-version: '18'
          cache: 'npm'
      
      - name: Install dependencies
        run: npm ci
      
      - name: Run linting
        run: npm run lint
      
      - name: Build application
        run: npm run build
      
      - name: Run unit tests
        run: npm test -- --coverage
      
      - name: Upload coverage
        uses: codecov/codecov-action@v3
        if: always()
      
      - name: Security scanning
        run: npm audit --production --audit-level=moderate || true
```

**File: `.github/workflows/deploy-qa.yml`**

```yaml
name: deploy-qa

on:
  push:
    branches:
      - integration

jobs:
  deploy:
    runs-on: ubuntu-latest
    environment: qa
    
    steps:
      - uses: actions/checkout@v4
      
      - name: Deploy to QA
        run: |
          echo "🚀 Deploying to QA..."
          # Your deployment commands
          # docker build -t myapp:qa-${{ github.sha }} .
          # kubectl set image deployment/myapp myapp=myapp:qa-${{ github.sha }} -n qa
      
      - name: Run QA smoke tests
        run: |
          echo "✅ Running QA tests..."
          # npm run test:qa
      
      - name: Notify on failure
        if: failure()
        uses: slackapi/slack-github-action@v1.24.0
        with:
          webhook-url: ${{ secrets.SLACK_WEBHOOK_QA }}
          payload: |
            {
              "text": "❌ QA deployment failed"
            }
```

**File: `.github/workflows/deploy-preprod.yml`**

```yaml
name: deploy-preprod

on:
  workflow_run:
    workflows: ["deploy-qa"]
    types:
      - completed

jobs:
  deploy:
    if: ${{ github.event.workflow_run.conclusion == 'success' }}
    runs-on: ubuntu-latest
    environment: preprod
    
    steps:
      - uses: actions/checkout@v4
        with:
          ref: ${{ github.event.workflow_run.head_sha }}
      
      - name: Deploy to Pre-Prod
        run: |
          echo "🚀 Deploying to Pre-Prod..."
          # kubectl set image deployment/myapp myapp=myapp:preprod-${{ github.event.workflow_run.head_sha }} -n preprod
      
      - name: Run Pre-Prod validation tests
        run: |
          echo "✅ Running Pre-Prod tests..."
          # npm run test:preprod
      
      - name: Verify database migrations
        run: echo "Checking database migrations..."
```

**File: `.github/workflows/deploy-prod.yml`**

```yaml
name: deploy-prod

on:
  workflow_run:
    workflows: ["deploy-preprod"]
    types:
      - completed

jobs:
  deploy:
    if: ${{ github.event.workflow_run.conclusion == 'success' }}
    runs-on: ubuntu-latest
    environment: production
    
    steps:
      - uses: actions/checkout@v4
        with:
          ref: ${{ github.event.workflow_run.head_sha }}
      
      - name: Deploy to Production
        run: |
          echo "🚀 DEPLOYING TO PRODUCTION..."
          # kubectl set image deployment/myapp myapp=myapp:prod-${{ github.event.workflow_run.head_sha }} -n production
      
      - name: Run production smoke tests
        run: |
          echo "✅ Production smoke tests..."
          # curl https://api.example.com/health
      
      - name: Notify success
        if: success()
        uses: slackapi/slack-github-action@v1.24.0
        with:
          webhook-url: ${{ secrets.SLACK_WEBHOOK_PROD }}
          payload: |
            {
              "text": "✅ Production deployment successful! Ready to merge integration → master"
            }
      
      - name: Notify failure
        if: failure()
        uses: slackapi/slack-github-action@v1.24.0
        with:
          webhook-url: ${{ secrets.SLACK_WEBHOOK_PROD }}
          payload: |
            {
              "text": "❌ PRODUCTION DEPLOYMENT FAILED"
            }
```

### Step 3: Local Development

**Setup (one-time):**

```bash
git clone https://github.com/yourorg/yourrepo.git
cd yourrepo

git config user.name "Your Name"
git config user.email "your.email@company.com"

git fetch origin
git checkout integration && git pull origin integration
git checkout master && git pull origin master
```

**Creating a Feature:**

```bash
git checkout integration
git pull origin integration

git checkout -b feature/TICKET-123-add-payment

echo "new code" > payment.js
git add payment.js
git commit -m "TICKET-123: Implement payment integration"

git push -u origin feature/TICKET-123-add-payment

gh pr create --base integration \
  --title "TICKET-123: Add payment integration" \
  --body "Implements Stripe integration for purchases"
```

**Merging PR:**

```bash
# After CI passes and review approved:
gh pr merge <PR-NUMBER> --merge --delete-branch

# Triggers: QA deploy → Pre-Prod deploy → Prod deploy
```

**After Prod Success:**

```bash
git fetch origin
git checkout integration && git pull origin integration
git checkout master && git pull origin master

git merge --ff-only integration

git tag -a v1.2.3 -m "Release v1.2.3"

git push origin master v1.2.3
```

### Handling Conflicts

```bash
git fetch origin
git merge origin/integration

# Git shows conflict markers in conflicted files
# Edit files to resolve conflicts manually

git add <resolved-files>
git commit -m "Resolve merge conflicts"

git push origin feature/TICKET-123
```

### Handling Hotfixes

```bash
# Create hotfix from MASTER
git checkout master
git pull origin master
git checkout -b hotfix/v1.2.2-payment-crash

git commit -am "HOTFIX: Fix payment service crash"
git push -u origin hotfix/v1.2.2-payment-crash

# Create PR to master
gh pr create --base master --head hotfix/v1.2.2-payment-crash

# After approval and merge:
git checkout master
git merge hotfix/v1.2.2-payment-crash
git tag -a v1.2.2 -m "Hotfix v1.2.2"
git push origin master v1.2.2

# ALSO merge to integration
git checkout integration
git merge hotfix/v1.2.2-payment-crash
git push origin integration
```

---

## Git Commands Reference — Complete

### Essential Daily Commands

| Command | Purpose | Example |
|---------|---------|---------|
| `git status` | See working directory status | `git status` |
| `git add <file>` | Stage file for commit | `git add src/auth.js` |
| `git commit -m "msg"` | Record staged changes | `git commit -m "TICKET-123: Add auth"` |
| `git push origin <branch>` | Upload commits to remote | `git push origin feature/auth` |
| `git pull origin <branch>` | Fetch and merge remote | `git pull origin integration` |
| `git log --oneline` | View commit history | `git log --oneline -10` |
| `git diff` | Show unstaged changes | `git diff src/auth.js` |
| `git branch -a` | List all branches | `git branch -a` |
| `git checkout <branch>` | Switch branches | `git checkout main` |
| `git fetch origin` | Download remote changes | `git fetch origin` |

### Advanced Commands

| Command | Purpose | Example |
|---------|---------|---------|
| `git rebase <branch>` | Re-apply commits on new base | `git rebase origin/integration` |
| `git cherry-pick <hash>` | Apply specific commit | `git cherry-pick abc123` |
| `git reset --soft HEAD~1` | Undo commit, keep changes staged | `git reset --soft HEAD~1` |
| `git reset --hard <commit>` | Discard changes and move to commit | `git reset --hard HEAD~2` |
| `git revert <hash>` | Create commit undoing changes | `git revert abc123` |
| `git stash` | Temporarily save changes | `git stash` |
| `git stash pop` | Restore stashed changes | `git stash pop` |
| `git reflog` | Show HEAD movement history | `git reflog` |
| `git tag -a <name>` | Create version tag | `git tag -a v1.0 -m "Release"` |

---

## Common Issues & Troubleshooting

### Issue 1: Merge Conflict

**Problem**: Git can't auto-merge same file modified differently

**Solution**:
```bash
git status                 # See conflicted files
cat <file>               # Edit file, remove conflict markers
git add <file>
git commit -m "Resolve merge conflict"
```

### Issue 2: Committed to Wrong Branch

**Problem**: Important commit on main instead of feature branch

**Solution**:
```bash
git log --oneline          # Find the commit
git checkout -b feature/correct-branch
git checkout main
git reset --hard HEAD~1    # Remove from main (if not pushed)
```

### Issue 3: Detached HEAD with Commits

**Problem**: Committed while in detached HEAD state

**Solution**:
```bash
git reflog                 # Find lost commit hash
git checkout -b recovery <commit-hash>
git checkout integration
git merge recovery
```

### Issue 4: Lost Commits After Reset

**Problem**: Used `git reset --hard HEAD~N` and lost commits

**Solution**:
```bash
git reflog               # Find lost commit
git reset --hard <lost-hash>   # Restore
```

### Issue 5: Stale Feature Branch

**Problem**: Feature branch diverged from integration; merge causes conflicts

**Solution**:
```bash
git fetch origin
git rebase origin/integration      # Rebase on latest
# Resolve conflicts if needed
git push --force-with-lease origin feature/branch
```

### Issue 6: Index Lock Error

**Problem**: `.git/index.lock` exists; can't commit

**Solution**:
```bash
rm -f .git/index.lock
git reset --mixed HEAD
git status
```

### Issue 7: Repository Corruption

**Problem**: `git fsck --full` reports errors

**Solution**:
```bash
git fsck --full
git gc --aggressive --prune=now
# If severe: clone fresh copy from remote
```

---

## Best Practices

### Naming Conventions

```
feature/TICKET-123-short-description
bugfix/TICKET-456-fix-issue
hotfix/TICKET-789-security-patch
chore/update-dependencies
docs/api-documentation
```

### Commit Message Format

```
TICKET-123: Short summary (50 chars max)

Optional longer explanation here (wrap at 72 chars)
explaining the WHY, not the WHAT

- List specific changes if helpful
- Keep focused on this change only
```

### Code Review Standards

**What to Review**:
- Does code solve the stated problem?
- Any obvious bugs or logic errors?
- Follows project style/conventions?
- Tests sufficient and passing?
- Performance implications?
- Security considerations?
- Breaking changes?

### Pull Request Guidelines

**Good PR**:
- < 400 lines of changes
- Clear title and description
- Links to ticket/issue
- Testing notes included
- Screenshots for UI changes
- Ready for review (no TODOs)

**Bad PR**:
- 1000+ lines (too big)
- No description
- "WIP" or incomplete
- Failing tests
- Not ready

### Commit Size Guidelines

**Good commits**:
- 100-400 lines changed
- Logical unit of work
- Understood in < 5 minutes
- Clear before/after

**Bad commits**:
- 1000+ lines (split them)
- Mixed unrelated changes
- No clear purpose
- Hard to review

---

## Team Onboarding (3-Week Plan)

### Week 1: Fundamentals

**Topics:**
1. Git basics (clone, commit, push, pull)
2. Your repository strategy (master + integration model)
3. Feature branch workflow
4. Creating and reviewing PRs

**Exercise 1** (1 hour):
```bash
git clone <url>
git checkout -b feature/test-onboarding
echo "# Added by me" >> README.md
git commit -am "Onboarding: Add my name"
git push -u origin feature/test-onboarding
gh pr create --base integration --title "Onboarding test"
```

**Exercise 2** (1 hour):
- Review a peer's PR
- Leave constructive feedback
- Approve

### Week 2: Workflows & Recovery

**Topics:**
1. Rebasing and conflict resolution
2. Accidental commits and recovery
3. Merge conflicts
4. Stashing changes

**Hands-On:**
- Simulate merge conflict
- Practice manual resolution
- Test rollback procedures

### Week 3: Advanced Scenarios

**Topics:**
1. Hotfixes and urgent fixes
2. Reverting problematic commits
3. Force-push scenarios
4. Troubleshooting

**Exercise:**
- Simulate production emergency
- Create hotfix from master
- Sync back to integration

---

## Monitoring & Metrics

### Key Metrics

| Metric | Target | Why Important |
|--------|--------|---|
| **Time to Merge** | < 2 days | Reduces branch drift |
| **PR Size** | < 400 lines | Better reviews |
| **Deployment Frequency** | 2-5 per week | Rapid feedback |
| **Lead Time** | < 1 week | Velocity indicator |
| **Failed Deployments** | < 5% | Quality metric |
| **MTTR** | < 1 hour | Incident response |
| **Code Review Time** | < 24 hours | Identifies bottlenecks |
| **Merge Conflicts** | < 10% of PRs | Integration health |

---

**This guide provides everything needed to implement, manage, and troubleshoot your Git branching strategy successfully. Use it as your team's reference for all Git-related decisions and procedures.**

Last Updated: January 2026
