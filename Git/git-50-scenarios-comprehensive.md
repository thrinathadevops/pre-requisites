# 50+ Git Scenarios: Comprehensive Troubleshooting & Optimization Guide

## Complete Reference with Detailed Explanations, Visualizations & Best Practices

---

## TABLE OF CONTENTS

### PART 1: MERGE & CONFLICT HANDLING (Scenarios 1-10)
### PART 2: ADVANCED DEBUGGING & RECOVERY (Scenarios 11-20)
### PART 3: WORKTREES, HOOKS & ATTRIBUTES (Scenarios 21-30)
### PART 4: PERFORMANCE & ENTERPRISE SCALE (Scenarios 31-47)

---

# PART 1: MERGE & CONFLICT HANDLING (Scenarios 1-10)

---

## Scenario 1: Merge Conflict on File

### Overview
Merge conflicts occur when Git cannot automatically combine changes from two branches because both branches modified the same lines in the same file. This is one of the most common Git operations that developers encounter, especially in team environments where multiple people work on overlapping code.

### Root Cause Analysis
When you merge two branches (or rebase), Git performs a three-way merge:
1. **Common ancestor** (base commit before branches diverged)
2. **Our version** (current branch HEAD)
3. **Their version** (branch being merged in)

If both versions changed the same lines differently, Git cannot decide which is correct and marks the file as conflicted.

### Detailed Reproduction Steps

```bash
# Step 1: Initialize repository and create base state
mkdir git-merge-test && cd git-merge-test
git init

# Step 2: Create initial shared state
echo "Original content line 1" > conflict.txt
echo "Original content line 2" >> conflict.txt
echo "Original content line 3" >> conflict.txt
git add conflict.txt
git commit -m "Initial commit: base file"

# Step 3: Create feature branch and make changes
git checkout -b feature-branch
echo "Feature branch content line 1" > conflict.txt
echo "Feature branch content line 2" >> conflict.txt
echo "Feature branch content line 3" >> conflict.txt
git add conflict.txt
git commit -m "Feature branch: modified conflict.txt"

# Step 4: Switch to main and make different changes
git checkout main
echo "Main branch content line 1" > conflict.txt
echo "Main branch content line 2" >> conflict.txt
echo "Main branch content line 3" >> conflict.txt
git add conflict.txt
git commit -m "Main branch: modified conflict.txt differently"

# Step 5: Attempt merge (triggers conflict)
git merge feature-branch
# Result: CONFLICT (content): Merge conflict in conflict.txt
```

### What Happens During Merge Conflict

```
Before Merge:

main:              feature-branch:
  A                  A
  |                  |
  B ─ (diverge) ─ C
  |
  D (current HEAD)

Merge Attempt (git merge feature-branch):
  - Git finds common ancestor: A
  - Compares: A → D (main changes)
  - Compares: A → C (feature changes)
  - Lines 1-3 all modified → CONFLICT
```

### Understanding Conflict Markers

```
<<<<<<< HEAD
This is the content from the current branch (main)
This line was modified on main
=======
This is the content from the branch being merged (feature-branch)
This line was modified on feature-branch
>>>>>>> feature-branch
```

**Breakdown:**
- `<<<<<<< HEAD`: Start of conflict, shows current branch
- `=======`: Separator between two conflicting versions
- `>>>>>>> feature-branch`: End of conflict, shows incoming branch name

### Resolution Strategies

#### Strategy 1: Accept Current Branch (Ours)
```bash
# Keep your current branch's version
git checkout --ours conflict.txt
git add conflict.txt
git commit -m "Resolved conflict: kept main branch version"
```

#### Strategy 2: Accept Incoming Branch (Theirs)
```bash
# Take the version from the branch being merged
git checkout --theirs conflict.txt
git add conflict.txt
git commit -m "Resolved conflict: took feature-branch version"
```

#### Strategy 3: Manual Resolution
```bash
# Edit file manually to combine both changes
# Open conflict.txt and edit to desired state:

# For example, keep both changes:
Original content line 1
Feature content addition
Main content addition
Original content line 2

# Then stage and commit
git add conflict.txt
git commit -m "Resolved conflict: combined both versions"
```

#### Strategy 4: Use Merge Tool
```bash
# Git can use external merge tools (Kdiff3, Meld, vimdiff, etc.)
git config --global merge.tool vimdiff  # set default tool
git mergetool conflict.txt              # launches visual merge tool

# After resolving in the tool:
git add conflict.txt
git commit -m "Resolved conflict using merge tool"
```

### Complete Resolution Workflow

```bash
# 1. Check conflict status
git status
# Output:
# On branch main
# You have unmerged paths.
# (fix conflicts and run "git commit")
# both modified: conflict.txt

# 2. View the conflicted file
cat conflict.txt

# 3. Edit the file in your editor to resolve
nano conflict.txt  # or vim, VS Code, etc.

# 4. Stage the resolved file
git add conflict.txt

# 5. Verify status
git status
# Output: all paths resolved

# 6. Complete the merge
git commit -m "Resolve merge conflict in conflict.txt"

# 7. Verify the merge completed
git log --oneline --graph --all
```

### Visual Merge Conflict Resolution Diagram

```
Merge Conflict Resolution Flow:

MERGE INITIATED
    ↓
CONFLICT DETECTED in conflict.txt
    ↓
[Check Status] → git status
    ↓
[View Conflict] → cat/edit conflict.txt (understand markers)
    ↓
[Resolve Conflict] ← Choose one of:
├── Keep ours: git checkout --ours
├── Keep theirs: git checkout --theirs
├── Manual edit: manually combine changes
└── Merge tool: git mergetool
    ↓
[Stage Resolution] → git add conflict.txt
    ↓
[Verify] → git status (should show 'all resolved')
    ↓
[Commit] → git commit -m "Resolved merge conflict"
    ↓
MERGE COMPLETE ✓
```

### Preventing Conflicts

1. **Keep branches short-lived** (merge to main within 1-2 days)
2. **Frequent rebasing** (rebase feature on main regularly)
3. **Good communication** (coordinate on overlapping files)
4. **Code reviews** (identify potential conflicts early)
5. **Atomic commits** (small logical changes reduce conflict surface)

