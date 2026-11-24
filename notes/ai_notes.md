# AI Tooling Notes

Notes on working with Claude Code and running an AI-maintained wiki in Obsidian.

_Merged from `claude_notes.txt` and `LLM_notes.txt`._


```table-of-contents
```

---

## Claude Code
### How to control Claude Code's access to certain data and scripts

Modify `.claude/settings.local.json` in the project folder: 
- Add deny rules in the `permissions`
- Enable `sandbox`. It seems `sandbox` only works in Mac and Linux (need to check).

But `.claude/settings.local.json` in the project folder is in the list of .gitignore now. If we want to ensure all team members use the same setting, we should do:
- **Move to `.claude/settings.json`:** the `deny` rules, `disableBypassPermissionsMode` and the `sandbox` block. These are team-wide safety rules.
- **Keep in `settings.local.json`:** anything personal, like your `Bash(python3 -c ' *)` allow rule.

#### Example:
Deny rules:
https://github.com/harrisonzhu508/d3a_agentic_demo/blob/main/demo/.claude/settings.json

```
  "permissions": {
    "deny": [
      "Read(../dataset/**)",
      "Read(**/dataset_script.ipynb)",
      "Read(./checks/split.py)",
      "Edit(./config/**)",
      "Bash(git push --force *)",
      "Bash(git push -f *)",
      "Bash(git reset --hard *)",
      "Bash(rm -rf *)"
    ]
  },
```


## Claude Code CLI

Useful commands to know once you're up and running, in the terminal:

```bash
claude              # launch Claude Code
claude --doctor     # run diagnostics
claude --continue   # continue a previous session
```

Inside a Claude Code session:

```
/help   # view all available commands
/exit   # leave Claude Code
```

## LLM Wiki Workflow (Obsidian)

Reference video: [Helpful walkthrough](https://www.youtube.com/watch?v=iXd0t60YmMw)

### Setup

- Use **Obsidian** as the IDE — download and install it locally.
- Have an AI tool available: Claude Code, Codex, etc.
- Keep a folder on your computer for raw resources.
- Suggested folder structure: `raw/`, `wiki/`, `templates/` (optional).
- You can download a `CLAUDE.md` template online to seed the setup.

### Limitation

AI can make mistakes. This workflow is designed for up to ~100 raw files.

### Usage

Typical flow when using the AI in the terminal:

1. Add some new raw files.
2. Ask the AI to update the wiki, e.g.:
   > I have added some new raw files, update the wiki.
3. Periodically ask for a health check:
   > Please lint wiki
