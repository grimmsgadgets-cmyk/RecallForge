# /morning-brief — RecallForge Daily Brief

Surfaces what's waiting in your knowledge vault so you know what to work on before you start.

---

## Steps

**1. Detect the vault path**

Run `cat $HOME/.recallforge/config 2>/dev/null | grep vault_path | cut -d= -f2`. If empty, fall back to `$HOME/RecallVault`.

If the vault directory doesn't exist, say: "No vault found at [path]. Run `/feynman <anything>` to create your first note." Stop.

**2. Check the queue**

List files in `$VAULT/Queue/` matching `* - TO REVIEW.md`. Count them. If any exist, list the concept names (strip the ` - TO REVIEW.md` suffix).

**3. Check spaced repetition dues**

Scan all `.md` files under `$VAULT/Feynman Sessions/` for YAML frontmatter containing `sr_due`. Parse each date. Collect all notes where `sr_due` <= today.

For each overdue note, extract the `concept` field from frontmatter.

**4. Report**

Format the output as:

```
RECALL BRIEF — [today's date]

Queued for first pass: [N]
[list concept names, one per line, prefixed with •]

Due for spaced repetition review: [N]
[list concept names + how many days overdue, e.g. "• TCP handshake (2 days overdue)"]

Suggested session: [pick the most overdue item, or oldest queue item if nothing is overdue]
→ Run `/feynman [concept]` to start.
```

If nothing is queued and nothing is due: "All clear. Run `/feynman <anything>` to add to your vault."

**5. Done**

One line: "[N] queued, [N] due for review." Then hand back to the user.

---

## Tone

Short, functional, no filler. In and out in under 30 seconds of reading.