### Related Scenarios
- Scenario 9: Broken Rebase with Conflicts
- Scenario 11: Complex Interactive Rebase
- Scenario 6: Octopus Merge Conflicts

---

## Scenario 2: Accidental Commit to Wrong Branch

### Overview
A developer makes a commit on the wrong branch (e.g., commits to `main` instead of creating a feature branch). This is a common mistake that needs to be fixed without losing the work.

### Root Cause
Forgot to create/checkout a feature branch before starting work; commits go to the wrong branch, often the default (main).

### Detailed Reproduction

```bash
mkdir wrong-branch-test && cd wrong-branch-test
git init

# Create initial state
echo "file1" > file1.txt
git add file1.txt
git commit -m "Initial commit"

# MISTAKE: Forget to create feature branch, work on main directly
echo "feature content" > feature.txt
git add feature.txt
git commit -m "Feature work on wrong branch"  # oops, this is on main!

# Now realize the mistake
git branch           # Currently on main
git log --oneline    # Shows the feature commit on main (should be on feature-branch)
```

### Why This is a Problem

1. **Main branch contamination**: Feature work mixed with production code
2. **CI/CD issues**: Main branch may be set to auto-deploy; feature code goes live prematurely
3. **Release management**: Cannot selectively include/exclude this feature
4. **Team confusion**: Others pulling main get incomplete/unreviewed feature work

### Solution Approach 1: Move Commit to New Branch (Recommended for Unpushed)

```bash
# Before pushing - easiest recovery
git log --oneline -5
# Shows: abc1234 Feature work on wrong branch
#        def5678 Initial commit

# Step 1: Create and switch to new feature branch at current HEAD
git checkout -b feature-branch
# This preserves the commit on the new branch

# Step 2: Go back to main and remove the commit
git checkout main
git reset --hard HEAD~1
# HEAD~1 is the parent of the commit we want to remove

# Step 3: Verify
git log --oneline main        # Should not have feature commit
git log --oneline feature-branch  # Should have feature commit
```

### Solution Approach 2: Cherry-Pick (For Pushed Commits)

```bash
# If already pushed to remote - must use different approach

# Step 1: Create feature branch from correct parent
git checkout -b feature-branch <parent-commit-hash>

# Step 2: Cherry-pick the feature commit onto feature-branch
git cherry-pick <wrong-commit-hash>

# Step 3: Remove from main using revert (for pushed commits)
git checkout main
git revert <wrong-commit-hash>
# This creates a NEW commit that undoes the changes (safer than reset on shared branches)

# Step 4: Force push back to remote (coordinate with team!)
git push origin main --force-with-lease
git push origin feature-branch
```

### Solution Approach 3: Using Reset (For Recent Unpushed Commits)

```bash
# Scenario: Made 3 commits on main by mistake

git log --oneline
# abc1234 Feature work 3
# def5678 Feature work 2
# ghi9012 Feature work 1
# jkl3456 Initial commit

# Solution 1: Move HEAD back and recreate branch
git branch feature-branch        # Create branch from current HEAD (has all commits)
git reset --soft HEAD~3         # Move HEAD back 3 commits (keeps changes staged)
git reset --hard HEAD           # Remove all 3 commits from main entirely

# Or more directly:
git checkout -b feature-branch HEAD
git checkout main
git reset --hard HEAD~3

# Solution 2: Using reflog to find the right spot
git reflog
# 9a8b7c6 HEAD@{0}: commit: Feature work 3
# 8d9e0f1 HEAD@{1}: commit: Feature work 2
# 7c6d5e4 HEAD@{2}: commit: Feature work 1
# 6b5c4d3 HEAD@{3}: commit: Initial commit

git reset --hard 6b5c4d3  # Reset to initial commit
git branch feature-branch 9a8b7c6  # Create branch from saved HEAD position
```

### Preventing This Mistake

1. **Pre-commit hook** - prevent commits on main (in .githooks/pre-commit):
```bash
#!/bin/sh
BRANCH=$(git rev-parse --abbrev-ref HEAD)
if [ "$BRANCH" = "main" ] || [ "$BRANCH" = "master" ]; then
  echo "❌ ERROR: You're committing directly to $BRANCH"
  echo "❌ Create a feature branch instead:"
  echo "   git checkout -b feature/your-feature-name"
  exit 1
fi
exit 0
```

2. **Branch protection rules** - prevent direct commits to main on remote
3. **Team policy** - all commits go through PRs, never direct pushes to main
4. **IDE configuration** - show current branch prominently

### Key Takeaways

| Scenario | Best Action | Risk Level |
|----------|------------|-----------|
| Unpushed, recent commit | `reset --hard HEAD~1` then `checkout -b feature` | Low |
| Unpushed, several commits | Create branch first, then reset | Low |
| Already pushed | Use `revert` (safer) or force-push after `reset` | High |
| On shared branch | Coordinate with team before any rewriting | Critical |

---

## Scenario 3: Detached HEAD & Lost Commit

### Overview
A developer checks out a specific commit (not a branch), making changes and committing. The commit exists but is not on any branch, making it vulnerable to garbage collection. This scenario teaches how to recover from accidental detached HEAD commits.

### Understanding Detached HEAD

```
Normal State:           Detached HEAD State:
main                    main (ref points here)
  ↓                       ↓
[A]←[B]←[C]←[D]         [A]←[B]←[C]←[D]
                                      ↑
HEAD → (points to main)          HEAD (not on any branch!)
```

### Detailed Reproduction

```bash
mkdir detached-head && cd detached-head
git init

# Create some commits
echo "v1" > version.txt
git add version.txt
git commit -m "Version 1"

echo "v2" > version.txt
git commit -am "Version 2"

echo "v3" > version.txt
git commit -am "Version 3"

# Now check the commits
git log --oneline
# abc1234 Version 3
# def5678 Version 2
# ghi9012 Version 1

# MISTAKE: Check out a specific commit (detached HEAD)
git checkout HEAD~2  # Go back to "Version 1" commit
# Warning: You are in 'detached HEAD' state.
# You can look around, make experimental changes...

git status
# Output: HEAD detached at ghi9012

# Make experimental changes while detached
echo "experimental" > experiment.txt
git add experiment.txt
git commit -m "Experimental commit"

# Now try to switch back to main
git checkout main
# Warning: Your changes to the following files would be overwritten:
#   experiment.txt
# The commit you just made exists, but it's not on any branch!
```

