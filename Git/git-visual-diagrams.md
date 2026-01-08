# Git Branching Strategies - Complete Visual Reference

## 18 Comprehensive Diagrams & Flowcharts

---

## 1. GitFlow Complete Lifecycle

```
┌─────────────────────────────────────────────────────────────────────────────┐
│ MAIN (Production) - v1.0 through v2.0                                      │
└─────────────┬──────────────────────────────────────────────────────┬───────┘
              │                                                       │
              │                                                       │
┌─────────────▼──────────────────────────────────────────────────────▼───────┐
│ DEVELOP (Integration) - Features aggregated here                           │
│ ├─ feature/auth (dev1)                                                    │
│ ├─ feature/payments (dev2)                                                │
│ ├─ feature/mobile (dev3)                                                  │
│ │                     → (merge) → release/v2.0 → stabilize → tag v2.0    │
│ │                                                                           │
│ ├─ feature/analytics (new)                                                │
│ ├─ feature/reporting (new)                                                │
│ └─ feature/api-v2 (new)                                                   │
└──────────────────────────────────────────────────────────────────────────────┘

Timeline:
Week 1-4: Feature development
Week 5: Release branch created, stabilization begins
Week 6: Version bump, final testing
       → Merge to main, tag v2.0
Week 7: Merge back to develop for v3.0
```

---

## 2. GitHub Flow - Daily Deployment

```
main (always deployable) - deployed 5x per day

Mon 9 AM:
developer-a: feature/auth
  │ 9:30 AM: Push
  │ 10 AM: CI passes
  │ 10:30 AM: Review approved
  │ 10:30 AM: MERGE & DEPLOY ✅
  │ 11 AM: Live

Mon 10 AM:
developer-b: feature/payment (parallel with developer-a)
  │ 11 AM: Push
  │ 11:30 AM: CI passes
  │ 12 PM: MERGE & DEPLOY ✅
  │ 12:30 PM: Live

Mon 2 PM:
developer-c: bugfix/email-validation (parallel)
  │ 2:30 PM: Push
  │ 3 PM: CI passes
  │ 3:30 PM: MERGE & DEPLOY ✅
  │ 4 PM: Live

Result: 3 features live by end of day
```

---

## 3. GitLab Flow - Environment Progression

```
feature branch
    │ (Code review + CI)
    ▼
main (production-ready)
    │ Auto-deploy
    ▼
staging environment
    │ (Integration tests, QA validation)
    │ Manual approval
    ▼
production environment
    │ (Blue-green deployment)
    ▼
Live to customers ✅
```

---

## 4. Trunk-Based Development - Minimal Model

```
main (THE ONLY PERMANENT BRANCH)

9:00 AM:
dev-a: ─┐
        ├─ c1: feature-cache (30 min feature branch)
        │  └─ Push → CI (✅ 5 min) → Review → Merge
        ▼
9:30 AM: Merged to main ✅

10:00 AM:
dev-b: ─┐
        ├─ c2: feature-api (45 min feature branch)
        │  └─ Push → CI (✅ 8 min) → Review → Merge
        ▼
10:45 AM: Merged to main ✅

11:00 AM:
dev-c: ─┐
        ├─ c3: bugfix-validation (30 min feature branch)
        │  └─ Push → CI (✅ 7 min) → Review → Merge
        ▼
11:30 AM: Merged to main ✅

Result: 3 commits merged to main by noon
All deployed to production
```

---

## 5. Merge Conflict Resolution

```
Before merge attempt:
develop:    A - B - C - D (has validation code)
                      │
                      └─ feature: E - F - G (has caching code)

Git attempts merge:
Same file modified differently
Cannot auto-merge
CONFLICT ❌

Developer resolution:
<<<<<<< HEAD (develop)
  if (!id) throw Error("Invalid ID");
=======
  const user = cache.get(id);
  if (user) return user;
>>>>>>> feature/cache

Manual edit:
  if (!id) throw Error("Invalid ID");
  const user = cache.get(id);
  if (user) return user;

After resolution:
git add resolved-file
git commit -m "Resolve merge conflict"
Merge complete ✅
```

