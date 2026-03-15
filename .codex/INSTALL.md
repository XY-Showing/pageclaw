# Installing page-claw in Codex

## Prerequisites

- Git

## Installation

### 1. Clone the repository

```bash
git clone https://github.com/XY-Showing/pageclaw ~/skills/pageclaw
```

### 2. Create a symlink

**Linux / macOS:**
```bash
mkdir -p ~/.codex/skills
ln -s ~/skills/pageclaw/skills/page-claw ~/.codex/skills/page-claw
```

**Windows (PowerShell, run as Administrator):**
```powershell
New-Item -ItemType Directory -Force -Path "$env:USERPROFILE\.codex\skills"
New-Item -ItemType SymbolicLink `
  -Path "$env:USERPROFILE\.codex\skills\page-claw" `
  -Target "$env:USERPROFILE\skills\pageclaw\skills\page-claw"
```

### 3. Verify

```bash
ls ~/.codex/skills/
```

You should see `page-claw` listed.

Restart Codex. Invoke with `/page-claw` in any session.

---

## Updating

```bash
cd ~/skills/pageclaw && git pull
```

Changes take effect immediately — no restart needed.

## Uninstalling

```bash
rm ~/.codex/skills/page-claw
# Optionally remove the repo:
rm -rf ~/skills/pageclaw
```
