Key Concepts & Terminology

Before diving into strategies, a few common terms:

Main / Master: the branch with production‐ready code.

Develop / Integration branch: branch where features are merged for the next release.

Feature branch: branch for individual features or tasks.

Release branch: branch to prepare a release; for stabilization, bug fixes, polishing.

Hotfix branch: for urgent (production) fixes.

CI/CD: Continuous Integration / Continuous Deployment.

Feature flags / toggles: technique to hide/inactivate incomplete features even if code is merged.

Branching Strategies

Here are some widely used ones:

| Strategy                                                       | How it works / Workflow                                                                                                                                                                                                                                                                                                                                                                      | Pros                                                                                                                                                                                                                                       | Cons                                                                                                                                                                                                                                                                         | When to use                                                                                                                                                                                                             |
| -------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **GitFlow**                                                    | Very structured. Persistent long‐lived branches like `main` (or master) + `develop`. Developers branch off `develop` to build features. When a release is ready, a `release` branch is created from `develop`, stabilized, then merged into both `main` and `develop`. For urgent fixes on production, `hotfix` branches are created from `main` and merged back into both. ([Atlassian][1]) | + Good for managing releases, versioning, multiple versions in production. <br>+ Clear separation of development, release, production. <br>+ Hotfixes are handled cleanly. ([codemag.com][2])                                              | − Relatively complex, many branches & merges. <br>− Can slow things down (merge conflicts, waiting for features to finish). <br>− Not ideal for very fast/continuous deployment. <br>− Overhead in managing release branches & merges. ([Atlassian][1])                      | Teams that have: scheduled release cycles, strict versioning (e.g. software that supports multiple versions), need stability, where releases are not constantly pushed. Also good where hotfixes/bugfixes are frequent. |
| **GitHub Flow**                                                | Simpler. Have a single main branch that's always deployable. Feature branches are made off `main`. When a feature is ready and reviewed, merge it (via pull request) into `main`, deploy. No separate `develop`, no dedicated `release` branch. ([GitKraken][3])                                                                                                                             | + Simpler, less overhead. <br>+ Encourages small, frequent merges; good for CI/CD. <br>+ Easier to follow the flow, especially for smaller teams. ([GitKraken][3])                                                                         | − Less structure around releases; risk that `main` may become unstable if feature branches aren’t well tested. <br>− Not well suited if you need multiple concurrent versions or strict release preparation. ([Infinum][4])                                                  | Smaller teams, web apps or services that are deployed frequently, continuous deployment setup, agile workflows.                                                                                                         |
| **GitLab Flow**                                                | More flexible / hybrid. It builds on GitHub Flow but brings in environments (staging, production), feature branches, optionally release or environment branches, plus merge requests and environment‐based deployments. Often you have branches that reflect environments (e.g. `staging`) or tags, etc. ([Infinum][4])                                                                      | + Supports multiple environments while keeping things simpler than full GitFlow. <br>+ Good support for deploying to staging/testing before prod. <br>+ Flexibility to adapt to team/project needs. ([phoenixNAP | Global IT Services][5]) | − Can get complex if many environments / many branches. <br>− Needs good discipline and tooling. <br>− Might have merge conflicts if many feature branches diverge. ([Infinum][4])                                                                                           | Projects that need staging/pre-prod environments, want more control over promotion of code, or when release readiness and QA/testing need separate branches. Medium‐sized teams.                                        |
| **Trunk-Based Development (TBD)**                              | Here, there is essentially a single long-lived branch (often `main` or `trunk`). Developers create very short-lived feature branches (or sometimes commit directly), frequently merge to trunk, often multiple times per day. Features not ready for release may be controlled via feature flags. The idea is: keep integration very frequent so branches don’t drift. ([GitKraken][3])      | + Minimizes merge conflicts. <br>+ Encourages fast feedback, continuous integration/deployment. <br>+ Simpler in terms of branch structure. <br>+ Better visibility of what’s in trunk. ([Infinum][4])                                     | − Requires strong automated testing, CI/CD pipelines. <br>− Needs good safeguards (tests, reviews) because mistakes can go directly into trunk. <br>− Feature flags need discipline. <br>− In larger teams, coordination overhead can still be significant. ([GitKraken][3]) | Teams that deploy frequently (or want to), highly automated infrastructure, small or medium teams, or those aiming for continuous delivery. When you want fast iteration and minimal branch overhead.                   |
| **Feature-Branch Workflow** (sometimes overlapping with above) | The idea is: for each feature/task/fix, you create a separate branch off a stable branch (maybe `develop` or `main`). Once done, it's merged back (via PR), tested, etc. Branches are typically short-lived. Used in both GitFlow, GitHub Flow, GitLab Flow, etc. ([DataCamp][6])                                                                                                            | + Good modularity, isolation. <br>+ Easier code reviews, better tracking of who did what. <br>+ If branch is bad, can abandon without hurting main code. ([DataCamp][6])                                                                   | − Branches can drift, causing conflicts. <br>− If branches live too long, integration can become pain. <br>− Need to keep branches small and frequently merge ‒ otherwise overhead. ([DataCamp][6])                                                                          | Almost always use in combination with one of the above major strategies. Especially useful when multiple features/fixes are worked on in parallel.                                                                      |

[1]: https://www.atlassian.com/git/tutorials/comparing-workflows/gitflow-workflow?utm_source=chatgpt.com "Gitflow Workflow | Atlassian Git Tutorial"
[2]: https://www.codemag.com/Article/2507021/Git-Branching-Strategies?utm_source=chatgpt.com "Git Branching Strategies"
[3]: https://www.gitkraken.com/blog/trunk-based-development?utm_source=chatgpt.com "What is Trunk Based Development? | Git Branching Strategies"
[4]: https://infinum.com/handbook/dotnet/git/branching-strategies?utm_source=chatgpt.com ".NET Handbook | Git / Branching Strategies"
[5]: https://phoenixnap.com/kb/git-branching-strategy?utm_source=chatgpt.com "Git Branching Strategies: What Are Different Branching Strategies?"
[6]: https://www.datacamp.com/tutorial/git-branching-strategy-guide?utm_source=chatgpt.com "Git Branching Strategy: A Complete Guide | DataCamp"

Trade-Offs / What to Consider

When choosing a strategy, consider:

Release frequency: If you deploy many times a day or week, simpler workflows (GitHub Flow, TBD) are better. If you have scheduled releases (monthly, quarterly), you might need something more structured (GitFlow, or GitLab Flow with release branches).

Size of team: Larger teams tend to benefit from more structure to avoid conflicts and confusion. Smaller teams can afford to be more lightweight.

Risk tolerance / Quality assurance: If mistakes in production are very costly, you’ll want more testing, staging branches, hotfix paths, etc.

Infrastructure / automation: If you have strong CI/CD pipelines, automated tests, feature flags, it’s easier to use simpler/fast workflows. If not, you might need more guard rails (release branches, etc.).

Multiple production versions: If you have customers on different versions (say v1.x, v2.x), you’ll need branching to manage maintenance, bug fixes etc.

Developer discipline: No matter the strategy, you’ll need good naming conventions, frequent merges, code reviews, keeping branches short, etc. Poor discipline diminishes advantages.

Comparisons

GitFlow vs GitHub Flow: GitFlow is heavier, more branches, good for versioned releases; GitHub Flow is lighter, continuous, riskier if not properly tested. 
statsig.com
+2
Infinum
+2

GitFlow vs Trunk-Based: Trunk-Based pushes for frequent merges, less branch drift; GitFlow delays merging until features/release stable. More rigorous for those wanting high deployment speed.

Example Scenarios: What to Use When

| Scenario                                                                                        | Strategy that might fit well                        |
| ----------------------------------------------------------------------------------------------- | --------------------------------------------------- |
| A startup releasing a web app every few days, want rapid feedback                               | Trunk Based or GitHub Flow                          |
| A large enterprise with quarterly releases, strict versioning, support for older versions       | GitFlow or GitLab Flow with release/hotfix branches |
| Teams that have environments like dev / staging / QA / prod and want to control what goes where | GitLab Flow or GitFlow with environment branches    |
| Very small team (2–5 ppl) and frequent releases; minimal overhead                               | GitHub Flow or Trunk Based + feature flags          |


local git commands for feature branches and merging,

GitHub Actions workflows (CI + sequential deploys for QA → Pre-Prod → Prod),

GitHub branch protection configuration you must set (so PRs can’t be merged unless the feature branch CI is green and integration has passed QA+PreProd),

how to tag and move master to integration after production success,

an example showing a second feature branch flow.

I assume your repo is on GitHub and you have push access + permission to edit branch protection rules and add GitHub Actions workflows. I’ll use branch names you gave: master and integration. Feature branches will be feature/<ticket>.

1) Branch setup — local & remote (commands)

Start from latest integration (make sure you have latest from origin):

# fetch all and update local refs
git fetch origin

# make sure local integration exists and is up to date
git checkout integration
git pull origin integration


Create a feature branch (example feature FEATURE-123-add-foo):

# create feature branch
git checkout -b feature/FEATURE-123-add-foo

# make changes, then commit
git add .
git commit -m "FEATURE-123: implement foo"

# push to remote
git push -u origin feature/FEATURE-123-add-foo


Repeat the above for any other feature branch.

2) Create CI that must pass on feature branch before PR merge

Goal: PR to integration must be approved only if the feature-branch CI passes. To enforce this we:

