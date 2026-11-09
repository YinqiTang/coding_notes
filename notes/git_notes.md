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

Delete a branch in my local computer
```bash
# To safely delete a local branch, run:
git branch -d <branch-name>

# To force delete a branch** (discarding any unmerged changes)
git branch -D <branch-name>
```


## Fix Issues
### Fix a divergence issue

**Issue: When `git push` is rejected or `git pull` says “divergent branches”**

**The best way is to avoid it in advance by running `git pull` before editing anything.**

If a divergence does happen, use the commands as follows.
```bash
git pull --rebase
git push
```

If the pull stops with a **conflict**:
1. Run `git status` and look for files listed as `both modified`.
2. Open each one and fix the `<<<<<<<` / `=======` / `>>>>>>>` blocks. During a rebase, the top half is GitHub’s version and the bottom half is yours.
3. Then run:

```bash
git add <file>
git rebase --continue
```

4. Repeat until you see “Successfully rebased”, then run `git push`.

**If `git push` says “not currently on a branch”:** the rebase isn’t finished yet. Run `git rebase --continue` first, then push.

**If you get lost:** `git rebase --abort` undoes everything back to before the pull, so you can start over.

### Accidentally Edit Stuff in the Main Branch
**Issue**: In a repository with a main branch protection rule, you have edited stuff in the Main branch and ran the following codes.
```bash
git add XX
git commit -m "xxx"
git push # But it fails
```

- Option A: it was only a test and you don’t need it
```bash
# Delete the commit and put your local `main` back in sync with GitHub
# All the new changes will be removed
git reset --hard origin/main
```

- Option B: You want to keep the change
```bash
git switch -c test-protection     # Make a new branch that includes your commit
git push -u origin test-protection
git switch main
git reset --hard origin/main      # clean up local main
```


## Current project-specific codes
Before opening a pull request, run the following codes
```bash
# Check codes using ruff
uv run ruff check .

# Reformat using ruff
uv run ruff format .
```