### Why This is Dangerous

When you're in detached HEAD state:
1. **Commits are orphaned** - no branch reference points to them
2. **Garbage collection** - Git periodically removes unreachable commits (default 30 days)
3. **Easy to lose work** - accidentally switch branches and the commit "disappears"
4. **Not obvious** - many developers don't realize they're in detached HEAD

### Recovery Strategy 1: Create Branch from Lost Commit

```bash
# You're still in detached HEAD (commit not lost yet)
git log --oneline
# abc5678 Experimental commit (this is the one you want to keep!)

# Create a branch at current HEAD
git branch experimental-work abc5678

# Now switch to the new branch (safe!)
git checkout experimental-work

# Or more directly:
git checkout -b experimental-work  # Creates branch from current detached HEAD
```

### Recovery Strategy 2: Using Reflog (If You Already Switched Away)

```bash
# You already switched to main and lost the reference
# Use reflog to find the experimental commit

git reflog
# Shows: 
# abc5678 HEAD@{0}: checkout: moving from detached HEAD to main
# def1234 HEAD@{1}: commit: Experimental commit  ← This is the one!
# ghi5678 HEAD@{2}: checkout: moving from main to HEAD~2

# Recover by creating branch from the reflog entry
git checkout -b experimental-recovery def1234

# Or directly from reflog notation:
git checkout -b experimental-recovery HEAD@{1}
```

### Recovery Strategy 3: Using fsck to Find Lost Commits

```bash
# If reflog is expired (30+ days old), use git fsck
git fsck --lost-found

# Shows all unreachable commits:
# unreachable commit abc5678d...
# unreachable commit def1234e...

# View the unreachable commit
git show abc5678d

# Create branch from lost commit
git branch recovery-branch abc5678d
```

### Complete Workflow: Preventing Detached HEAD Issues

```bash
# WRONG: Checking out a commit directly
git checkout abc5678  # ❌ Creates detached HEAD

# RIGHT: Always work on branches
git checkout -b investigation-abc5678  # ✓ Creates a branch

# If you need to investigate a specific commit temporarily:
git checkout abc5678 --detach  # Explicit about detached state
# ... look around ...
git checkout main  # Go back safely

# If you made changes while detached:
git stash  # Save work before switching
git checkout -b recovery-branch  # Create branch
git stash pop  # Restore work on new branch
```

### Visual Detached HEAD Workflow

```
Detached HEAD Risk Mitigation:

[1] Accidentally enter detached HEAD state:
    git checkout abc5678
    ↓
[2] Make commits while detached:
    echo "experiment" > file.txt
    git commit -m "work"
    ↓
[3] Realize the risk (⚠️ Commit is not on any branch!)
    ↓
[4] Options to recover:
    ├─ Still in detached HEAD?
    │  └─ git checkout -b recovery-branch  ← Best option
    │
    └─ Already switched away?
       ├─ git reflog → find commit
       ├─ git branch recovery <commit>
       └─ Or: git fsck --lost-found
```

### Key Commands Reference

| Situation | Command | Explanation |
|-----------|---------|-------------|
| In detached HEAD with work | `git checkout -b feature` | Create branch from current HEAD |
| Lost commit recently | `git reflog` | Find commit in recent history |
| Lost commit long ago | `git fsck --lost-found` | Search for unreachable commits |
| Want to investigate safely | `git checkout --detach <commit>` | Explicit detached HEAD |

---

## Scenario 4: Undo Last Commit But Keep Changes

### Overview
A developer made a commit but realizes they want to undo it while preserving the changes (staged or unstaged). This is different from reverting - they want to "undo" the commit itself, not create a undo commit.

### Use Cases
1. **Wrong commit message** - want to re-commit with correct message
2. **Incomplete work** - staged too early
3. **Need to split commit** - changes should be multiple commits
4. **Wrong branch** - committed to main instead of feature

### Detailed Reproduction

```bash
mkdir undo-commit && cd undo-commit
git init

# Initial state
echo "v1" > file.txt
git add file.txt
git commit -m "Initial commit"

# Make a change
echo "changed content" > file.txt

# Accidentally commit with wrong message
git commit -am "Oops wrong commit message and content"

# Realize the mistake
git log --oneline
# abc1234 Oops wrong commit message and content
# def5678 Initial commit
```

### Solution 1: git reset --soft (Keep Changes Staged)

```bash
# Undo last commit, keep changes in staging area
git reset --soft HEAD~1

# Check status
git status
# On branch main
# Changes to be committed:
#   modified: file.txt

# Re-commit with correct message
git commit -m "Fix: proper commit message with correct content"

# Or modify content and then commit
echo "correct content" > file.txt
git add file.txt
git commit -m "Fix: proper commit message with correct content"
```

**Visual explanation:**
```
Before reset --soft:
  [Commit] → (file.txt changes stored in commit object)
  
After reset --soft:
  [Staging Area] ← (file.txt changes restored here)
  (Commit is removed, but changes preserved)
```

### Solution 2: git reset --mixed (Keep Changes Unstaged)

```bash
# Undo last commit, keep changes in working directory (unstaged)
git reset --mixed HEAD~1
# or simply: git reset HEAD~1 (--mixed is the default)

# Check status
git status
# On branch main
# Changes not staged for commit:
#   modified: file.txt

# Edit and stage specific changes
git add file.txt
git commit -m "Correct commit message"

# Or check changes first
git diff
```

**Visual explanation:**
```
Before reset --mixed:
  [Commit] → [Staging Area] → (file.txt)
  
After reset --mixed:
  [Working Directory] ← (file.txt changes restored here, unstaged)
  (Commit is removed, staging area cleared)
```

### Solution 3: git reset --hard (Discard Changes - ⚠️ DANGEROUS)

```bash
# ONLY USE IF YOU WANT TO DISCARD CHANGES COMPLETELY
git reset --hard HEAD~1

# Check status
git status
# On branch main
# nothing to commit, working tree clean

# WARNING: Changes are GONE (cannot be recovered except via reflog)
```

### Solution 4: git commit --amend (For Last Commit Only)

