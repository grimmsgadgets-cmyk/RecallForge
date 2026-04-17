# /learn-from-session — Extract Learning from This Conversation

Run this at the end of any session. Scans the conversation for concepts you encountered but may not fully understand, then queues them for Feynman review.

---

## Steps

**1. Scan the conversation**

Look back through this session for:
- Terms, libraries, patterns, or concepts used but not deeply explained
- Things that had to be looked up, errors not initially understood, concepts introduced without definition
- Any moment where the explanation was "just do X" without explaining why
- Architectural decisions — why was something structured a certain way?
- Best practices that appeared — parameterized queries, separation of concerns, error handling patterns
- Security concepts — any time something was done to prevent an attack or vulnerability
- Named patterns — foreign keys, race conditions, event loops, any named thing
- "Why" questions the user might have had but didn't ask — surface those proactively

**2. Build a candidate list**

List 3–7 concepts worth understanding more deeply. For each, write one sentence on *why it matters* — not what it is, but why deep understanding makes you more capable.

```
1. [concept] — [why understanding it deeply matters]
2. ...
```

**3. Present and default to now**

Present the list, then ask:

> "Ready to work through these now? Say **now** to start immediately, **later** to save them as stubs, or pick numbers (e.g. '1, 3') to do a subset now."

Default is **now**.

**4. For each concept the user wants to cover — run Feynman inline**

Run the full `/feynman` flow inline for each concept in sequence. Do not ask again per concept. Just start.

After each concept completes, say: "Saved. Moving to [next concept]..." and continue.

**5. For concepts the user defers ("later")**

Detect the vault path: `cat $HOME/.recallforge/config 2>/dev/null | grep vault_path | cut -d= -f2`. If empty, fall back to `$HOME/RecallVault`.

Run `mkdir -p "$VAULT/Queue"`

**Source enrichment (run in parallel for all deferred concepts):**

Spawn an Agent with this prompt:
> "For each of these concepts, find one authoritative reference URL — the canonical source a practitioner would actually use (official docs, RFC, MDN, CVE entry, paper, spec). Concepts: [list]. Return: concept → URL + one-sentence description of what the source covers. If no clean authoritative source exists, return 'none' for that concept. No Wikipedia."

Embed the returned URLs in the stubs below.

Write a stub to `$VAULT/Queue/[concept] - TO REVIEW.md`:

```markdown
---
concept: [concept]
date: YYYY-MM-DD
source_date: YYYY-MM-DD
tags: [feynman, queued]
sr_due: YYYY-MM-DD
---

# [concept] — Queued for Review

Encountered: [today's date]
Context: [one sentence on why it came up — what were you doing when this concept appeared?]

Reference: [URL from source enrichment agent, or "none found"]

---

*Run `/feynman [concept]` to work through this.*
```

**6. Session log**

After handling concepts, ask once:

> "Want a session log saved? (y/n)"

If yes, detect vault path, run `mkdir -p "$VAULT/Session Logs"`, and write `$VAULT/Session Logs/YYYY-MM-DD.md`:

```markdown
---
date: YYYY-MM-DD
tags: [session-log]
---

# Session Log — YYYY-MM-DD

## What Was Done

[2–4 bullets: tasks completed, problems solved, things shipped]

## Key Decisions

[Any architectural, design, or approach choices made — and the reasoning behind them]

## Concepts Encountered

[The concept list from this session]

## Open Questions

[Anything unresolved or worth checking next time]

## Next Session

[Where to pick up from here]
```

**7. End**

Say: "Done — [N] Feynman sessions completed. [N] queued to `RecallVault/Queue/`." If a session log was saved: "+ session log written to `Session Logs/YYYY-MM-DD.md`."

---

## Tone

Efficient. The user just finished a session — don't make this a lecture. Capture what slipped past and make sure it doesn't stay that way.
