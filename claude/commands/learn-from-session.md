# /learn-from-session — Extract Learning from This Conversation

Run this at the end of any session. Scans the conversation for concepts you encountered but may not fully understand, then queues them for Feynman review.

---

## Steps

**1. Scan the conversation**

Look back through this session for:
- Terms, libraries, patterns, or concepts that were used but not deeply explained
- Things that had to be looked up, errors not initially understood, concepts introduced without definition
- Any moment where the explanation was "just do X" without explaining why
- Architectural decisions — why was something structured a certain way?
- Best practices that appeared — parameterized queries, separation of concerns, error handling patterns
- Security concepts — any time something was done to prevent an attack or vulnerability
- Named patterns — foreign keys, race conditions, event loops, any named thing
- "Why" questions the user might have but didn't ask — surface those proactively

**2. Build a candidate list**

List 3–7 concepts worth understanding more deeply. For each one, write one sentence on *why it matters* — not what it is, but why deep understanding would make you more capable.

```
1. [concept] — [why it matters to understand deeply]
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

Detect the vault path by running `cat $HOME/.recallforge/config 2>/dev/null | grep vault_path | cut -d= -f2`. If empty, fall back to `$HOME/RecallVault`.

Run `mkdir -p "$VAULT/Queue"`

Write a stub to `$VAULT/Queue/[concept] - TO REVIEW.md`:

```markdown
---
concept: [concept]
date: YYYY-MM-DD
tags: [feynman, queued]
sr_due: YYYY-MM-DD
---

# [concept] — Queued for Review

Encountered: [today's date]
Context: [one sentence on why it came up in this session]

---

*Run `/feynman [concept]` to work through this.*
```

**6. End**

Say: "Done — [N] Feynman sessions completed. [N] queued to `RecallVault/Queue/`."

---

## Tone

Efficient. The user just finished a session — don't make this a lecture. Capture what slipped past and make sure it doesn't stay that way.
