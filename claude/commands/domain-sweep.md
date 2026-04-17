# /domain-sweep — RecallForge Domain Refresh

**Usage:** `/domain-sweep <tag>`

Triggered manually or from `/morning-brief` when a domain goes cold (45+ days no review). Spawns an agent that reads all notes in the domain, generates a synthesis, and recommends a review order — turning a cold-domain warning into an actionable sweep.

---

## Steps

**1. Detect the vault path**

Run `cat $HOME/.recallforge/config 2>/dev/null | grep vault_path | cut -d= -f2`. If empty, fall back to `$HOME/RecallVault`.

**2. Collect domain notes**

Scan `$VAULT/Feynman Sessions/` for all notes with `$ARGUMENTS` in their `tags` frontmatter field. Extract: concept name, date, sr_repetitions, sr_due, last review date, gaps to revisit, and any misconceptions flag.

If no notes found: "No notes tagged `$ARGUMENTS`. Run `/feynman <concept>` with that tag to start building this domain." Stop.

**3. Spawn domain synthesis agent**

Spawn an Agent with this prompt:

> "You are a curriculum designer reviewing a learner's knowledge of a specific domain: `$ARGUMENTS`.
>
> You have been given their learning notes for this domain:
> [paste note summaries: concept, gaps, date, repetitions]
>
> Produce:
> 1. **Domain health summary** — what do they genuinely understand vs what is shaky?
> 2. **Knowledge map** — how do the concepts in this domain relate to each other? What's foundational vs advanced?
> 3. **Review order** — given their gaps and time since last review, in what order should they review these concepts? Most foundational first, or most overdue first — explain the tradeoff and make a recommendation.
> 4. **Missing concepts** — what concepts in `$ARGUMENTS` are absent from their vault entirely but would fill important gaps?
> 5. **One sweep session plan** — if they have 45 minutes, what's the most efficient path through this domain right now?
>
> Be opinionated. Don't hedge. Make a clear recommendation."

**4. Report inline**

Present the domain synthesis directly in the conversation — no file write needed unless the user asks. Keep it scannable.

```
DOMAIN SWEEP — $ARGUMENTS

Health: [one line]

Knowledge map:
[foundations → advanced, with gaps marked]

Review order:
1. /feynman [concept] — [why first]
2. /feynman [concept] — [why second]
...

Missing from vault: [concepts worth adding]

45-minute plan: [agent's recommendation]
```

**5. Done**

Say: "Start with `/feynman [top concept]` to kick off the sweep."

---

## Tone

Decisive. The user has a cold domain — they need a clear action plan, not options.