Create a GitHub Actions workflow that runs on pushes to feature/** (and on PRs).

Name that workflow (the workflow name will appear as a required status check).

Configure GitHub Branch Protection on integration to require that workflow's status check.

Create file .github/workflows/feature-ci.yml:

name: feature-ci

on:
  push:
    branches:
      - 'feature/**'
  pull_request:
    branches:
      - integration

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Set up Node.js    # example: adjust to your language
        uses: actions/setup-node@v4
        with:
          node-version: '18'
      - name: Install deps
        run: npm ci
      - name: Run tests
        run: npm test


Notes:

name: feature-ci is important — this name will be listed as the status check GitHub expects.

Replace node setup / npm with build/test commands appropriate to your project (Maven, Gradle, Go, Python, etc.)

Now configure branch protection for integration:

Go to your repo → Settings → Branches → Branch protection rules → Add rule:

Branch name pattern: integration

Check Require status checks to pass before merging

Select the feature-ci check (you may need to open at least one PR so the workflow runs once and the check name appears)

Optionally: enable Require pull request reviews before merging (recommended)

Optionally: enable Require linear history, Require signed commits, etc.

Save.

This ensures: a PR into integration cannot be merged until feature-ci (the workflow run triggered from the feature branch / PR) succeeds.

3) PR → integration flow (commands + GH CLI)

After pushing feature branch:

# create a PR using GitHub CLI (or use GitHub UI)
gh pr create --base integration --head feature/FEATURE-123-add-foo \
  --title "FEATURE-123: implement foo" --body "Implements foo; ready for CI"


When PR is open:

The feature-ci workflow runs for that feature branch & PR.

If it fails, branch protection prevents merge (PR cannot be merged).

If it succeeds, reviewers can approve and merge the PR into integration.

To merge via CLI after approval:

gh pr merge <PR_NUMBER_OR_URL> --merge --delete-branch
# or --squash or --rebase depending on your policy


gh pr merge will fail if required status checks are not green.

4) QA → Pre-Prod → Prod deployments (sequential checks)

You said: after merging into integration, code must be tested in QA, then Pre-Prod, then Prod, and the production move must happen only if previous environments succeeded.

We'll implement three GitHub Actions workflows:

deploy-qa.yml — triggers on push to integration.

deploy-preprod.yml — triggers only after deploy-qa completes successfully (uses workflow_run).

deploy-prod.yml — triggers only after deploy-preprod completes successfully (uses workflow_run).

This ensures sequential promotion.

Create .github/workflows/deploy-qa.yml:

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
      - name: Prepare deployment
        run: echo "Preparing QA deployment..."
      - name: Deploy to QA
        run: |
          # run deployment commands or call CD tool (kubectl, aws, etc.)
          echo "Deploying to QA..."
      - name: Run QA smoke tests
        run: |
          # run your QA test script here; exit non-zero to fail
          ./scripts/run-qa-tests.sh


Create .github/workflows/deploy-preprod.yml:

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
      - name: Run Pre-Prod tests
        run: |
          ./scripts/run-preprod-tests.sh


Create .github/workflows/deploy-prod.yml:

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
      - name: Deploy to Prod
        run: |
          echo "Deploying to Production..."
      - name: Run Prod smoke tests
        run: |
          ./scripts/run-prod-smoke-tests.sh


What this does:

deploy-qa runs on every push to integration.

deploy-preprod runs only if deploy-qa completed successfully.

deploy-prod runs only if deploy-preprod completed successfully.

If any stage fails, the next stage will not run (because workflow_run job uses if: ...conclusion == 'success'), and the pipeline stops.

5) Enforce that master only moves to integration tip once Prod is successful

We want master to be updated only after deploy-prod succeeds. There are two complementary controls:

A. Use branch protection on master to require status checks for the deploy-prod workflow (and any others you want). That prevents merging into master before deploy-prod passes.

B. Operational step (recommended): After deploy-prod is green, fast-forward master to integration and tag.

Here are commands to move master to integration and create the release tag (to be run after you confirm deploy-prod succeeded):

# fetch
git fetch origin

# ensure both branches are local and up-to-date
git checkout integration
git pull origin integration

# switch to master & fast-forward merge (fail if not a fast-forward)
git checkout master
git pull origin master

# fast-forward master to integration tip
git merge --ff-only integration

# create tag (example v1.0)
git tag -a v1.0 -m "Release v1.0 - promoted from integration on $(date -u +%F)"

# push master and tags
git push origin master
git push origin v1.0


If you prefer to do this via GitHub (recommended for auditability):

Open a Pull Request from integration → master (this PR will show the deploy-prod status check result).

Merge that PR once deploy-prod is green.

After merge, create a tag via GitHub UI or via the same local commands (tag after you fetch the merged master).

You can also automate tag creation with an extra workflow that runs on successful deploy-prod completion. Example quick workflow tag-on-prod-success.yml:

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
      - name: Configure git
        run: |
          git config user.name "github-actions[bot]"
          git config user.email "github-actions[bot]@users.noreply.github.com"
      - name: Fast-forward master to integration and tag
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
        run: |
          # fetch all branches
          git fetch origin
          git checkout master
          git pull origin master
          git checkout integration
          git pull origin integration
          # fast-forward master to integration
          git checkout master
          git merge --ff-only integration || (echo "ff-only merge failed"; exit 1)
          # compute version (you may want to derive it dynamically)
          TAG="v1.0"
          git tag -a "$TAG" -m "Release $TAG"
          git push origin master
          git push origin "$TAG"


Note: For production safety you may want this action to be triggered manually (via workflow_dispatch) or require approvals. GitHub enforces limits for the GITHUB_TOKEN when trying to perform protected-branch operations — you might need to use a personal access token in a secret for pushing to protected master, unless you permit GitHub Actions to bypass restrictions. Many organizations choose to perform final tag/merge manually to preserve control.

6) Example — second feature branch flow (end-to-end)

Assume integration currently up to date.

Developer A (feature1):

git checkout integration
git pull origin integration
git checkout -b feature/FEATURE-123-add-foo
# implement
git add .
git commit -m "FEATURE-123: add foo"
git push -u origin feature/FEATURE-123-add-foo
# create PR: feature/FEATURE-123-add-foo -> integration
gh pr create --base integration --head feature/FEATURE-123-add-foo --title "FEATURE-123" --body "..."


feature-ci runs on the feature branch.

If feature-ci passes, reviewers can approve and merge to integration.

Once merged to integration, deploy-qa runs (on integration push) → if success then deploy-preprod → if success then deploy-prod.

Developer B (feature2) while feature1 is in progress:

git checkout integration
git pull origin integration      # get latest integration (if feature1 merged you will have it)
git checkout -b feature/FEATURE-456-add-bar
# implement
git add .
git commit -m "FEATURE-456: add bar"
git push -u origin feature/FEATURE-456-add-bar
gh pr create --base integration --head feature/FEATURE-456-add-bar --title "FEATURE-456" --body "..."


Again the feature-ci runs for feature2. Merge only if CI green.

Multiple features can be merged into integration (serialized by PR merges).

Deployments run on integration tip (so QA will see all merged features present in integration at that moment).

7) Important implementation notes & permissions

Branch protection behavior

Branch protection rules are set on the target branch (integration, master) and require specific status checks to pass before merge. Status checks correspond to the workflow name shown by Actions (e.g., feature-ci, deploy-qa, etc.). You may need to run a workflow at least once so the check name appears in the repo’s list when adding required status checks.

PR cannot be merged if feature branch CI fails

Because integration requires feature-ci check, GitHub will block merging until the feature branch CI run is green.

Sequential deploys

Using workflow_run makes the deploys sequential and dependable: preprod only starts if QA succeeded; prod only starts if preprod succeeded.

Protected branches & Actions pushing

If you automate merging/master fast-forward/tagging using Actions, you may need to store a PAT (personal access token) in repo secrets with appropriate permissions if GITHUB_TOKEN is restricted from pushing to protected branches.

Feature flags

If features are merged to integration but not ready for production, use feature flags to hide in-progress features from users but still keep them in codebase and CI.

Rollback & failure handling

Make your deploy scripts robust: on failure, mark the environment failed, notify the team, and do not proceed to the next stage. workflow_run chain prevents propagation on failure.

Notifications

Configure Actions to notify Slack/email on failures or successes per environment so your team can act quickly.

8) Minimal checklist to implement this in your repo

Add the three workflow files:

.github/workflows/feature-ci.yml

.github/workflows/deploy-qa.yml

.github/workflows/deploy-preprod.yml

.github/workflows/deploy-prod.yml

(optional) .github/workflows/tag-on-prod-success.yml for auto-tagging

Run at least one PR/one push so Actions appear in the repo.

Configure Branch Protection:

For integration: require feature-ci status check + PR reviews.

For master: require deploy-prod (and optionally deploy-preprod, deploy-qa) before merging.

Developers: always branch off integration, push feature branches, create PRs to integration. Merge only when feature-ci is green and approvals are given.

Release: after deploy-prod success, merge integration → master (fast-forward) and tag vX.Y.

9) Example commands summary (quick cheat sheet)

Create feature branch & PR:

git checkout integration
git pull origin integration
git checkout -b feature/FEATURE-123
# code...
git commit -am "FEATURE-123: implement"
git push -u origin feature/FEATURE-123
gh pr create --base integration --head feature/FEATURE-123 --title "FEATURE-123"


Merge PR once CI green & reviews done (via GH UI or CLI):

gh pr merge <pr-number> --merge


After deploy-prod success — fast-forward master & tag:

git fetch origin
git checkout integration
git pull origin integration
git checkout master
git pull origin master
git merge --ff-only integration
git tag -a v1.0 -m "Release v1.0"
git push origin master
git push origin v1.0


If you want, I can:

Provide complete ready-to-use example workflow YAMLs tuned to your tech stack (Java/Maven, Python/pytest, Docker build, etc.).

Provide the exact Branch Protection setting steps with screenshots / UI guide.

Create a tag-on-prod-success workflow that auto-increments semantic versions (e.g., v1.0.1) instead of using a fixed v1.0.



1. Initialize a Git Repository
git init


✅ Explanation:
Initializes a new empty Git repository in your current directory. It creates a hidden folder .git/ that stores all commit objects, branches, and metadata.

🧩 What happens internally:

.git/ folder is created

A default branch (master or main) is created

Git now starts tracking files in this directory

📚 Extra:

You can also use git init <directory> to initialize in a different folder.

🧾 2. Check the status of files
git status


✅ Explanation:
Shows the current state of your working directory and staging area — tells which files are:

Modified but not staged

Staged but not committed

Untracked

🧩 Internally:
Git compares working directory ↔ staging area ↔ last commit snapshot.

📂 3. Add files to the Staging Area
git add file


✅ Explanation:
Moves changes from the working directory to the staging area.

🧩 Internally:
Git takes a snapshot of the file’s content and adds it to the staging area.

📚 Extra:

git add . → adds all modified/untracked files

git add -p → interactively stage only selected changes

💾 4. Commit files
git commit file -m "add comment"


✅ Explanation:
Records the changes from staging area into the local repository’s history.

🧩 Internally:
A commit object is created with:

Hash ID (SHA-1)

Parent commit reference

Author and timestamp

Staged content snapshot

📚 Extra:

git commit -a -m "msg" → skips staging, commits all tracked files

git commit --amend → modify the last commit

📜 5. View commit logs
git log --oneline


✅ Explanation:
Shows all commits in short form: commit hash + commit message.

📚 Extra:

git log --graph --decorate --oneline → shows branch/tree visualization

🌍 6. Add a Remote Repository
git remote add origin https://github.com/username/repo.git


✅ Explanation:
Links your local Git repo with a remote GitHub repository named origin.

🧩 Internally:
Git stores the remote URL in .git/config file.

📚 Extra:

git remote -v → view all remote URLs

git remote remove origin → remove connection

☁️ 7. Push Changes to Remote
git push origin master


✅ Explanation:
Uploads your local commits to the remote branch (master in this case).

🧩 Internally:
Git compares local branch with remote tracking branch and sends new commits.

📚 Extra:

git push -u origin main → sets upstream branch (next time just do git push)

Authentication required (username/token)

🪞 8. Clone a Remote Repository
git clone https://github.com/username/repo.git


✅ Explanation:
Copies a remote repository to your local machine.

🧩 Internally:

Creates a directory with repo name

Initializes .git/ folder

Fetches all commits and sets origin remote automatically

🌱 9. Branching and Checkout
git checkout -b sarah


✅ Explanation:
Creates a new branch named sarah and switches to it.

git branch


✅ Shows all branches (current branch marked with *).

git checkout master


✅ Switch back to master branch.

🔀 10. Merge Branches
git merge max


✅ Explanation:
Merges branch max into the current branch.

🧩 Internally:
Git finds the common ancestor and merges commits — may produce conflicts if same lines changed.

☁️ 11. Push Branch to Remote
git push origin sarah


✅ Explanation:
Pushes your local branch sarah to the remote repository.

🔄 12. Fetch, Pull and Rebase
a) Fetch
git fetch origin master


✅ Downloads changes from remote without merging.

b) Pull
git pull origin master


✅ Fetches and merges remote branch into your current branch.

c) Rebase
git rebase sarah


✅ Re-applies commits from your current branch on top of sarah. Keeps history linear.

✏️ 13. Interactive Rebase
git rebase -i HEAD~4


✅ Explanation:
Allows you to edit, squash, reorder last 4 commits.

🧩 Used for:

Cleaning commit history before push

🍒 14. Cherry-pick
git cherry-pick aaba5


✅ Explanation:
Applies a specific commit (aaba5) from another branch into current branch.

🔍 15. Inspect Commit Contents
git cat-file -p 4cdf4


✅ Explanation:
Displays the contents and metadata of a commit, tree, or blob.

↩️ 16. Revert and Reset
git revert 8ad5d


✅ Creates a new commit that undoes the changes made by commit 8ad5d.

git reset --soft HEAD~1


✅ Moves HEAD back by one commit, keeps changes in staging area.

git reset --hard HEAD~1


✅ Moves HEAD back by one commit and discards all local changes.

📚 Extra:

git reset --mixed (default) → unstages files but keeps changes in working directory

🧳 17. Stashing Changes
git stash


✅ Saves uncommitted changes temporarily and cleans working directory.

git stash list


✅ Lists all stashes.

git stash show stash@{1}


✅ Shows what changes are saved in stash 1.

git stash pop stash@{1}


✅ Applies stash 1 back to working directory and removes it from stash list.

🧠 18. Important Missing / Related Commands
| Command                                              | Purpose                                                    |
| ---------------------------------------------------- | ---------------------------------------------------------- |
| `git config --global user.name "Name"`               | Set username for commits                                   |
| `git config --global user.email "email@example.com"` | Set commit email                                           |
| `git diff`                                           | View differences between files or commits                  |
| `git rm filename`                                    | Remove a file from Git and staging area                    |
| `git mv old new`                                     | Rename or move a file                                      |
| `git tag v1.0`                                       | Create version tag                                         |
| `git show <commit_id>`                               | View full details of a commit                              |
| `git branch -d branchname`                           | Delete a branch                                            |
| `git remote show origin`                             | Show details of remote branches                            |
| `git reflog`                                         | Show history of HEAD movements (undo dangerous operations) |

💡 Summary (Git Lifecycle)
Working Directory  →  git add  →  Staging Area  →  git commit  →  Local Repo
Local Repo  →  git push  →  Remote Repo
Remote Repo →  git pull/fetch  →  Local Repo


Would you like me to create a visual Git flow diagram (Working → Staging → Commit → Remote) to attach to these notes? It would help you explain this in interviews or documentation.

🧠 GIT ADMINISTRATION & TROUBLESHOOTING COMMANDS (Full Reference)
🧩 1. Troubleshooting and Debugging
| Command                                                   | Description / Use Case                                                                                                         |
| --------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------ |
| `git fsck`                                                | Verify the integrity and connectivity of objects in the `.git/objects` database. Detects corrupt or dangling commits.          |
| `git fsck --full`                                         | Performs a deep integrity check of all Git objects (commits, trees, blobs, and tags). Useful after crashes or disk corruption. |
| `git reflog`                                              | Shows the movement of `HEAD` and branches. Recover lost commits or branches after reset or rebase.                             |
| `git diff`                                                | Compares changes between working directory, staging area, or specific commits.                                                 |
| `git blame <file>`                                        | Displays who changed which line and in which commit — ideal for identifying regressions.                                       |
| `git log --oneline --graph --all`                         | Displays a visual history of all branches and commits.                                                                         |
| `git show-ref`                                            | Lists all references (branches/tags) and their commit hashes. Useful for diagnosing reference mismatches.                      |
| `git rev-parse HEAD`                                      | Shows the commit hash that `HEAD` currently points to. Useful for scripting or debugging detached HEAD states.                 |
| `git shortlog -sn`                                        | Summarizes commit activity by author (audit/ownership tracking).                                                               |
| `git show <commit>`                                       | Displays commit details (message, author, and changes).                                                                        |
| `git describe --tags`                                     | Shows the most recent tag reachable from a commit (useful for release debugging).                                              |
| `git bisect start` → `git bisect good` / `git bisect bad` | Binary search through commits to find the exact commit that introduced a bug.                                                  |


⚙️ 2. Repository Optimization and Maintenance

| Command                           | Description / Use Case                                                                                 |
| --------------------------------- | ------------------------------------------------------------------------------------------------------ |
| `git gc`                          | Runs garbage collection — cleans unnecessary files and optimizes local repo (compresses file history). |
| `git gc --prune=now --aggressive` | Aggressive cleanup for heavily used repos or after large deletions.                                    |
| `git prune`                       | Removes unreachable objects (dangling commits/blobs). Typically runs with `gc`.                        |
| `git clean -n`                    | Dry-run to show which untracked files/directories will be removed.                                     |
| `git clean -fd`                   | Deletes untracked files and directories. Common before clean rebuilds.                                 |
| `git maintenance run`             | Runs background optimization tasks (Git 2.30+). Keeps repos fast without manual GC.                    |
| `git repack`                      | Repackages loose objects into packed files to save space.                                              |
| `git count-objects -vH`           | Displays unpacked object count and storage space used in human-readable format.                        |
| `git verify-pack <pack-file>`     | Verifies integrity of `.pack` files in the object database.                                            |
| `git remote prune origin`         | Removes stale remote-tracking branches that were deleted remotely.                                     |
| `git fsmonitor--daemon`           | Monitors filesystem changes to optimize status/diff operations.                                        |
| `git config --global gc.auto 0`   | Disables automatic garbage collection (useful for CI/CD performance tuning).                           |

🧰 3. Handling Errors and Recovery

| Command                                | Description / Use Case                                                                    |
| -------------------------------------- | ----------------------------------------------------------------------------------------- |
| `git reset --hard <commit>`            | Resets working directory, staging area, and HEAD to specific commit (⚠ destructive).      |
| `git restore <file>`                   | Restores file from last commit (Git 2.23+ replacement for `checkout -- file`).            |
| `git checkout <commit>`                | Temporarily move HEAD to a specific commit or branch.                                     |
| `git revert <commit>`                  | Creates a new commit that undoes the effect of a specific commit (safe for shared repos). |
| `git stash`                            | Temporarily saves uncommitted changes (useful before switching branches).                 |
| `git stash list`                       | Lists all stashes.                                                                        |
| `git stash apply`                      | Applies most recent stash (keeps it in stash list).                                       |
| `git stash pop`                        | Applies most recent stash and removes it from stash list.                                 |
| `git reflog expire --expire=now --all` | Clean up old reflog entries after recovery.                                               |
| `git reset --merge`                    | Abort merge operation but keep uncommitted local changes.                                 |
| `git merge --abort`                    | Cancels a merge in progress, reverting the merge state.                                   |
| `git rebase --abort`                   | Cancels an ongoing rebase safely.                                                         |
| `git restore --staged <file>`          | Unstage a file from the staging area.                                                     |
| `git cherry-pick --abort`              | Abort ongoing cherry-pick process.                                                        |
| `git fsck --lost-found`                | Collects dangling commits in `.git/lost-found` for manual recovery.                       |


🧪 4. Advanced Debugging and Low-Level Interrogation

| Command                             | Description / Use Case                                                                 |
| ----------------------------------- | -------------------------------------------------------------------------------------- |
| `git cat-file -p <object>`          | Displays the content of a Git object (commit/tree/blob).                               |
| `git cat-file -t <object>`          | Shows the type of the object (commit/tree/blob/tag).                                   |
| `git ls-tree <tree-ish>`            | Lists contents of a tree object (like `ls` for commits).                               |
| `git rev-list --all`                | Lists all commits in reverse chronological order.                                      |
| `git for-each-ref`                  | Iterates over all references and displays metadata (used in automation and reporting). |
| `git check-ignore <path>`           | Tests whether a file is ignored by `.gitignore`.                                       |
| `git check-attr <attribute> <path>` | Displays attribute information (e.g., `binary`, `text`, `merge`).                      |
| `git bugreport`                     | Collects Git environment and configuration details to file a Git bug report.           |
| `git verify-commit <commit>`        | Verifies the GPG signature of a commit.                                                |
| `git verify-tag <tag>`              | Verifies GPG-signed tag.                                                               |
| `git show-index <pack-file>.idx`    | Displays index contents for a pack file.                                               |
| `git unpack-objects`                | Reads a packfile and writes objects into `.git/objects` (for recovery).                |
| `git update-ref`                    | Manually update reference pointers (branch heads/tags). Used cautiously.               |

🪪 5. Environment & Configuration Debugging

| Command                                          | Description / Use Case                                                          |
| ------------------------------------------------ | ------------------------------------------------------------------------------- |
| `git --version`                                  | Check installed Git version.                                                    |
| `which git`                                      | Show location of Git binary (Linux/macOS).                                      |
| `git config --list --show-origin`                | Shows all Git configuration sources and values.                                 |
| `git help <command>` or `git <command> --help`   | View manual for a Git command.                                                  |
| `git help --all`                                 | Lists all available Git commands.                                               |
| `git help -g`                                    | Lists Git concept guides (branches, workflows, etc.).                           |
| `GIT_TRACE=1 git <command>`                      | Enable general tracing (show what Git is executing).                            |
| `GIT_TRACE_PACKET=1 git <command>`               | Show packet-level info (network communication debugging).                       |
| `GIT_TRACE_PERFORMANCE=1 git <command>`          | Display performance timings for Git operations.                                 |
| `GIT_CURL_VERBOSE=1 git <command>`               | Show detailed HTTP(S) communication — useful for GitHub auth/debug.             |
| `GIT_SSH_COMMAND="ssh -i <key>" git clone <url>` | Use custom SSH key for Git operation.                                           |
| `GIT_AUTHOR_NAME` / `GIT_AUTHOR_EMAIL`           | Override commit metadata temporarily.                                           |
| `GIT_PAGER=cat git log`                          | Disable paging of Git output.                                                   |
| `GIT_DIR` / `GIT_WORK_TREE`                      | Manually specify repository directory and working tree (admin troubleshooting). |

🧩 6. Git Repository Recovery and Backup (Advanced)

| Command                                  | Description / Use Case                                                     |                                                   |
| ---------------------------------------- | -------------------------------------------------------------------------- | ------------------------------------------------- |
| `git bundle create backup.bundle --all`  | Create a single-file backup of the repository.                             |                                                   |
| `git clone --mirror <repo>`              | Creates a bare mirror of all refs and objects (for backup or migration).   |                                                   |
| `git bundle verify backup.bundle`        | Check the validity of a Git bundle file.                                   |                                                   |
| `git bundle list-heads backup.bundle`    | Show refs included in a bundle.                                            |                                                   |
| `git fsck --lost-found`                  | Recover orphaned commits and move them into `.git/lost-found`.             |                                                   |
| `git rev-list --objects --all            | grep "<filename>"`                                                         | Locate which commit introduced a particular file. |
| `git replace <bad-commit> <good-commit>` | Temporarily replace a commit reference (useful for broken history repair). |                                                   |


🧱 7. Git Server-Side Administration (Bare Repos / Shared Repos)

| Command                                            | Description / Use Case                                                |
| -------------------------------------------------- | --------------------------------------------------------------------- |
| `git init --bare`                                  | Initialize a bare repository (no working directory, used on servers). |
| `git daemon --reuseaddr --base-path=/repos /repos` | Start Git service to serve repositories over git:// protocol.         |
| `git update-server-info`                           | Update server metadata files for dumb HTTP transports.                |
| `git receive-pack <repo>`                          | Receive and process pushes to a repository (used by servers).         |
| `git upload-pack <repo>`                           | Handle fetch/clone operations (server-side).                          |
| `git config receive.denyNonFastForwards true`      | Prevent force-push to protect main branches.                          |
| `git config core.sharedRepository group`           | Allow group write access (multi-user repos).                          |

🧭 8. Useful Diagnostic Workflows

| Goal                                 | Commands                                                                |                           |
| ------------------------------------ | ----------------------------------------------------------------------- | ------------------------- |
| **Recover lost commit after reset**  | `git reflog` → `git checkout <commit>` → `git branch recovery <commit>` |                           |
| **Identify corrupted object**        | `git fsck` → `git cat-file -p <object>`                                 |                           |
| **Audit repo size**                  | `git count-objects -vH` → `du -sh .git/objects/`                        |                           |
| **Identify large files in history**  | `git rev-list --objects --all                                           | sort -k 2 > allfiles.txt` |
| **Inspect ignored files issue**      | `git check-ignore -v <path>`                                            |                           |
| **Test push using specific SSH key** | `GIT_SSH_COMMAND="ssh -i ~/.ssh/key.pem" git push origin main`          |                           |
| **Debug slow operations**            | `GIT_TRACE_PERFORMANCE=1 git fetch -v`                                  |                           |

🧾 9. Bonus: Automation & Audit Commands for Admins

| Command                                | Description                                      |
| -------------------------------------- | ------------------------------------------------ |
| `git config --system`                  | Edit system-wide Git configuration.              |
| `git config --global`                  | Edit user-wide Git configuration.                |
| `git config --local`                   | Edit repository-level Git configuration.         |
| `git credential reject`                | Remove cached credentials.                       |
| `git verify-commit` / `git verify-tag` | Verify signed commits/tags for audit compliance. |
| `git notes add -m "comment"`           | Attach admin/audit notes to a commit.            |


🧱 Understanding .git/ Folder Structure

When you run:

git init


Git creates a hidden folder called .git/ in your project root.

This folder is the entire brain of your repository — it stores everything needed for version control: commits, branches, tags, logs, and configuration.

If you delete .git/, you lose the whole repo history.

📂 Typical .git/ Folder Layout

.git/
├── HEAD
├── config
├── description
├── index
├── hooks/
├── info/
├── objects/
│   ├── info/
│   └── pack/
├── refs/
│   ├── heads/
│   ├── remotes/
│   └── tags/
├── logs/
│   ├── HEAD
│   └── refs/
│       ├── heads/
│       └── remotes/
├── packed-refs
└── COMMIT_EDITMSG

🧩 Detailed Explanation of Each Component

1️⃣ HEAD

📄 File: .git/HEAD

✅ Purpose:
It points to the current branch reference or a specific commit (in detached state).

🧩 Example content:

ref: refs/heads/main


This means you’re currently on branch main.

If you checkout a specific commit (detached HEAD):

8adf5e123a2fbd8e7bdf2c69b0e9e5e4a3b2c3d1


💡 Helps you when:

Repo shows “detached HEAD” issue — you can inspect or manually reset .git/HEAD.

Recovering to last branch: you can edit this file to point back to refs/heads/main.

2️⃣ config

📄 File: .git/config

✅ Purpose:
Stores repository-specific configuration such as remote URLs, branches, user info.

🧩 Example content:

[core]
    repositoryformatversion = 0
    filemode = true
    bare = false
[remote "origin"]
    url = https://github.com/user/repo.git
    fetch = +refs/heads/*:refs/remotes/origin/*
[branch "main"]
    remote = origin
    merge = refs/heads/main


💡 Helps you when:

Remote push/pull fails — check this file for bad URLs.

Wrong branch mapping — check [branch "main"] section.

You can manually fix it if git remote commands break.

3️⃣ description

📄 File: .git/description

✅ Purpose:
Used by GitWeb or visual Git interfaces as a short description of the repository.

💡 You can ignore this in normal development.

4️⃣ index

📄 File: .git/index

✅ Purpose:
Binary file that represents the staging area (snapshot of files added using git add).

🧩 Internally:
When you run git add file.txt, its hash and metadata are stored here until commit.

💡 Helps you when:

“Staging area corrupted” or “index.lock” errors appear.
Fix:

rm -f .git/index
git reset


This rebuilds the index from HEAD commit.

5️⃣ hooks/

📁 Folder: .git/hooks/

✅ Purpose:
Contains sample hook scripts that run automatically at certain Git events (commit, push, etc.)

🧩 Examples:

pre-commit.sample
post-commit.sample
pre-push.sample


💡 Helps you when:

You want to enforce policies (e.g., reject commits without JIRA ID).

Custom automation (e.g., auto-run tests before push).

You can rename pre-commit.sample → pre-commit and make it executable to activate.

6️⃣ info/

📁 Folder: .git/info/

✅ Purpose:
Contains exclude patterns that are local-only (not shared like .gitignore).

🧩 Important file:

.git/info/exclude


Works just like .gitignore but not checked into the repo.

💡 Helps you when:

You want to ignore local files (e.g., .vscode/, *.log) without modifying .gitignore.

7️⃣ objects/

📁 Folder: .git/objects/

✅ Purpose:
Stores all Git data — commits, trees, and file blobs — in a content-addressable format.

📂 Structure:
objects/
├── ab/
│   └── cdef1234... (compressed file blob)
├── info/
└── pack/
    ├── pack-123abc.pack
    └── pack-123abc.idx
🧩 Explanation:

Each file (commit, tree, blob, tag) stored as a compressed object identified by SHA-1 hash.

pack/ contains packed objects (compressed archive of multiple Git objects).

💡 Helps you when:

Repo corruption → use git fsck to verify objects here.

Disk cleanup → use git gc, git prune to optimize or remove unreachable objects.

8️⃣ refs/

📁 Folder: .git/refs/

✅ Purpose:
Stores pointers (references) to commit hashes for branches, tags, and remotes.

📂 Structure:
refs/
├── heads/
│   ├── main
│   ├── feature/login
├── tags/
│   └── v1.0
└── remotes/
    └── origin/
        ├── main
        ├── dev

🧩 Example content of .git/refs/heads/main:

4cdf4a5f8734a9d14b5e23d1c9e81f2b34d2a1a3


💡 Helps you when:

You lose a branch — you can inspect these refs directly.

Recover deleted branch:

git reflog show main
git branch recover <commit_hash>

9️⃣ logs/

📁 Folder: .git/logs/

✅ Purpose:
Contains reflog history — tracks every movement of HEAD and branches.

📂 Structure:
logs/
├── HEAD
└── refs/
    ├── heads/
    │   ├── main
    │   └── feature/login
    └── remotes/
        └── origin/
            ├── main
💡 Helps you when:

You accidentally reset, rebase, or delete commits.

git reflog


shows where HEAD has been → recover lost commits.

🔟 packed-refs

📄 File: .git/packed-refs

✅ Purpose:
Git sometimes packs branch and tag references into this file for efficiency.

🧩 Example content:

d3b07384d113edec49eaa6238ad5ff00 refs/tags/v1.0
a7c6d0cfa4c8e8bb423cdd7ff892dd3a refs/heads/main


💡 Helps you when:

You can’t find a tag or branch file under refs/ — check here.

You can safely delete it; Git will rebuild it with git pack-refs --all.

🔢 COMMIT_EDITMSG

📄 File: .git/COMMIT_EDITMSG

✅ Purpose:
Stores the message from your most recent commit (temporary file).

💡 Helps you when:

You abort a commit mid-way and want to recover the last commit message.

🧠 Other Supporting Files (May Appear Later)
| File/Folder                           | Purpose                                                                              |
| ------------------------------------- | ------------------------------------------------------------------------------------ |
| `.git/ORIG_HEAD`                      | Saves previous HEAD before dangerous ops (merge/rebase/reset). Helpful to rollback.  |
| `.git/MERGE_HEAD`                     | Created during merge — stores the commit hash being merged.                          |
| `.git/REBASE_HEAD`                    | Tracks progress during rebase.                                                       |
| `.git/CHERRY_PICK_HEAD`               | Identifies the commit currently being cherry-picked.                                 |
| `.git/HEAD.lock` or `.git/index.lock` | Lock files created during operations — can delete safely if Git crashed mid-process. |
| `.git/shallow`                        | Exists in shallow clones — lists the boundary commits.                               |
| `.git/FETCH_HEAD`                     | Records the result of the last `git fetch` operation (used by `git pull`).           |
| `.git/ORIG_HEAD`                      | Backup of HEAD before a rebase/reset/merge — useful to recover from mistakes.        |

🧩 When Things Go Wrong — Recovery Tips
| Problem                       | Recovery using `.git` files                                 |
| ----------------------------- | ----------------------------------------------------------- |
| **Lost commits**              | Use `.git/logs/HEAD` or `git reflog` to find commit hashes. |
| **Detached HEAD**             | Check `.git/HEAD` and point it back to branch ref.          |
| **Corrupt staging area**      | Delete `.git/index` and run `git reset`.                    |
| **Branch missing**            | Check `.git/refs/heads/` or `packed-refs` to recreate it.   |
| **Remote URL broken**         | Fix `.git/config` `[remote "origin"]` entry manually.       |
| **Merge stuck / aborted**     | Delete `.git/MERGE_HEAD` and `.git/index.lock` then retry.  |
| **Repo performance degraded** | Run `git gc --aggressive` to repack `.git/objects`.         |
| **Disk corruption**           | Run `git fsck` to verify and `.git/lost-found` to recover.  |

🧾 Summary Table

| Component                       | Type   | Role                               |
| ------------------------------- | ------ | ---------------------------------- |
| `HEAD`                          | File   | Points to current branch or commit |
| `config`                        | File   | Repository configuration           |
| `index`                         | File   | Tracks staged changes              |
| `objects/`                      | Folder | Stores all commit data             |
| `refs/`                         | Folder | Branches, tags, remotes            |
| `logs/`                         | Folder | Tracks HEAD & branch changes       |
| `hooks/`                        | Folder | Custom scripts for Git events      |
| `info/`                         | Folder | Local ignore rules                 |
| `packed-refs`                   | File   | Packed branch/tag pointers         |
| `description`                   | File   | Description for GitWeb             |
| `COMMIT_EDITMSG`                | File   | Last commit message                |
| `MERGE_HEAD`, `ORIG_HEAD`, etc. | Files  | Temporary operational metadata     |

🧠 Pro Tip: Quick Commands for .git Diagnostics

| Goal                               | Command                                      |
| ---------------------------------- | -------------------------------------------- |
| View current HEAD                  | `cat .git/HEAD`                              |
| List local branches                | `ls .git/refs/heads/`                        |
| View current config                | `cat .git/config`                            |
| Check object count                 | `git count-objects -vH`                      |
| View reflog (recover lost commits) | `git reflog`                                 |
| Validate repo integrity            | `git fsck --full`                            |
| See packfile contents              | `git verify-pack -v .git/objects/pack/*.idx` |

ISSUE 1 — Merge conflict on the same file (Beginner)

Goal: practice creating a branch, generating a conflict, and resolving it cleanly.

GitHub issue template (paste into an Issue)

Title: Merge conflict in README.md when merging feature/a into main

Body:

**Summary**
Merging branch `feature/a` into `main` causes a conflict in README.md.

**Steps to reproduce**
1. Clone repo and run: (see reproduction commands in the issue description)
2. Create branch `feature/a` and modify README.md.
3. Create branch `feature/b` and modify same lines in README.md.
4. Attempt to merge `feature/a` into `main`.

**Expected**
Merge succeeds or shows a trivial conflict that is easy to resolve.

**Actual**
`git merge` stops with conflict markers inside README.md.

**Logs / output**
Include `git status` and `git diff` output.

**Environment**
Git version: <run `git --version`>
OS: <your OS>

Reproduce locally (commands)
mkdir git-issue-merge && cd git-issue-merge
git init
echo "Project README" > README.md
git add README.md
git commit -m "initial commit"

# branch A
git checkout -b feature/a
sed -n '1,$p' README.md > /tmp/old && printf "Project README\n\nFeature A: added text\n" > README.md
git add README.md
git commit -m "feature/a: update README"

# back to main and branch B
git checkout main
git checkout -b feature/b
printf "Project README\n\nFeature B: different text\n" > README.md
git add README.md
git commit -m "feature/b: update README"

# merge feature/a into main (via feature/b to cause conflict)
git checkout main
git merge feature/a   # if feature/b also merged earlier, conflict appears; otherwise merge now after merging feature/b
# to ensure conflict: first merge feature/b into main
git merge feature/b
# Now try merging feature/a (will conflict if both modified same lines)
git merge feature/a


(One of the merges will produce a conflict.)

Diagnose

git status — shows files with conflicts.

git diff — shows conflict regions with <<<<<<<, =======, >>>>>>>.

git log --graph --oneline --all — see branch relationships.

Fix (step-by-step)

Open README.md, you'll see conflict markers:

<<<<<<< HEAD
Feature B: different text
=======
Feature A: added text
>>>>>>> feature/a


Edit file to the desired final content (choose A, B, or combine).

Stage and commit:

git add README.md
git commit -m "Resolve merge conflict in README.md: merged A and B manually"


If you want to abort the merge entirely:

git merge --abort

Verification

git status should be clean.

git log --graph --oneline shows merge commit.

Prevention

Communicate and coordinate changes on shared files.

Use smaller, focused commits and feature branches.

Use git pull --rebase to keep branches up to date (team policy dependent).

ISSUE 2 — Corrupt index or index.lock preventing commits (Intermediate)

Goal: simulate an index corruption or stuck lock, diagnose, and repair without losing work.

GitHub issue template

Title: Unable to commit — .git/index corrupted or index.lock present

Body:

**Summary**
`git commit` fails with messages like:
- "fatal: Unable to write new index file"
- "error: could not lock index"

**Steps to reproduce**
(See reproduction steps attached.)

**Expected**
Able to stage and commit changes.

**Actual**
Commit fails; `git status` may show staged or unstaged changes but cannot proceed.

**Output / logs**
Please attach output of:
- `git status`
- `ls -la .git/index*`
- `cat .git/index.lock` (if present)

Reproduce locally

Create the scenario by simulating a stale lock:

mkdir git-issue-index && cd git-issue-index
git init
echo "line1" > file.txt
git add file.txt
git commit -m "initial"

# simulate a crash by creating lock file
touch .git/index.lock

# Now try to commit change
echo "line2" >> file.txt
git add file.txt
git commit -m "add line2"
# This should fail with locking message


Or create a corrupted index by truncating it (destructive; keep a copy first):

cp .git/index /tmp/index.backup
# truncate file to zero (simulate corruption)
: > .git/index

Diagnose

git status — likely error or unhelpful output.

ls -la .git/index* — see if .git/index.lock exists.

file .git/index — check if index is non-empty and appears valid.

If corrupted, git fsck may not directly show index issues (index is separate).

Fix (safe approach)

If index.lock exists and no Git process is running:

# ensure no git operations running (ps / kill if necessary), then:
rm -f .git/index.lock
# then try
git status


If .git/index corrupted: rebuild index from HEAD

# make a safety copy first
cp .git/index .git/index.bak

# rebuild index from current commit (HEAD)
git reset --mixed HEAD
# or safer:
git reset


git reset without args will rebuild the index from HEAD commit and keep working tree changes unstaged. Then re-add files and commit.

If you have an index backup or want to restore from that:

mv /tmp/index.backup .git/index


(Only if you previously saved index.)

If you accidentally removed index and repository seems inconsistent, you can reconstruct by checking out:

git checkout -- .
# This will overwrite working tree with HEAD — destructive! Use only if acceptable.

Verify

git status should show expected staged/unstaged files.

git commit should succeed.

Prevention

Avoid simultaneous Git operations from multiple processes.

Avoid editing .git/* files manually unless necessary.

Ensure build systems/CI don’t leave stale lock files (use CI job cleanup).

ISSUE 3 — Packfile/object corruption (Hard / Advanced)

Goal: create a packfile corruption git fsck complains about, then recover by repacking, fetching from remote, or using git fsck --lost-found.

Warning: packfile/object corruption scenarios can be destructive. Don’t run destructive recovery on a repo without backups.

GitHub issue template

Title: Repository integrity error — git fsck reports corrupted objects / invalid packfile

Body:

**Summary**
`git fsck --full` reports errors such as:
- "error: object file .git/objects/pack/pack-*.pack: bad CRC"
- "error: missing blob 1234abcd"
- "fatal: loose object 1234... is corrupt"

**Steps to reproduce**
1. (We intentionally corrupt a packfile in test repo.)
2. Run `git fsck --full` to see errors.

**Expected**
Repository integrity checks pass.

**Actual**
`git fsck` reports corrupt or missing objects. Push/pull operations may fail.

**Environment**
Git version:
OS:

Reproduce locally (simulate corruption)

Create a repo and pack objects, then corrupt the pack:

mkdir git-issue-pack && cd git-issue-pack
git init
for i in {1..10}; do echo "file $i" > file$i.txt; git add file$i.txt; git commit -m "Add file $i"; done

# create pack by repacking
git gc --aggressive --prune=now

# locate a pack file
ls .git/objects/pack
# corrupt a pack (destructive)
head -c 1000 /dev/urandom > .git/objects/pack/pack-*.pack   # overwrite start of pack


Now git fsck --full should report corruption.

Diagnose

git fsck --full — shows specific object hashes that are broken/missing.

git verify-pack -v .git/objects/pack/*.idx — lists pack index and sizes (can fail if pack corrupted).

git count-objects -vH — see size and number of loose objects.

Fix strategies (ordered: least destructive → more drastic)
1) Get objects from a remote (best, if remote is healthy)

If the repo has a healthy remote (origin), reclone or fetch from remote:

# safest: clone a fresh copy
cd ..
git clone /path/to/remote repo-recovered

# OR if you want to preserve local unpushed commits, fetch into a new clone:
git clone --mirror /path/to/bare/remote repo-mirror.git
# then fetch or push missing refs


If you have local unpushed commits that are not on remote, you may need to recover them from .git/lost-found or reflog before recloning.

2) Use git fsck --lost-found to recover dangling objects
git fsck --full --no-reflogs --lost-found
# This will place orphaned blobs/commits into .git/lost-found/commit and .git/lost-found/other


Inspect git show <hash> for objects in .git/lost-found and reconstruct branches:

git show <dangling-commit-hash>
git branch recovered <dangling-commit-hash>

3) Repack/unpack approach (if pack index broken)

If only index (.idx) is broken but .pack remains, try:

# backup current packfiles first
mkdir -p /tmp/pack-backup && cp .git/objects/pack/* /tmp/pack-backup

# attempt to repack from loose objects (if present)
git repack -a -d --window=250 --depth=250

# If repack fails, try to unpack objects (dangerous)
git unpack-objects < .git/objects/pack/pack-*.pack


Be cautious: unpack-objects writes into .git/objects and may corrupt if pack is partially broken.

4) If nothing works — recover from clone (if possible) & cherry-pick

Create a fresh clone from remote, then cherry-pick local work you can salvage (using patches, or copying .git/refs you can trust).

5) As a last resort: restore from backups or bundle

If you have a git bundle or a server mirror:

git clone repo.bundle repo-restored

Verify

Run git fsck --full and expect no errors.

Ensure git log shows expected commits.

Push to remote (if appropriate) and ensure remote accepts.

Prevention

Use reliable storage and avoid manual editing of .git/objects.

Keep backups or mirrors (bare repo clones).

Run regular git gc and git maintenance run on big repos.

Monitor disk health (fsck on filesystem).

Extra: How to create a good issue on GitHub (step-by-step + CLI)

If you want to open the problem as an issue in GitHub:

Web UI

Go to repo → Issues → New issue.

Paste the issue template block from above.

Fill in environment fields and attach logs: git status, git fsck output, ls -la .git.

Using gh CLI (GitHub CLI)
# interactive:
gh issue create --title "Merge conflict in README.md when merging feature/a" --body-file ./issue-body.txt
# or noninteractive:
gh issue create --title "Repo corruption: bad packfile" --body "$(cat issue-body.txt)" --label "bug,git"


(Install gh and authenticate first.)

Quick cheat-sheet for diagnosing repo problems

Check repo health and object integrity:

git fsck --full
git count-objects -vH
git verify-pack -v .git/objects/pack/*.idx


Look for lost commits:

git reflog
git log --graph --oneline --all


If commit missing but ref exists:

cat .git/refs/heads/<branch>
cat .git/packed-refs


Stale locks:

ls .git/*.lock
rm -f .git/index.lock


Rebuild staging area:

cp .git/index .git/index.bak
git reset


Recover dangling objects created by fsck:

git fsck --full --lost-found
ls -la .git/lost-found

ISSUE A — Interactive rebase gone wrong (Intermediate → Hard)

Difficulty: Intermediate → Hard
Danger: Medium — can rewrite history; do on local or feature branches only.

Issue title

Interactive rebase corrupted history — lost commits or conflicting rebase

Issue body (paste)
**Summary**
An interactive rebase (git rebase -i) was used to squash/reorder commits. After resolving conflicts and finishing rebase, some expected commits appear missing, or history is confusing.

**Steps to reproduce**
1. See reproduction commands below (local).
2. Run interactive rebase and intentionally reorder/squash commits and produce conflicts.

**Expected**
Clean linear history with all intended changes retained.

**Actual**
Some commits appear lost or the working tree is not in the expected state.

**Attach**
Outputs of `git reflog`, `git log --graph --oneline --all`, and `git status`.

Reproduce locally
mkdir rebase-issue && cd rebase-issue
git init
echo a > file.txt; git add file.txt; git commit -m "A"
echo b >> file.txt; git add file.txt; git commit -m "B"
echo c >> file.txt; git add file.txt; git commit -m "C"
echo d >> file.txt; git add file.txt; git commit -m "D"

# create a branch to rebase
git checkout -b feature/rebase
# add some commits to make it interesting
echo e >> file.txt; git add file.txt; git commit -m "E"
echo f >> file.txt; git add file.txt; git commit -m "F"

# do interactive rebase against origin/main (simulate)
git rebase -i HEAD~5
# In the editor: reorder, squash, or drop commits; then save.
# Simulate conflict by editing same lines when prompted; resolve incorrectly to reproduce loss.

Diagnose

git status — check rebase state (rebase in progress).

cat .git/REBASE_HEAD and .git/rebase-apply / .git/rebase-merge — show rebase metadata.

git reflog — shows previous HEAD positions (first place to look for lost commits).

git log --graph --oneline --all — see where commits went.

git fsck --lost-found — find dangling commits if needed.

Fixes
Non-destructive recovery (recommended)

Use git reflog to find the commit(s) you had before rebase:

git reflog
# find commit hash before rebase (e.g., abc123)
git checkout -b recover-branch abc123


Now inspect recover-branch to pick any missing commits and reapply them.

If rebase is still in progress and you want to abort:

git rebase --abort
# this returns you to state before rebase started


If you want to continue but resolve a mistake:

Manually reapply missing changes via git cherry-pick <hash> from reflog/recover-branch.

If you already finished rebase and pushed (history rewritten)

If you have local lost commits and remote has old commits, create a branch from the reflog commit then git push origin recover-branch and create PR to reapply changes.

Dangerous: force-push to remote (only when coordinated)
# after reconstructing correct local history
git push --force-with-lease origin feature/rebase


Use --force-with-lease instead of --force to reduce surprise overwrites.

Verification

git log --graph --oneline --all shows commit(s) restored or a correct linear history.

git status is clean.

If pushed, remote PR merges show expected commit content.

Prevention

Always create a safety branch before interactive rebase: git branch before-rebase.

Use git reflog regularly and know how to read it.

Prefer interactive rebase only on local feature branches, not shared branches.

ISSUE B — Force-push overwrote remote branch (Advanced)

Difficulty: Advanced
Danger: High — can permanently rewrite or lose collaborators’ commits.

Issue title

Force-push accidentally overwrote remote branch with older/incorrect history

Issue body
**Summary**
A `git push --force` or `git push --force-with-lease` overwritten the remote branch, removing commits pushed earlier by others.

**Steps to reproduce**
See reproduction commands below (local simulation).

**Expected**
Force push should be used only when safe; team should not lose commits.

**Actual**
Remote branch lost commits not present in local history.

**Attach**
Output of `git reflog`, `git log --graph --oneline --all`, and `git ls-remote origin refs/heads/<branch>`.

Reproduce locally

Simulate two collaborators:

mkdir forcepush-issue && cd forcepush-issue
git init
echo 1 > a.txt; git add a.txt; git commit -m "init"
git checkout -b remote-sim
# Simulate remote: create bare repo somewhere or create another clone
cd ..
git clone --bare forcepush-issue force-bare.git
cd forcepush-issue
git remote add origin ../force-bare.git
git push origin main

# collaborator A: make commit and push (simulate remote change)
git clone ../force-bare.git collab1
cd collab1
echo X > collab.txt; git add collab.txt; git commit -m "collab work"
git push origin main
cd ../forcepush-issue

# Your local has older history; you overwrite remote:
echo Y > other.txt; git add other.txt; git commit -m "old local"
git push --force origin main   # BAD: overwrites collab1’s commit

Diagnose

On remote/bare repo run git reflog (on bare, inspect refs logs if recorded).

Locally git reflog can still show prior positions (but reflog is local).

Use git ls-remote origin to check remote refs (might show current hash only).

If remote is a server with reflogs enabled, check server reflog (admin).

Fixes
If remote has reflogs / backups

Ask remote admin to check the server’s reflog or backups. If server has reflog entries for the branch, you can restore:

# on server or admin:
cd /path/to/bare/repo
git reflog show refs/heads/main
# find hash, then:
git update-ref refs/heads/main <good-hash>

If you have colleague’s commit locally (cloned or other machine)

Clone remote (fresh) to a new folder — see if the collaborator pushed somewhere else or their clone still has commit. If yes:

Create branch from that commit and force-push correct history.

If lost commits are only on collaborator’s machine

Have collaborator push a branch with their current HEAD:

# collab machine
git checkout -b collab-recovery
git push origin collab-recovery


Merge / cherry-pick their commits into corrected main, then push.

If all else fails

Restore from backup/mirror/bare repository copy.

Prevention

Use --force-with-lease instead of --force.

Protect important branches on remote with branch-protection rules.

Require PR-based merges for protected branches.

ISSUE C — Large file accidentally committed & pushed (History rewrite) (Advanced)

Difficulty: Advanced
Danger: High — needs history rewrite and force-push; may break clones.

Issue title

Accidentally pushed large binary (100+ MB) — need to remove from repo history

Issue body
**Summary**
A large file (e.g., secret/report.zip or video.mov) was committed and pushed, bloating repository and failing CI.

**Steps to reproduce**
1. Commit a big file and push to origin.
2. CI fails due to repo size or hosting rejects push.

**Expected**
Large files should be stored in LFS or external store.

**Actual**
Repo size increased; need to purge file from history.

**Attach**
Outputs of `git rev-list --objects --all | grep <filename>` and `git count-objects -vH`.

Reproduce locally
mkdir largefile && cd largefile
git init
fallocate -l 120M big.bin   # or dd if fallocate not available
git add big.bin
git commit -m "Add big.bin"
# Create a bare remote and push to simulate remote
cd ..
git clone --bare largefile large-bare.git
cd largefile
git remote add origin ../large-bare.git
git push origin main

Diagnose

git rev-list --objects --all | sort -k2 — shows file references in history.

git count-objects -vH — shows repo size.

git verify-pack -v .git/objects/pack/*.idx | sort -k3 -n — identifies largest objects.

Fixes
Option 1 — Use git filter-repo (recommended)

git filter-repo is the modern, safe and fast tool (not shipped with Git). Steps:

# backup current repository first:
git clone --mirror . ../repo-backup.bundle

# Install git-filter-repo (if available)
# Then run:
git filter-repo --invert-paths --path big.bin

# After rewrite push:
git push origin --force --all
git push origin --force --tags


Notes: Everyone who clones must reclone or run git fetch + reset; coordinate with team.

Option 2 — BFG Repo-Cleaner (if git-filter-repo not available)
# create mirror
git clone --mirror ../repo repo-mirror.git
cd repo-mirror.git
# run BFG
bfg --delete-files big.bin
git reflog expire --expire=now --all && git gc --prune=now --aggressive
git push --force

Option 3 — filter-branch (old, slower — caution)
git filter-branch --force --index-filter \
  "git rm --cached --ignore-unmatch big.bin" \
  --prune-empty --tag-name-filter cat -- --all
git reflog expire --expire=now --all
git gc --prune=now --aggressive
git push --force --all


After rewrite: Instruct team to reclone or run:

git fetch origin
git reset --hard origin/main

Verification

git rev-list --objects --all | grep big.bin should return nothing.

git count-objects -vH should show smaller repo size.

git verify-pack -v .git/objects/pack/*.idx should not list large blob.

Prevention

Use Git LFS for large files: git lfs track "*.bin" then commit .gitattributes.

Add size-check pre-commit hook or CI check to reject large files.

Educate team about repository size limits.

ISSUE D — Diverged branches with many commits needing reconcile (Intermediate → Advanced)

Difficulty: Intermediate → Advanced
Danger: Medium — merging vs rebasing decision impacts history.

Issue title

Branch diverged: main and feature/<x> have conflicting histories with many commits

Issue body
**Summary**
The feature branch and main have diverged heavily (hundreds of commits) and merging produces many conflicts. Team wants a clean strategy to reconcile without losing work.

**Steps to reproduce**
1. Simulate heavy divergent commits on main and feature.
2. Attempt to merge feature into main.

**Attach**
`git log --graph --stat --all` and `git status`.

Reproduce locally
mkdir diverge && cd diverge
git init
echo base > file.txt; git add file.txt; git commit -m "base"
git checkout -b main-activity
for i in {1..10}; do echo main$i >> file.txt; git commit -am "main $i"; done
git checkout -b feature
for i in {1..8}; do echo feature$i >> file.txt; git commit -am "feature $i"; done
# Now main-activity has 10 commits ahead; feature has 8 commits ahead
git checkout main-activity
git merge feature   # may produce conflicts if overlapping edits

Diagnose

git log --graph --oneline --all — visualize divergence.

git diff main..feature — see changesets between branches.

git merge --no-ff feature — run in dry-run? use git merge --no-commit --no-ff feature to inspect conflicts.

Fixes / Strategies
Strategy 1 — Merge (preserve history) — recommended for shared branches

Merge and resolve conflicts:

git checkout main
git merge --no-ff feature
# resolve conflicts in editor
git add <resolved files>
git commit


Push merge commit.

Strategy 2 — Rebase feature onto main (clean linear history) — good if feature exclusively yours

Rebase and resolve conflicts:

git checkout feature
git rebase main
# resolve conflicts as they come
git push --force-with-lease origin feature  # coordinate if others use branch


Merge or fast-forward to main.

Strategy 3 — Create a new branch and cherrypick important commits

If many commits not required, pick the important commits:

git checkout -b reconcile main
git cherry-pick <commit1> <commit2> ...


Resolve conflicts manually in fewer steps.

Verification

git log --graph --oneline shows expected final structure.

Run project tests or CI to ensure no regression.

Prevention

Keep branches short-lived and regularly synced with main via rebase or merge.

Use feature toggles instead of long-lived branches.

ISSUE E — Submodule broken after ref update or submodule path moved (Advanced)

Difficulty: Advanced
Danger: Medium — impacts submodule pointers and CI.

Issue title

Submodule broken: path changed or submodule ref missing after remote update

Issue body
**Summary**
Submodule points to a commit that no longer exists (force-push on submodule repo) or submodule folder path changed.

**Steps to reproduce**
1. Add submodule, push.
2. Force-push or rebase submodule repository removing commit.
3. `git submodule update --init` fails.

**Attach**
Output from `git submodule status`, `.gitmodules`, and `git ls-tree` for the referencing commit.

Reproduce locally
# main repo
git init main-repo && cd main-repo
mkdir sub && cd sub
git init
echo sub > s.txt; git add s.txt; git commit -m "sub initial"
cd ..
git submodule add ../sub sub
git commit -am "add submodule"
# In sub, rewrite history:
cd sub
echo change > s.txt; git add s.txt; git commit -m "change"
git reset --hard HEAD~1   # remove last commit that main refers to (simulate missing)
# Back to main and try update
cd ../main-repo
git submodule update --init --recursive  # should complain about missing commit

Diagnose

git submodule status — displays a - for missing or + for modified.

cd sub && git fsck --full — check submodule integrity.

Check .gitmodules for correct path/URL.

Inspect .git/modules/<submodule> if submodule is partially initialized.

Fixes

If commit was removed in submodule (force push), recover the commit from submodule reflog or backup:

In submodule repo:

git reflog
git branch recovered <hash>
git push origin recovered


Then update main repo submodule pointer to recovered commit:

cd main-repo
git submodule update --init --recursive
cd sub
git checkout recovered
cd ..
git add sub
git commit -m "point submodule to recovered commit"
git push


If submodule remote path changed, update .gitmodules:

git config -f .gitmodules submodule.sub.path.url <new-url>
git submodule sync
git submodule update --init --recursive


If submodule was removed remotely and cannot be recovered: remove submodule and replace with a subtree or vendor copy:

git submodule deinit -f sub
git rm -f sub
rm -rf .git/modules/sub
git commit -m "remove submodule"

Verification

git submodule status shows correct commit.

git submodule update --init --recursive runs without error.

Prevention

Avoid rewriting history in submodule repos that others reference.

Prefer tags/releases for submodule pointers instead of raw branch HEAD.

ISSUE F — Commit contains accidental credentials (secret in history) (Advanced, security)

Difficulty: Advanced
Danger: Very High — security sensitive. Sensitive data could be cached in remote forks and mirrors.

Important: If a secret (API key, password) is exposed, assume it’s compromised and rotate/ revoke immediately. History rewrite only removes it from Git history — it does not revoke exposure.

Issue title

Secret committed and pushed — remove from history and rotate credentials

Issue body
**Summary**
A secret (API key / password) was committed and pushed. Must remove from history and rotate secrets.

**Steps to reproduce**
Add a dummy secret into a file and commit + push.

**Attach**
Output of `git log --all -- <file-with-secret>` and `git rev-list --objects --all | grep <filename>`.

Reproduce locally (use dummy secret)
mkdir secret && cd secret
git init
echo "API_KEY=123456" > .env
git add .env
git commit -m "add secret"
git clone --bare . ../secret-bare.git
cd ..
git clone ../secret-bare.git origin-sim
cd origin-sim
git push origin-sim main
# simulate accidental commit pushed to remote

Immediate actions (security first)

Rotate/revoke the exposed credential immediately — this is critical.

Notify stakeholders and follow incident protocol.

History-cleaning steps (remove secret from history)
Recommended: git filter-repo (fast, safe)
# backup
git clone --mirror ../secret secret-mirror.git
cd secret-mirror.git
git filter-repo --path .env --invert-paths
# push cleaned mirror to remote (force)
git push --force --all
git push --force --tags

BFG as alternative
git clone --mirror ../secret secret-mirror.git
cd secret-mirror.git
bfg --delete-files .env
git reflog expire --expire=now --all && git gc --prune=now --aggressive
git push --force

After rewrite: advise all collaborators

Everyone must re-clone repo or run:

git fetch origin
git reset --hard origin/main


Invalidate any local copies of the secret and ensure rotated credential is used.

Verification

git grep -n "123456" $(git rev-list --all) should find nothing.

Confirm remote no longer contains file in history.

Prevention

Add .env to .gitignore.

Use pre-commit hooks to prevent committing secrets (integrate detect-secrets, git-secrets).

Enforce secret scanning in CI.

ISSUE G — Corrupted packfile on server, clients fail fetch (Hard)

Difficulty: Hard
Danger: High — may need server-side backup or mirror.

Issue title

Server packfile corrupted: clients fail to fetch or clone

Issue body
**Summary**
Clients report errors such as "error: object file .git/objects/pack/pack-xxxx.pack: bad CRC" when cloning or fetching.

**Steps to reproduce**
Simulate by corrupting a packfile in bare repository and try to git fetch/clone.

**Attach**
`git fsck --full` output from the bare server.

Reproduce locally

Create bare repo and corrupt pack:

git init --bare server-repo.git
git clone server-repo.git client
cd client
echo hi > a.txt; git add a.txt; git commit -m "hi"
git push origin main
# server side: corrupt a packfile
cd ../server-repo.git/objects/pack
# find pack file and overwrite header
printf "corrupt" | dd of=pack-*.pack bs=1 count=7 conv=notrunc
# client tries to fetch or clone -> error

Diagnose

On server run: git fsck --full — identifies bad pack files and corrupted objects.

git verify-pack -v objects/pack/*.idx — may fail.

Inspect pack files timestamps / sizes.

Fixes (prefer non-destructive)
1) Restore from backup / mirror (best)

If server has backups or mirrors, restore packfiles or bare repo from backup.

2) Replace corrupted packfile with a fresh pack created from a good clone

On a healthy clone:

cd good-clone
git repack -a -d
# copy pack and idx files from good-clone/.git/objects/pack to server bare repo .git/objects/pack
scp .git/objects/pack/pack-*.pack server:/path/to/repo/objects/pack/
scp .git/objects/pack/pack-*.idx server:/path/to/repo/objects/pack/


Run git gc on server after copying.

3) If only idx corrupted, rebuild idx from pack on server

Use git index-pack or git verify-pack tools carefully.

4) If no backups, attempt to git fsck --lost-found on server to recover dangling objects, but recovery may be incomplete.
Prevention

Maintain bare repo mirrors and file-level backups.

Use checksums and monitoring to detect early disk corruption.

Run git maintenance run periodically.

ISSUE H — CI failing due to client-side hook or corrupted hooks (Intermediate)

Difficulty: Intermediate
Danger: Low → Medium

Issue title

CI fails: pre-commit/pre-push hook blocks job or is corrupt

Issue body
**Summary**
A client-side or repository-distributed hook runs during CI steps and causes CI job failures (non-zero exit or interactive prompt). Hooks intended for local dev should not break CI.

**Steps to reproduce**
1. Add a pre-push or pre-commit hook that asks for input or returns non-zero.
2. CI that runs `git clone` and `git push` fails during hook.

**Attach**
CI job logs, and `.git/hooks` contents.

Reproduce locally
mkdir hook-issue && cd hook-issue
git init
cat > .git/hooks/pre-commit <<'EOF'
#!/bin/sh
echo "This hook blocks CI"
exit 1
EOF
chmod +x .git/hooks/pre-commit
# now attempt to commit
touch a.txt; git add a.txt; git commit -m "test"  # will fail due to hook

Diagnose

Check .git/hooks for unexpected hooks (on CI runner or developer machine).

git config core.hooksPath — custom hooks location may be set.

CI logs show hook output and failure.

Fixes

For distributed hooks, store them in repo folder (e.g., scripts/hooks/) and add instructions to install locally rather than in .git/hooks directly.

Disable hooks in CI by setting HUSKY or environment variables, or run git commands with --no-verify if safe:

git commit --no-verify


Update CI pipeline to not execute local git hooks (CI runs fresh clones and should not use developer local hooks).

Verification

Commit succeeds locally or in CI after hook removal or CI config change.

CI job logs are clean.

Prevention

Use server-side hooks for enforcement on server (e.g., pre-receive).

Keep developer hooks non-interactive and idempotent.

Document hook installation and CI behavior.

Final recommendations & practice plan

Pick one intermediate and one advanced issue to practice per session.

Always make a full .git backup before destructive experiments:

cp -a .git ../git-backup-$(date +%s)


Learn to read git reflog — it will save you repeatedly.

Use --force-with-lease over --force.

For security incidents (secrets), rotate first, clean history later.

Scenario 1 — Merge conflict on file

Title: Merge conflict in conflict.txt when merging feature-branch into main

Reproduction Steps

mkdir git-merge-test && cd git-merge-test
git init
echo "Original content line 1" > conflict.txt
git add conflict.txt && git commit -m "Initial commit"
git checkout -b feature-branch
echo "Feature branch content line 1" > conflict.txt
git add conflict.txt && git commit -m "Feature branch changes"
git checkout main
echo "Main branch content line 1" > conflict.txt
git add conflict.txt && git commit -m "Main branch changes"
git merge feature-branch


Expected behavior
Merge completes automatically or prompts to resolve trivial conflicts.

Actual behavior
Merge fails with conflict markers in conflict.txt.

Root cause
Same file changed differently on both branches; Git can't auto-merge.

Resolution (commands & steps)

Inspect status and file:

git status
cat conflict.txt


Edit conflict.txt and remove conflict markers <<<<<<<, =======, >>>>>>> keeping desired content.

Mark resolved and finish:

git add conflict.txt
git commit -m "Resolved merge conflict between main and feature-branch"
git log --oneline --graph --all


Extra tips

While resolving, use git mergetool if you have a GUI merge tool configured.

If you want both versions, combine them in the file manually.
Prevention

Pull & rebase feature branches frequently.

Keep changes small and narrow in scope.
Labels: bug, merge-conflict, help wanted

Scenario 2 — Accidental commit to wrong branch

Title: Commit mistakenly made on main instead of feature-branch

Reproduction Steps

git init wrong-branch-test && cd wrong-branch-test
echo "file1" > file1.txt
git add file1.txt && git commit -m "Initial commit"
echo "feature content" > feature.txt
git add feature.txt && git commit -m "Feature work on wrong branch"   # oops


Expected behavior
Commit is on a feature-branch.

Actual behavior
Commit is on main.

Root cause
Forgot to create / switch to feature branch before committing.

Resolution
Option A — move commit to new branch and remove from main:

git log --oneline    # note the top commit hash if needed
git checkout -b feature-branch  # creates branch at current HEAD (commit is preserved here)
git checkout main
git reset --hard HEAD~1  # remove that commit from main (use with caution)


Option B — if you prefer to keep history on main and create branch from previous commit:

git checkout -b feature-branch <commit-hash>   # use commit hash you want


Safety note: If you've already pushed the wrong commit to remote, avoid --hard reset unless you coordinate with teammates. Use git revert <commit> on main to undo publicly pushed commit.

Prevention

Always git status and git branch before committing.

Use hooks or branch protection (on remote) to prevent direct commits to main.
Labels: user-error, branching, easy-fix

Scenario 3 — Detached HEAD & lost commit

Title: Committed while in detached HEAD state; commit appears lost

Reproduction Steps

git init detached-head && cd detached-head
echo "v1" > version.txt; git add version.txt; git commit -m "Version 1"
echo "v2" > version.txt; git commit -am "Version 2"
echo "v3" > version.txt; git commit -am "Version 3"
git checkout HEAD~2   # detached HEAD at Version 1
echo "experimental" > experiment.txt; git add experiment.txt
git commit -m "Experimental commit"  # commit created on detached HEAD


Expected behavior
New commit saved on a branch so it’s easy to find later.

Actual behavior
Commit exists but is not on any branch; could be lost by garbage collection if unreferenced.

Root cause
Checked out a commit (not a branch) and committed—HEAD was detached.

Resolution
Find the commit and attach it to a branch:

git reflog              # locate experimental commit hash (HEAD@{n})
git checkout -b experimental-branch <experimental-commit-hash>
# or if you're back on main and want to apply:
git checkout main
git cherry-pick <experimental-commit-hash>


Prevention

Create a branch before making commits: git checkout -b my-experiment

If you get detached, run git switch -c <branch> to make a branch from current HEAD.

Labels: detached-head, recovery, medium

Scenario 4 — Undo last commit but keep changes

Title: Undo last commit while keeping working changes (staged or unstaged)

Reproduction Steps

git init undo-commit && cd undo-commit
echo "initial" > file.txt; git add file.txt; git commit -m "Initial commit"
echo "wrong commit" > file.txt
git commit -am "Oops wrong commit message and content"


Expected behavior
Undo commit but retain changes in the working tree (optionally staged).

Resolution
Keep changes staged:

git reset --soft HEAD~1
git status   # changes are staged


Unstage but keep changes:

git reset --mixed HEAD~1   # or `git reset HEAD~1`
git status   # changes unstaged but preserved in working tree


If you want to discard changes entirely:

git reset --hard HEAD~1


Safety note: --hard irreversibly discards working changes.

Prevention

Use git commit --amend if you just want to edit last commit message/content.
Labels: undo, git-reset, low

Scenario 5 — Lost commits after hard reset

Title: Last two commits lost after git reset --hard HEAD~2

Reproduction Steps

git init lost-commits && cd lost-commits
# commit v1, v2, v3
git reset --hard HEAD~2


Expected behavior
User intentionally wants to move HEAD back but still can restore old commits if needed.

Actual behavior
HEAD moved back; last two commits are not referenced by any branch.

Root cause
Hard reset removed branch refs pointing to those commits; they remain reachable via reflog until GC.

Resolution
Recover via reflog and restore:

git reflog         # find the lost commit hash(es)
git reset --hard <lost-commit-hash>   # moves branch to the lost commit
# or cherry-pick specific commits:
git cherry-pick <commit-hash>


Extra note: Reflog entries exist for at least 30 days by default; avoid git gc until recovery is done.

Prevention

Use git revert for public history changes.

Use branches for experiments.
Labels: recovery, reset, medium

Scenario 6 — Simulated corruption (missing pack idx)

Title: Repo corruption simulated by removing pack index files

Reproduction Steps

git init corrupt-repo && cd corrupt-repo
echo "data" > file1.txt; git add file1.txt; git commit -m "Initial commit"
rm -f .git/objects/pack/*.idx   # simulate broken pack index


Expected behavior
Repository integrity preserved.

Actual behavior
git commands may complain about missing objects/indexes.

Diagnosis & Resolution

Run integrity checks:

git fsck --full
git fsck --lost-found


Clean / rebuild packs and optimize:

git count-objects -v
git repack -a -d
git prune
git gc --aggressive


If corruption persists, restore from a known good backup/remote:

# easiest recovery if you have remote:
rm -rf .git
git clone <remote-url> .


Prevention

Keep regular backups or mirrors (remote repository).

Avoid manual manipulation of .git/objects.
Labels: corruption, repo-health, high

Scenario 7 — Large file accidentally committed (100MB)

Title: Large binary (100MB) accidentally committed to history

Reproduction Steps

git init large-file && cd large-file
echo "normal file" > normal.txt; git add normal.txt; git commit -m "Normal commit"
dd if=/dev/zero of=largefile.bin bs=1M count=100
git add largefile.bin; git commit -m "Accidentally committed large file"


Expected behavior
Large files are avoided (or managed via Git LFS).

Resolution (local, not pushed)
Remove from last commit:

git reset --soft HEAD~1
git reset HEAD largefile.bin
rm largefile.bin
git commit -m "Normal commit without large file"


If already pushed to remote or deeper in history:

Use BFG Repo-Cleaner (recommended) or git filter-branch:

# example filter-branch (slow & older approach)
git filter-branch --force --index-filter \
'git rm --cached --ignore-unmatch largefile.bin' -- --all
git reflog expire --expire=now --all
git gc --prune=now --aggressive


Recommended approach

Use BFG (faster, easier) and then force-push cleaned repo to remote (coordinate with team).

Adopt Git LFS for large binaries going forward.

Prevention

Add patterns to .gitignore

Use pre-commit hooks to block large files (e.g. pre-commit framework) or enable Git LFS.
Labels: large-file, history-rewrite, critical

Scenario 8 — Mixed staged and unstaged changes

Title: Mixed staged (file1.txt) and unstaged (file2.txt) changes after partial git add

Reproduction Steps

git init staging-mess && cd staging-mess
echo "v1" > file1.txt; echo "v1" > file2.txt
git add .; git commit -m "Initial commit"
echo "staged change" > file1.txt; git add file1.txt
echo "unstaged change" > file2.txt


Expected behavior
User understands current staged vs unstaged differences.

Resolution
Check status and diffs:

git status
git diff        # unstaged
git diff --staged  # staged


To unstage:

git restore --staged file1.txt


To discard unstaged changes (if desired):

git restore file2.txt


To temporarily save everything:

git stash
git stash list
git stash pop


Prevention

git status frequently.

Use git add -p to interactively stage hunks.
Labels: staging, workflow, easy

Scenario 9 — Broken rebase with conflicts

Title: Rebase of feature onto main fails with conflicts

Reproduction Steps

git init rebase-conflict && cd rebase-conflict
# create base, feature (2 commits), main commit then git rebase main while on feature
git rebase main


Expected behavior
Clean rebase or clear guidance to resolve conflicts.

Actual behavior
Conflicts in file.txt; rebase halted.

Resolution

Inspect status & files:

git status
cat file.txt  # resolve conflict markers


Resolve, add and continue:

git add file.txt
git rebase --continue


If too messy or you prefer merge:

git rebase --abort
git checkout main
git merge feature


If you have multiple commits and want to rework history, consider git rebase -i main (interactive).

Prevention

Rebase frequently to reduce conflict surface.

Communicate & coordinate when touching the same files.
Labels: rebase, conflict, medium

Scenario 10 — Broken refs (invalid HEAD ref)

Title: .git/refs/heads/main corrupted with invalid hash

Reproduction Steps

git init integrity-test && cd integrity-test
echo "data1" > file1.txt; git add file1.txt; git commit -m "Commit 1"
echo "data2" > file2.txt; git add file2.txt; git commit -m "Commit 2"
echo "invalid_hash" > .git/refs/heads/main  # simulate broken ref


Expected behavior
Refs point to valid commit hashes.

Actual behavior
git commands fail because main points to invalid hash.

Diagnosis & Resolution

Check repo health and refs:

git fsck --full
git show-ref
git reflog


Find a correct commit hash via git reflog or git log --oneline --all.

Restore ref:

git update-ref refs/heads/main <correct-commit-hash>
git log --oneline   # verify


Run garbage collection if needed:

git gc --prune=now


Prevention

Do not manually edit .git/refs/* files.

Keep remote copies or backups for recovery.
Labels: refs, corruption, high

Advanced debugging command cheat-sheet (copyable)
git status -v
git log --all --oneline --graph --decorate
git reflog show HEAD
git fsck --full --no-dangling
git show-ref --heads --tags
git ls-files -s
git diff-index HEAD

Scenario 1 — Complex interactive rebase with conflicts

Title: Interactive rebase with squash/edit/drop/fixup produces conflicts on feature branch

Reproduction Steps

mkdir complex-rebase && cd complex-rebase
git init
echo "base" > file.txt
git add file.txt
git commit -m "Initial commit"

for i in {1..7}; do
  echo "Line $i from commit $i" >> file.txt
  git commit -am "Commit $i"
done

git checkout -b feature HEAD~3
echo "Feature change at line 3" >> file.txt
git commit -am "Feature modification"

git rebase -i main
# In interactive editor: pick, squash, reword, edit, drop, fixup, pick as described


Expected behavior
Interactive rebase completes with desired squashes/edits and a clean history.

Actual behavior
Conflicts appear while reapplying commits; rebase stops and requires manual resolution.

Root cause
Reordering/squashing/editing commits changes patch order and creates overlapping edits in the same file/lines.

Resolution (step-by-step)

Start interactive rebase and perform planned editorial changes in the editor (mark commits pick/squash/reword/edit/drop/fixup).

When rebase stops on conflict:

git status                # see current rebase state and conflicted files
cat file.txt              # inspect conflict markers <<<<<<< ======= >>>>>>>
# Resolve conflicts manually in file.txt (preserve intended content)
git add file.txt
git rebase --continue


If you stopped for edit to change a commit:

# make desired changes
git commit --amend
git rebase --continue


If you need to abort and rethink:

git rebase --abort


Verify final history:

git log --oneline --graph
git diff main..feature


Prevention / best practices

Perform a dry run on a temporary branch: git branch temp feature && git checkout temp.

Rebase smaller batches of commits.

Use git rerere (git config --global rerere.enabled true) to reuse recorded resolutions.

Run tests after each rebase step (or use --exec "make test" in rebase).

Labels: rebase, conflict, interactive, advanced

Scenario 2 — Git bisect with automated test script

Title: Use git bisect with an automated test to find the commit that introduced a failing pattern

Reproduction Steps

mkdir bisect-auto && cd bisect-auto
git init
# create test.sh as provided (exit 1 if BUG string present)
chmod +x test.sh

# create commits 1..20 with BUG introduced at commit 15
# (see your script loop)


Expected behavior
Bisect automatically finds the first bad commit (15) using the test script.

Actual behavior
Manual bisect is slow; automated bisect fails if the test is incorrect/unreliable.

Root cause
Binary or textual bug introduced at a specific commit; manual bisect is time-consuming.

Resolution

Manual bisect:

git bisect start
git bisect bad HEAD
git bisect good HEAD~19
./test.sh && git bisect good || git bisect bad   # mark each commit
# repeat until commit found


Automated bisect:

git bisect start HEAD HEAD~19
git bisect run ./test.sh
git bisect log
git bisect reset
git show <bad-commit-hash>


If git bisect run fails due to environment differences, make test script self-contained (setup/build steps inside script).

Prevention / best practices

Add regression tests to CI so bisect can be run automatically on CI artifacts.

Keep atomic commits and descriptive messages so bisect iterations are informative.

Labels: bisect, testing, automation

Scenario 3 — Submodule corruption and recovery

Title: Submodule broken after .git inside submodule removed (missing history)

Reproduction Steps

# create main and submodule repos, add submodule as ../submodule-repo sub
# then inside sub: rm -rf .git
git status   # shows modified/broken submodule


Expected behavior
Submodule remains intact and points to valid commit.

Actual behavior
Submodule appears as modified/uninitialized and local .git removed — broken.

Root cause
Submodule’s metadata or repository directory was removed; submodule reference now invalid.

Resolution

Inspect:

git submodule status
git submodule summary


Deinitialize and remove broken submodule state:

git submodule deinit -f sub
git rm -f sub
rm -rf .git/modules/sub


Re-add submodule cleanly:

git submodule add ../submodule-repo sub
git submodule update --init --recursive
git commit -m "Re-add submodule 'sub'"


Alternatively, if submodule should be preserved and .git existed as a file pointing to .git/modules/..., restore that file from backup or reclone submodule repo and update .git/modules.

Extra fixes

git submodule sync
git submodule update --remote --merge
git submodule foreach git fsck


Prevention

Avoid manual rm -rf .git inside submodules.

Use submodule-aware operations and keep remote backups.

Labels: submodule, corruption, recovery

Scenario 4 — Split repository preserving history (monorepo -> project repo)

Title: Split project-a (and shared) from monorepo preserving commit history

Reproduction Steps

# monorepo with project-a, project-b, shared and 10 mixed commits


Expected behavior
A new project-a repository containing only project-a and shared files with preserved history.

Actual behavior
History is mixed; naive copy loses commit ancestry or keeps unrelated files.

Root cause
Monorepo history mixes both projects; need to filter commits by path to preserve relevant history.

Resolution
Method A — modern (recommended): git-filter-repo

# from monorepo root
git clone --no-local . ../project-a-filtered
cd ../project-a-filtered
# keep project-a and optionally shared
git filter-repo --path project-a/ --path shared/ --force
git log --oneline --all
git reflog expire --expire=now --all
git gc --prune=now --aggressive


Method B — legacy git filter-branch (slower, deprecated)

git clone monorepo project-a-split
cd project-a-split
git filter-branch --subdirectory-filter project-a -- --all
git gc --aggressive
git prune


After split

Verify: git log --oneline and file tree.

Push to new remote: git remote add origin <new-url>; git push -u origin main

Prevention

Plan monorepo layout and boundaries; document shared libs and their ownership.

Prefer git-filter-repo over filter-branch.

Labels: monorepo, filter-repo, history-preservation

Scenario 5 — Recover from force-push disaster (Dev1 force-push overwrites Dev2)

Title: Dev1 force-push overwrote remote main, orphaning Dev2’s commits

Reproduction Steps

# create bare original repo, dev1 pushes feature1
# dev2 commits feature2 locally
# dev1 resets and force-pushes destroying commit(s) on remote
# dev2 fetches and finds commit orphaned


Expected behavior
Force-push should not silently remove others' work.

Actual behavior
Dev2's commits no longer referenced on remote.

Root cause
Force push rewrote remote history and removed references to commits Dev2 had locally.

Resolution

On Dev2, find lost commits:

git reflog          # find lost commit hashes locally
git log --reflog --oneline


Recover by creating a branch from lost commit:

git branch recovery-branch <lost-commit-hash>
git checkout recovery-branch


Integrate recovered work into main:

git checkout main
git pull origin main
git cherry-pick <commit-hash>   # or merge recovery branch
# if conflict, resolve and commit


If multiple devs affected and you have server backups, restore from server backup or reflogs on server if accessible.

Prevention

Protect critical branches with branch protection rules (require PRs; block force push).

On server: git config receive.denyNonFastForwards true.

Educate team on impacts of --force; use --force-with-lease instead.

Labels: force-push, recovery, critical

Scenario 6 — Octopus merge conflicts (multiple branches)

Title: Octopus merge of 4 feature branches fails due to conflicts

Reproduction Steps

# create feature1..feature4 with conflicting changes in file.txt
git checkout main
git merge feature1 feature2 feature3 feature4
# merge fails with conflicts


Expected behavior
Octopus merge succeeds only if there are no conflicts; otherwise merges must be sequential.

Actual behavior
Merge aborts due to conflicts.

Root cause
Octopus merge cannot resolve multiple conflicting edits across branches in one step.

Resolution
Option A — sequential merges (recommended):

git merge --abort
git merge feature1
# resolve conflicts, git add, git commit
git merge feature2
# resolve conflicts...
# repeat for feature3 and feature4


Option B — rebase each feature onto main first:

for branch in feature1 feature2 feature3 feature4; do
  git checkout $branch
  git rebase main
  # resolve conflicts during rebase
done

git checkout main
git merge --no-ff feature1 feature2 feature3 feature4


Prevention

Coordinate changes; keep branches small and rebase frequently.

Consider integrating branches one-by-one in CI to detect conflicts early.

Labels: merge, octopus, conflict

Scenario 7 — Fix corrupted pack files

Title: Repository packfile corrupted after garbled write, git fsck reports errors

Reproduction Steps

# create repo with many commits; run git gc --aggressive
# corrupt pack by writing random bytes into pack file
git fsck --full   # shows corruption


Expected behavior
Repository pack files are valid; git fsck reports no errors.

Actual behavior
git fsck reports corrupted pack/index and missing objects.

Root cause
Packfile damaged (disk corruption or manual tampering).

Resolution (diagnose then recover)

Diagnose:

git fsck --full --no-dangling
git verify-pack -v .git/objects/pack/*.idx 2>&1 | grep "error"
git reflog --all
git fsck --lost-found


Attempt manual unpack & rebuild:

cd .git/objects/pack/
for pack in *.pack; do
  git unpack-objects < "$pack" || true
done
cd ../../..
rm -f .git/objects/pack/*    # remove corrupted packs if necessary
git fsck --full
git repack -a -d
git gc --aggressive


If recovery fails, restore from remote:

git fetch origin
git reset --hard origin/main


As last resort, extract commit list and reconstruct repository manually.

Prevention

Keep remote mirrors/backups.

Ensure disk integrity and avoid manual edits to .git/objects.

Labels: pack, corruption, forensics

Scenario 8 — Complex cherry-pick chain with dependencies

Title: Need to cherry-pick non-consecutive dependent commits onto hotfix branch

Reproduction Steps

# feature branch has commits 1..4 (each depends on previous)
# hotfix branch created from main
# requirement: cherry-pick commits 2 and 4 only onto hotfix


Expected behavior
Hotfix contains the desired changes without breaking dependencies.

Actual behavior
Cherry-picking commit 2 fails due to missing dependency commit 1, etc.

Root cause
Selected commits depend on earlier commits not included — resulting in patch application failures.

Resolution
Option 1 — include dependencies:

git checkout hotfix
git cherry-pick <commit-1-hash>
git cherry-pick <commit-2-hash>
# repeat for commit-3/4 as required


Option 2 — create a temp branch and rebase/squash:

git checkout -b temp-branch feature
git rebase -i main
# in editor: keep only commits 2 and 4, reorder/add dependencies as needed
git checkout hotfix
git merge --no-ff temp-branch


Option 3 — apply patches:

git format-patch <commit-1-hash>..<commit-2-hash> --stdout > patch.mbox
git checkout hotfix
git am < patch.mbox


Prevention

Make commits more atomic and independent where possible.

Document dependencies between commits (commit messages).

Labels: cherry-pick, dependencies, advanced

Scenario 9 — Detangle intertwined branch history (messy merges)

Title: Tangled merge history across dev1, dev2, and main; need clean linear history or clear branches

Reproduction Steps

# create dev1/dev2 with several merges back and forth producing complex graph
git log --oneline --graph --all  # shows tangled history


Expected behavior
Readable, maintainable history; optionally linearized.

Actual behavior
History is heavily merged and confusing; hard to audit changes.

Root cause
Multiple merges in different orders and back-merges create complex DAG.

Resolution (strategies)

Analyze:

git log --oneline --graph --all --decorate
git log --merges --oneline


Linearize with care:

Option A — rebase dev branches onto main:

git checkout dev1
git rebase -i --rebase-merges main   # preserves some merges if needed


Option B — extract logical changes into clean branches:

git checkout -b clean-dev1 main
# cherry-pick non-merge commits from dev1
git cherry-pick <hash1> <hash2> ...


Option C — squash merges into semantic commits:

git checkout -b linearized main
git merge --squash dev1
git commit -m "All dev1 changes"
git merge --squash dev2
git commit -m "All dev2 changes"


Communicate the history rewriting to the team and coordinate force pushes if rewriting shared branches.

Prevention

Establish merge/rebase policy (e.g., always rebase feature branches onto main before merging).

Prefer PRs and CI checks; avoid frequent cross-merging without coordination.

Labels: history, rebase, refactor

Scenario 10 — Forensics after repository attack / malicious history rewriting

Title: Forensic investigation and recovery after suspected malicious commits and history rewriting

Reproduction Steps

# simulate attacker injecting code and rewriting history (amend, rebase, squash)


Expected behavior
Repository is trustworthy and auditable; malicious changes are quickly identified and reverted.

Actual behavior
Commits show suspicious messages/dates; backdoor file added; commits squashed to hide history.

Root cause
Repository was tampered with; attacker rewrote history and attempted to remove traces.

Forensic steps & recovery

Gather comprehensive logs:

git log --all --full-history --pretty=fuller
git log --all --source --full-history --oneline
git log --format="%ai %ci %H %s" --all
git log --all --pretty=format: --name-only | sort -u   # all files ever added


Inspect reflogs:

git reflog show --all
git log -g --abbrev-commit --pretty=oneline


Check object integrity:

git fsck --full --strict --verbose


Find suspicious authors/timestamps:

git log --all --pretty=format:"%H %an %ae %ai %s" | grep -E "(root|admin|system)"


Review diffs for malicious changes:

git log -p --all


Recover a known good state:

git reset --hard <known-good-commit>
# or create a new branch from known-good and push to a secure remote


Create audit trail:

git log --all --format="%H|%an|%ae|%ai|%ci|%s" > audit_trail.csv


Harden repository:

Enable commit signing: git config --global commit.gpgsign true

Enforce branch protections and required reviewers.

Rotate credentials and audit server access logs.

Prevention

Enforce GPG-signed commits and verify signatures in CI.

Protect branches, enable required reviews and status checks.

Maintain offsite backups and audit logs.

Labels: security, forensics, incident-response, critical

Scenario 11 — Worktree merge conflict when same file modified in multiple worktrees

Title: Merge conflict in app.py after concurrent edits in multiple worktrees (feature-auth, feature-payment)

Reproduction Steps

mkdir worktree-complex && cd worktree-complex
git init
echo "main code" > app.py
git add app.py; git commit -m "Initial commit"

git checkout -b feature-auth
echo "auth module" > auth.py; git commit -am "Add auth"

git checkout main
git checkout -b feature-payment
echo "payment module" > payment.py; git commit -am "Add payment"

# Create worktrees
git worktree add ../worktree-auth feature-auth
git worktree add ../worktree-payment feature-payment

# Conflicting edits
cd ../worktree-auth
echo "conflicting change from auth" >> app.py; git commit -am "Auth changes main file"

cd ../worktree-payment
echo "conflicting change from payment" >> app.py; git commit -am "Payment changes main file"

# Merge attempts
cd ../worktree-complex
git merge feature-auth
git merge feature-payment   # Conflict appears


Expected behavior
Both feature branches merge cleanly or conflicts are obvious and resolvable.

Actual behavior
Merge conflicts in app.py require manual resolution.

Root cause
Concurrent divergent edits to the same file from separate worktrees/branches; worktrees are independent checkouts of the same repo and can produce genuine conflicts.

Resolution (step-by-step)

Inspect worktrees and status:

git worktree list --porcelain
git status


Open file and resolve conflict markers:

cat app.py    # see <<<<<<<, =======, >>>>>>>
# manually edit app.py to produce the desired merged content
git add app.py
git commit -m "Resolve merge conflict: combine auth and payment changes"


If you commit inside a worktree, push or switch back to main as needed:

git merge --continue   # if in middle of an interrupted merge


Cleanup worktrees:

git worktree remove ../worktree-auth
git worktree remove ../worktree-payment
# if worktree is locked or refusing removal:
git worktree remove --force ../worktree-auth
git worktree prune


Repair or move worktrees if necessary:

git worktree repair
git worktree move ../worktree-auth ../new-location/worktree-auth
git worktree list --verbose


Prevention / best practices

Coordinate edits to the same files across worktrees; treat them like separate clones.

Before merging, git fetch and git rebase feature branches onto latest main to reduce conflicts.

Use CI to run merges sequentially and detect conflicts early.

Labels: worktree, merge-conflict, coordination

Scenario 12 — Confusing custom refspecs across multiple remotes

Title: Unexpected remote branch mapping / push destinations because of custom refspecs

Reproduction Steps

mkdir refspec-advanced && cd refspec-advanced
git init
# create bare remotes origin (upstream-repo) and qa (qa-repo)
git remote add origin ../upstream-repo
git remote add qa ../qa-repo

# configure custom fetch/push refspecs
git config remote.origin.fetch "+refs/heads/*:refs/remotes/origin/*"
git config --add remote.origin.fetch "+refs/tags/*:refs/tags/*"
git config remote.origin.push "refs/heads/main:refs/heads/main"
git config --add remote.origin.push "refs/heads/develop:refs/heads/develop"

# push with ad-hoc refspecs
git push origin main:refs/heads/qa-main
git push qa feature/complex-feature:refs/heads/production


Expected behavior
Branches map to intended remote refs and fetch/push behave predictably.

Actual behavior
Branches get pushed to unexpected refs or remotes; confusion over where branches live.

Root cause
Custom refspecs override default mappings and cause pushes/fetches to target nonstandard ref namespaces.

Resolution (step-by-step)

Audit configuration:

git config --get-regexp remote.*.fetch
git config --get-regexp remote.*.push
git remote -v
cat .git/config
git ls-remote origin


Reset and set clear fetch/push refspecs:

git config remote.origin.fetch "+refs/heads/*:refs/remotes/origin/*"
git config remote.origin.push "refs/heads/*:refs/heads/*"
git config remote.qa.fetch "+refs/heads/*:refs/remotes/qa/*"
git config remote.qa.push "refs/heads/main:refs/heads/production"


Push a specific branch to a custom remote branch deliberately:

git push origin feature/complex-feature:refs/heads/feature-release


Fetch a remote PR-style refspec:

git config --add remote.origin.fetch "+refs/pull/*/head:refs/remotes/origin/pr/*"
git fetch origin
git checkout origin/pr/123


Delete a remote branch using safe syntax:

git push origin --delete obsolete-branch


Prevention / best practices

Avoid adding unusual push refspecs unless required; prefer explicit refspecs when pushing ad-hoc.

Document any custom mappings in repo README or .git/config.

Use git remote -v before pushing to confirm targets.

Labels: refspec, remote, configuration

Scenario 13 — Server-side hooks blocking pushes (policy enforcement)

Title: Push rejected by pre-receive / update hooks enforcing branch protection and commit formats

Reproduction Steps

# create bare repo server-hooks (server-side)
# add pre-receive and update hooks as provided (reject force pushes to main, enforce commit message)
cd client-repo
echo "test" > test.txt
git add test.txt
git commit -m "bad commit message"
git push origin main   # expected to be rejected by hooks


Expected behavior
Push either accepted (if policy satisfied) or rejected with clear error message.

Actual behavior
Push rejected due to policy violation (commit message, force-push detection, large file, etc.).

Root cause
Server-side hooks enforce rules; client commit violates them.

Resolution (step-by-step)

Read hook error message on push (remote will echo rejection reason).

Fix commit locally to satisfy hook (example: amend commit message):

git commit --amend -m "feat: add test functionality"
git push origin main


For large files rejected by size-check hook, remove or split large file:

git reset --soft HEAD~1        # undo last commit if needed
git rm --cached largefile.bin
git commit -m "feat: remove large file"
git push origin main


On server, manage hooks safely:

Add post-receive for notifications (make sure webhooks use secure endpoints).

Keep hook scripts robust and fail-safe (log reasons).

Prevention / best practices

Document server-side policies and share with the team.

Implement helpful rejection messages and guidance in hooks.

Use branch protection and CI checks for easier client-side validation.

Labels: hooks, policy, pre-receive

Scenario 14 — Complex client-side hook chain rejects commits

Title: Local pre-commit / commit-msg / prepare-commit-msg hooks block commits due to multiple validation rules

Reproduction Steps

git init hook-chain
# add pre-commit, commit-msg, prepare-commit-msg hooks (debugger detection, TODO checks, pylint, commit format enforcement)
echo "console.log('debug');" > app.js
git add app.js
git commit -m "add debug"   # hooks fail and block commit


Expected behavior
Commits pass only when checks succeed; helpful messages point to fixes.

Actual behavior
Hooks block commits; developer frustrated or bypasses with --no-verify.

Root cause
Multiple validations detect problems (debugger code, non-conforming commit message, large files).

Resolution (step-by-step)

Fix the code and commit message:

# remove debug statements
sed -i '/console\.log/d' app.js
git add app.js
git commit -m "feat(app): add main application logic"


If urgent and approved, bypass hooks (use sparingly):

git commit --no-verify -m "emergency fix"


Improve hooks to show clear diagnostics and non-fatal warnings; use set -e and accumulate exit code rather than immediate exit.

Share hooks with team using core.hooksPath:

git config core.hooksPath .githooks
mkdir .githooks
# move hooks into .githooks and commit
git add .githooks; git commit -m "chore: add shared git hooks"


Prevention / best practices

Keep hooks fast, clear, and well-documented.

Use pre-commit framework for portable checks and easy installation.

Provide a CI pipeline enforcing same rules to avoid bypass temptation.

Labels: hooks, pre-commit, developer-experience

Scenario 15 — Custom merge/diff drivers and .gitattributes for JSON, LFS and secret filtering

Title: Merge conflicts for config.json handled by custom JSON merge driver and attributes

Reproduction Steps

git init custom-merge
# configure merge=json driver and .gitattributes for *.json
# create config.json, branch feature1 and feature2 with conflicting JSON edits
git merge feature1
git merge feature2   # conflicts resolved by custom driver or fail if driver broken


Expected behavior
Custom merge driver merges JSON sensibly (e.g., deep-merge or field-priority) without human conflict resolution.

Actual behavior
If driver is misconfigured or buggy, merges fail or produce invalid JSON.

Root cause
Custom driver command or script mis-specified; attributes not applied; gitattributes precedence or path errors.

Resolution (step-by-step)

Inspect attributes and driver:

git check-attr -a config.json
git check-attr merge config.json


Replace inline config with robust script .git/json-merge.py and point config:

git config merge.json.driver ".git/json-merge.py %O %A %B"
chmod +x .git/json-merge.py


Test merge driver locally by creating conflict and running git merge feature2 and checking output resulting JSON validity.

For LFS files, ensure git lfs install and .gitattributes track large types; commit the .gitattributes.

Prevention / best practices

Write merge driver scripts to handle error cases and return non-zero on failure.

Test diff/merge drivers thoroughly and include them in repo (document in README).

Use git ls-files --eol and git check-attr to debug attribute application.

Labels: gitattributes, merge-driver, lfs

Scenario 16 — Incomplete git bundle for air-gapped transfers (missing branches)

Title: Cloned bundle missing feature branch because bundle was created incompletely

Reproduction Steps

# repo with main and feature branches
# created bundle: git bundle create repo.bundle main  # forgot feature
git clone air-gapped/repo.bundle secure-env
cd secure-env
git branch -a   # feature missing


Expected behavior
Bundle contains all branches required for air-gapped environment.

Actual behavior
Feature branch or other refs missing in cloned repo.

Root cause
Bundle created with insufficient refs; e.g., only main included.

Resolution (step-by-step)

Create a complete bundle:

cd ../air-gapped
git bundle create complete.bundle --all
git bundle verify complete.bundle
git bundle list-heads complete.bundle


Create incremental/update bundles if needed:

git bundle create incremental.bundle main~1..main


Clone/fetch from bundle in secure env:

git clone ../air-gapped/complete.bundle full-secure-env
# or, in an existing repo:
git fetch ../air-gapped/update.bundle main:main


To include specific refs/branches:

git bundle create specific.bundle feature~5..feature


Prevention / best practices

Use --all when bundling full repo or explicitly include all refs you need.

Always git bundle verify and git bundle list-heads before transferring.

Labels: bundle, air-gapped, backup

Scenario 17 — Remove sensitive data using git replace, grafts, or filter-repo

Title: Secret (API key) accidentally committed — need to replace or graft history without losing entire history

Reproduction Steps

# commit with secret in Version 2; multiple later commits exist
# Want to remove secret from history without full rewrite (or with minimal disruption)


Expected behavior
Secret removed from history (or replaced) and repository cleaned.

Actual behavior
Secret still present in existing commits until replaced/repacked; naive replace leaves original objects reachable unless filter-branch or filter-repo used.

Root cause
Sensitive data committed; Git immutable history requires rewriting or replacement references to remove traces.

Resolution (step-by-step)

Create a cleaned commit and use git replace:

# make cleaned version of commit
git checkout $secret_commit
# edit file to remove secret
git commit --amend -m "Version 2 (cleaned)"
cleaned_commit=$(git rev-parse HEAD)
git replace $secret_commit $cleaned_commit
git log --oneline   # shows replacement


Make replacement permanent (rewrite history):

git filter-branch -- --all   # or better: use git-filter-repo to rewrite
# If using git-filter-repo:
# git filter-repo --replace-text replacements.txt
git replace -d $secret_commit
git reflog expire --expire=now --all
git gc --prune=now --aggressive


Alternative approach using grafts:

echo "$bad_commit $new_root" >> .git/info/grafts
git filter-branch -- --all
rm .git/info/grafts


Verify secret removed: git grep 'secret123' $(git rev-list --all) should return nothing.

Prevention / best practices

Use pre-commit hooks to prevent accidental secrets (pre-commit + detect-secrets).

Move secrets to secure vault or env vars; use .gitignore / filter to avoid checking in secrets.

If rewriting public history, coordinate with all collaborators (force push required).

Labels: secrets, history-rewrite, critical

Scenario 18 — Pushing and fetching Git notes across clones / namespaces

Title: Git notes not propagated to remote clone — notes seem to be missing after push/clone

Reproduction Steps

git init notes-management
# commit several commits
git notes add -m "Reviewed by: John" HEAD
# push to notes-remote bare repo by default and clone remote
git remote add origin ../notes-remote.git
git push origin main   # notes not pushed
git clone notes-remote.git notes-clone
cd notes-clone
git log --show-notes    # no notes shown


Expected behavior
Notes are visible after push and clone.

Actual behavior
Git notes are not pushed/fetched by default; separate refs under refs/notes/*.

Root cause
Notes live under refs/notes/* and are not part of standard push/fetch unless configured.

Resolution (step-by-step)

Configure push refspec for notes:

git config --add remote.origin.push '+refs/notes/*:refs/notes/*'
git push origin


Fetch notes explicitly on other clones:

git fetch origin 'refs/notes/*:refs/notes/*'
git log --show-notes=*


Use namespaced notes refs:

git notes --ref=bugs add -m "Bug #123 fixed" HEAD
git push origin refs/notes/bugs:refs/notes/bugs


Backup/restore notes:

git bundle create notes-backup.bundle refs/notes/*
git fetch notes-backup.bundle 'refs/notes/*:refs/notes/*'


Prevention / best practices

Document notes usage and push conventions.

Add notes push refspec to repo template so all clones push notes automatically.

Labels: notes, metadata, integration

Scenario 19 — Export/archive without secrets using .gitattributes

Title: git archive includes secrets.txt despite intent to omit it for partner transfer

Reproduction Steps

# project contains secrets.txt and committed
git archive --format=zip --output=project.zip HEAD
# secrets.txt included in archive


Expected behavior
Exported archive excludes secrets.txt and other sensitive paths.

Actual behavior
git archive includes all tracked files unless export-ignore is used.

Root cause
.gitattributes not configured with export-ignore for sensitive files.

Resolution (step-by-step)

Add export-ignore entries to .gitattributes:

echo "secrets.txt export-ignore" >> .gitattributes
echo "tests/ export-ignore" >> .gitattributes
echo "*.log export-ignore" >> .gitattributes
git add .gitattributes
git commit -m "Add export-ignore attributes"


Create archive again:

git archive --format=zip --output=clean.zip HEAD
unzip -l clean.zip  # verify secrets excluded


Alternative: archive specific tree or paths:

git archive --format=tar --output=src-only.tar HEAD:src/


Prevention / best practices

Never commit secrets; store them outside repo and use secret management.

Use .gitattributes export-ignore for controlled exports.

Verify archives before sharing.

Labels: export, gitattributes, security

Scenario 20 — Git performance issues in very large repo and optimizations

Title: Slow git status / git log in large repo (many big binary objects / many commits)

Reproduction Steps

# create repo with many binary files and frequent commits
time git status   # slow
time git log --all --oneline  # slow


Expected behavior
git status & git log are reasonably fast.

Actual behavior
Commands are slow due to many objects / pack inefficiencies and lacking optimization.

Root cause
Large number of objects, non-optimal packing, no commit-graph or bitmap indexes; huge number of loose files or heavy pack files.

Resolution (step-by-step)

Diagnose:

GIT_TRACE=1 git status
git count-objects -vH
du -sh .git


Repack & GC optimizations:

git gc --aggressive --prune=now
git repack -a -d -f --depth=250 --window=250


Create bitmap and commit-graph for faster traversal:

git repack -a -d -b
git commit-graph write --reachable --changed-paths
git multi-pack-index write


Enable fsmonitor, split index and untracked cache:

git config core.fsmonitor true
git config core.untrackedCache true
git update-index --split-index


Use sparse-checkout or partial clone for working with subsets:

git clone --filter=blob:none --sparse <url>
git sparse-checkout set src/core


Use Git maintenance tasks:

git maintenance start
git maintenance run --task=incremental-repack
git verify-pack -v .git/objects/pack/*.idx | head


Measure improvements:

time git status
time git log --all --oneline


Prevention / best practices

Use Git LFS for large binaries.

Enforce .gitattributes and avoid committing unnecessary large files.

Run periodic maintenance (repack, commit-graph) on server and local clones.

Consider repository splitting if monorepo grows too large.

Labels: performance, gc, large-repo

Scenario 21 — Advanced partial clone + sparse-checkout for huge monorepo

Title: Standard clone of large-monorepo is massive and slow; needs partial clone + sparse-checkout

Reproduction Steps

mkdir large-monorepo && cd large-monorepo
git init
# create frontend/backend/mobile dirs and many large binary files (dd ... file*.bin)
git add .
git commit -m "Large monorepo structure"
# add many small commits
for i in {2..20}; do
  echo "Update $i" >> frontend/src/update.txt
  echo "Update $i" >> backend/api/update.txt
  git commit -am "Update $i"
done

cd ..
time git clone large-monorepo standard-clone
du -sh standard-clone


Expected behavior
Clone is small/fast for day-to-day work (download only necessary blobs).

Actual behavior
Full clone downloads all blobs => large disk & slow operations.

Root cause
All blobs are fetched by default; repository contains many large binary blobs.

Resolution (commands & steps)

Partial clone + sparse-checkout:

git clone --filter=blob:none --sparse large-monorepo partial-clone
cd partial-clone
git sparse-checkout init --cone
git sparse-checkout set frontend/src
du -sh .git
time git status


Shallow + partial (careful — loses history):

git clone --depth=1 --filter=blob:limit=1m --sparse large-monorepo shallow-partial
git sparse-checkout set backend/api


Treeless clone for metadata-only:

git clone --filter=tree:0 large-monorepo treeless-clone
git sparse-checkout init --cone
git sparse-checkout set mobile/ios


Dynamically expand/contract:

git sparse-checkout add frontend/tests
git sparse-checkout list
git sparse-checkout disable


Convert existing clone to partial:

git config core.sparseCheckout true
git config remote.origin.promisor true
git config remote.origin.partialclonefilter "blob:none"


Prevention / best practices

Use Git LFS for large binaries.

Encourage componentization and smaller repos where appropriate.

Document sparse/partial clone workflows for contributors.

Labels: partial-clone, sparse-checkout, performance

Scenario 22 — Git LFS migration & performance tuning

Title: Repo with many MP4s is huge and clone is slow — migrate to Git LFS and tune performance

Reproduction Steps

mkdir lfs-migration && cd lfs-migration
git init
for i in {1..10}; do
  dd if=/dev/urandom of=video$i.mp4 bs=1M count=50
  git add video$i.mp4
  git commit -m "Add video $i"
done
du -sh .git/objects
time git clone lfs-migration lfs-clone


Expected behavior
Large media tracked by LFS; clones are faster and repo size reduced.

Actual behavior
Large files stored in Git history => big .git and slow clones.

Root cause
Binaries committed directly to git history, not tracked by LFS.

Resolution (commands & steps)

Install and configure LFS:

git lfs install
git lfs track "*.mp4"
git add .gitattributes
git commit -m "Configure LFS tracking"


Migrate existing files into LFS:

git lfs migrate import --include="*.mp4" --everything
git lfs ls-files
git lfs status


Tune LFS performance:

git config lfs.concurrenttransfers 8
git config lfs.batch true
git lfs fetch --all
git lfs prune


For bandwidth-limited clones:

git lfs install --skip-smudge
git pull   # get pointers only
git lfs pull --include="video1.mp4"


Verify integrity and cleanup:

git lfs fsck
git lfs prune --older-than=30d


Prevention / best practices

Add LFS tracking before large files are committed.

Add pre-commit hooks to block large files.

Monitor LFS server quotas and usage.

Labels: git-lfs, migration, performance

Scenario 23 — Wire protocol v2 optimization for many refs

Title: Cloning/ls-remote slow with protocol v1 for repos with thousands of refs — enable protocol v2

Reproduction Steps

# create repo with 1000 branches and 1000 tags
git clone --bare protocol-test protocol-remote.git
GIT_TRACE_PACKET=1 git -c protocol.version=1 clone protocol-remote.git slow-clone
# measure pkt-line overhead


Expected behavior
Network fetches advertise & transfer refs efficiently; clone/fetch is faster.

Actual behavior
Protocol v1 transfers more metadata resulting in slower operations.

Root cause
Protocol v1 is less efficient for many refs and large ref advertisements.

Resolution (commands & steps)

Enable protocol v2 client-side:

git config --global protocol.version 2
GIT_TRACE_PACKET=1 git clone protocol-remote.git fast-clone


If you control server, enable server-side features:

cd protocol-remote.git
git config uploadpack.allowFilter true
git config protocol.version 2


Use feature: ref-in-want to reduce ref advertisement and fetch only needed refs.

Measure performance:

GIT_TRACE2_PERF=1 git fetch origin 2>&1 | grep "data-bytes"


Prevention / best practices

Prefer protocol v2 for high-ref repos.

Keep remote server configuration aligned with clients.

Use specialized refs (lightweight tags) where appropriate.

Labels: protocol-v2, network, optimization

Scenario 24 — Advanced packing & compression optimization

Title: Many loose objects and suboptimal packfiles — repo needs aggressive repack and bitmap indexes

Reproduction Steps

# create repo with many large binary commits (file*.dat)
git count-objects -v
du -sh .git/objects
git repack


Expected behavior
Efficient packfiles, low .git size, and faster operations.

Actual behavior
Multiple small packs, many loose objects, slow gc & fetch.

Root cause
Inefficient pack parameters and no bitmap/commit-graph indexes.

Resolution (commands & steps)

Aggressive repack with tuning:

git repack -a -d -f --depth=250 --window=250
git repack -a -d -b --write-bitmap-index
git commit-graph write --reachable --changed-paths
git multi-pack-index write


Prune & garbage-collect:

git reflog expire --expire=now --all
git gc --prune=now --aggressive


Split/limit pack sizes if needed:

git repack -a -d --max-pack-size=100m


Configure automatic maintenance:

git config gc.auto 256
git maintenance start


Prevention / best practices

Run periodic maintenance on server.

Use LFS for large binaries to avoid packing them into Git history.

Monitor pack statistics: git verify-pack -v .git/objects/pack/*.idx.

Labels: packing, gc, storage

Scenario 25 — File system cache & index optimizations for large working trees

Title: git status slow on repository with many files — enable untracked cache, FSMonitor, split index

Reproduction Steps

# create repo with 10 directories × 1000 files each
git add .
git commit -m "Many files"
time git status  # slow


Expected behavior
git status responds quickly even on large working trees.

Actual behavior
Status is slow due to scanning many untracked/changed files.

Root cause
No untracked cache / FS monitor enabled; single monolithic index hitting FS.

Resolution (commands & steps)

Enable untracked cache:

git config core.untrackedCache true
git update-index --untracked-cache


Enable FSMonitor (e.g., watchman) if available:

git config core.fsmonitor "watchman"
git config core.untrackedCache true


Use split index:

git update-index --split-index
git config splitIndex.maxPercentChange 20


Consider index version 4:

git config index.version 4
rm .git/index
git reset --hard


Use sparse-checkout for working subsets:

git sparse-checkout init --cone
git sparse-checkout set dir1 dir2


Prevention / best practices

Avoid extremely large single working trees if unnecessary.

Install & configure FSMonitor on developer machines.

Document repository optimization steps for contributors.

Labels: index, fsmonitor, performance

Scenario 26 — Network & transfer optimization for large remote clones

Title: Network clone/fetch is slow — optimize compression, partial clones, and transfer settings

Reproduction Steps

# create repo with many large binary commits and bare remote
git clone network-remote.git slow-network-clone
time git clone network-remote.git slow-network-clone


Expected behavior
Efficient clone/fetch over network with compression and partial transfers.

Actual behavior
Slow clones due to large blob transfer and non-optimized network settings.

Resolution (commands & steps)

Optimize compression & pack settings:

git config --global core.compression 9
git config --global pack.window 250
git config --global pack.depth 250


Use HTTP/2 and increase http.postBuffer for large pushes:

git config --global http.version HTTP/2
git config --global http.postBuffer 524288000


Use shallow/partial/filtered clones:

git clone --depth=1 --filter=blob:limit=1m network-remote.git shallow-fast
git clone --filter=blob:none --sparse network-remote.git partial-network


Use bundles or reference repos for offline/cache-based transfers:

git bundle create repo.bundle --all
git clone --reference=/local/cache/repo network-remote.git fast-reference


Enable SSH compression:

export GIT_SSH_COMMAND="ssh -C"


Prevention / best practices

Prefer partial clone & LFS where suitable.

Maintain a local mirror/cache for large teams.

Use server-side pack tuning and enable protocol v2.

Labels: network, transfer, partial-clone

Scenario 27 — Commit-graph & reachability optimization for complex graphs

Title: Slow git log / git merge-base on complex DAG — generate commit-graph and enable related features

Reproduction Steps

# create many feature branches and merges producing complex graph
time git log --graph --all --oneline
time git merge-base main feature-25


Expected behavior
Commit reachability and traversal operations are fast.

Actual behavior
Operations are slow due to traversing full commit history without commit-graph optimizations.

Root cause
No commit-graph, missing bloom filters, large DAG to traverse.

Resolution (commands & steps)

Generate commit-graph:

git commit-graph write --reachable --changed-paths
git commit-graph verify
git config core.commitGraph true
git config gc.writeCommitGraph true


Use incremental/split commit-graph updates:

git commit-graph write --reachable --split
git maintenance run --task=commit-graph


Enable bloom filters for changed paths:

git config commitGraph.readChangedPaths true


Re-verify and measure:

time git log --graph --all --oneline
time git merge-base main feature-25


Prevention / best practices

Enable commit-graph and maintenance on server and heavy-duty developer machines.

Use commit-graph write in CI maintenance tasks.

Keep DAG manageable (avoid unnecessary merge commits when not needed).

Scenario 28 — GPG commit signing and verification (prevent impersonation)

Title: Commits not signed — impossible to verify author authenticity (impersonation risk)

Reproduction Steps

mkdir gpg-signing && cd gpg-signing
git init
echo "unsigned code" > app.py
git add app.py
git commit -m "Unsigned commit"

# Attacker impersonates identity locally
git config user.name "Attacker"
git config user.email "attacker@evil.com"
echo "malicious code" > malware.py
git add malware.py
git commit -m "Backdoor added"

git log --pretty=format:"%H %an %ae %s"


Expected behavior
Commits are verifiable as coming from legitimate developers via GPG signatures.

Actual behavior
Unsigned commits allow spoofed author/committer fields; trust cannot be established.

Root cause
No commit signing policy; Git records identity fields but does not verify them by default.

Resolution (commands & steps)

Generate and configure GPG:

gpg --full-generate-key           # create RSA 4096 key, follow prompts
gpg --list-secret-keys --keyid-format=long
GPG_KEY_ID=$(gpg --list-secret-keys --keyid-format=long | awk '/sec/ {print $2}' | cut -d'/' -f2)
git config --global user.signingkey $GPG_KEY_ID
git config --global gpg.program gpg
git config --global commit.gpgsign true
git config --global tag.gpgsign true
echo 'export GPG_TTY=$(tty)' >> ~/.bashrc && export GPG_TTY=$(tty)


Create signed commits:

git config user.name "Your Name"
git config user.email "you@domain.com"
echo "secure code" > secure.py
git add secure.py
git commit -S -m "Signed commit"
git log --show-signature -1


Verify commits and tags:

git verify-commit <commit-hash>
git tag -s v1.0 -m "Signed v1.0"
git verify-tag v1.0


Enforce on server (example pre-receive hook — reject unsigned commits):

# server-side hooks/pre-receive (simplified)
while read oldrev newrev refname; do
  for commit in $(git rev-list $oldrev..$newrev); do
    if ! git verify-commit $commit >/dev/null 2>&1; then
      echo "ERROR: Commit $commit is not signed!" && exit 1
    fi
  done
done
exit 0


Rotate GPG agent caching for convenience:

echo "default-cache-ttl 3600" >> ~/.gnupg/gpg-agent.conf
echo "max-cache-ttl 7200" >> ~/.gnupg/gpg-agent.conf
gpg-connect-agent reloadagent /bye


Optionally sign existing commits (history rewrite — coordinate first):

git rebase --exec 'git commit --amend --no-edit -S' -i HEAD~N
# or batch rewrite carefully (beware force-push)


Prevention / best practices

Require GPG-signed commits on protected branches (server policy/hooks or platform settings).

Distribute and trust developers’ public keys (use web of trust or Company PKI).

Use commit signing + CI verification to block unsigned pushes.

Educate contributors how to export/import keys (gpg --armor --export).

Labels: security, gpg, policy, critical

Scenario 29 — Git rerere (reuse recorded conflict resolutions)

Title: Repeatedly resolving same conflict manually — enable rerere to reuse resolutions

Reproduction Steps

mkdir rerere-test && cd rerere-test
git init
# create base file and branches with conflicting edits
echo -e "line 1\nline 2\nline 3" > config.txt
git add config.txt; git commit -m "Base config"
git checkout -b feature
# change line 2 in feature
# commit
git checkout main
# change line 2 in main different way
# commit
git merge feature   # conflict — resolve manually
# later similar conflict repeats during rebase


Expected behavior
Once you resolve a conflict once, Git can remember and reapply the recorded resolution automatically.

Actual behavior
Same conflict must be resolved manually repeated times.

Root cause
git rerere (reuse recorded resolution) not enabled or not trained before conflicts occurred.

Resolution (commands & steps)

Enable rerere:

git config --global rerere.enabled true
git config --global rerere.autoupdate true


Recreate and resolve conflict once so rerere records resolution:

# reproduce conflict, resolve the file, git add file, git commit
# rerere will record resolution in .git/rr-cache
ls .git/rr-cache/


Re-run the conflicting operation — rerere should auto-apply:

# e.g., rebase/merge again
git rebase main   # rerere may auto-resolve


Inspect/maintain rerere DB:

git rerere status
git rerere diff
git rerere forget <path>    # if you want to forget a recorded resolution
rm -rf .git/rr-cache/       # clear all (if needed)


Share rerere DB across team (optional, rare):

tar -czf rerere-db.tar.gz .git/rr-cache/
# team can extract into their .git/rr-cache/


Prevention / best practices

Enable rerere globally on developer machines for projects with recurring conflicts.

Use rerere.autoupdate to auto-stage conflict resolutions where safe.

Keep rerere DB manageable; don’t blindly accept auto-applied resolutions—inspect when ambiguous.

Labels: rerere, merge-conflict, workflow

Scenario 30 — Advanced stash usage (include untracked/ignored, selective stash)

Title: git stash doesn't capture untracked or ignored files by default — need advanced stash commands

Reproduction Steps

mkdir stash-advanced && cd stash-advanced
git init
echo "tracked modified" > tracked.txt
git add tracked.txt; git commit -m "Initial"
# make changes: modified tracked, staged new file, untracked file, ignored file
git stash            # default stash; untracked/ignored not included
git status           # untracked.txt and debug.log still present


Expected behavior
Developer can stash a set of changes including untracked or even ignored files when needed.

Actual behavior
Default git stash does not include untracked/ignored files; developers confused.

Resolution (commands & steps)

Common stash variants:

git stash push -m "WIP description"        # stash staged + unstaged (no untracked)
git stash push -u -m "WIP include untracked"  # include untracked files
git stash push -a -m "WIP include ignored"    # include ignored files (use cautiously)
git stash --keep-index                      # stash unstaged only; keep staged
git stash push -- 'src/*.py' 'tests/'      # stash specific pathspecs


Inspect and apply specific stash:

git stash list --stat
git stash show -p stash@{0}
git stash apply stash@{0}
git stash pop stash@{0}
git stash branch recovered-work stash@{0}   # create branch from stash


Convert stash to commit or patch:

git stash show -p stash@{0} > stash-patch.patch
git apply stash-patch.patch
git commit -am "Apply stash as commit"


Recover dropped stash (advanced):

git fsck --unreachable | grep commit | cut -d' ' -f3 | xargs git log -1
# locate stash commit hash and git stash apply <hash>


Partial/interactive stash:

git stash -p   # interactively choose hunks to stash


Prevention / best practices

Use descriptive stash messages.

Use git stash branch instead of blind pop to avoid losing stashes.

Avoid stashing ignored files unless absolutely necessary.

Keep stash usage documented on team workflows.

Labels: stash, workflow, recovery

Scenario 31 — Git blame and history investigation (blame across renames & ignore revs)

Title: Need to find who introduced a bug across renames/moves — advanced git blame and pickaxe techniques

Reproduction Steps

mkdir history-investigation && cd history-investigation
git init
# create app.js and a sequence of commits by different users
git mv app.js lib/processor.js
git commit -m "Reorganize code"
# Bug found; need to find author and commit that introduced it


Expected behavior
Ability to find the commit/author that introduced a specific line or bug, following renames and ignoring noise commits.

Actual behavior
Simple git blame may not follow renames or skip formatting/refactor commits.

Resolution (commands & steps)

Blame with follow/rename support:

git blame -w --follow -L <start>,<end> -- lib/processor.js
git blame -e --date=short lib/processor.js


Use pickaxe and advanced logs:

git log -S "TODO: Add validation" --source --all
git log -G "function process" --patch --follow lib/processor.js
git log -L :process:lib/processor.js


Ignore noisy revisions in blame:

# create .git-blame-ignore-revs with commit SHAs to ignore
git config blame.ignoreRevsFile .git-blame-ignore-revs
git blame lib/processor.js


Use bisect + tests to find the introducing commit:

git bisect start
git bisect bad HEAD
git bisect good <known-good-commit>
# run test, mark good/bad until found
git bisect reset


Contributor stats and search:

git shortlog -s -n -- lib/processor.js
git log --name-only --oneline | grep -B1 "lib/processor.js"


Prevention / best practices

Keep small, focused commits with descriptive messages.

Use git-blame-ignore-revs file for large refactors to reduce noise.

Add automated tests so git bisect can be automated by git bisect run.

Labels: investigation, blame, forensics

Scenario 32 — Git worktree for multi-environment development (multiple concurrent features)

Title: Need to work on multiple features concurrently; switching branches too slow — use worktrees

Reproduction Steps

mkdir main-project && cd main-project
git init
# create main + features 1..5
# switching branches loses context and is slow


Expected behavior
Developers can work concurrently on multiple branches/contexts locally without costly branch switching.

Actual behavior
Switching branches is disruptive; context lost and switching is slow for large repos.

Resolution (commands & steps)

Create multiple worktrees:

git worktree add ../work-feature-1 feature-1
git worktree add ../work-feature-2 feature-2
git worktree add ../work-feature-3 feature-3
# create hotfix worktree from main:
git worktree add ../hotfix-1 -b hotfix-1 main


Work independently and manage worktrees:

git worktree list --porcelain
git worktree lock ../work-feature-1 --reason "Critical"
git worktree unlock ../work-feature-1
git worktree move ../work-feature-1 ../new-location/feature-1
git worktree prune
git worktree remove ../work-feature-2


Use detached worktree for inspection:

git worktree add --detach ../inspection HEAD~5


Worktree-specific config:

cd ../work-feature-3
git config --worktree user.email "feature-team@company.com"
git config --worktree core.filemode false
git config --worktree --list


Bisect or CI in separate worktree:

git worktree add ../bisect-workspace -b bisect-branch
cd ../bisect-workspace
git bisect start HEAD HEAD~20


Prevention / best practices

Use worktrees for parallel local development, reviews, and bisecting.

Keep worktree count manageable and clean up stale worktrees.

Document worktree workflow for team to avoid cross-worktree conflicts.

Labels: worktree, multi-env, workflow

Scenario 33 — Git maintenance and automated housekeeping

Title: Repository accumulates loose objects and grows in size — schedule maintenance tasks

Reproduction Steps

mkdir auto-maintenance && cd auto-maintenance
git init
# many large commits and deletes over time; reflog grows
git count-objects -vH
git reflog --all | wc -l


Expected behavior
Repository remains compact, fast and GC/maintenance tasks automatically keep it healthy.

Actual behavior
Repository performance and size degrade over time due to lack of maintenance.

Resolution (commands & steps)

Enable built-in maintenance and schedule:

git maintenance start
git config maintenance.auto true
git config maintenance.strategy incremental
# enable commit-graph and loose object maintenance
git config maintenance.commit-graph.enabled true
git config maintenance.loose-objects.enabled true
git maintenance run --task=commit-graph --task=loose-objects


Create scheduled daily maintenance script (example tasks):

git reflog expire --expire=30.days.ago --all
git prune --expire=2.weeks.ago
git repack -d
git commit-graph write --reachable
git gc --auto
git fsck --connectivity-only


Automate via cron / systemd timer and produce maintenance reports.

Ad-hoc cleanup (when needed):

git reflog expire --expire=now --all
git gc --prune=now --aggressive
git repack -a -d -f


Prevention / best practices

Enable git maintenance on server and heavy dev machines.

Avoid committing large binaries — use LFS or external storage.

Keep regular backups and mirrors before aggressive rewriting.

Labels: maintenance, gc, ops

Scenario 34 — Git attributes for custom behavior (diffs, filters, LFS, export-ignore)

Title: Need consistent behavior for binary files, secrets filtering, JSON diff/merge, and export control via .gitattributes

Reproduction Steps

mkdir git-attributes-advanced && cd git-attributes-advanced
git init
# add app.js, styles.css, config.ini (contains secrets), package.json
# currently diffs show secrets and binary files produce noisy diffs


Expected behavior
Binary files treated as binary, secrets redacted in diffs, JSON diffs readable, exports exclude private files.

Actual behavior
Secrets leak in diffs/archives; diffs for JSON are unreadable; large files are tracked normally.

Resolution (commands & steps)

Create .gitattributes (examples):

* text=auto
*.json diff=json
*.md merge=union
*.lock merge=ours
secrets/* filter=remove-secrets
*.jpg binary
*.mp4 filter=lfs diff=lfs merge=lfs -text
tests/ export-ignore
.github/ export-ignore
secrets.txt export-ignore


Configure filters and drivers:

git config diff.json.textconv "python3 -m json.tool"
git config filter.remove-secrets.clean "sed 's/Password=.*/Password=***REDACTED***/'"
git config filter.remove-secrets.smudge "cat"
# Custom merge driver
cat > .git/xml-merge.sh <<'EOF'
#!/bin/bash
base=$1; ours=$2; theirs=$3
# simple: prefer theirs, then ours
cat "$theirs" > "$ours"
EOF
chmod +x .git/xml-merge.sh
git config merge.xmlmerge.driver ".git/xml-merge.sh %O %A %B"
git config merge.xmlmerge.name "Custom XML merge driver"


