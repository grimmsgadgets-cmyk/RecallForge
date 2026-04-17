# RecallForge

Automates the Feynman learning loop inside Claude Code. Every session surfaces what you half-understood, runs you through structured Socratic questioning, and saves notes with spaced repetition scheduling — so knowledge actually sticks.

No Docker. No external services. No API keys. Just Claude Code, a folder of markdown files, and four slash commands.

See `WORKFLOW.md` for a plain-language explanation of how it works before you set it up.

---

## What You Get

| Command | What it does |
|---|---|
| `/feynman <concept>` | Full Feynman session — confidence prediction, gap analysis, adversarial questions, teaching mode. Saves structured note with spaced repetition. |
| `/feynman <concept> --teach` | Teaching mode — Claude plays the confused student. Unlocked after 3+ reviews with strong recall. |
| `/deep-feynman <concept>` | Research-backed Feynman — spawns a researcher agent to gather authoritative sources first, then a challenger agent to stress-test your explanation. Use for security, specs, protocols, and anything where being wrong has consequences. |
| `/learn-from-session` | Scans the current session for concepts to queue. Run at the end of any session. Optionally saves a session log. |
| `/morning-brief` | Shows misconceptions, what's due for review, cold domains, and teaching-ready concepts. |

Plus a **Stop hook** that fires automatically after substantive sessions and triggers `/learn-from-session` without you thinking about it.

---

## Vault Structure

```
~/RecallVault/
├── Feynman Sessions/          ← one file per concept
│   ├── 2026-04-17 - DNS resolution.md
│   └── ...
├── Misconceptions/            ← concepts you actively got wrong
│   └── DNS resolution.md
├── Analogies/                 ← best analogy from each session
│   └── DNS resolution.md
├── Queue/                     ← concepts deferred for later
│   └── docker volumes - TO REVIEW.md
├── Session Logs/              ← optional session narrative logs
│   └── 2026-04-17.md
└── Templates/
    └── Feynman Session Template.md
```

---

## Requirements

- [Claude Code](https://claude.ai/code) CLI, authenticated
- Any text editor (or [Obsidian](https://obsidian.md/) for wikilinks and graph view)
- macOS, Linux, or Windows with WSL2

---

## Setup

### 1. Copy the slash commands

```bash
mkdir -p ~/.claude/commands
cp claude/commands/feynman.md ~/.claude/commands/
cp claude/commands/deep-feynman.md ~/.claude/commands/
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
mkdir -p "$VAULT/Misconceptions"
mkdir -p "$VAULT/Analogies"
mkdir -p "$VAULT/Session Logs"
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
```
/feynman DNS resolution
```
Should run a session and save a note to your vault.

**Test deep Feynman:**
```
/deep-feynman TLS handshake
```
Should spawn a researcher agent for sources, then run the full session.

**Test the brief:**
```
/morning-brief
```
Should show 1 item due for review.

**Test the hook:**
Run a session with 8+ tool calls and end it. Claude should automatically offer to run `/learn-from-session`.

---

## Using with Obsidian

Point Obsidian at your vault folder: **Open folder as vault** → select the path from `~/.recallforge/config`.

Wikilinks in `## Connections` link to other notes as your vault grows. The `Misconceptions/` and `Analogies/` folders surface as separate graph clusters.

---

## Troubleshooting

**Notes aren't saving**
Check that the vault path in `~/.recallforge/config` exists and is writable.

**Hook not firing**
Verify `~/.claude/settings.json` has the Stop hook registered and that `auto-learn.sh` is executable:
```bash
chmod +x ~/.claude/hooks/auto-learn.sh
```

**morning-brief shows nothing**
Either the vault path isn't set, or no notes exist yet. Run `/feynman <anything>` first.
