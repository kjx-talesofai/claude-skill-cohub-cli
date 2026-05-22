# Cohub CLI — A Claude Code Skill

> **Cohub** is a cloud platform for space-based agent creation, execution, and collaboration. This skill is a reference for efficiently using the `@neta-art/cohub-cli` from your terminal.

## What Is Cohub?

Cohub is a cloud platform built by [Neta Art](https://www.neta.art) that combines:

- **JupyterLab / Colab-like** in-browser creation and debugging
- **Heroku / Fly.io-like** local-to-cloud deployment
- **GitHub / Hugging Face-like** sharing, reuse, and community collaboration

At its core, a **Space** is a live, isolated working environment where users and agents create together — containing conversations, file changes, experiments, and drafts. **Checkpoints** are immutable snapshots saved from a space for sharing, rollback, and reuse.

## What Is the CLI?

`@neta-art/cohub-cli` is the official command-line client for Cohub. It lets you:

- Send prompts to spaces and sessions
- Manage files in space workspaces
- List, create, and inspect sessions (chats)
- Save checkpoints
- Search across spaces and turns
- Generate images and other multimodal outputs
- Schedule one-time or recurring prompts (cron jobs)

All from your terminal, scriptable with `--json`.

---

## Install

```bash
npm install -g @neta-art/cohub-cli
cohub auth login
cohub auth whoami
```

The CLI keeps you signed in and refreshes your session automatically.

---

## Quick Reference

### Top-level options
```
-s, --space <id>    Target space ID for scoped commands
--json              Machine-readable JSON output
```

### Core workflows

```bash
# Send a prompt to a space
cohub -s <space-id> prompt "message"

# Send to an existing session
cohub -s <space-id> prompt --session <session-id> "message"

# Create a new chat and send
cohub -s <space-id> prompt --title "Chat title" "message"

# Schedule once
cohub -s <space-id> prompt --at "2026-05-22T09:00:00+08:00" "message"

# Schedule recurring
cohub -s <space-id> prompt --cron "0 9 * * 1-5" --timezone "Asia/Shanghai" --title "Daily" "message"

# List sessions (chats) in a space
cohub -s <space-id> spaces sessions ls --json

# List files
cohub -s <space-id> spaces files ls --json

# Read a file
cohub -s <space-id> spaces files cat <path>

# Write a file
cohub -s <space-id> spaces files write <path> -c "content"

# Upload files
cohub -s <space-id> spaces files upload <files...> --dir <dir>

# Save a checkpoint
cohub -s <space-id> spaces checkpoints create "description" --json

# List checkpoints
cohub -s <space-id> spaces checkpoints ls --json

# Search across spaces and turns
cohub search "query" --limit 20 --json

# List available models
cohub models ls --json

# Generate an image
cohub generate "prompt" --model <model> --output file.png --json

# List cron jobs
cohub cron-jobs ls <space-id> --json

# Toggle a cron job
cohub cron-jobs toggle <cron-job-id> on|off

# List task runs
cohub tasks ls --space <space-id> --json
```

### Pipe long content
```bash
cat prompt.md | cohub -s <space-id> prompt --json
```

---

## Terminology (UI vs CLI)

| Product UI | CLI / API |
|---|---|
| Chat | Session |
| Save | Checkpoint |
| Tasks | Task runs |
| Scheduled prompt | `spaces prompt` schedule |
| Recurring scheduled prompt | Cron job |

---

## Best Practices

- **Always use `--json` when chaining commands or extracting IDs** — the table output is human-friendly but brittle for scripts.
- **Use `-s <space-id>` as the global scope switch** — it prefixes nearly all space-scoped commands.
- **Confirm before destructive actions** — the CLI prompts before deleting files, toggling cron jobs, or creating side-effect prompts.
- **Scheduling rules** — only one of `--delay-ms`, `--at`, or `--cron`. `--cron` requires `--timezone`.
- **Environment override** — `ENV=dev cohub ...` targets the dev environment. `COHUB_EXECUTION_TOKEN` is an ephemeral auth override for CI / sandbox.

---

## Credits

<div align="center">

<a href="https://www.neta.art"><img src="https://www.neta.art/favicon.ico" width="56" height="56" /></a>

<br/>

**[Neta Art 捏Ta](https://www.neta.art)** &nbsp;·&nbsp; [GitHub @kjx-talesofai](https://github.com/kjx-talesofai)

</div>

---

## License

MIT — see [LICENSE](./LICENSE)

© 2026 Neta Art