```bash
# If you only want to change the commit message or content:

# Change commit message
git commit --amend -m "Correct commit message"

# Change the content AND message
echo "correct content" > file.txt
git add file.txt
git commit --amend -m "Correct message"

# NOTE: This modifies the last commit, not "undoing" it
# Only use if commit is not yet pushed
```

### Practical Workflow: Undoing Multiple Commits

```bash
# Scenario: Made 3 commits, want to undo and re-do them properly

git log --oneline
# abc1234 Commit 3 (with wrong message)
# def5678 Commit 2 (incomplete)
# ghi9012 Commit 1
# jkl3456 Initial

# Undo last 3 commits, keeping all changes staged
git reset --soft HEAD~3

# Check what we have
git status
# Changes to be committed: (all changes from 3 commits)

# Now we can re-commit properly
git reset HEAD  # Unstage everything
# Or selectively:
git reset HEAD file1.txt  # Unstage file1
# Keep file2, file3 staged

# Re-stage and commit in logical chunks
git add important-file.txt
git commit -m "feat: important feature"

git add another-file.txt
git commit -m "fix: bug fix"

git add other-file.txt
git commit -m "refactor: code organization"

# Result: 3 cleaner, better-organized commits
```

### Comparison Table: When to Use Each Reset

| Goal | Command | Result | Safety |
|------|---------|--------|--------|
| Undo commit, keep staged | `reset --soft HEAD~1` | Changes in staging area | ✓ Safe |
| Undo commit, keep unstaged | `reset --mixed HEAD~1` | Changes in working dir | ✓ Safe |
| Discard commit & changes | `reset --hard HEAD~1` | Everything gone | ⚠️ Dangerous |
| Fix last commit message | `commit --amend` | Modify last commit | ✓ Safe (unpushed) |

### Preventing Wrong Commits

```bash
# Review before committing
git diff --cached  # See what will be committed

# Interactive commit (choose what to commit)
git commit -p  # Prompts for each hunk

# Dry run merge
git merge --no-commit --no-ff feature
git status
git merge --abort  # Cancel if something is wrong
```

---

## Scenario 5: Lost Commits After Hard Reset

### Overview
A developer runs `git reset --hard HEAD~2` or similar, thinking they're undoing commits, but actually loses the commit references. The commits still exist in the repository (temporarily) but are not referenced by any branch, making them appear "lost."

### Root Cause
Hard reset removes the branch reference to commits, orphaning them. They remain in the Git object database for 30 days (default reflog expiration) before garbage collection removes them permanently.

### Detailed Reproduction

```bash
mkdir lost-commits && cd lost-commits
git init

# Create some commits
for i in {1..5}; do
  echo "v$i" > version.txt
  git commit -am "Version $i"
done

git log --oneline
# abc5678 Version 5
# def4321 Version 4
# ghi8901 Version 3
# jkl2345 Version 2
# mno6789 Version 1

# MISTAKE: Reset hard to remove last 2 commits
git reset --hard HEAD~2

# Now check log
git log --oneline
# ghi8901 Version 3
# jkl2345 Version 2
# mno6789 Version 1

# Version 4 and 5 are gone from the branch
# But they still exist in Git database (temporarily!)
```

### Why Commits Still Exist

Git uses a **reference-based system**:
- Branches are just pointers to commits
- Deleting the pointer (via reset) doesn't delete the commit itself
- The commit stays in `.git/objects` unless garbage collection removes it
- `git reflog` keeps a record of where HEAD has been for 30 days

### Recovery Strategy 1: Using Reflog (Fastest Recovery)

```bash
# View reflog - shows all positions HEAD has been in
git reflog

# Output:
# ghi8901 HEAD@{0}: reset: moving from abc5678 to ghi8901
# abc5678 HEAD@{1}: commit: Version 5  ← This is what we lost!
# def4321 HEAD@{2}: commit: Version 4  ← And this!
# ghi8901 HEAD@{3}: reset: moving to HEAD~2

# Recover by resetting back to the lost commit
git reset --hard abc5678

# Or using reflog notation:
git reset --hard HEAD@{1}

# Verify
git log --oneline  # Should show all 5 versions again
```

### Recovery Strategy 2: Using git fsck (When Reflog Expired)

```bash
# If more than 30 days have passed and reflog expired:

# Find all unreachable commits
git fsck --lost-found

# Output:
# unreachable commit abc5678deadbeef...
# unreachable commit def4321deadbeef...
# unreachable commit (other dangling objects)

# View the unreachable commit
git show abc5678deadbeef

# Create a branch from the lost commit
git branch recovered-work abc5678deadbeef
git checkout recovered-work

# Verify the lost commits are back
git log --oneline
```

### Recovery Strategy 3: Using Reflog Show (Detailed History)

```bash
# Show full reflog with commit messages
git reflog show HEAD

# More detailed view
git log -g --oneline  # Reflog in log format

# Find the commit you want
git show HEAD@{5}  # View commit at that position

# Reset to that position
git reset --hard HEAD@{5}
```

### Complete Workflow: Safe Recovery Process

```bash
#!/bin/bash
# Safe recovery process for lost commits

# Step 1: Don't panic! Check reflog first
echo "=== Reflog (what HEAD has been) ==="
git reflog

# Step 2: Identify the lost commit
echo "=== Find the lost commit ==="
git show HEAD@{N}  # Replace N with position number

# Step 3: Create a recovery branch (safe, doesn't affect current branch)
git branch recovery-branch HEAD@{N}

# Step 4: Inspect the recovery branch
git log --oneline recovery-branch
git diff main recovery-branch

# Step 5: Decide what to do:
# Option A: Merge recovery branch back
git checkout main
git merge recovery-branch

# Option B: Use it as a separate branch
git checkout recovery-branch
git push origin recovery-branch

# Option C: Cherry-pick specific commits
git cherry-pick abc5678  # specific commit from recovery-branch

# Step 6: Clean up
git branch -d recovery-branch  # Only if no longer needed
```

### Prevention: Making Reset Safer

```bash
# Before doing a destructive reset, create a backup branch
git branch backup-before-reset
git reset --hard HEAD~2

# If something goes wrong, you have a backup:
git reset --hard backup-before-reset

# Or use reflog:
git reflog
git reset --hard HEAD@{1}

# More cautious approach: use revert instead of reset for shared commits
git revert HEAD~2..HEAD  # Creates undo commits instead of rewriting
```

