# RecallForge

Automates the Feynman learning loop inside Claude Code. Every session surfaces what you half-understood, runs you through structured Socratic questioning, and saves notes with spaced repetition scheduling — so knowledge actually sticks.

No Docker. No external services. No API keys. Just Claude Code, a folder of markdown files, and three slash commands.

See `WORKFLOW.md` for a plain-language explanation of how it works before you set it up.

---

## What You Get

| Command | What it does |
|---|---|
| `/feynman <concept>` | 5-phase Feynman session on anything. Saves a structured note with a review date. |
| `/learn-from-session` | Scans the current session for concepts to queue. Run it at the end of any session. |
| `/morning-brief` | Shows what's queued and what's overdue for review. |

Plus a **Stop hook** that fires automatically after substantive sessions and triggers `/learn-from-session` without you thinking about it.

---

## Requirements

- [Claude Code](https://claude.ai/code) CLI, authenticated
- Any text editor (or [Obsidian](https://obsidian.md/) if you want wikilinks and graph view)
- macOS, Linux, or Windows with WSL2

That's it.

---

## Setup

### 1. Copy the slash commands

```bash
mkdir -p ~/.claude/commands
cp claude/commands/feynman.md ~/.claude/commands/
cp claude/commands/learn-from-session.md ~/.claude/commands/
cp claude/commands/morning-brief.md ~/.claude/commands/
```

### 2. Set your vault path

RecallForge saves notes to `~/RecallVault` by default. If you want a different location — including an existing Obsidian vault — set it now:

```bash
mkdir -p ~/.recallforge
echo "vault_path=$HOME/RecallVault" > ~/.recallforge/config

# Or use a custom path:
# echo "vault_path=$HOME/Documents/MyVault" > ~/.recallforge/config
```

Create the vault structure:

```bash
VAULT=$(cat ~/.recallforge/config | grep vault_path | cut -d= -f2)
mkdir -p "$VAULT/Feynman Sessions"
mkdir -p "$VAULT/Queue"
mkdir -p "$VAULT/Templates"
cp vault/Templates/"Feynman Session Template.md" "$VAULT/Templates/"
```

### 3. Set up the auto-learn hook

```bash
mkdir -p ~/.claude/hooks
cp claude/hooks/auto-learn.sh ~/.claude/hooks/
chmod +x ~/.claude/hooks/auto-learn.sh
```

Register it in Claude Code settings. If you don't have `~/.claude/settings.json` yet:

```bash
cp claude/settings-template.json ~/.claude/settings.json
```

If you already have one, add this inside the `"hooks"` object:

```json
"Stop": [
  {
    "hooks": [
      {
        "type": "command",
        "command": "python3 $HOME/.claude/hooks/auto-learn.sh",
        "timeout": 10,
        "statusMessage": "Checking for learning opportunities..."
      }
    ]
  }
]
```

---

## Verify It Works

**Test Feynman:**
Open any Claude Code session and run:
```
/feynman DNS resolution
```
Claude should run a 5-phase session and save a note to your vault.

**Test the brief:**
```
/morning-brief
```
Should report 1 item queued for review (the note you just created).

**Test the hook:**
Run a session with 8+ tool calls and end it. Claude should automatically offer to run `/learn-from-session`.

---

## Vault Structure

```
~/RecallVault/
├── Feynman Sessions/          ← one file per concept
│   ├── 2026-04-17 - DNS resolution.md
│   └── ...
├── Queue/                     ← concepts queued from /learn-from-session
│   └── docker volumes - TO REVIEW.md
└── Templates/
    └── Feynman Session Template.md
```

Files are plain markdown. Open the vault folder in Obsidian for wikilinks and graph view — or just use any text editor.

---

## Using with Obsidian

Point Obsidian at your vault folder: **Open folder as vault** → select the path from `~/.recallforge/config`.

Wikilinks in the `## Connections` section of each note will link automatically to other notes in the vault as your knowledge base grows.

---

## Troubleshooting

**Notes aren't saving**
Check that the vault path in `~/.recallforge/config` exists and is writable.

**Hook not firing**
Verify `~/.claude/settings.json` has the Stop hook and that `auto-learn.sh` is executable:
```bash
chmod +x ~/.claude/hooks/auto-learn.sh
```

**morning-brief shows nothing**
Either the vault path isn't set, or no notes exist yet. Run `/feynman <anything>` first.