---

## 6. Rebase vs. Merge

```
MERGE:
develop:  A - B - C - D
          |
feature:  └─ E - F - G

After merge:
develop:  A - B - C - D - [MERGE]
          |         │    ╱
feature:  └─ E - F - G ──┘

Result:
✅ Complete history
✅ Shows branch existed
❌ Merge commit adds noise
✅ Safe for shared branches


REBASE:
develop:  A - B - C - D
          |
feature:  └─ E - F - G

After rebase:
develop:  A - B - C - D
                      │
feature:              └─ E' - F' - G'
                      (replayed commits, new hashes)

Result:
✅ Linear history
❌ Rewrites history (unsafe on shared!)
✅ Clean appearance
✅ Good for local-only branches
```

---

## 7. Pull Request Approval Flow

```
PR Created
    │
    ▼
CI Pipeline Runs
├─ Linting
├─ Unit tests
├─ Build
└─ Security scan
    │
    ├─ PASS ✅ → Proceed to review
    │
    └─ FAIL ❌ → Developer fixes → Re-run CI


Code Review
    │
    ├─ Reviewer 1: Comment/Request changes
    │
    ├─ Developer: Address feedback
    │
    ├─ Reviewer 1: Approve ✅
    │
    └─ Reviewer 2: Approve ✅


Branch Protection Checks
├─ CI status: ✅ passing
├─ All reviews: ✅ approved
├─ No conflicts: ✅ true
├─ Linear history: ✅ true
    │
    ▼
MERGE ALLOWED ✅
    │
    ▼
Deploy Pipeline Triggered
```

---

## 8. Deployment Pipeline Stages

```
Commit to integration
    │
    ▼
┌─ Stage 1: BUILD ─────────────────┐
│ ├─ Compile                       │
│ ├─ Run tests                     │
│ ├─ Build Docker image            │
│ └─ Push to registry              │
└─ ✅ Success (5 min)              │
    │                              │
    ▼                              │
┌─ Stage 2: QA DEPLOY ─────────────┼─────┐
│ ├─ Deploy to QA cluster          │     │
│ ├─ Run smoke tests               │     │ Approval: Team Lead
│ └─ Notify team                   │     │
└─ ✅ Success (15 min)             │     │
    │ (awaiting approval)          │     │
    └─────────────────────────┬────┘     │
                              │ ✅ Approved
                              ▼
                    ┌─ Stage 3: PRE-PROD ──────┐
                    │ ├─ Deploy to pre-prod    │
                    │ ├─ Full integration tests│
                    │ └─ Performance benchmarks│
                    └─ ✅ Success (20 min)     │
                        │ (awaiting approval)  │
                        │ ✅ Approved          │
                        ▼
                    ┌─ Stage 4: PRODUCTION ────┐
                    │ ├─ Blue-green deploy     │
                    │ ├─ Canary rollout (10%)  │
                    │ ├─ Monitor metrics       │
                    │ └─ Full rollout (100%)   │
                    └─ ✅ Success              │
                        │
                        ▼
                    LIVE ✅
```

---

## 9. Branch Protection Rules

```
Configuration Example for 'integration' branch:

┌──────────────────────────────────────────────┐
│ Integration Branch Protection Rules          │
├──────────────────────────────────────────────┤
│ ☑ Require pull request reviews               │
│    └─ Minimum 1 approvals required           │
│                                              │
│ ☑ Require status checks to pass              │
│    └─ Select: feature-ci workflow            │
│                                              │
│ ☑ Require branches up to date                │
│    before merging                            │
│                                              │
│ ☑ Require linear history                     │
│                                              │
│ ☑ Dismiss stale pull request approvals       │
│                                              │
└──────────────────────────────────────────────┘

Result:
✅ PR cannot merge without:
   - Passing CI
   - Approved review
   - No conflicts
   - Linear commits
```

---

## 10. Feature Branch Lifecycle