### Key Takeaways

| Situation | Action | Command |
|-----------|--------|---------|
| Just lost commits | Check reflog immediately | `git reflog` |
| Know the position | Reset directly | `git reset --hard HEAD@{N}` |
| Don't remember position | Browse reflog entries | `git show HEAD@{N}` |
| Reflog expired | Use fsck | `git fsck --lost-found` |
| On public branch | Use revert instead | `git revert HEAD~2` |

---

## Scenario 6: Simulated Corruption (Missing Pack Idx)

### Overview
Git objects are stored in "pack files" for efficiency. The pack file needs an index (.idx) file. If the index file is corrupted or missing, Git cannot efficiently find objects.

### Root Cause
- Disk corruption
- Manual tampering with `.git/objects/pack/`
- Incomplete pack creation
- Network failure during repack

### Detailed Reproduction

```bash
mkdir corrupt-repo && cd corrupt-repo
git init

# Create some data
echo "data1" > file1.txt
git add file1.txt
git commit -m "Commit 1"

echo "data2" > file2.txt
git add file2.txt
git commit -m "Commit 2"

# Pack the objects
git gc

# Simulate corruption by removing the index file
ls .git/objects/pack/
# pack-abc123.pack
# pack-abc123.idx  ← Remove this file

rm .git/objects/pack/*.idx

# Try to use Git
git status
# May produce errors like:
# error: index file .git/objects/pack/pack-xxxx.pack is invalid
```

### Diagnosis Commands

```bash
# Check repository integrity
git fsck --full
# Output: error: object file .git/objects/pack/pack-xxx.pack: bad CRC

# Detailed analysis
git verify-pack -v .git/objects/pack/*.idx 2>&1 | grep -i error

# Count objects
git count-objects -v
```

### Fix Strategy 1: Rebuild Missing Index

```bash
# If pack file exists but idx is missing, rebuild the index
cd .git/objects/pack

# Unpack all objects from the pack file
git unpack-objects < pack-abc123.pack

# Repack to create new idx file
cd ../../..
git repack -a -d
git count-objects -v

# Verify integrity
git fsck --full
```

### Fix Strategy 2: Full Garbage Collection

```bash
# This rewrites all packs cleanly
git gc --aggressive --prune=now

# Verify
git fsck --full --no-dangling

# Check performance improvement
git count-objects -vH
```

### Fix Strategy 3: Restore from Backup/Remote

```bash
# If repository is corrupted beyond repair, restore from remote

# Fresh clone from remote
git fetch origin

# Or full restore:
rm -rf .git
git clone <remote-url> .  # This removes the backup, so backup first!
```

### Complete Recovery Workflow

```bash
#!/bin/bash
# Corruption recovery process

# Step 1: Diagnose
echo "=== Checking integrity ==="
git fsck --full --verbose

# Step 2: Attempt repair
echo "=== Attempting automatic repair ==="
git gc --aggressive --prune=now

# Step 3: Verify repair
echo "=== Verifying ==="
git fsck --full --no-dangling

# Step 4: If still broken, try unpacking
echo "=== Unpacking and repacking ==="
cd .git/objects/pack
for pack in *.pack; do
  echo "Unpacking $pack..."
  git unpack-objects < "$pack" || true
done
cd ../../..

# Step 5: Re-create packs
git repack -a -d -f

# Step 6: Final verification
git fsck --full
echo "=== Recovery complete ==="
```

### Prevention

```bash
# Keep regular backups
git clone --mirror <repo> backup-<date>.git

# Enable Git maintenance
git maintenance start

# Monitor pack integrity regularly
git verify-pack -v .git/objects/pack/*.idx

# Avoid manual manipulation of .git/objects
# Use Git commands instead
```

---

## Scenario 7: Large File Accidentally Committed

### Overview
A developer accidentally commits a large file (100+ MB binary, video, database backup, etc.) that bloats the repository. Subsequent clones and operations become slow and fail due to size limits.

### Root Cause
- Forgot `.gitignore` entry
- Misunderstood file size
- Temporary build artifact included
- Sensitive large file (e.g., database dump)

### Detailed Reproduction

```bash
mkdir large-file && cd large-file
git init

# Create a large file (100 MB)
fallocate -l 100M bigfile.bin  # or: dd if=/dev/zero of=bigfile.bin bs=1M count=100

echo "normal file" > normal.txt

# Add both to Git
git add .
git commit -m "Add bigfile and normal file"

# Check size
du -sh .git/objects
# Output: ~100M

# Now this repo is huge
git clone . cloned-repo
# Slow operation
```

### Quick Fix (If Not Yet Pushed)

```bash
# Easiest: undo the commit before pushing
git reset --soft HEAD~1

# Remove the large file
git reset HEAD bigfile.bin
rm bigfile.bin

# Add to .gitignore
echo "bigfile.bin" >> .gitignore

# Recommit without the file
git add .gitignore normal.txt
git commit -m "Add files without large binary"

# Verify
git log --oneline
git ls-tree -r HEAD  # Should not list bigfile.bin
```

### Fix (After Already Pushed)

This requires history rewriting, which is more complex.

#### Option 1: git filter-repo (Recommended - Modern Approach)

```bash
# Backup first!
git clone --mirror . backup.git

# Install git-filter-repo (requires Python 3.7+)
# pip install git-filter-repo
# or: https://github.com/newren/git-filter-repo

# Remove the large file from all history
git filter-repo --invert-paths --path bigfile.bin

# Force push to remote (alert team!)
git push origin --force --all
git push origin --force --tags

# Team members must reclone or update
# git fetch origin && git reset --hard origin/main
```

#### Option 2: BFG Repo-Cleaner (If filter-repo unavailable)

```bash
# Create a mirror first
git clone --mirror . repo.git

# Use BFG to remove file
cd repo.git
bfg --delete-files bigfile.bin

# Clean up
git reflog expire --expire=now --all
git gc --prune=now --aggressive

# Push back
git push --force

# Clean clients
cd ../
git fetch origin
git reset --hard origin/main
```

#### Option 3: git filter-branch (Old, Slower Method)

