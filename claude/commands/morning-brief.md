# /morning-brief — RecallForge Daily Brief

Surfaces what's waiting in your knowledge vault so you know what to work on before you start.

---

## Steps

**1. Detect the vault path**

Run `cat $HOME/.recallforge/config 2>/dev/null | grep vault_path | cut -d= -f2`. If empty, fall back to `$HOME/RecallVault`.

If the vault directory doesn't exist: "No vault found at [path]. Run `/feynman <anything>` to create your first note." Stop.

**2. Check misconceptions**

Scan `$VAULT/Misconceptions/` for any files where frontmatter contains `misconception_resolved: false`. List them by concept name. These are highest priority — an unresolved misconception compounds silently.

**3. Check the queue**

List files in `$VAULT/Queue/` matching `* - TO REVIEW.md`. Extract concept names (strip ` - TO REVIEW.md`).

**4. Check spaced repetition dues**

Scan all `.md` files under `$VAULT/Feynman Sessions/` for `sr_due` in YAML frontmatter. Collect all notes where `sr_due` <= today. Extract `concept` and days overdue for each.

**5. Check domain decay**

Scan all `.md` files in `$VAULT/Feynman Sessions/`. Group notes by their non-base tags (exclude `feynman`, `learning`, `queued`). For each domain group, find the most recent `date` or `sr_due`. If no note in a domain has been touched in 45+ days, flag it as cold.

This catches knowledge rot at the domain level — SM-2 handles individual concepts, but a whole domain going cold is a different signal.

**6. Check teaching-ready concepts**

Scan for notes where `sr_repetitions >= 3`. For each, check the most recent review section score. If score >= 4, list the concept as teaching-ready.

Teaching mode is unlocked by demonstrated mastery, not just time. These concepts are ready to be stress-tested.

**7. Report**

```
RECALL BRIEF — [today's date]

⚠ UNRESOLVED MISCONCEPTIONS: [N]
[list concepts — these are things you actively believed wrong]

Due for spaced repetition review: [N]
[• concept (X days overdue)]

Queued for first pass: [N]
[• concept]

Cold domains (45+ days): [list tags]
→ Consider a sweep of [coldest domain].

Teaching-ready: [N]
[• concept] → /feynman [concept] --teach

Suggested session: [most overdue misconception → most overdue SR → oldest queue item]
→ Run `/feynman [concept]` to start.
```

If nothing is queued, nothing is due, no misconceptions, no cold domains: "All clear. Run `/feynman <anything>` to add to your vault."

**8. Done**

One line summary. Hand back to the user.

---

## Tone

Short, functional, no filler. In and out in under 30 seconds of reading.
