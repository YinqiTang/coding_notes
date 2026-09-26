# Git Notes

Everyday Git commands and branch workflows.

## Basic Workflow

```bash
git status
git add .
git commit -m "blabla"
git push
```

## Switching Branches

```bash
git checkout main
```

## Making a New Branch

```bash
git checkout -b new_branch_name
```

## Merging Another Branch Into Your Current One

Merge new things from another branch (e.g. `main`) into your current working branch:

```bash
git checkout main
git pull
git checkout my_working_branch
git merge main
```

If this gets you stuck in Vim (e.g. for a merge commit message):

1. Press `ESC`
2. Write and quit: `:wq`

## Change a branch name

If I have not pushed the old branch to the remote, it only exists on my computer
```bash
# Rename the branch
git branch -m <old-branch-name> <new-branch-name>

# Ready to push this newly renamed branch to your remote repository for the first time
git push -u origin <new-branch-name>
```

If I have already pushed the old branch to the remote, it exists on both my computer and the remote.
```bash
# Rename the branch
git branch -m <old-branch-name> <new-branch-name>

# Delete the old branch from the remote
git push origin --delete <old-branch-name>

# Push the new branch and reset the upstream branch
git push origin -u <new-branch-name>
```

## Cleanup
### Clean up branches in the local computer
```bash
git fetch --prune
```

This fetches the latest updates and deletes any local tracking branches that no longer exist on the remote server GitHub.

## Current project-specific codes
Before opening a pull request, run the following codes
```bash
# Check codes using ruff
uv run ruff check .

# Reformat using ruff
uv run ruff format .
```