Add LFS for large files:

git lfs install
echo "*.mp4 filter=lfs diff=lfs merge=lfs -text" >> .gitattributes
git add .gitattributes; git commit -m "Add gitattributes & LFS"


Test behavior:

git check-attr --all -- config.ini
git diff --cached config.ini   # should show redacted password
git archive --format=tar HEAD | tar -t  # export should exclude export-ignore entries


Global attributes:

echo "*.log -diff" > ~/.gitattributes_global
git config --global core.attributesfile ~/.gitattributes_global


Prevention / best practices

Add .gitattributes and .gitignore templates early in project.

Use secret scanning tools and pre-commit hooks to prevent commits of secrets.

Test custom drivers and filters in CI before widespread adoption.

Document attribute rules in repo README for contributors.

Labels: gitattributes, lfs, security, developer-experience

Scenario 35 — Use Scalar to optimize enterprise-scale monorepo

Title: Standard Git operations extremely slow on enterprise monorepo — adopt Scalar and Scalar-like tuning

Reproduction Steps

mkdir enterprise-monorepo && cd enterprise-monorepo
git init

# create massive repo structure and many large files (dd ... file*.bin)
# add and commit everything
git add .
git commit -m "Enterprise monorepo initial commit"

# add hundreds of commits
for i in {1..500}; do
  echo "Update $i" >> frontend/src/update.txt
  git commit -am "Update $i"