```bash
# Less efficient than filter-repo, but works

git filter-branch --force --index-filter \
  'git rm --cached --ignore-unmatch bigfile.bin' \
  --prune-empty --tag-name-filter cat -- --all

# Clean up
git reflog expire --expire=now --all
git gc --prune=now --aggressive
git push origin --force --all
```

### Verification After Cleanup

```bash
# Verify file is gone from history
git rev-list --objects --all | grep bigfile.bin
# Should return nothing

# Check new repo size
git count-objects -vH

# Clients can now clone efficiently
du -sh .git
```

### Prevention

```bash
# 1. Pre-commit hook to prevent large files
cat > .git/hooks/pre-commit <<'EOF'
#!/bin/bash
# Prevent commits of files > 50MB
MAX_SIZE=52428800  # 50MB in bytes

git diff --cached --name-only | while read file; do
  if [ -f "$file" ]; then
    size=$(stat -f%z "$file" 2>/dev/null || stat -c%s "$file" 2>/dev/null)
    if [ $size -gt $MAX_SIZE ]; then
      echo "ERROR: File too large: $file ($size bytes)"
      echo "Use Git LFS for large files:"
      echo "  git lfs track '$file'"
      exit 1
    fi
  fi
done
EOF
chmod +x .git/hooks/pre-commit

# 2. Add comprehensive .gitignore
cat > .gitignore <<'EOF'
# Build artifacts
*.o
*.so
*.exe
*.dll

# Large files
*.zip
*.tar.gz
*.rar
*.iso

# Database backups
*.bak
*.sql
*.dump

# Media files (use Git LFS)
*.mp4
*.avi
*.mov
*.mp3
*.wav

# IDE artifacts
.idea/
.vscode/
*.swp
*.swo

# OS
.DS_Store
Thumbs.db
EOF

# 3. Use Git LFS for large media
git lfs install
git lfs track "*.mp4" "*.mov" "*.iso"
git add .gitattributes
git commit -m "Add LFS configuration"
```

### Git LFS Alternative

```bash
# For ongoing large file management
git lfs install

# Track large file types
git lfs track "*.mp4"
git lfs track "*.psd"
git lfs track "*.zip"

# Add the .gitattributes file
git add .gitattributes
git commit -m "Configure Git LFS"

# Now large files are stored outside of Git history
# Clones are much faster
```

---

## Scenario 8: Mixed Staged and Unstaged Changes

### Overview
After making changes, you've partially staged some files (added to index) while others remain unstaged (in working directory). Understanding this mixed state is crucial for Git mastery.

### Understanding Git's Three States

```
Working Directory     Staging Area (Index)     Repository
┌──────────────────┐ ┌──────────────────────┐ ┌─────────────┐
│ file.txt (v2)    │ │ file.txt (v1)        │ │ file.txt(v0)│
│ config.ini (v3)  │ │ config.ini (v3)      │ │ config.ini  │
│ app.js (new)     │ │ (app.js not staged)  │ │ (in HEAD)   │
└──────────────────┘ └──────────────────────┘ └─────────────┘
  Changes not yet      Changes staged for      Committed
  staged              committing               objects
```

### Detailed Reproduction

```bash
mkdir staging-mess && cd staging-mess
git init

# Initial state
echo "v1" > file.txt
echo "v1" > config.ini
git add .
git commit -m "Initial commit"

# Now make changes to multiple files
echo "changed in working dir" > file.txt
echo "staged change" > config.ini

# Stage only config.ini
git add config.ini

# Don't stage app.js changes
echo "new file" > app.js

# Check mixed state
git status
# On branch main
# Changes to be committed:
#   modified: config.ini
# Changes not staged for commit:
#   modified: file.txt
# Untracked files:
#   app.js
```

### Understanding Each Status

```bash
# What's staged (will be in next commit)
git diff --staged

# What's unstaged (not in next commit)
git diff

# What's untracked (Git doesn't know about)
git status --short

# Output format of git status --short:
# M  = staged modification
#  M = unstaged modification
# A  = staged addition
# ?? = untracked
# Example output:
# M  config.ini      (staged change)
#  M file.txt        (unstaged change)
# ?? app.js          (untracked)
```

### Working with Mixed Changes

#### Viewing Differences

```bash
# See what will be committed
git diff --cached
# or
git diff --staged

# See what's NOT staged
git diff

# See all changes (staged + unstaged)
git diff HEAD

# Compare specific file
git diff file.txt        # unstaged changes in file.txt
git diff --cached file.txt  # staged changes in file.txt
```

#### Selective Staging (git add -p)

```bash
# Interactively choose which parts to stage
git add -p file.txt

# This shows each "hunk" (logical change block) and asks:
# Stage this hunk? [y,n,q,a,d,j,J,g,/,e,p,?]
# y = stage this hunk
# n = don't stage this hunk
# q = quit
# a = stage all hunks in this file
# d = don't stage any hunk in this file

# Example workflow:
echo -e "line1\nline2\nline3\nline4\nline5" > multi-change.txt
git add -p multi-change.txt  # Choose specific lines to stage
```

#### Unstaging Specific Files

```bash
# Unstage everything
git reset HEAD

# Unstage specific file
git reset HEAD file.txt

# Unstage specific file using restore (Git 2.23+)
git restore --staged file.txt

# Verify
git status
```

#### Discarding Changes

```bash
# Discard unstaged changes in specific file
git restore file.txt
# or (older Git)
git checkout -- file.txt

# Discard all unstaged changes
git restore .

# Discard staged changes (be careful!)
git restore --staged file.txt  # Unstage but keep in working dir
git restore file.txt            # Discard entirely

# Discard BOTH staged and unstaged
git reset --hard HEAD
# WARNING: This loses all changes!
```

#### Using Stash for Complex Workflows

```bash
# You have mixed changes and want to switch branches
git status
# Changes to be committed: config.ini
# Changes not staged: file.txt, app.js

# Option 1: Stash everything temporarily
git stash -u  # -u includes untracked

# Option 2: Stash only specific files
git stash push -m "WIP files" file.txt app.js

# Switch branch, do work
git checkout other-branch
# ... work ...

# Return to original branch
git checkout main

# Restore stash
git stash pop
# or
git stash apply  # Apply without removing stash

# Continue where you left off
git status  # Shows mixed changes again
```

