# Claude Guide

> Personal reference for using Claude effectively across different surfaces.

---

## General

### 1. Set Memory in Settings
Go to Settings → set your preferences so Claude always has context about you without repeating yourself every session.

### 2. Use Models Accordingly
| Model | Best For |
|---|---|
| **Haiku** | Simple tasks, quick lookups, short generations |
| **Sonnet** | Balanced work, everyday coding, writing |
| **Opus** | Complex reasoning, deep analysis, hard problems |

Don't burn Opus on stuff Haiku can handle.

### 3. Separate Sessions Throughout the Day
Keep sessions time-boxed to avoid context bloat and stay focused:

| Session | Time |
|---|---|
| Morning | 7AM – 10AM |
| Afternoon | 12PM – 3PM |
| Evening | 7PM – 10PM |

---

## Claude Browser (claude.ai)

### 1. Edit the Prompt If Results Are Off
Don't just retry — actually edit your original prompt. Small tweaks go a long way.

### 2. Restart After 15–20 Messages
Long chats degrade response quality. After ~15–20 messages:
1. Summarize the session
2. Open a new chat
3. Paste the summary as context

### 3. Batch Your Tasks Into One Context Load
Instead of sending 3 separate prompts, combine them:
```
Task 1: ...
Task 2: ...
Task 3: ...
```
Fewer round trips, better coherence.

### 4. Upload Files Into the Project
Use **Projects** to store reference files once — no need to re-upload every session. Claude will always have access to them.

---

## Claude Code

### 1. Installation

Run this in CMD:
```cmd
curl -fsSL https://claude.ai/install.cmd -o install.cmd && install.cmd && del install.cmd
```

After install, add these to your **Environment Variables → Path**:
```
Disk:\Users\User\.local\bin
Disk:\Users\User\ProgramData\Git\bin\bash.exe
```
> Replace `Disk` and `User` with your actual drive letter and username.

---

### 2. Check Token Usage
```
/context
```
Shows how much of the context window is currently used.

---

### 3. Initialize the Project File
```
init
```
This creates a `CLAUDE.md` file — your project's instruction file that Claude reads at the start of every session.

> **Keep `CLAUDE.md` under 200 lines.** Trim the fat, keep only what's essential.

---

### 4. Create an Agent
```
/agents
```
Navigate using the arrow button → go to **Libraries** to browse or set up agents.

---

### 5. Claude Permissions

Continue from a **previous conversation** (skip permission prompts):
```bash
claude -r --dangerously-skip-permissions
```

Start a **new chat** (skip permission prompts):
```bash
claude --dangerously-skip-permissions
```

---

### 6. Restart a Conversation (Handoff)

When you want to clear the session without losing progress:

1. Ask Claude to summarize:
   ```
   summarise session for handoff
   ```
2. Copy the summary output
3. Clear the session:
   ```
   /clear
   ```
4. Paste the summary and let Claude pick up from there

---

### 7. Statusline
```
/statusline
```
Shows current session status info.

---

## Quick Reference Card

| Action | Command |
|---|---|
| Check token usage | `/context` |
| Initialize project | `init` |
| Create agent | `/agents` |
| Skip permissions (new) | `claude --dangerously-skip-permissions` |
| Skip permissions (resume) | `claude -r --dangerously-skip-permissions` |
| Handoff summary | `summarise session for handoff` |
| Clear session | `/clear` |
| Statusline | `/statusline` |

---

*Last updated: April 2026*
