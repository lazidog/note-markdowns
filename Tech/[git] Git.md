# Git

## Fundamental

- **Definition**
  - **Repository**: contain project files
  - **Branch**: A copy of the project at the time it’s branched, all the changes will not affect to the main branch
  - **Staging:** Hold changes you want to commit next, after use `git add`
  - **Stash**: A cache that holds unwanted changes you want to comeback later
  - **HEAD:** Reference name for the latest commit
- **Command**
  - Configuration: `git config <user.name/user.email> [new username/email]`
  - Clone a repo: `git clone <remote_repo_url>`
  - Stash: `git stash [-u: include untracked file] <command>`
    pop
    apply
    drop
    list
  - Reset: `git reset <commitid>` reset current branch to specific commit, keep changes by default (—soft)
    `--hard` discard all changes
  - Branch: `git checkout -b <branch name>` create new branch and switch to
    -d: safe delete (prevent delete unmerged)
    -D: force delete
    -m: rename
  - Log: `git log <branch>` log commits
    `--after=”YYYY-MM-DD”`
    `--before=”YYYY-MM-DD”`
  - Fetch: `git fetch` fetch all commits and branches from remote without apply to local
  - Pull: `git pull` fetch and merge to local repo
  - Merge: `git merge` merge branches
    `--squash <branch>` merge branch without commits

## Manage project with github

- **Create project board**
  - `Project tab` => `Create a project`
  - Select template `Feature`
- **Create `Issues` and `Milestones`**
  - Create `Milestones` to organize issues and pull requests, or manage the version
  - Choose milestone just created and create issues (`Task`, `Feature`, `Bug`)
    - `Feature`: list all task needed for feature and click small button `convert issue` on each row after submit feature to create new task and link it to feature
    - `Task`: describe task: mongodb, graphql, rgpc, ...
    - `Bug`: describe bug: steps, expected result, actual result
  - Group feature using `interation` (or can change its name to `feature`)
- Checkout new branch `feature/task-title` for each task
- Merge to branch `dev` with name of feature
- Merge to branch `staging` with name version

**`References`**

[How i manage my personal project with github workflow](https://dev.to/jorenrui/a-look-into-how-i-manage-my-personal-projects-my-git-github-workflow-1e7h)

[Git for Small Projects and Class Work](https://www.grotto-networking.com/personalGit.html)
