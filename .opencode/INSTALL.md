# Installing page-claw in OpenCode

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
mkdir -p ~/.opencode/skills
ln -s ~/skills/pageclaw/skills/page-claw ~/.opencode/skills/page-claw
```

**Windows (PowerShell, run as Administrator):**
```powershell
New-Item -ItemType Directory -Force -Path "$env:USERPROFILE\.opencode\skills"
New-Item -ItemType SymbolicLink `
  -Path "$env:USERPROFILE\.opencode\skills\page-claw" `
  -Target "$env:USERPROFILE\skills\pageclaw\skills\page-claw"
```

### 3. Verify

```bash
ls ~/.opencode/skills/
```

You should see `page-claw` listed.

Restart OpenCode. Invoke with `/page-claw` in any session.

---

## Updating

```bash
cd ~/skills/pageclaw && git pull
```

Changes take effect immediately — no restart needed.

## Uninstalling

```bash
rm ~/.opencode/skills/page-claw
# Optionally remove the repo:
rm -rf ~/skills/pageclaw
```