done

time git status
time git log --all --oneline | wc -l


Expected behavior
Interactive operations (status, log, fetch, checkout) are responsive at enterprise scale.

Actual behavior
Commands are very slow; large .git and long GC/pack times.

Root cause
Repository contains huge number of objects and blobs, no enterprise optimizations (no commit-graph, multi-pack index, FS monitor, partial clone, etc.).

Resolution (commands & steps)

Install Scalar (recommended for Windows/macOS; build on Linux) and clone using Scalar:

# install per-platform instructions (winget / brew / build)
scalar clone enterprise-monorepo enterprise-scalar
scalar diagnose
scalar run maintenance


Apply Scalar-like Git tuning on existing repo:

git config core.fsmonitor true
git config core.untrackedCache true
git config core.multiPackIndex true
git config core.commitGraph true
git config gc.writeCommitGraph true
git config pack.useSparse true
git config pack.writeBitmaps true
git config index.threads true
git maintenance start
git commit-graph write --reachable --changed-paths
git multi-pack-index write --bitmap


Convert to partial clone / sparse where appropriate:

git config core.sparseCheckout true
git sparse-checkout init --cone
git sparse-checkout set frontend backend


Tune parallelism:

git config checkout.workers 8
git config fetch.parallel 8


Register repo with Scalar if available:

