# AI Tooling Notes

Notes on working with Claude Code and running an AI-maintained wiki in Obsidian.

_Merged from `claude_notes.txt` and `LLM_notes.txt`._

## Table of Contents

- [[#Claude Code CLI]]
- [[#LLM Wiki Workflow (Obsidian)]]

---

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