### Interactive Commit Workflow

```bash
# Powerful workflow: commit only what you want

# View mixed state
git status

# Commit only staged changes
git commit -m "Update config"

# Now only unstaged and untracked remain
git status

# Stage and commit the next logical change
git add file.txt
git commit -m "Fix file.txt bug"

# Add new file
git add app.js
git commit -m "Add app.js feature"

# Result: Clean, logical commits instead of one big messy commit
```

### Preventing Accidental Commits

```bash
# Always review before committing
git diff --cached  # See what will be committed

# If adding multiple files, do it selectively
git add -p  # Interactive mode

# Pre-commit hook to require explicit commits
cat > .git/hooks/pre-commit <<'EOF'
#!/bin/bash
# Warn if committing many files
count=$(git diff --cached --name-only | wc -l)
if [ $count -gt 5 ]; then
  echo "⚠️  WARNING: Committing $count files"
  echo "Run: git diff --cached"
  read -p "Continue? (y/n) " -n 1 -r
  if [[ ! $REPLY =~ ^[Yy]$ ]]; then
    exit 1
  fi
fi
exit 0
EOF
chmod +x .git/hooks/pre-commit
```

### Summary Table: Mixed State Commands

| Goal | Command | Result |
|------|---------|--------|
| Stage all changes | `git add .` | Everything staged |
| Stage specific file | `git add file.txt` | Only file.txt staged |
| Stage interactively | `git add -p` | Choose each hunk |
| Unstage everything | `git reset HEAD` | Nothing staged |
| Unstage one file | `git reset HEAD file.txt` | file.txt unstaged |
| See staged changes | `git diff --cached` | View what commits |
| See unstaged | `git diff` | View what doesn't |
| Discard unstaged | `git restore file.txt` | File reverted to HEAD |
| Discard all | `git reset --hard HEAD` | **WARNING** Loses all |

---

## Scenario 9: Broken Rebase with Conflicts

### Overview
Interactive rebase (`git rebase -i`) allows reordering, squashing, or editing commits. When rebasing commits onto a different base, conflicts can occur and halt the rebase.

### Understanding Rebase Conflicts

Rebase is essentially:
1. Remove commits from current branch
2. Move branch pointer to new base
3. Replay commits one-by-one on new base
4. If replay causes conflicts, stop and wait for resolution

```
Before rebase:
main:      A — B — C
              \
feature:       D — E — F

git rebase main (while on feature):
Replaying D, E, F onto C:
If D conflicts with changes in C:
  STOP — wait for user to resolve

After rebase (if successful):
main:      A — B — C
                    \
feature:             D' — E' — F'
```

### Detailed Reproduction

```bash
mkdir rebase-conflict && cd rebase-conflict
git init

# Create base commit
echo "base content" > file.txt
git add file.txt
git commit -m "Base commit"

# Create feature branch
git checkout -b feature
echo "feature line 1" >> file.txt
git commit -am "Feature: add line 1"

echo "feature line 2" >> file.txt
git commit -am "Feature: add line 2"

# Switch back to main and make conflicting changes
git checkout main
echo "main line 1" >> file.txt
git commit -am "Main: add conflicting line"

# Now try to rebase feature onto main
git checkout feature
git rebase main
# Output:
# CONFLICT (content): Merge conflict in file.txt
# error: could not apply abc1234... Feature: add line 1
```

### Resolving During Rebase

```bash
# While rebase is stopped:

# 1. Check status
git status
# interactive rebase in progress; onto def5678
# Currently rebasing branch 'feature' on 'def5678'
# You are currently rebasing.
#   (fix conflicts and then run "git rebase --continue")
#   (use "git rebase --abort" to cancel the rebase)

# 2. View the conflicted file
cat file.txt
# Shows conflict markers like merge conflicts

# 3. Edit to resolve conflicts
nano file.txt  # Resolve <<<<<<< markers

# 4. Stage the resolution
git add file.txt

# 5. Continue rebase
git rebase --continue
# Continues with the next commit

# If the next commit also has conflicts:
# Repeat steps 2-5

# After all commits are rebased successfully:
# git rebase (with no other arguments) is complete
```

### Rebase with Multiple Conflicted Commits

```bash
# If multiple commits have conflicts:

$ git rebase main
# CONFLICT in commit 1
# Fix it
git add file.txt
git rebase --continue
#
# CONFLICT in commit 2
# Fix it again
git add file.txt
git rebase --continue
#
# Success - all commits rebased

# View the resulting history
git log --oneline
# Should show feature commits rebased onto main
```

### Recovery Options During Rebase

```bash
# Option 1: Abort the rebase entirely
git rebase --abort
# Returns to the state before the rebase started
# feature branch is unchanged

# Option 2: Continue after resolving
git rebase --continue

# Option 3: Skip this commit
git rebase --skip
# Continues rebase without this commit

# Option 4: Edit this commit
git rebase --edit-todo
# Opens editor to modify the rebase plan
```

### Interactive Rebase Walkthrough

```bash
# Start interactive rebase
git rebase -i main

# This opens an editor with:
# pick abc1234 Feature: add line 1
# pick def5678 Feature: add line 2
# pick ghi9012 Feature: add line 3
#
# Commands:
# p, pick = use commit
# r, reword = use commit, but edit the commit message
# e, edit = use commit, but stop for amending
# s, squash = use commit, but meld into previous commit
# f, fixup = like squash, but discard log message
# d, drop = remove commit
# x, exec = run command

# Example modifications:
pick abc1234 Feature: add line 1
s def5678 Feature: add line 2           # Squash into previous
r ghi9012 Feature: add line 3           # Keep but rename
# (x git test)                          # Run test between commits

# Save and exit editor
# If conflicts occur, resolve them as described above
```

### Visual Rebase Conflict Flow

```
Rebase Start
    ↓
[Pick first commit to replay]
    ↓
[Replay onto new base]
    ↓
Conflict? 
    ├─ YES → [Resolve conflicts]
    │        ↓
    │    [git add]
    │        ↓
    │    [git rebase --continue]
    │        ↓
    │    [Next commit]
    │
    └─ NO → [Next commit]
    ↓
[All commits replayed successfully]
    ↓
Rebase Complete ✓
```

