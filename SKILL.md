---
name: cohub-cli
description: Cohub CLI for spaces, sessions, files, checkpoints, and prompts. Triggers: cohub, space, session.
---

# Cohub CLI Skill

## Overview

Cohub is a cloud platform by Neta Art for space-based agent creation, execution, and collaboration. The `@neta-art/cohub-cli` is the official CLI client for working with Spaces, Chats (Sessions), files, Saves (Checkpoints), Tasks, scheduled prompts, search, and multimodal generation from the terminal.

**Origin:** Neta Art (捏Ta) — https://www.neta.art  
**Package:** `@neta-art/cohub-cli` on npm  
**Auth:** `cohub auth login` (Logto-based, auto-refreshing)  
**Env overrides:** `ENV=dev` for dev environment, `COHUB_EXECUTION_TOKEN` for CI/sandbox

## Core Concepts

| Concept | Description |
|---|---|
| **Space** | Live isolated working environment; the primary creative surface |
| **Session** | LLM conversation context inside a space (UI calls it "Chat") |
| **Checkpoint** | Immutable snapshot of a space state (UI calls it "Save") |
| **Task run** | Record of an executed background task |
| **Cron job** | Recurring scheduled prompt |

## Command Patterns

### Global flags
```bash
-s, --space <id>     # Target space ID
--json               # Machine-readable output
```

### Prompts (the primary interaction)
```bash
# Immediate send
cohub -s <space-id> prompt "message" --json

# Existing session
cohub -s <space-id> prompt --session <session-id> "message" --json

# New session with title
cohub -s <space-id> prompt --title "Chat title" "message" --json

# Pipe long content
cat prompt.md | cohub -s <space-id> prompt --json

# Schedule once
cohub -s <space-id> prompt --at "2026-05-22T09:00:00+08:00" "message" --json

# Recurring
cohub -s <space-id> prompt \
  --cron "0 9 * * 1-5" \
  --timezone "Asia/Shanghai" \
  --title "Daily reminder" \
  "message" \
  --json
```
**Scheduling constraint:** use only one of `--delay-ms`, `--at`, or `--cron`. `--cron` requires `--timezone`.

### Sessions
```bash
cohub -s <space-id> spaces sessions ls --json
cohub -s <space-id> spaces sessions create "title" --json
cohub -s <space-id> spaces sessions get <session-id> --json
cohub -s <space-id> spaces sessions turns ls <session-id>  # list messages
```

### Files
```bash
cohub -s <space-id> spaces files ls [path] --json
cohub -s <space-id> spaces files cat <path>
cohub -s <space-id> spaces files write <path> -c "content"
cohub -s <space-id> spaces files upload <files...> --dir <dir>
cohub -s <space-id> spaces files mv <from> <to>
cohub -s <space-id> spaces files rm <path>
```

### Checkpoints
```bash
cohub -s <space-id> spaces checkpoints ls --json
cohub -s <space-id> spaces checkpoints get <id> --json
cohub -s <space-id> spaces checkpoints create "description" --json
```

### Search
```bash
cohub search "query" --limit 20 --json
```

### Models & Generation
```bash
cohub models ls --json
cohub models ls --model-type multimodal --json
cohub generate "prompt" --model <model> --output file.png --json
cohub generate "restyle" --model <model> --image ./input.png --param size=1024x1024 --json
```

### Cron Jobs (recurring scheduled prompts)
```bash
cohub cron-jobs ls <space-id> --json
cohub cron-jobs runs <cron-job-id> --json
cohub cron-jobs toggle <cron-job-id> on|off
cohub cron-jobs delete <cron-job-id>
```

### Tasks
```bash
cohub tasks ls --space <space-id> --json
cohub tasks get <task-run-id> --json
```

### Spaces management
```bash
cohub spaces ls --json
cohub spaces get <space-id> --json
cohub spaces create --name "name" --description "desc" --json
cohub spaces rename <space-id> "new name"
```

## Available Models (as of 2026-05)

`gpt-5.5`, `ultimate`, `performance`, `qwen3.7-max`, `qwen3.6-plus`, `deepseek-v4-pro`, `kimi-k2.5`, `glm-5.1`, `gpt-5.4-mini`, `doubao-2.0-lite`, `doubao-2.0-pro`, `qwen3.6-flash`, `fm/gpt-5.5`, `bailian/glm-5`

## Safety Rules

Confirm before:
- Deleting files or directories
- Creating scheduled or recurring prompts with side effects
- Enabling, disabling, or deleting recurring scheduled prompts
- Changing access policies, member roles, or membership

## Efficient Usage Guidelines

1. **Use `--json` for scripting** — table output is human-friendly; JSON is for extraction and chaining.
2. **Discover via help** — `cohub -h`, `cohub spaces -h`, `cohub spaces prompt -h`. The CLI's built-in help is comprehensive and well-designed.
3. **Space ID once** — set `-s <id>` early; most subsequent commands inherit it.
4. **Session continuity** — reuse `--session <id>` to continue a conversation rather than creating new sessions.
5. **Checkpoints for milestones** — save checkpoints before risky operations or at meaningful milestones.
6. **Piping** — use `cat file.md | cohub -s <id> prompt` for large prompts instead of inline strings.