```
Monday 9 AM:
feature/TICKET-123 created from develop
    │
    ├─ Commit 1: "Add auth API client"
    │ ├─ Push
    │ ├─ CI runs (lint, test) ✅
    │
    ├─ Commit 2: "Add unit tests"
    │ ├─ Push
    │ ├─ CI runs ✅
    │
    ├─ Commit 3: "Fix test edge case"
    │ ├─ Push
    │ ├─ CI runs ✅
    │
    └─ Ready for review
        │
        ├─ Code review starts
        │
        ├─ Reviewer feedback: "Simplify validation"
        │
        ├─ Commit 4: "Simplify per review"
        │ ├─ Push
        │ ├─ CI runs ✅
        │
        ├─ Review approved ✅
        │
        └─ Merge to develop
            │
            └─ Branch auto-deleted ✅

Friday evening: Feature branch deleted, feature in develop
```

---

## 11. Hotfix During Release

```
Production Issue Found (v1.0 in production)
    │
    ├─ Create hotfix from MAIN (not develop!)
    │
    ├─ hotfix/v1.1-critical-bug
    │  ├─ Commit: "Fix security issue"
    │  ├─ Push
    │  ├─ CI: ✅ passing
    │  └─ Review: ✅ approved
    │      │
    │      ├─ Merge to MAIN
    │      │  ├─ Tag v1.1
    │      │  ├─ Deploy to production
    │      │  └─ Live in 30 minutes ✅
    │      │
    │      └─ Merge to DEVELOP (critical!)
    │         └─ Ensure fix in v2.0
    │
    └─ Meanwhile: v2.0 development continues
       ├─ feature/auth
       ├─ feature/payments
       └─ v2.0 now includes v1.1 fix
```

---

## 12. Git Object Model

```
WORKING DIRECTORY      STAGING AREA          REPOSITORY
(your files)          (git index)            (.git/objects)

file.js
index.js                 │
config.json     git add  │                  ┌─ Commit Object ─┐
                ────────→│                  │ ├─ Tree: xyz    │
                         │  git commit      │ ├─ Parent: abc  │
                         ├───────────────→ │ └─ Message: ... │
                         │                 └─────────────────┘
                         │                    │
                         │                    ├─ Tree Object ──┐
                         │                    │ ├─ file.js: s1 │
                         │                    │ ├─ index.js: s2│
                         │                    │ └─ config: s3  │
                         │                    └────────────────┘
                         │                       │
                         │                       ├─ Blob Objects
                         │                       ├─ Blob s1 (file.js content)
                         │                       ├─ Blob s2 (index.js content)
                         │                       └─ Blob s3 (config content)

Each object identified by SHA-1 hash of its content
Changing content = different hash
```

---

## 13. .git Folder Structure

```
.git/
├── HEAD (points to current branch)
│   └─ Content: "ref: refs/heads/main"
│
├── config (repository settings)
│   ├─ [remote "origin"]
│   │   └─ url = https://github.com/user/repo.git
│   └─ [branch "main"]
│       └─ remote = origin
│
├── index (staging area - binary)
│
├── objects/ (all Git data)
│   ├─ ab/cdef123... (blob)
│   ├─ 12/34567890... (commit)
│   └─ pack/ (compressed)
│
├── refs/ (branch/tag pointers)
│   ├─ heads/
│   │  ├─ main → 8a7d5e123
│   │  ├─ feature/auth → 6f3c2b1a
│   │  └─ develop → 4b2a1f8e
│   ├─ tags/
│   │  ├─ v1.0 → 9d8e7f6c
│   │  └─ v1.1 → 7c6b5a4f
│   └─ remotes/origin/
│      ├─ main → 8a7d5e123
│      └─ develop → 4b2a1f8e
│
├── logs/ (reflog - recovery history)
│   ├─ HEAD (where HEAD has been)
│   └─ refs/heads/ (branch movements)
│
├── hooks/ (event scripts)
│   ├─ pre-commit.sample
│   ├─ post-commit.sample
│   └─ pre-push.sample
│
└── info/exclude (local gitignore)
```

---

## 14. Branching Strategy Decision Tree

