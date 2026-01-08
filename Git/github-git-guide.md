# Comprehensive Git Branching & Workflow Guide

## Table of Contents
1. [Key Concepts & Terminology](#key-concepts--terminology)
2. [Branching Strategies Comparison](#branching-strategies-comparison)
3. [Trade-offs & Decision Factors](#trade-offs--decision-factors)
4. [Implementation Guide for Your Repository](#implementation-guide-for-your-repository)
5. [Git Commands Reference](#git-commands-reference)
6. [Understanding .git Folder Structure](#understanding-git-folder-structure)
7. [Common Issues & Troubleshooting](#common-issues--troubleshooting)
8. [Real-World Scenarios & Solutions](#real-world-scenarios--solutions)
9. [Best Practices & Recommendations](#best-practices--recommendations)

---

## Key Concepts & Terminology

Before diving into strategies, understand these fundamental terms:

### Branch Types

| Term | Definition | Lifetime | Purpose |
|------|-----------|----------|---------|
| **Main / Master** | Production-ready branch | Long-lived | Contains stable, release-ready code deployed to production |
| **Develop / Integration** | Integration branch | Long-lived | Aggregates features for the next release; staging point for features |
| **Feature Branch** | Individual feature branch | Short-lived (days-weeks) | Isolates work on a single feature, bug fix, or task |
| **Release Branch** | Pre-release preparation | Short-lived (weeks) | Stabilizes code, applies version numbers, bug fixes; no new features |
| **Hotfix Branch** | Emergency production fix | Short-lived (hours-days) | Urgent fixes for production issues; created from main/master |
| **Bugfix Branch** | Non-urgent bug fixes | Short-lived (days-weeks) | Fixes for identified issues; usually branched off develop |

### Core Concepts

- **CI/CD (Continuous Integration / Continuous Deployment)**: Automated testing and deployment pipelines that validate code changes before merging and automatically deploy to environments.
  - **Continuous Integration**: Automatically run tests on feature branches to validate changes
  - **Continuous Deployment**: Automatically push validated code to environments (staging, production)

- **Feature Flags / Toggles**: Technique to hide, enable, or disable incomplete features even if code is merged into main branch. Allows decoupling code deployment from feature release.
  - Example: Users won't see new UI unless `FEATURE_NEW_DASHBOARD=true`
  - Benefits: Safe merge to main without exposing in-progress work

- **Merge vs. Rebase**:
  - **Merge**: Creates a merge commit linking two branches; preserves complete history
  - **Rebase**: Re-applies commits on top of another branch; creates linear history but rewrites commit history

- **Fast-Forward Merge**: A merge where the target branch simply advances to the feature branch's commit (no merge commit created) if target hasn't diverged.

---

## Branching Strategies Comparison

### 1. GitFlow Workflow

**Structure**: Highly organized with persistent branches

```
main (master)
    ↑ (merge when ready)
    |
develop
    ↑ (merge features)
    |
feature/* branches
release/* branches (created from develop)
    ↑ (merged to main & develop)
hotfix/* branches (created from main)
    ↑ (merged to main & develop)
```

#### How It Works
1. Developers branch off `develop` to build features (`feature/*`)
2. When feature complete, PR to `develop` for review and merge
3. When release ready, create `release/*` branch from `develop`
4. Stabilize, test, and fix bugs in `release/*` branch
5. Merge `release/*` into both `main` (production) and back into `develop`
6. For urgent production fixes, create `hotfix/*` from `main`
7. Merge `hotfix/*` back into both `main` and `develop`

#### Advantages
- ✅ Excellent for managing releases and versions
- ✅ Clear separation of development, staging, and production
- ✅ Hotfixes handled cleanly without blocking feature development
- ✅ Supports multiple concurrent versions in production
- ✅ Good for teams with scheduled releases and strict versioning (e.g., v1.0, v2.0)

#### Disadvantages
- ❌ Complex with many branches and frequent merges
- ❌ Can be slow (merge conflicts, waiting for features to finish)
- ❌ Not ideal for continuous/fast deployment scenarios
- ❌ Overhead in managing release branches and coordinating merges
- ❌ Steeper learning curve for new team members

#### Best For
- Large teams with scheduled releases (monthly, quarterly)
- Projects requiring strict versioning and multiple supported versions
- Enterprise software with change management processes
- Teams where hotfixes and bugfixes are frequent and must not block releases
- Products supporting multiple customer versions simultaneously

#### Implementation Checklist
- [ ] Create `develop` branch from `main`
- [ ] Set branch protection rules on `main` and `develop`
- [ ] Require PR reviews and CI checks
- [ ] Document release process
- [ ] Train team on naming conventions

---

### 2. GitHub Flow Workflow

**Structure**: Simple and lightweight, single main branch

```
main
  ↑ (merge via PR when ready)
  |
feature/* branches (short-lived, days)
```

#### How It Works
1. Main/master branch is always deployable and production-ready
2. Create feature branch off `main` for each feature/fix
3. Work on feature branch, commit frequently
4. Create Pull Request when ready for review
5. Code review, address feedback
6. Merge PR into `main` (auto-deploy if CI green)
7. Delete feature branch after merge

#### Advantages
- ✅ Simple workflow, easy to understand and follow
- ✅ Less overhead, fewer branches to manage
- ✅ Encourages small, frequent merges (reduces conflicts)
- ✅ Excellent for continuous deployment and CI/CD
- ✅ Lower barrier to entry for new developers
- ✅ Faster feedback loop

#### Disadvantages
- ❌ Less structure around releases
- ❌ Risk that `main` becomes unstable if features not thoroughly tested
- ❌ Not suitable for managing multiple concurrent versions
- ❌ Harder to prepare and stabilize releases
- ❌ Difficult to support older versions with bugfixes

#### Best For
- Web apps and services deployed frequently (daily/multiple times per day)
- Startups and small teams (2-10 people)
- Continuous deployment environments with strong automation
- Agile/rapid iteration teams
- Open-source projects with frequent releases

#### Implementation Checklist
- [ ] Set `main` as default branch
- [ ] Enable branch protection on `main`
- [ ] Require PR reviews
- [ ] Setup automated CI/CD for main
- [ ] Configure auto-merge on PR (optional)
- [ ] Delete head branches after merge

---

### 3. GitLab Flow Workflow

**Structure**: Hybrid approach with environment branches

```
main
  ↑ (merge when QA green)
  |
staging (or pre-prod)
  ↑ (merge when dev green)
  |
develop (or dev)
  ↑ (merge features)
  |
feature/* branches
release/* branches (optional)
```

#### How It Works
1. Start with GitHub Flow as base
2. Add environment-specific branches (staging, pre-prod, production)
3. Feature branches merge to `develop` (or directly to `main`)
4. Merges are promoted through environments: dev → staging → production
5. Each environment branch is automatically deployed to corresponding environment
6. Optionally add `release/*` or `environment/*` branches for pre-release QA

#### Advantages
- ✅ Supports multiple environments (dev, staging, QA, production)
- ✅ Good control over promotion of code to production
- ✅ Simpler than full GitFlow but more structure than GitHub Flow
- ✅ Environment-based testing before production
- ✅ Flexibility to adapt to team/project needs
- ✅ Deployment status visible per environment

#### Disadvantages
- ❌ Can become complex with many environment branches
- ❌ Requires good CI/CD tooling and discipline
- ❌ More branches can increase merge conflicts
- ❌ Need to ensure code flows consistently through all environments
- ❌ Risk of environment branches diverging

#### Best For
- Medium teams (5-20 people)
- Projects requiring staging/pre-prod validation before production
- Teams with QA/testing that need separate environment branches
- Organizations wanting release control with minimal complexity
- Microservices deployed to multiple environments

#### Implementation Checklist
- [ ] Create feature branches off main
- [ ] Create staging and pre-prod branches
- [ ] Setup deployment pipelines per branch
- [ ] Configure environment-specific CI checks
- [ ] Document promotion process (when code moves to next env)

---

### 4. Trunk-Based Development (TBD)

**Structure**: Single long-lived branch, very frequent merges

```
main (or trunk)
    ↑ (merge multiple times per day)
    |
feature/* branches (hours, feature-flagged)
```

#### How It Works
1. Single long-lived `main` or `trunk` branch
2. Developers create very short-lived feature branches (hours to 1 day)
3. Frequently merge to trunk (multiple times per day)
4. Features not ready for users controlled via feature flags
5. Strong automated testing and CI to catch issues early
6. Keep integration happening constantly to prevent branch drift

#### Advantages
- ✅ Minimizes merge conflicts (frequent integration)
- ✅ Encourages fast feedback and continuous integration
- ✅ Simple branch structure (one main branch)
- ✅ Better visibility of what's in production
- ✅ Simpler to reason about current state
- ✅ Ideal for rapid iteration and experimentation

#### Disadvantages
- ❌ Requires strong automated testing and CI/CD pipelines
- ❌ Mistakes can go directly into trunk (shared branch)
- ❌ Needs good safeguards: tests, reviews, feature flags
- ❌ Feature flag discipline required
- ❌ Large teams can still have coordination overhead
- ❌ Not suitable without mature testing infrastructure

#### Best For
- Teams deploying very frequently (multiple times per day)
- Highly automated infrastructure with strong CI/CD
- Small to medium teams (2-15 people)
- Organizations aiming for continuous delivery
- Startups with rapid iteration needs

#### Implementation Checklist
- [ ] Implement comprehensive automated testing
- [ ] Setup feature flag system
- [ ] Enable strong branch protection
- [ ] Require code reviews and CI checks
- [ ] Ensure quick feedback loop (tests < 10 min)
- [ ] Train team on feature flag discipline

---

### 5. Feature-Branch Workflow

**Structure**: Each change gets its own isolated branch

```
develop (or main)
    ↑ (merge features via PR)
    |
feature/* branches
bugfix/* branches
hotfix/* branches
```

#### How It Works
1. For each feature/task/bug, create a separate branch
2. Branch off a stable branch (often `develop` or `main`)
3. Work is isolated from other changes
4. Create PR when ready, get reviewed
5. Tests and QA pass before merge
6. Merge back into main branch
7. Delete feature branch after merge

#### Advantages
- ✅ Good modularity and code isolation
- ✅ Easier code reviews (changes grouped by feature)
- ✅ Better tracking of who did what and when
- ✅ If branch is bad, can abandon without affecting main
- ✅ Scales well for parallel development
- ✅ Clear separation of concerns

#### Disadvantages
- ❌ Branches can drift from main if long-lived
- ❌ Merge conflicts if branches live too long
- ❌ Requires discipline to keep branches small
- ❌ More branches to manage overall
- ❌ Overhead if branches not frequently synced

#### Best For
- Used as a component of **all** major strategies above
- When multiple features/fixes worked on in parallel
- Teams practicing code reviews

#### Implementation Checklist
- [ ] Establish naming conventions (feature/*, bugfix/*, hotfix/*)
- [ ] Keep feature branches < 1 week old
- [ ] Require small, focused PRs
- [ ] Enable branch auto-deletion on merge

---

## Trade-offs & Decision Factors

When choosing a branching strategy, consider these factors:

### 1. Release Frequency
| Strategy | Daily/Weekly | Monthly | Quarterly |
|----------|---|---|---|
| **GitHub Flow** | ⭐⭐⭐⭐⭐ | ⭐⭐⭐ | ⭐⭐ |
| **Trunk-Based** | ⭐⭐⭐⭐⭐ | ⭐⭐⭐ | ⭐⭐ |
| **GitLab Flow** | ⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐ |
| **GitFlow** | ⭐⭐⭐ | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ |

**Guidance**:
- Many deployments per day → GitHub Flow or Trunk-Based
- Scheduled releases (monthly, quarterly) → GitFlow or GitLab Flow with release branches

### 2. Team Size & Structure
| Team Size | Recommendation | Rationale |
|-----------|---|---|
| 1-3 people | GitHub Flow or Trunk-Based | Minimal overhead, simple communication |
| 4-10 people | GitHub Flow or GitLab Flow | More structure, still manageable |
| 10-30 people | GitLab Flow or GitFlow | Need for more structure and clear processes |
| 30+ people | GitFlow or multiple repos | Clear separation, release management essential |

### 3. Risk Tolerance & Quality Requirements
- **High risk tolerance** (startups, internal tools): GitHub Flow or Trunk-Based
- **Medium risk** (web apps): GitLab Flow with staging
- **Low risk tolerance** (banking, healthcare): GitFlow with detailed release process

### 4. Infrastructure & Automation
- **Weak CI/CD**: Need GitFlow with release branches for manual gates
- **Strong CI/CD**: Can use GitHub Flow or Trunk-Based
- **Excellent CI/CD + Feature Flags**: Trunk-Based recommended

### 5. Version Management Needs
- **Single production version**: GitHub Flow, Trunk-Based
- **Multiple supported versions**: GitFlow required
- **Multi-tenant with version pinning**: GitFlow or complex GitLab Flow

### 6. Developer Discipline Requirements
All strategies depend on:
- Good naming conventions for branches
- Frequent merges (keep branches short-lived)
- Code reviews before merge
- Keeping branches synchronized with main
- Avoiding long-lived feature branches

**Poor discipline kills any strategy's benefits**

---

## Implementation Guide for Your Repository

This guide assumes:
- Repository hosted on GitHub
- You have `master` and `integration` branches
- Feature branches named `feature/<TICKET>-<description>`
- You want QA → Pre-Prod → Prod progression
- GitHub Actions for CI/CD

### Step 1: Set Up Branch Protection Rules

#### For `integration` Branch (development)
1. Go to repo → **Settings** → **Branches** → **Add rule**
2. Configure:
   - **Branch name pattern**: `integration`
   - ☑️ **Require pull request reviews before merging** (min 1-2 reviewers)
   - ☑️ **Require status checks to pass** (select: feature-ci workflow)
   - ☑️ **Require branches to be up to date before merging**
   - ☑️ **Require linear history** (recommended for clean history)
   - ☑️ **Dismiss stale pull request approvals when new commits are pushed**
   - ☑️ **Require code owners review** (if using CODEOWNERS file)

#### For `master` Branch (production)
1. Go to repo → **Settings** → **Branches** → **Add rule**
2. Configure:
   - **Branch name pattern**: `master`
   - ☑️ **Require pull request reviews** (min 2+ reviewers for prod)
   - ☑️ **Require status checks to pass** (select: deploy-prod workflow)
   - ☑️ **Require branches to be up to date**
   - ☑️ **Require linear history**
   - ☑️ **Restrict who can push to matching branches** (admins only)
   - ☑️ **Allow auto-merge** (optional, set to squash or rebase)

### Step 2: Create GitHub Actions Workflows

Create the following files in `.github/workflows/`:

#### 2.1 Feature CI Workflow (`.github/workflows/feature-ci.yml`)

```yaml
name: feature-ci

on:
  push:
    branches:
      - 'feature/**'
  pull_request:
    branches:
      - integration

jobs:
  lint:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Set up runtime
        uses: actions/setup-node@v4  # or python, java, etc.
        with:
          node-version: '18'
      
      - name: Install dependencies
        run: npm ci
      
      - name: Lint code
        run: npm run lint
      
      - name: Build application
        run: npm run build

  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Set up runtime
        uses: actions/setup-node@v4
        with:
          node-version: '18'
      
      - name: Install dependencies
        run: npm ci
      
      - name: Run unit tests
        run: npm test -- --coverage
      
      - name: Upload coverage
        uses: codecov/codecov-action@v3
        if: always()

  security-scan:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Run security scan
        run: npm audit --audit-level=moderate || true  # non-blocking advisory
```

**Key points**:
- Runs on all pushes to `feature/**` branches
- Also runs on PRs to `integration`
- Fails if lint or tests fail (blocks merge)
- This workflow name (`feature-ci`) becomes required status check

#### 2.2 QA Deployment Workflow (`.github/workflows/deploy-qa.yml`)

```yaml
name: deploy-qa

on:
  push:
    branches:
      - integration

jobs:
  deploy:
    runs-on: ubuntu-latest
    
    steps:
      - uses: actions/checkout@v4
      - name: Configure AWS credentials
        uses: aws-actions/configure-aws-credentials@v2
        with:
          aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }}
          aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
          aws-region: us-east-1
      
      - name: Deploy to QA environment
        run: |
          echo "Deploying to QA..."
          # Example: kubectl deploy, terraform apply, docker push, etc.
          # kubectl set image deployment/myapp myapp=${{ github.sha }} -n qa
      
      - name: Run QA smoke tests
        run: |
          echo "Running QA tests..."
          # curl http://qa.internal/health
          # npm run test:qa
      
      - name: Notify Slack on success
        if: success()
        run: echo "QA deployment succeeded"
      
      - name: Notify Slack on failure
        if: failure()
        uses: slackapi/slack-github-action@v1.24.0
        with:
          webhook-url: ${{ secrets.SLACK_WEBHOOK }}
          payload: |
            {
              "text": "❌ QA deployment failed for commit ${{ github.sha }}"
            }
```

#### 2.3 Pre-Prod Deployment Workflow (`.github/workflows/deploy-preprod.yml`)

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
    
    steps:
      - uses: actions/checkout@v4
      - name: Deploy to Pre-Prod
        run: |
          echo "Deploying to Pre-Prod..."
          # kubectl set image deployment/myapp myapp=${{ github.event.workflow_run.head_sha }} -n preprod
      
      - name: Run Pre-Prod tests
        run: |
          echo "Running Pre-Prod validation..."
          # npm run test:preprod
      
      - name: Validate database migrations
        run: |
          echo "Validating DB migrations..."
      
      - name: Notify on success
        if: success()
        run: echo "Pre-Prod deployment succeeded"
```

**Key points**:
- Uses `workflow_run` trigger to wait for deploy-qa
- Only runs if deploy-qa succeeded (`conclusion == 'success'`)
- Waits for QA to be green before starting
- Creates sequential pipeline

#### 2.4 Production Deployment Workflow (`.github/workflows/deploy-prod.yml`)

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
    
    steps:
      - uses: actions/checkout@v4
      
      - name: Pre-deployment checks
        run: |
          echo "Running pre-deployment checks..."
          # Verify all prerequisites
      
      - name: Deploy to Production
        run: |
          echo "🚀 Deploying to Production..."
          # kubectl set image deployment/myapp myapp=${{ github.event.workflow_run.head_sha }} -n prod
      
      - name: Run production smoke tests
        run: |
          echo "Running production smoke tests..."
          # curl https://api.example.com/health
      
      - name: Database backup before deploy
        run: |
          echo "Backup production database..."
      
      - name: Notify success
        if: success()
        uses: slackapi/slack-github-action@v1.24.0
        with:
          webhook-url: ${{ secrets.SLACK_WEBHOOK }}
          payload: |
            {
              "text": "✅ Production deployment succeeded!"
            }
      
      - name: Notify failure
        if: failure()
        uses: slackapi/slack-github-action@v1.24.0
        with:
          webhook-url: ${{ secrets.SLACK_WEBHOOK }}
          payload: |
            {
              "text": "❌ Production deployment failed - ROLLBACK REQUIRED"
            }
```

#### 2.5 Auto-Tagging on Production Success (Optional)

```yaml
name: tag-on-prod-success

on:
  workflow_run:
    workflows: ["deploy-prod"]
    types:
      - completed

jobs:
  tag:
    if: ${{ github.event.workflow_run.conclusion == 'success' }}
    runs-on: ubuntu-latest
    
    steps:
      - uses: actions/checkout@v4
        with:
          fetch-depth: 0
      
      - name: Configure git
        run: |
          git config user.name "github-actions[bot]"
          git config user.email "github-actions[bot]@users.noreply.github.com"
      
      - name: Fast-forward master to integration and tag
        run: |
          git fetch origin
          git checkout integration
          git pull origin integration
          git checkout master
          git pull origin master
          git merge --ff-only integration || (echo "Not a fast-forward"; exit 1)
          
          # Generate semantic version tag
          CURRENT_TAG=$(git describe --tags --abbrev=0 2>/dev/null || echo "v0.0.0")
          MAJOR=$(echo $CURRENT_TAG | cut -d. -f1 | cut -dv -f2)
          MINOR=$(echo $CURRENT_TAG | cut -d. -f2)
          PATCH=$(echo $CURRENT_TAG | cut -d. -f3)
          NEW_TAG="v$MAJOR.$MINOR.$((PATCH + 1))"
          
          git tag -a "$NEW_TAG" -m "Release $NEW_TAG - $(date)"
          git push origin master
          git push origin "$NEW_TAG"
          
          echo "Tagged as $NEW_TAG"
```

### Step 3: Local Development Workflow

#### Initial Setup

```bash
# Clone repository
git clone https://github.com/yourorg/yourrepo.git
cd yourrepo

# Configure git
git config user.name "Your Name"
git config user.email "your.email@company.com"

# Ensure both main branches are local and up to date
git fetch origin
git checkout integration
git pull origin integration
git checkout master
git pull origin master
```

#### Creating and Working on a Feature

```bash
# Always start from integration branch
git checkout integration
git pull origin integration

# Create feature branch with TICKET reference
git checkout -b feature/TICKET-123-add-login-page

# Make changes and commit
git add .
git commit -m "TICKET-123: implement login form component"

# Push to remote
git push -u origin feature/TICKET-123-add-login-page

# Create PR (via GitHub UI or CLI)
gh pr create --base integration \
  --head feature/TICKET-123-add-login-page \
  --title "TICKET-123: Add login page" \
  --body "Implements login page component with form validation"
```

#### Reviewing and Merging PRs

```bash
# List open PRs
gh pr list --base integration

# Check PR status (reviews, CI checks)
gh pr view <PR_NUMBER>

# Once CI passes and reviews approved:
gh pr merge <PR_NUMBER> --merge --delete-branch
# or use squash for cleaner history:
# gh pr merge <PR_NUMBER> --squash --delete-branch
```

#### After QA and Pre-Prod Pass

```bash
# Once deploy-prod workflow succeeds:
git fetch origin
git checkout integration
git pull origin integration
git checkout master
git pull origin master

# Fast-forward master to integration (only works if linear history)
git merge --ff-only integration

# Create release tag
git tag -a v1.2.3 -m "Release v1.2.3 - Production deployment $(date)"

# Push master and tag
git push origin master
git push origin v1.2.3
```

#### Keeping Feature Branch Updated

```bash
# If integration has new commits and you have conflicts:
git fetch origin
git rebase origin/integration
# resolve any conflicts
git add .
git rebase --continue
git push --force-with-lease origin feature/TICKET-123-add-login-page
```

### Step 4: Handling Common Scenarios

#### Scenario: Merge Conflicts in Feature PR

```bash
# On your feature branch
git fetch origin
git merge origin/integration
# Git will show conflicts
# Edit files and resolve conflict markers

git add .
git commit -m "Resolve merge conflicts with integration"
git push origin feature/TICKET-123-add-login-page
```

#### Scenario: Need to Revert a Merged PR

```bash
# Safe approach: create revert commit (doesn't rewrite history)
git checkout integration
git pull origin integration
git revert <commit-hash>
# Git creates new commit that undoes the change
git push origin integration

# Create PR for the revert
gh pr create --base integration --title "Revert TICKET-123: Add login page"
```

#### Scenario: Hotfix to Production

```bash
# Create hotfix branch from master (not integration)
git checkout master
git pull origin master
git checkout -b hotfix/TICKET-456-fix-payment-bug

# Make fix
git add .
git commit -m "TICKET-456: fix payment processing error"
git push -u origin hotfix/TICKET-456-fix-payment-bug

# Create PR to master
gh pr create --base master \
  --head hotfix/TICKET-456-fix-payment-bug \
  --title "HOTFIX: Payment processing error"

# Once merged to master, also create PR to integration
git checkout integration
git pull origin integration
git merge hotfix/TICKET-456-fix-payment-bug
git push origin integration
```

---

## Git Commands Reference

### Essential Commands

| Command | Description | Usage |
|---------|-------------|-------|
| `git clone <url>` | Copy remote repo to local | Initial setup |
| `git status` | Show working directory status | Before any action |
| `git add <file>` | Stage file for commit | Preparing to commit |
| `git commit -m "msg"` | Record staged changes | Create local history |
| `git push <remote> <branch>` | Upload commits to remote | Share work |
| `git pull <remote> <branch>` | Fetch & merge remote changes | Update local branch |
| `git fetch <remote>` | Download remote changes (no merge) | Safe update |
| `git branch <name>` | Create new branch | Start feature work |
| `git checkout <branch>` | Switch to branch | Change working context |
| `git merge <branch>` | Integrate another branch | Combine work |
| `git log --oneline` | View commit history | Review changes |
| `git diff` | Show unstaged changes | Review modifications |

### Advanced Commands

| Command | Description | Usage |
|---------|-------------|-------|
| `git rebase <branch>` | Re-apply commits on top of another | Clean history |
| `git cherry-pick <hash>` | Apply specific commit to current branch | Selective merge |
| `git reset --hard <commit>` | Discard changes & reset to commit | Undo (destructive) |
| `git revert <commit>` | Create new commit undoing changes | Safe undo |
| `git stash` | Temporarily save changes | Switch branches with pending work |
| `git tag <name>` | Create version marker | Mark releases |
| `git reflog` | Show HEAD movement history | Recover lost commits |
| `git fsck --full` | Check repository integrity | Diagnose corruption |

### Useful Aliases

Add to `~/.gitconfig`:

```ini
[alias]
    st = status
    co = checkout
    br = branch
    ci = commit
    unstage = reset HEAD --
    last = log -1 HEAD
    visual = log --graph --oneline --all --decorate
    sync = !git fetch origin && git rebase origin/main
    feature = checkout -b
    publish = push -u origin
    cleanup = !git branch --merged | grep -v '\*' | xargs -n 1 git branch -d
```

---

## Understanding .git Folder Structure

The `.git/` folder is the heart of your repository. Understanding it helps with troubleshooting.

### Folder Layout

```
.git/
├── HEAD                          # Current branch pointer
├── config                        # Repository configuration
├── description                   # Repository description (for GitWeb)
├── index                         # Staging area snapshot
├── index.lock                    # Lock file (created during operations)
│
├── objects/                      # All Git data (commits, trees, blobs)
│   ├── ab/                       # First 2 chars of hash
│   ├── cd/
│   ├── info/                     # Object packing info
│   └── pack/                     # Compressed object archives
│       ├── pack-*.pack
│       └── pack-*.idx
│
├── refs/                         # Branch and tag pointers
│   ├── heads/                    # Local branches
│   │   ├── master
│   │   └── feature/login
│   ├── remotes/                  # Remote tracking branches
│   │   └── origin/
│   │       ├── master
│   │       └── integration
│   └── tags/                     # Version tags
│       └── v1.0
│
├── logs/                         # Reflog history (tracks HEAD movements)
│   ├── HEAD
│   └── refs/
│       ├── heads/
│       └── remotes/
│
├── hooks/                        # Git event scripts
│   ├── pre-commit.sample
│   ├── post-commit.sample
│   └── pre-push.sample
│
├── info/                         # Local repository metadata
│   └── exclude                   # Local .gitignore patterns
│
└── packed-refs                   # Compressed branch/tag references
```

### Key Files Explained

#### HEAD
- Points to current branch or commit
- Content: `ref: refs/heads/main` (normal) or commit hash (detached)
- Purpose: Git knows which branch you're on

#### config
- Repository-specific settings
- Contains: remote URLs, branch tracking, user info
- Example: `[remote "origin"] url = https://github.com/user/repo.git`

#### objects/
- Database of all Git content
- Commits, source code, blobs all stored here
- Content addressed (hash-based names)
- Run `git gc` to optimize pack files

#### refs/
- Pointers to commits
- `refs/heads/` = local branches
- `refs/remotes/origin/` = remote tracking branches
- Each file contains a commit hash

#### logs/
- History of HEAD and branch movements
- Critical for `git reflog` (recover lost commits)
- Keep these for safety

### Common Issues & Fixes

| Problem | Cause | Fix |
|---------|-------|-----|
| "index.lock" error | Git crashed mid-operation | `rm .git/index.lock` then retry |
| "detached HEAD" | Checked out commit instead of branch | `git checkout <branch>` to reattach |
| Missing commits | Local/remote mismatch | Use `git reflog` to find and recover |
| Slow operations | Many loose objects | `git gc --aggressive` to repack |
| Corrupt object | Disk issue or data corruption | `git fsck --lost-found` to diagnose |

---

## Common Issues & Troubleshooting

### Issue 1: Merge Conflict

**Problem**: Git can't auto-merge when same file changed differently

**Solution**:
```bash
# See which files conflict
git status

# View conflicts in file (Git marks them with <<<<<<, =======, >>>>>>>)
cat <conflicted-file>

# Edit file and remove conflict markers, keeping desired content

# Mark as resolved and commit
git add <file>
git commit -m "Resolve merge conflict"
```

### Issue 2: Committed to Wrong Branch

**Problem**: Made commit on main instead of feature branch

**Solution - Option A (preserve on main)**:
```bash
# Create feature branch with current commit
git checkout -b feature/correct-branch

# Return to main and revert
git checkout main
git revert HEAD

# Or reset if not pushed:
git reset --hard HEAD~1
```

**Solution - Option B (undo completely)**:
```bash
git reset --hard HEAD~1
git checkout -b feature/correct-branch
git cherry-pick <commit-hash>  # if needed
```

### Issue 3: Detached HEAD with Commits

**Problem**: Made commits while in detached HEAD state; appear lost

**Solution**:
```bash
# Find the commit
git reflog

# Create branch to save commits
git checkout -b save-my-work <commit-hash>

# Now can merge into integration
git checkout integration
git merge save-my-work
```

### Issue 4: Rebase Conflicts

**Problem**: Interactive rebase fails with conflicts

**Solution**:
```bash
# See status
git rebase --status

# Resolve conflict in editor
# Then continue
git add .
git rebase --continue

# Or abort if too messy
git rebase --abort
```

### Issue 5: Slow Git Operations

**Problem**: `git status`, `git log` are very slow

**Solutions**:
```bash
# Clean up and optimize repository
git gc --aggressive --prune=now

# For monorepos, use sparse checkout
git sparse-checkout init --cone
git sparse-checkout set frontend/

# Enable filesystem monitor
git config core.fsmonitor true
git config core.untrackedCache true
```

### Issue 6: Lost Commits After Reset

**Problem**: Used `git reset --hard` and lost commits

**Solution**:
```bash
# Check reflog to find lost commit
git reflog

# Create branch from lost commit
git checkout -b recover <commit-hash>

# Inspect and cherry-pick to correct branch
git log -p recover

git checkout integration
git cherry-pick <commit-hash>
```

### Issue 7: Large File Committed

**Problem**: Accidentally committed 100MB file, bloating repo

**Solution**:
```bash
# Install git-filter-repo
pip install git-filter-repo

# Remove file from history
git filter-repo --path large-file.bin --invert-paths

# Force push (coordinate with team!)
git push origin --force --all
```

### Issue 8: Corrupted Repository

**Problem**: `git fsck` reports errors, operations failing

**Solution**:
```bash
# Check health
git fsck --full

# If loose objects corrupt
git repack -a -d

# If severe, restore from backup
git clone <backup-url> repo-recovered
```

---

## Real-World Scenarios & Solutions

### Scenario 1: Feature Takes Longer Than Expected

**Situation**: Feature branch existed for 3 weeks, main has diverged

**Solution**:
```bash
# Update feature with latest main changes
git checkout feature/long-feature
git fetch origin
git rebase origin/integration

# Resolve any conflicts
# Force-push only if not shared (coordinate if shared)
git push --force-with-lease origin feature/long-feature

# Create/update PR with resolved changes
```

### Scenario 2: Production Bug Found Mid-Release

**Situation**: Release branch exists, bug found in main, affects release

**Solution**:
```bash
# Create hotfix from main
git checkout master
git checkout -b hotfix/urgent-bug

# Fix and test
git add .
git commit -m "HOTFIX: urgent bug"
git push -u origin hotfix/urgent-bug

# Merge to main
gh pr create --base master --head hotfix/urgent-bug

# Also merge to integration/release
git checkout integration
git pull origin integration
git merge hotfix/urgent-bug
git push origin integration
```

### Scenario 3: Need to Rollback Production Deployment

**Situation**: Deployment to production caused issues, need to rollback

**Solution**:
```bash
# On production server, check what's running
git log -1

# Create revert commit
git checkout master
git pull origin master
git revert <bad-commit-hash>
git tag v1.2.4-rollback
git push origin master

# Deploy the revert (master now points to reverted state)
# Or manually kill bad deployment and run previous version

# Also update integration so it doesn't re-deploy bad code
git checkout integration
git pull origin integration
git revert <bad-commit-hash>
git push origin integration
```

### Scenario 4: Multiple Teams Need Isolation

**Situation**: Frontend and backend teams stepping on each other, conflicts

**Solution - Option A: Separate Branches Per Team**:
```bash
# Create team-specific develop branches
develop-frontend
develop-backend
develop-shared-api

# PRs within team first
# Sync to main integration only when stable

# Requires more manual coordination
```

**Solution - Option B: Trunk-Based with Feature Flags**:
```bash
# All teams push to integration multiple times/day
# Incomplete features hidden by flags

# Pros: always integrated
# Cons: needs feature flag discipline
```

### Scenario 5: Need to Extract Specific Commits

**Situation**: Feature branch has good commits but also test commits to exclude

**Solution**:
```bash
# Interactive rebase to clean history
git checkout feature/messy
git rebase -i HEAD~10   # last 10 commits

# In editor, mark commits as:
# pick (keep)
# squash (combine with previous)
# reword (change message)
# drop (delete)

git push --force-with-lease origin feature/messy
```

---

## Best Practices & Recommendations

### 1. Branch Naming Conventions

```
feature/TICKET-123-short-description
  └─ Use ticket ID for traceability
  
bugfix/TICKET-456-fix-crash
  └─ Prefix helps filter branches
  
hotfix/TICKET-789-security-patch
  └─ Clear intent
  
chore/update-dependencies
  └─ For maintenance work
```

**Benefits**:
- Searchable branch history
- Automation can trigger based on prefix
- Easy to understand intent
- Maps to ticket system

### 2. Commit Message Conventions

**Format**:
```
TICKET-123: Short summary (50 chars max)

Optional longer explanation here (wrap at 72 chars)
explaining the WHY, not the WHAT

- List specific changes if helpful
- Keep focused on this change only
```

**Example**:
```
TICKET-456: Optimize login query performance

Refactored user lookup query to use indexed columns
instead of full table scan. Reduces login time from
800ms to 120ms in typical case.

- Changed search from LIKE to exact match
- Added index on email column
- Updated tests to verify performance
```

**Benefits**:
- Searchable in git log
- Shows intent and reasoning
- Helps during code reviews
- Easier to understand commits months later

### 3. Pull Request Best Practices

**Size**:
- Keep PRs small (< 400 lines changed if possible)
- Small PRs get reviewed faster and have fewer conflicts
- If PR > 1000 lines, consider breaking into smaller PRs

**Description**:
```markdown
# What
Brief description of change

# Why
Business context or problem being solved

# How
Technical approach taken

# Testing
- Unit tests added
- Tested on staging
- Scenario: X works now, Y still works

# Related
- Fixes #123
- Related to #456
```

**Before Merging**:
- ✅ CI/CD pipeline passes
- ✅ At least 1-2 reviews from team members
- ✅ No merge conflicts (or resolved cleanly)
- ✅ All conversations resolved
- ✅ Branch up to date with target

### 4. Code Review Checklist

As **Reviewer**:
- [ ] Code solves the stated problem
- [ ] No obvious bugs or logic errors
- [ ] Follows team coding standards
- [ ] Tests are sufficient
- [ ] No performance regressions
- [ ] Documentation updated if needed
- [ ] Security implications considered

As **Author**:
- [ ] Code is well-tested
- [ ] Peer review feedback incorporated
- [ ] No leftover debug code or console.log
- [ ] Commit messages are clear
- [ ] PR description is complete

### 5. Release Process Checklist

Before moving to production:
- [ ] All tests passing (unit, integration, E2E)
- [ ] Code reviewed and approved
- [ ] Staging environment validated
- [ ] Performance tested
- [ ] Security scan passed
- [ ] Database migrations tested
- [ ] Rollback plan documented
- [ ] Release notes prepared
- [ ] Stakeholders notified
- [ ] Monitoring/alerts configured

### 6. Monitoring & Alerting After Deploy

Post-deployment checklist:
- [ ] Application health checks passing
- [ ] No spike in error rates
- [ ] Performance metrics normal
- [ ] Database queries performing
- [ ] User traffic normal
- [ ] No customer complaints
- [ ] Team on standby for 30+ minutes

### 7. When to Use Each Strategy

**Use GitHub Flow if**:
- Deploying multiple times per week/day
- Small team (< 10 people)
- Web application or SaaS
- No need to support multiple versions

**Use Trunk-Based if**:
- Deploying multiple times per day
- Excellent CI/CD and testing
- Feature flags implemented
- Monorepo or shared codebase

**Use GitFlow if**:
- Scheduled releases (monthly, quarterly)
- Supporting multiple versions
- Large team with many features in parallel
- Strict change management needed

**Use GitLab Flow if**:
- Multiple environments (dev, staging, prod)
- Need pre-release QA
- Balance between structure and flexibility
- Medium-sized team

### 8. Team Training & Onboarding

New developers should learn:
1. **Day 1**: Clone repo, make small change, PR → merge
2. **Day 2**: Recover from mistakes (revert, reset, stash)
3. **Week 1**: Understand branching strategy for team
4. **Week 2**: Lead release process under supervision

### 9. Automation Opportunities

Automate with GitHub Actions:
- [ ] Run tests on every PR
- [ ] Lint and format checks
- [ ] Security scanning (SAST, dependency scan)
- [ ] Automated deployment to staging
- [ ] Performance testing
- [ ] Notification on merge
- [ ] Auto-cleanup old branches
- [ ] Generate release notes

### 10. Common Mistakes to Avoid

| Mistake | Problem | Solution |
|---------|---------|----------|
| Committing directly to main | No code review, deploys untested code | Always use feature branches + PRs |
| Long-lived branches (weeks) | Merge conflicts, branch drift | Merge to main every 1-3 days |
| Huge PRs (1000+ lines) | Hard to review, slow merging | Split into smaller PRs |
| Rewriting pushed history | Breaks teammates' local clones | Only rewrite local history |
| No feature flags | Can't merge incomplete work | Implement feature flag system |
| Weak test coverage | Bugs escape to production | Require tests in PRs |
| Skipping code reviews | Low code quality | Always require reviews (min 1-2) |
| Unclear commit messages | Can't understand history | Use conventional commits |

---

## Quick Reference: Strategy Selection Matrix

```
                Release Freq | Team Size | Risk Tolerance | CI/CD Maturity | Best Choice
                ------------|-----------|----------------|----------------|-------------
Startup MVP      Multiple/day|  2-5      | High          | Basic          | GitHub Flow
                                                                         or Trunk-Based

Growing SaaS     Weekly      | 5-15      | Medium        | Good           | GitHub Flow
                                                                         or GitLab Flow

Enterprise       Monthly     | 20-50     | Low           | Excellent      | GitFlow
Product          Quarterly   |           |               |                |

Monorepo (Google Several/day | 100+      | Medium        | Excellent      | Trunk-Based
style)                       |           |               |                |

Microservices    Daily-Weekly| 10-30     | Medium        | Good           | GitLab Flow
Platform         per team    | per team  |               |                |
```

---

## Additional Resources

- **Git Documentation**: https://git-scm.com/doc
- **GitHub Flow**: https://guides.github.com/introduction/flow/
- **Atlassian GitFlow**: https://www.atlassian.com/git/tutorials/comparing-workflows/gitflow-workflow
- **Trunk-Based Development**: https://trunkbaseddevelopment.com/
- **Conventional Commits**: https://www.conventionalcommits.org/
- **GitHub Actions**: https://docs.github.com/en/actions

---

## Checklist: Implementation Steps for Your Repository

- [ ] **Week 1: Planning**
  - [ ] Review strategies above
  - [ ] Choose strategy for your team
  - [ ] Document decision and rationale
  - [ ] Get team buy-in

- [ ] **Week 2: Setup**
  - [ ] Create branch protection rules
  - [ ] Add GitHub Actions workflows
  - [ ] Create pull request template
  - [ ] Setup commit hooks (pre-commit)

- [ ] **Week 3: Training**
  - [ ] Show team the workflows
  - [ ] Practice with a real feature
  - [ ] Document common scenarios
  - [ ] Create troubleshooting guide

- [ ] **Week 4: Gradual Rollout**
  - [ ] Soft launch (warnings, not blocks)
  - [ ] Address issues and questions
  - [ ] Adjust based on feedback
  - [ ] Make protection rules strict

- [ ] **Month 2: Monitor & Optimize**
  - [ ] Track metrics (PR review time, deployment frequency)
  - [ ] Gather team feedback
  - [ ] Adjust workflow if needed
  - [ ] Document lessons learned

---

**Last Updated**: January 2026  
**Author**: DevOps/Git Best Practices Guide  
**Status**: Ready for Team Implementation