### Aborting vs Completing Rebase

```bash
# Abort mid-rebase (return to original state)
git rebase --abort

# Complete after resolving
git rebase --continue

# To verify it worked
git log --oneline --graph main feature
# Should show feature rebased onto main
```

### Prevention & Best Practices

```bash
# 1. Rebase frequently to reduce conflicts
# Rebasing every day reduces conflict surface

# 2. Keep commits small and focused
# Larger commits = more likely to conflict

# 3. Communicate with team
# Avoid rebasing overlapping commits

# 4. Create backup before rebasing
git branch backup-before-rebase
git rebase main
# If something goes wrong:
git reset --hard backup-before-rebase

# 5. Use rerere to remember conflict resolutions
git config --global rerere.enabled true
# First conflict: you resolve it
# Same conflict again: Git automatically applies your resolution
```

---

## Scenario 10: Broken Refs (Invalid HEAD Ref)

### Overview
Git references (refs) are stored in `.git/refs/` and `.git/refs/heads/` files. If these files contain invalid commit hashes, Git commands will fail when trying to access those branches.

### How Refs Work

```
.git/
├── refs/
│   ├── heads/
│   │   ├── main → contains: abc123def456...  (commit hash)
│   │   └── feature → contains: def456ghi789...
│   ├── remotes/
│   │   └── origin/
│   │       ├── main → contains: abc123def456...
│   │       └── feature → contains: def456ghi789...
│   └── tags/
│       ├── v1.0 → contains: abc123def456...
│       └── v1.1 → contains: def456ghi789...
└── HEAD → contains: "ref: refs/heads/main"
```

### Detailed Reproduction

```bash
mkdir integrity-test && cd integrity-test
git init

# Create some commits
echo "data1" > file1.txt
git add file1.txt
git commit -m "Commit 1"

echo "data2" > file2.txt
git add file2.txt
git commit -m "Commit 2"

# Check the valid refs
cat .git/refs/heads/main
# Output: abc123def456... (valid commit hash)

# SIMULATE CORRUPTION: Write invalid hash
echo "invalid_hash_not_a_real_commit" > .git/refs/heads/main

# Try to use Git
git status
# Output: error: shorthand commit 'invalid_hash_not_a_real_commit' is invalid

git log
# Output: error: ref HEAD doesn't point to a valid commit
```

### Diagnosis

```bash
# Check integrity
git fsck --full
# Output:
# error: invalid ref format for refs/heads/main
# broken ref

# Show current refs
git show-ref

# View reflog (if available)
git reflog
```

### Recovery Strategy 1: Use Reflog to Find Good Hash

```bash
# Reflog shows where HEAD has been
git reflog

# Output:
# abc1234 HEAD@{0}: commit: Commit 2  ← This is the good one!
# def5678 HEAD@{1}: commit: Commit 1

# Restore using reflog entry
git update-ref refs/heads/main abc1234

# Verify
git log --oneline
# Should show both commits again
```

### Recovery Strategy 2: Rebuild Ref from Commit Hash

```bash
# If you know the commit hash (from backup, remote, etc.)
correct_hash="abc1234def456..."

git update-ref refs/heads/main $correct_hash

# Or more directly:
git reset --hard abc1234def456

# Verify
git log
```

### Recovery Strategy 3: Reset to Remote

```bash
# If you have a remote backup
git fetch origin

# Reset to remote's version
git reset --hard origin/main

# Verify
git log --oneline
```

### Complete Diagnosis & Recovery Script

```bash
#!/bin/bash
# Check and repair broken refs

echo "=== Checking Git integrity ==="
git fsck --full --verbose

echo "=== Checking all refs ==="
git show-ref --heads --tags

echo "=== Recent reflog ==="
git reflog --all | head -20

echo "=== Attempting to find good commit ==="
good_commit=$(git reflog show HEAD | head -1 | awk '{print $1}')

if [ -n "$good_commit" ]; then
  echo "Found good commit: $good_commit"
  echo "Restoring..."
  git update-ref refs/heads/main $good_commit
  
  echo "=== Verification ==="
  git log --oneline | head -5
  
  echo "=== Repair successful ==="
else
  echo "Could not find good commit in reflog"
  echo "Try restoring from remote:"
  echo "  git fetch origin"
  echo "  git reset --hard origin/main"
fi
```

### Prevention

```bash
# 1. Don't manually edit .git/refs files
# Always use Git commands

# 2. Keep remote backups
git push origin main
git push origin --all
git push origin --tags

# 3. Enable reflogs
git config core.logAllRefUpdates true

# 4. Run regular integrity checks
git fsck --full --strict

# 5. Use branch protection on remote
# Prevent force-pushes that corrupt refs
```

### Key Commands Reference

| Problem | Diagnosis | Solution |
|---------|-----------|----------|
| Invalid ref hash | `git fsck --full` | `git update-ref` |
| Unknown ref value | `git show-ref` | `git reflog` or remote |
| Lost commit ref | `git reflog` | `git reset --hard <hash>` |
| Ref file missing | `git fsck` | Restore from `.git/refs/backup` |

---

This completes Part 1: Merge & Conflict Handling (Scenarios 1-10) with detailed explanations, code examples, and visual diagrams for each scenario.

---

Would you like me to continue with:
- **PART 2**: Advanced Debugging & Recovery (Scenarios 11-20) - Complex rebases, submodules, monorepo splits, forensics
- **PART 3**: Worktrees, Hooks & Attributes (Scenarios 21-30) - Advanced Git infrastructure
- **PART 4**: Performance & Enterprise Scale (Scenarios 31-47) - Large repo optimization, LFS, Scalar

Each section will include the same level of detail: full explanations, reproduction steps, visual workflows, code examples, and prevention strategies.

---

**Document Features:**
✅ 10+ detailed scenarios per section
✅ Complete reproduction steps (copy-paste ready)
✅ Visual diagrams and workflow charts
✅ Multiple solution approaches per scenario
✅ Prevention & best practices
✅ Real-world examples
✅ Command references and tables
✅ Recovery procedures with scripts
✅ Safety considerations and warnings

**Total Expected Length**: 8,000-12,000 words for all 47 scenarios
**All visual elements**: ASCII diagrams (no external images needed)