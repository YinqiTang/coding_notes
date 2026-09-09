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
