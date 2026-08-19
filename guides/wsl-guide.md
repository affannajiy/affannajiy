# WSL Setup Guide

> Getting Windows Subsystem for Linux + Ubuntu running from scratch. Generic — no project
> specifics. Applies to Windows 10 (Build 21362+) and Windows 11.

---

## Prerequisites

| Requirement | Notes |
| --- | --- |
| Windows 10 Build 21362+ or Windows 11 | Required for WSLg (running Linux GUI apps from WSL) |
| WSL2 | `wsl --install` sets this up; `wsl --status` shows the current version |
| Virtualisation enabled in BIOS | Usually on by default; only an issue on older machines |

---

## 1. Install WSL

Skip if WSL is already installed — check with `wsl --status`.

1. Open **PowerShell as Administrator**.
2. Run:

```powershell
wsl --install
```

3. Restart when prompted.

`wsl --install` with no arguments installs WSL2 and Ubuntu together on current Windows builds.
The next section covers installing a distribution explicitly.

---

## 2. Install Ubuntu

1. Open **PowerShell as Administrator**.
2. Run:

```powershell
wsl --install Ubuntu
```

3. Restart once installation completes.
4. On first launch you are prompted for a **Unix username and password**. Pick something
   memorable — it does not need to match your Windows credentials. The password is what
   `sudo` will ask for.

List what is available or already installed:

```powershell
wsl --list --online
wsl --list --verbose
```

---

## 3. Open the Ubuntu terminal

**Option A — Windows Terminal UI**

1. Open Windows Terminal.
2. Click the down arrow (⌄) next to the tab bar.
3. Select **Ubuntu**.

**Option B — command**

```powershell
wsl -d Ubuntu
```

Set Ubuntu as the default distro so a bare `wsl` opens it:

```powershell
wsl --set-default Ubuntu
```

---

## 4. First-run housekeeping

Inside the Ubuntu terminal:

```bash
sudo apt-get update
sudo apt-get upgrade
```

Install Python and build basics if you need them:

```bash
sudo apt-get install python3 python3-pip
```

---

## 5. Filesystem mapping

Windows drives are mounted under `/mnt/`:

| Windows path | WSL path |
| --- | --- |
| `C:\` | `/mnt/c/` |
| `C:\Projects\app` | `/mnt/c/Projects/app` |
| `D:\` | `/mnt/d/` |

Your Linux home is `~` (`/home/<username>`), reachable from Windows Explorer at
`\wsl$\Ubuntu\home\<username>`.

**Performance note:** files under `/mnt/c/` cross the Windows↔Linux filesystem boundary and
are noticeably slower for heavy I/O (git operations, `node_modules`, builds). Keep
performance-sensitive project files in the Linux home directory instead. Use `/mnt/c/` when
Windows tools also need to read the same files.

---

## 6. Bash aliases

Aliases turn long commands into short names. Store them in `~/.bash_aliases` so they survive
across sessions.

**Step 1 — open the file**

```bash
vi ~/.bash_aliases
```

**Step 2 — enter Insert mode**

Press `i`. You will see `-- INSERT --` at the bottom of the terminal.

**Step 3 — add definitions**

```bash
alias myDir="cd /mnt/c/YourFolder"
alias ll="ls -alh"
alias up="sudo apt-get update && sudo apt-get upgrade"
```

**Step 4 — save and exit**

Press `Esc`, then type `:wq` and press Enter.

| vi command | Action |
| --- | --- |
| `i` | Enter Insert mode |
| `Esc` | Exit Insert mode |
| `:w` | Save |
| `:wq` | Save and quit |
| `:q` | Quit without saving |
| `:q!` | Force quit, discard changes |

**Step 5 — apply to the current session**

```bash
source ~/.bash_aliases
```

Only needed once per terminal session — new sessions load `~/.bash_aliases` automatically
(Ubuntu's default `~/.bashrc` sources it).

---

## 7. Useful WSL commands

| Command | What it does |
| --- | --- |
| `wsl --status` | Current WSL version and default distro |
| `wsl --list --verbose` | Installed distros, their state and WSL version |
| `wsl --update` | Update the WSL kernel (fixes most WSLg issues) |
| `wsl --shutdown` | Stop all distros — the standard "turn it off and on again" |
| `wsl --set-default <distro>` | Set the default distro |
| `wsl --unregister <distro>` | **Deletes the distro and all its data.** No undo. |
| `explorer.exe .` | Open the current WSL directory in Windows Explorer |
| `code .` | Open the current directory in VS Code (with the WSL extension) |

> ⚠️ `wsl --unregister` permanently destroys that distribution's entire filesystem — every
> file in the Linux home directory is gone, with no recycle bin and no recovery. Back up
> anything you need out to `/mnt/c/` first.

---

## 8. GUI apps (WSLg)

Windows 11 and Windows 10 Build 21362+ run Linux GUI apps out of the box via WSLg — no X
server needed. Check it is active from inside WSL:

```bash
echo $DISPLAY
echo $WAYLAND_DISPLAY
```

If both are empty, WSLg is not running. Fix:

```powershell
wsl --update
wsl --shutdown
```

On older Windows 10 builds without WSLg, you need a third-party X server (VcXsrv or
MobaXterm) running on the Windows side.

---

## 9. Troubleshooting

| Symptom | Resolution |
| --- | --- |
| `wsl.exe` not found | WSL is not installed. Run `wsl --install` in PowerShell as Admin, restart. If blocked, check with IT — some corporate policies disable it. |
| `wsl --install` fails, or version shows WSL 1 | Enable the **Virtual Machine Platform** Windows feature, then `wsl --update` |
| WSL does not respond / hangs | `wsl --shutdown`, wait ~10 seconds, then start it again |
| WSL exits with an unexpected code | `wsl --status`, then `wsl --shutdown`. If it persists: `wsl --update` and restart Windows. |
| Blank window / GUI app shows nothing | `wsl --update` then `wsl --shutdown`. Windows 10 without WSLg: start VcXsrv or MobaXterm. |
| `python3: command not found` | `sudo apt-get install python3` |
| `pip3` refuses to install ("externally-managed-environment") | Use `sudo apt-get install python3-<package>`, or a venv. `--break-system-packages` works but bypasses a safety check. |
| Aliases disappear after closing the terminal | They were defined inline, not in `~/.bash_aliases`. Edit the file, then `source ~/.bash_aliases`. |
| Very slow file operations | The files are under `/mnt/c/`. Move them into the Linux home directory. |
| `sudo` rejects the password | It wants the **Unix** password set at first launch, not the Windows one. |
| No internet inside WSL | `wsl --shutdown` and restart. Corporate VPNs commonly break WSL networking — check the proxy settings. |