scalar register
scalar run maintenance


Prevention / best practices

Use Scalar or equivalent for enterprise monorepos.

Encourage partial clones/sparse-checkout and LFS for heavy binaries.

Schedule maintenance tasks (commit-graph, multi-pack index, repack) on server/CI.

Labels: scalar, enterprise, performance, maintenance

Scenario 36 — Deep delta-compression tuning for similar objects

Title: Delta compression inefficient for many similar versions — optimize pack settings and repack

Reproduction Steps

mkdir delta-optimization && cd delta-optimization
git init
# create base.txt and many similar version*.txt files, commit them
for i in {1..50}; do
  sed "s/input\[i\]/input[$i]/g" base.txt > version$i.txt
  git add version$i.txt
  git commit -m "Version $i"
done

git count-objects -v
git gc
git count-objects -v


Expected behavior
Good delta compression between similar objects; .git pack size minimized.

Actual behavior
Default settings produce suboptimal delta chains and larger pack size.

Root cause
Default pack/delta parameters conservative; not tuned for deep delta chains.

Resolution (commands & steps)

Tune pack parameters and repack aggressively:

git config pack.window 250
git config pack.depth 250
git config pack.windowMemory 2g
git config pack.deltaCacheSize 2g
git config pack.threads 0
git repack -a -d -f --depth=250 --window=250
git count-objects -vH
git verify-pack -v .git/objects/pack/*.idx | head -n 50


Optionally increase repack parameters for specific needs:

git repack -a -d -f --window-memory=2g --max-pack-size=2g
git config repack.geometric 2
git repack -d


Analyze delta chains and iterate:

git verify-pack -v .git/objects/pack/*.idx | awk '/chain length/ {print $0}' | sort -n | uniq -c


Prevention / best practices

Tune pack settings on large homogeneous-object repos.

Run scheduled aggressive repacks on server side during maintenance windows.

Use .gitattributes and LFS to avoid packing very large binaries.

Labels: delta-compression, packing, storage

Scenario 37 — Parallel checkout performance tuning

Title: Branch checkouts slow on repo with many files — enable parallel checkout and adjust thresholds

Reproduction Steps

mkdir parallel-checkout && cd parallel-checkout
git init
# create many modules with many files and commit
git checkout -b feature
# modify many files and commit
git checkout main
time git checkout feature  # slow


Expected behavior
git checkout uses multiple cores to process the working tree and completes faster.

Actual behavior
Checkouts run single-threaded; file operations dominate wall time.

Root cause
Parallel checkout not enabled or threshold too high/low for machine; FS contention possible.

Resolution (commands & steps)

Enable parallel checkout and tune:

git config checkout.workers 0     # 0 = use all cores
git config checkout.thresholdForParallelism 100
# or set explicit number:
git config checkout.workers 8


Tune complementary settings:

git config fetch.parallel $(nproc)
git config submodule.fetchJobs $(nproc)
git config index.threads true


Benchmark different values and balance for SSD vs HDD:

# baseline
git config checkout.workers 1; time git checkout feature
# parallel
git config checkout.workers 0; time git checkout feature


Combine with sparse-checkout to reduce file set:

git sparse-checkout init --cone
git sparse-checkout set module1 module2


Prevention / best practices

Configure per-machine settings in dotfiles or repo onboarding docs.

Prefer sparse-checkout when working on a small subset.

Labels: checkout, parallelism, performance

Scenario 38 — Network transfer compression optimization for mixed content

Title: Clone/fetch transfers too much data or is CPU bound due to poor compression strategy — tune network/compression trade-offs

Reproduction Steps

mkdir network-compression && cd network-compression
git init
# create many compressible text files and some random binary files; commit
cd ..
git clone --bare network-compression network-remote.git
time git clone network-remote.git slow-clone
du -sh slow-clone/.git


Expected behavior
Network data transfer and CPU usage balanced; clone speed optimized for network/cpu characteristics.

Actual behavior
Either CPU bound (max compression) or bandwidth heavy (no compression) — suboptimal tradeoff.

Root cause
One-size-fits-all compression defaults; need to select based on network and CPU.

Resolution (commands & steps)

Tune global compression and transport protocol:

# compression tradeoffs
git config --global core.compression 0      # low CPU, more bytes
git config --global pack.threads 0
git config --global http.version HTTP/2
git config --global protocol.version 2
# or for CPU-constrained but low bandwidth:
git config --global core.compression 6


Set network buffers and SSH compression:

git config --global http.postBuffer 1048576000
export GIT_SSH_COMMAND="ssh -C -o Compression=yes -o CompressionLevel=9"


Use partial/shallow clones to reduce transferred blobs:

git clone --filter=blob:limit=1m --depth=1 network-remote.git partial-clone


Profile different settings and choose best for environment:

# test clones with different core.compression values and measure wall time & bytes


Prevention / best practices

Document recommended clone settings for developer networks.

Use server-side pack tuning (bitmaps, multi-pack) to reduce server CPU and transfer size.

Provide local mirrors or caches for distributed teams.

Labels: network, compression, transfer

Scenario 39 — Extreme index performance optimization

Title: git status extremely slow on massive working tree — enable untracked cache, FSMonitor, split index, and index threads

Reproduction Steps

mkdir index-extreme && cd index-extreme
git init
# create huge directory tree (many files) and commit
git add .
git commit -m "Massive working tree"
time git status  # very slow
ls -lh .git/index


Expected behavior
git status, git add, and other index operations complete quickly even with massive trees.

Actual behavior
Index scans dominate runtime; repeated full filesystem scans.

Root cause
No untracked cache, no FS monitor, single monolithic index, no split index.

Resolution (commands & steps)

Enable index optimizations:

git config core.untrackedCache true
git config core.fsmonitor true
git config core.splitIndex true
git config index.threads true
git config index.version 4
git update-index --untracked-cache
git update-index --split-index
# if FSMonitor available:
git config core.fsmonitor watchman   # or appropriate tool


Rebuild index and test:

rm .git/index && git reset --hard
GIT_TRACE_UNTRACKED_STATS=1 git status
time git status


Tune split-index parameters:

git config splitIndex.maxPercentChange 20
git config splitIndex.sharedIndexExpire "2.weeks.ago"


Use sparse-checkout when appropriate:

git sparse-checkout init --cone
git sparse-checkout set level1_1 level1_2


Prevention / best practices

Enable these optimizations for large repos in onboarding scripts.

Install and configure FSMonitor (watchman) on dev machines.

Avoid huge working trees on developer machines if unnecessary.

Labels: index, fsmonitor, performance, scale

Scenario 40 — Multi-pack index and bitmap optimization for many packfiles

Title: Many packfiles slow object lookups; enable multi-pack-index and bitmap indices

Reproduction Steps

mkdir multipack-optimization && cd multipack-optimization
git init
# create many commits to generate multiple packfiles
git repack -d
# add more and re-run repack, resulting in multiple packs
ls -lh .git/objects/pack/
time git rev-list --objects --all | wc -l  # slow


Expected behavior
Object lookups and rev-list operations are fast even with many packfiles.

Actual behavior
Multiple pack files cause many index lookups; operations are slow.

Root cause
No multi-pack index and no bitmaps; Git must scan many pack idx files.

Resolution (commands & steps)

Enable multi-pack index and write bitmap:

git config core.multiPackIndex true
git multi-pack-index write
git multi-pack-index write --bitmap
git multi-pack-index verify


Repack with bitmap optimization:

git repack -a -d -b --write-bitmap-index
git config pack.writeBitmaps true
git config pack.writeBitmapHashCache true


Combine with commit-graph for best performance:

git commit-graph write --reachable --changed-paths
git multi-pack-index write --bitmap


Maintain and expire multi-pack layers:

git multi-pack-index expire
git maintenance run --task=multi-pack-index


Prevention / best practices

Enable multi-pack index on servers with many packs.

Schedule maintenance to write bitmaps and multi-pack indexes.

Monitor pack counts and repack periodically.

Labels: multi-pack-index, bitmap, storage, performance

Scenario 41 — Commit-graph bloom-filter advanced optimization for path queries

Title: Path-specific git log queries slow on complex graph — use commit-graph with bloom filters

Reproduction Steps

mkdir bloom-filter-opt && cd bloom-filter-opt
git init
# create many feature branches and merge them
git checkout main
# create feature branches and merge in a loop
time git log --all -- feature-25/file10.txt  # slow
time git log --all -- feature-10/  # slow


Expected behavior
Path-limited queries and git log -- <path> are fast using commit-graph bloom filters.

Actual behavior
Path queries traverse many commits; slow performance.

Root cause
Missing commit-graph with changed-path bloom filters; Git must scan full history.

Resolution (commands & steps)

Enable and write commit-graph with changed-paths (bloom filters):

git config core.commitGraph true
git config gc.writeCommitGraph true
git config commitGraph.readChangedPaths true
git commit-graph write --reachable --changed-paths
git commit-graph verify


Use split/incremental commit-graph if frequently updating:

git commit-graph write --reachable --changed-paths --split
git commit-graph verify


Combine with multi-pack & bitmap:

git multi-pack-index write --bitmap
git commit-graph write --reachable --changed-paths


Measure improvements:

time git log --all -- feature-25/file10.txt
time git log --all -- feature-10/
GIT_TRACE2_PERF=1 git log --all -- feature-25/ 2>&1 | grep -E "bloom|filter"


Prevention / best practices

Enable commit-graph maintenance in CI/server maintenance schedule.

Use bloom-filter options to accelerate path queries for large histories.

Run git maintenance tasks regularly.

Labels: commit-graph, bloom-filter, performance, maintenance

Scenario 42 — Memory consumption optimization for large repositories

Title: Git operations consume excessive memory on repository with many commits and binaries

Reproduction steps

mkdir memory-optimization && cd memory-optimization
git init

# many text commits
for i in {1..500}; do
  for j in {1..100}; do
    echo "Line $j in file $i - $(date)" >> data_$i.txt
  done
  git add data_$i.txt
  git commit -m "Commit $i"
done

# large binary files
for i in {1..50}; do
  dd if=/dev/urandom of=binary_$i.bin bs=10M count=1 2>/dev/null
  git add binary_$i.bin
  git commit -m "Binary commit $i"
done

/usr/bin/time -v git log --all --oneline 2>&1 | grep "Maximum resident"
/usr/bin/time -v git gc --aggressive 2>&1 | grep "Maximum resident"


Expected behavior
git log and git gc run within acceptable memory limits.

Actual behavior
Commands consume large amounts of RAM (OOM risk or swapping).

Root cause
Default pack/delta settings and repack operations use large memory footprints; large binary blobs and deep object graphs exacerbate memory usage.

Resolution (steps & commands)

Limit memory used by packing/delta operations:

git config pack.windowMemory 256m
git config pack.deltaCacheSize 128m
git config pack.threads 2
git config pack.packSizeLimit 1g
git config pack.window 16
git config pack.depth 16


Run a memory-efficient repack:

git repack -a -d --window=16 --depth=16 --window-memory=256m


Configure project-wide conservative defaults (example .git/config snippet):

[pack]
    windowMemory = 128m
    packSizeLimit = 500m
    deltaCacheSize = 64m
    threads = 1
    window = 10
    depth = 10
    compression = 6
[core]
    bigFileThreshold = 10m
    packedGitLimit = 128m
    packedGitWindowSize = 32m
[gc]
    auto = 128
    autoPackLimit = 5


Use shallow/filtered clones where possible:

git clone --depth=1 --single-branch --filter=blob:limit=5m . ../memory-efficient-clone


Profile memory usage:

# Example script to measure memory/time for key commands
./memory-profile.sh  # (as in reproduction)


Prevention / best practices

Use Git LFS for large binaries to keep packs small.

Enforce core.bigFileThreshold and pre-commit checks to block large files.

Tune pack settings conservatively on low-memory CI or developer machines.

Use partial/shallow clones for constrained environments.

Labels: memory, performance, pack, lfs

Scenario 43 — File system cache and I/O optimization

Title: High disk I/O on large codebase causing slow git status and git log

Reproduction steps

mkdir fs-cache-optimization && cd fs-cache-optimization
git init
# Create large tree and many commits
# (as in reproduction)
iostat -x 1 10 &    # monitor I/O
time git status
time git log --all --stat


Expected behavior
git status and path queries run with low disk I/O and complete fast.

Actual behavior
Excessive reads/writes, high iowait, slow Git commands.

Root cause
Full filesystem scans each operation (no FSMonitor, no preloaded index, default pack/index settings), causing heavy I/O.

Resolution (steps & commands)

Enable FSMonitor, untracked cache and preload index:

git config core.fsmonitor true
git config core.untrackedCache true
git config core.preloadIndex true
git update-index --untracked-cache


Start FSMonitor daemon (platform-specific):

git fsmonitor--daemon start
git fsmonitor--daemon status


Use commit-graph & multi-pack index to reduce I/O:

git config core.commitGraph true
git commit-graph write --reachable --changed-paths
git config core.multiPackIndex true
git multi-pack-index write --bitmap


Tune pack memory mapping (helps reduce random I/O):

git config core.packedGitLimit 512m
git config core.packedGitWindowSize 32m
git config pack.windowMemory 512m


Optimize for SSD vs HDD:

# SSD
git config core.checkStat minimal
git config checkout.workers $(nproc)
# HDD
git config core.preloadIndex false
git config checkout.workers 2


Use sparse-checkout to limit working set:

git sparse-checkout init --cone
git sparse-checkout set projects/project_{1..10}


Benchmark before/after with provided io-monitor.sh.

Prevention / best practices

Install and document FSMonitor (watchman) for developer machines.

Keep .gitignore precise so Git avoids scanning build artifacts.

Use sparse-checkout for large working trees.

Labels: fsmonitor, io, performance, maintenance

Scenario 44 — Object database maintenance and optimization

Title: Fragmented object database with many packs and unreachable objects — repo size & performance degrade

Reproduction steps

mkdir object-db-optimization && cd object-db-optimization
git init
# create many commits, deletes, repacks (per reproduction)
git count-objects -v
ls -lh .git/objects/pack/ | wc -l
du -sh .git/objects/


Expected behavior
Objects are packed efficiently, few pack files, small .git size, good performance.

Actual behavior
Many pack files, many unreachable objects, fragmented object DB and poor performance.

Root cause
Frequent small repacks, deletes creating dangling/unreachable objects, lack of comprehensive GC/maintenance.

Resolution (steps & commands)

Inspect current state:

git count-objects -vH
git fsck --connectivity-only
git fsck --unreachable | wc -l


Expire reflogs and prune unreachable objects:

git reflog expire --expire=30.days.ago --all
git prune --expire=2.weeks.ago --progress


Aggressive garbage collection and repack with optimal settings:

git gc --aggressive --prune=now
git repack -a -d -f -b --depth=250 --window=250


Rebuild commit-graph and multi-pack index:

git commit-graph write --reachable --changed-paths
git multi-pack-index write --bitmap


Optionally use geometric repacking:

git config repack.geometric 2
git repack -d --geometric=2


Pack refs and generate cruft packs for unreachable objects if needed:

git pack-refs --all --prune
git repack -d --cruft --cruft-expiration=1.week.ago


Verify and automate reporting with object-db-report.sh.

Prevention / best practices

Schedule incremental maintenance via git maintenance.

Avoid frequent unnecessary repacks; use server-side scheduled repack/maintenance windows.

Use LFS for large binary churn to reduce object churn.

Labels: object-db, gc, pack, maintenance

Scenario 45 — Batch operations and pipeline optimization

Title: CI clones & builds are slow; need optimized clone/fetch and reuse strategies for pipelines

Reproduction steps

mkdir batch-optimization && cd batch-optimization
git init
# create many services, commit (as reproduction)
for i in {1..5}; do
  time git clone . ../clone_$i
done


Expected behavior
CI clones are fast, incremental and use minimal bandwidth/time.

Actual behavior
Repeated full clones are slow; CI startup time large.

Resolution (strategies & commands)

Use shallow single-branch clones in CI:

git clone --depth=1 --single-branch --branch=main <repo> dest


Use partial/filtered clones when blobs are large:

git clone --filter=blob:limit=1m --depth=1 <repo> dest


Use a cached reference mirror on CI runners:

git clone --mirror batch-optimization cache.git
git clone --reference cache.git --dissociate batch-optimization dest


Reuse working directories or worktrees between jobs:

git worktree add ../worktree-1 main


Provide optimized CI clone helper (ci-clone.sh) and incremental updates:

# script as in reproduction: ci-clone.sh and incremental-update.sh


Tune CI Git settings to skip expensive checks:

git config fetch.fsckObjects false
git config advice.detachedHead false
git config core.fileMode false
git config checkout.workers $(nproc)


Benchmark methods with pipeline-benchmark.sh.

Prevention / best practices

Use a central cache/mirror for CI runners.

Prefer incremental builds (git fetch + reset) over reclone.

Document CI clone best practices and implement ci-clone.sh as standard.

Labels: ci, clone, pipeline, performance

Scenario 46 — Advanced caching and mirroring

Title: Multiple team clones are slow; provide local mirrors and reference clones for team efficiency

Reproduction steps

mkdir mirror-optimization && cd mirror-optimization
git init
# add many large binary commits
time git clone mirror-optimization clone1
time git clone mirror-optimization clone2


Expected behavior
Team clones from nearby mirror/cache are fast and efficient.

Actual behavior
Each developer performs a slow full clone from origin.

Resolution (steps & commands)

Create and maintain a team mirror:

git clone --mirror mirror-optimization team-mirror.git
cd team-mirror.git
git remote update --prune


Team clones from mirror:

git clone team-mirror.git fast-clone
# or reference clone
git clone --reference team-mirror.git --dissociate mirror-optimization reference-clone


Use alternates to share objects:

echo "../team-mirror.git/objects" > .git/objects/info/alternates
git fsck --connectivity-only


Provide bundle distribution for air-gapped or occasional consumers:

git bundle create repo.bundle --all
git clone repo.bundle bundle-clone


Automate mirror updates and bundle generation via post-update hooks and cron:

# post-update creates bundles; update-mirror.sh scheduled periodically


Consider HTTP caching or nginx caching layer for remote hosting.

Prevention / best practices

Maintain mirrors near developer teams or in the same cloud region.

Use reference repos or alternates to minimize duplicated storage.

Schedule regular mirror maintenance and repack tasks.

Labels: mirror, cache, team, distribution

Scenario 47 — Protocol & wire-format optimization

Title: ls-remote and other operations slow with many refs — enable Git protocol v2 and server-side negotiation features

Reproduction steps

mkdir protocol-optimization && cd protocol-optimization
git init
# create many branches and tags
cd ..
git clone --bare protocol-optimization protocol-remote.git
time git ls-remote protocol-remote.git | wc -l


Expected behavior
Remote operations (ls-remote, clone, fetch) run efficiently with minimal packet overhead.

Actual behavior
Protocol v1 advertises and transfers large ref lists and is slow.

Resolution (steps & commands)

Enable protocol v2 client-side:

git config --global protocol.version 2


If you control the server, enable server-side optimizations:

cd protocol-remote.git
git config uploadpack.allowRefInWant true
git config uploadpack.allowFilter true
git config uploadpack.allowAnySHA1InWant true
git pack-refs --all --prune


Use ref-in-want and partial clone:

git -c protocol.version=2 clone --filter=blob:none protocol-remote.git dest


Tune client config for better negotiation:

# ~/.gitconfig snippet
[protocol] version = 2
[http] version = HTTP/2
[fetch] negotiationAlgorithm = skipping


Benchmark protocol v1 vs v2:

time git -c protocol.version=1 ls-remote protocol-remote.git
time git -c protocol.version=2 ls-remote protocol-remote.git


Prevention / best practices

Default to protocol v2 in environments with many refs.

Keep server upload-pack optimizations aligned with client capabilities.

Use pack-refs and ref pruning to reduce advertised refs.

Labels: protocol, wire-format, network, optimization