```
                        START
                         │
                         ▼
            How often do you release?
                         │
            ┌────────────┼────────────┐
            │            │            │
        Multiple/day   Daily/Wk    Monthly
            │            │            │
            ▼            ▼            ▼
           TBD     GitHub Flow    GitFlow
            │            │            │
            │       Consider:        │
            │       GitLab Flow      │
            │       if need staging  │
            │                        │
            └────────┬──────────┬───┘
                     │          │
                     ▼          ▼
               Team size >= 20?
                     │
                 ┌───┴───┐
                 │ Yes   │ No
                 ▼       ▼
            GitFlow   Your choice

Support multiple versions?
        │
    ┌───┴───┐
    │ Yes   │ No
    ▼       ▼
 GitFlow  Current choice
```

---

## 15. Recovery Decision Tree

```
                PROBLEM OCCURRED
                     │
                     ▼
          Did you push to remote?
                     │
              ┌──────┴──────┐
              │ Yes   │ No
              ▼             ▼
           ❌ Dangerous     ✅ Safe
           recovery        options
             │              │
             │              ├─ git reset
             │              ├─ git revert
             │              ├─ git rebase -i
             │              └─ git branch -d
             │
    Try using:
    ├─ git reflog (find hash)
    ├─ git revert (create undo commit)
    └─ git push (share recovery)

GOLDEN RULE:
Never force-push to shared branches!
```

---

## 16. Conflict Resolution Flowchart

```
Attempt to merge
    │
    ▼
┌─ No conflicts? ──┐
│   Yes       No  │
│   │         │   │
▼   │         │   ▼
MERGE ✅      CONFLICT ❌
    │             │
    │             ├─ Mark conflicted files
    │             ├─ Show: <<<<<<, =======, >>>>>>
    │             │
    │             ▼
    │         Developer
    │         ├─ Edit file manually
    │         ├─ Choose version A or B
    │         ├─ Or combine both
    │         │
    │         ▼
    │     git add <file>
    │     git commit
    │
    └─→ Merge complete ✅
```

---

## 17. Command Workflow

```
You have changes
    │
    ▼
┌─ git status ──────────────────┐
│ See what changed              │
└──────────┬─────────────────────┘
           │
           ▼
┌─ git diff ────────────────────┐
│ Review the actual changes     │
└──────────┬─────────────────────┘
           │
           ├─ Yes, proceed
           │  │
           │  ▼
           │  git add <files>
           │  │
           │  ▼
           │  git commit -m "msg"
           │  │
           │  ▼
           │  git push origin <branch>
           │  │
           │  ▼
           │  Create PR
           │  │
           │  ▼
           │  CI runs
           │  │
           │  ├─ PASS → Review → Approve → Merge ✅
           │  │
           │  └─ FAIL → Fix → Re-push → CI again
           │
           └─ No, need different changes
              │
              ├─ git checkout <file> (discard)
              ├─ git reset HEAD <file> (unstage)
              └─ git reset --soft HEAD~1 (undo commit, keep changes)
```

---

## 18. Your Master + Integration Model

```
MASTER (Production)
    ▲
    │ (merge after prod success)
    │
    ├─ Auto-tagged: v1.0, v1.1, v1.2, ...
    │
INTEGRATION (Staging)
    ▲
    │ (feature PRs merged here)
    │
    ├─ feature/auth (dev-a)
    │  └─ Merge → QA Deploy → Pre-Prod → Prod ✅
    │
    ├─ feature/payment (dev-b) [parallel]
    │  └─ Merge → QA Deploy → Pre-Prod → Prod ✅
    │
    └─ feature/analytics (dev-c) [parallel]
       └─ Merge → QA Deploy → Pre-Prod → Prod ✅

Timeline:
Monday 10 AM: feature/auth merged to integration
             ├─ QA Deploy (15 min)
             ├─ Pre-Prod Deploy (20 min)
             ├─ Production Deploy (10 min)
             └─ v1.2.0 ready
                 │
                 └─ Merge to master ✅

All parallel features flow through same pipeline
```

---

**Use these diagrams as quick references during development, team presentations, and documentation.**

Last Updated: January 2026
