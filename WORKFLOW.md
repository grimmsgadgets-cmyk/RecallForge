# RecallForge — How It Works

RecallForge automates the Feynman learning loop so that every coding session, study session, or work session leaves you with durable knowledge — not just completed tasks.

---

## The Core Loop

```
You work on something
        ↓
Concepts surface that you half-understand
        ↓
Auto-learn hook fires when the session ends
        ↓
/learn-from-session extracts those concepts
        ↓
/feynman runs a structured session on each one
        ↓
A note is saved to your vault with a review date
        ↓
/morning-brief tells you what's due tomorrow
        ↓
/feynman reviews it — the interval grows each time
        ↓
You actually understand it now
```

Nothing about this requires you to remember to do it. The hook fires automatically. The brief surfaces what's waiting. The spaced repetition calculates the next review date.

---

## The Three Commands

### `/feynman <concept>`

The core session. Works on anything.

**First time on a concept:**
- Phase 0: Claude introduces it briefly, then asks you to explain it back
- Phase 1: Gap analysis — what you got right, wrong, and missed
- Phase 2: Three targeted Socratic questions on your specific gaps
- Phase 3: Claude responds to each answer, corrects what's off
- Phase 4: Summary — what you know, what to revisit, what to do next
- Phase 5: Saves a structured note to your vault with a review date set to tomorrow

**Reviewing an existing concept:**
- Claude loads your previous note first
- Runs the same session with focus on your documented gaps
- Asks you to score your recall (0–5)
- Updates the note and recalculates the review interval using SM-2

### `/learn-from-session`

Run at the end of any session. Scans the conversation and extracts 3–7 concepts you encountered but may not fully understand. Offers to run Feynman on them immediately or queue them as stubs in your vault for later.

### `/morning-brief`

Scans your vault and reports:
- Concepts queued for a first Feynman session
- Notes whose spaced repetition review date has passed
- Which one to start with

No internet, no API calls — just reads your local files.

---

## The Auto-Learn Hook

A Stop hook in Claude Code fires every time a session ends. If the session had 8 or more tool calls (meaning it was substantive), it automatically triggers `/learn-from-session` before you close the terminal.

You don't have to remember. It just happens.

---

## The Vault

Notes are plain markdown files with YAML frontmatter. They work in any text editor and in Obsidian (wikilinks, graph view, and tag search all work if you open the vault folder in Obsidian).

```
RecallVault/
├── Feynman Sessions/          ← one file per concept
│   ├── 2026-04-17 - TCP handshake.md
│   ├── 2026-04-18 - Docker volumes.md
│   └── ...
├── Queue/                     ← stubs from /learn-from-session (deferred)
│   └── foreign key - TO REVIEW.md
└── Templates/                 ← optional, for Obsidian users
    └── Feynman Session Template.md
```

---

## Spaced Repetition

Each note's frontmatter tracks:

```yaml
sr_interval: 3        # days until next review
sr_repetitions: 2     # how many times reviewed
sr_ease: 2.5          # difficulty multiplier (SM-2)
sr_due: 2026-04-20    # next review date
```

When you review a note via `/feynman`, you score your recall 0–5. RecallForge applies the SM-2 algorithm to update the interval:
- **Score 0–2** (blanked or wrong): resets to 1-day interval
- **Score 3** (recalled with effort): interval × ease factor
- **Score 4–5** (solid recall): interval × (ease + 0.1), ease grows slightly

Concepts you know well get reviewed less often. Concepts you struggle with come back sooner.

---

## Privacy

Everything is local. No API keys. No accounts. No telemetry. The vault is a folder on your machine. The spaced repetition state lives in the notes themselves. Nothing leaves your system.
