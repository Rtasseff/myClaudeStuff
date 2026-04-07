# Claude Code Permissions: Practical Setup Guide

## How It Works

Claude Code reads permissions from `~/.claude/settings.json` (global) and `.claude/settings.json` (per-project). Rules are evaluated: **deny → ask → allow**. Deny always wins.

**Key file locations:**
- `~/.claude/settings.json` — your global defaults (all projects)
- `.claude/settings.json` — shared project settings (commit to git)
- `.claude/settings.local.json` — personal project overrides (gitignored)

**Important quirks to know about:**
- Piped commands (`ls | wc -l`) may still prompt even if both individual commands are allowed — this is a [known issue](https://github.com/anthropics/claude-code/issues/13340)
- The `Bash(command:*)` syntax uses `:` as the separator between command and arguments
- `Bash(*)` allows ALL bash commands — be very specific instead
- Clicking "accept, do not ask again" in the UI can overwrite your manually configured wildcard permissions — [known bug](https://github.com/anthropics/claude-code/issues/9814), so prefer editing the JSON directly
- You can also press **Shift+Tab** during a session to cycle through permission modes (`default` → `acceptEdits` → `plan`)

---

## Community Resources

| Resource | What it is |
|----------|-----------|
| [dwillitzer/claude-settings](https://github.com/dwillitzer/claude-settings) | 900+ pattern allow list with comprehensive deny list. The "kitchen sink" approach. |
| [centminmod/my-claude-code-setup](https://github.com/centminmod/my-claude-code-setup) | Starter template with CLAUDE.md memory bank system |
| [feiskyer/claude-code-settings](https://github.com/feiskyer/claude-code-settings) | Plugin-based settings with commands, agents, and skills |
| [Korny's permissions hook](https://blog.korny.info/2025/10/10/better-claude-code-permissions) | Rust-based PreToolUse hook for regex-based permission control (solves the piped commands problem) |
| [Official docs](https://code.claude.com/docs/en/permissions) | Anthropic's permission reference with starter configs |
| [Pete Freitag's deep dive](https://www.petefreitag.com/blog/claude-code-permissions/) | Security analysis including gotchas around deny rules |

---

## Tier 1: Moderate — Stop the Noise (Recommended Start)

Auto-approves reads, common dev tools, and safe bash commands. Still asks for file edits and anything potentially destructive. This is where most people should start.

**`~/.claude/settings.json`:**

```json
{
  "permissions": {
    "deny": [
      "Bash(rm -rf /)",
      "Bash(rm -rf ~)",
      "Bash(rm -rf .)",
      "Bash(rm -rf *)",
      "Bash(sudo rm:*)",
      "Bash(chmod 777:*)",
      "Bash(> /dev/sda*)",
      "Bash(mkfs:*)",
      "Bash(dd if=:*)",
      "Bash(shutdown:*)",
      "Bash(reboot:*)",
      "Bash(passwd:*)",
      "Bash(curl * | bash*)",
      "Bash(curl * | sh*)",
      "Bash(wget * | bash*)",
      "Read(.env)",
      "Read(.env.*)"
    ],
    "allow": [
      "Read",
      "Glob",
      "Grep",
      "WebFetch",
      "WebSearch",
      "TodoRead",
      "TodoWrite",
      "Task",
      "Bash(git:*)",
      "Bash(ls:*)",
      "Bash(cat:*)",
      "Bash(head:*)",
      "Bash(tail:*)",
      "Bash(wc:*)",
      "Bash(pwd:*)",
      "Bash(echo:*)",
      "Bash(find:*)",
      "Bash(grep:*)",
      "Bash(rg:*)",
      "Bash(which:*)",
      "Bash(whoami:*)",
      "Bash(date:*)",
      "Bash(tree:*)",
      "Bash(diff:*)",
      "Bash(file:*)",
      "Bash(stat:*)",
      "Bash(du:*)",
      "Bash(df:*)",
      "Bash(sort:*)",
      "Bash(uniq:*)",
      "Bash(cut:*)",
      "Bash(awk:*)",
      "Bash(sed:*)",
      "Bash(tr:*)",
      "Bash(tee:*)",
      "Bash(xargs:*)",
      "Bash(dirname:*)",
      "Bash(basename:*)",
      "Bash(realpath:*)",
      "Bash(jq:*)",
      "Bash(mkdir:*)",
      "Bash(cp:*)",
      "Bash(mv:*)",
      "Bash(touch:*)",
      "Bash(python:*)",
      "Bash(python3:*)",
      "Bash(pip:*)",
      "Bash(pip3:*)",
      "Bash(conda:*)",
      "Bash(pytest:*)",
      "Bash(node:*)",
      "Bash(npm:*)",
      "Bash(npx:*)"
    ]
  }
}
```

**What this does:** You can read anything, search freely, and run safe shell commands without prompts. Claude still asks before editing files, running `rm`, or doing anything with network/deployment tools.

---

## Tier 2: Permissive — Trust the Agent with Edits

Everything from Tier 1, plus auto-approves file edits and a broader set of dev tools. Good for when you're in a project with git and can easily revert.

**`~/.claude/settings.json`:**

```json
{
  "permissions": {
    "deny": [
      "Bash(rm -rf /)",
      "Bash(rm -rf ~)",
      "Bash(rm -rf .)",
      "Bash(rm -rf *)",
      "Bash(sudo rm:*)",
      "Bash(chmod 777:*)",
      "Bash(> /dev/sda*)",
      "Bash(mkfs:*)",
      "Bash(dd if=:*)",
      "Bash(shutdown:*)",
      "Bash(reboot:*)",
      "Bash(passwd:*)",
      "Bash(curl * | bash*)",
      "Bash(curl * | sh*)",
      "Bash(wget * | bash*)",
      "Read(.env)",
      "Read(.env.*)",
      "Read(**/secrets/**)"
    ],
    "allow": [
      "Read",
      "Edit",
      "MultiEdit",
      "Write",
      "Glob",
      "Grep",
      "WebFetch",
      "WebSearch",
      "TodoRead",
      "TodoWrite",
      "Task",
      "Bash(git:*)",
      "Bash(gh:*)",
      "Bash(ls:*)",
      "Bash(cat:*)",
      "Bash(head:*)",
      "Bash(tail:*)",
      "Bash(wc:*)",
      "Bash(pwd:*)",
      "Bash(echo:*)",
      "Bash(find:*)",
      "Bash(grep:*)",
      "Bash(rg:*)",
      "Bash(which:*)",
      "Bash(whoami:*)",
      "Bash(date:*)",
      "Bash(tree:*)",
      "Bash(diff:*)",
      "Bash(file:*)",
      "Bash(stat:*)",
      "Bash(du:*)",
      "Bash(df:*)",
      "Bash(sort:*)",
      "Bash(uniq:*)",
      "Bash(cut:*)",
      "Bash(awk:*)",
      "Bash(sed:*)",
      "Bash(tr:*)",
      "Bash(tee:*)",
      "Bash(xargs:*)",
      "Bash(dirname:*)",
      "Bash(basename:*)",
      "Bash(realpath:*)",
      "Bash(jq:*)",
      "Bash(mkdir:*)",
      "Bash(cp:*)",
      "Bash(mv:*)",
      "Bash(touch:*)",
      "Bash(rm:*)",
      "Bash(python:*)",
      "Bash(python3:*)",
      "Bash(pip:*)",
      "Bash(pip3:*)",
      "Bash(conda:*)",
      "Bash(pytest:*)",
      "Bash(node:*)",
      "Bash(npm:*)",
      "Bash(npx:*)",
      "Bash(django-admin:*)",
      "Bash(manage.py:*)",
      "Bash(./manage.py:*)",
      "Bash(docker:*)",
      "Bash(docker-compose:*)",
      "Bash(make:*)",
      "Bash(tar:*)",
      "Bash(zip:*)",
      "Bash(unzip:*)",
      "Bash(gzip:*)",
      "Bash(curl:*)",
      "Bash(wget:*)",
      "Bash(ssh:*)",
      "Bash(scp:*)",
      "Bash(rsync:*)",
      "Bash(chmod:*)",
      "Bash(chown:*)"
    ]
  }
}
```

**What this does:** Claude can read, edit, and create files freely. It can run most dev commands including Docker, Django management, curl, etc. The deny list still blocks catastrophic commands and secrets access.

---

## Tier 3: Sandbox Mode — Let it Rip (Safely)

If you want maximum autonomy, enable sandboxing and let the OS enforce boundaries instead of permission prompts:

```json
{
  "permissions": {
    "deny": [
      "Read(.env)",
      "Read(.env.*)",
      "Read(**/secrets/**)"
    ],
    "allow": [
      "Read",
      "Edit",
      "MultiEdit",
      "Write",
      "Glob",
      "Grep",
      "WebFetch",
      "WebSearch",
      "TodoRead",
      "TodoWrite",
      "Task"
    ]
  },
  "sandbox": {
    "enabled": true,
    "autoAllowBashIfSandboxed": true,
    "network": {
      "allowedDomains": [
        "github.com",
        "*.github.com",
        "pypi.org",
        "files.pythonhosted.org",
        "*.npmjs.org",
        "registry.yarnpkg.com",
        "stackoverflow.com",
        "docs.python.org",
        "pytorch.org"
      ]
    }
  }
}
```

**What this does:** The sandbox restricts filesystem and network access at the OS level. With `autoAllowBashIfSandboxed: true`, Claude runs any bash command without prompting because the sandbox prevents it from reaching outside defined boundaries. This is defense-in-depth — you don't need permission prompts because the OS itself blocks bad actions.

---

## Quick Start: Which Tier to Pick?

- **Tier 1** if you want to stop the constant read/search prompts but still approve all writes. Safest stepping stone.
- **Tier 2** if you work in git repos and can easily `git diff` / `git checkout` to revert. This is where most experienced users land.
- **Tier 3** if your OS supports sandboxing (macOS with Seatbelt, Linux with kernel support) and you want near-YOLO without the actual danger.

## Installation

```bash
# Create the directory
mkdir -p ~/.claude

# Copy whichever tier you chose into your global settings
# (or paste it into ~/.claude/settings.json with your editor)
nano ~/.claude/settings.json
```

For per-project overrides, put a modified version in `.claude/settings.json` within your project directory.
