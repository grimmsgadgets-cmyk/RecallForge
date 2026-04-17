# /weekly-synthesis — RecallForge Weekly Knowledge Synthesis

Reads all vault notes touched in the past 7 days, finds cross-concept connections, and writes a synthesis document. This is where individual Feynman sessions become a cohesive mental model.

---

## Steps

**1. Detect the vault path**

Run `cat $HOME/.recallforge/config 2>/dev/null | grep vault_path | cut -d= -f2`. If empty, fall back to `$HOME/RecallVault`.

If no vault: "No vault found. Run `/feynman <anything>` to start." Stop.

**2. Collect this week's notes**

Scan `$VAULT/Feynman Sessions/` for notes with `date` or most recent `## Review` date within the last 7 days. Also include any new files in `$VAULT/Queue/` created this week.

If fewer than 2 notes: "Not enough activity this week to synthesize. Come back after a few more sessions." Stop.

**3. Spawn synthesis agent**

Spawn an Agent with this prompt:

> "You are a knowledge synthesis specialist. You have been given a set of learning notes from the past week. Your job is to find the hidden connections — concepts that reinforce each other, analogies that transfer across domains, patterns that repeat under different names, and gaps that appear across multiple notes.
>
> Notes to analyze:
> [paste full content of each note]
>
> Produce:
> 1. **Cross-concept connections** — which concepts link to each other and how (not just 'both are about networking' — what is the actual structural relationship?)
> 2. **Repeating patterns** — if the same underlying idea appeared in different forms this week, name it
> 3. **Analogy transfers** — where does a mental model from one concept illuminate another?
> 4. **Meta-gaps** — what concept, if learned, would tie the most threads together?
> 5. **Suggested Feynman sessions** — 1–3 concepts that would strengthen the weakest connections in this week's learning
>
> Be specific. Vague connections ('these are both related to data') are worthless. Name the exact structural relationship."

**4. Write the synthesis**

Run `mkdir -p "$VAULT/Session Logs"`

Write to `$VAULT/Session Logs/YYYY-[weeknum]-synthesis.md`:

```markdown
---
date: YYYY-MM-DD
week: [week number]
concepts_covered: [N]
tags: [synthesis, weekly]
---

# Weekly Synthesis — Week [N], YYYY

Concepts reviewed this week: [list]

---

## Cross-Concept Connections

[From synthesis agent]

---

## Repeating Patterns

[From synthesis agent]

---

## Analogy Transfers

[From synthesis agent]

---

## Meta-Gap

[From synthesis agent — the one concept that would tie the most threads together]

---

## Suggested Next Sessions

[From synthesis agent — 1–3 `/feynman` calls that would strengthen weakest links]
```

**5. Done**

Say: "Synthesis saved to `Session Logs/YYYY-[weeknum]-synthesis.md`. [N] concepts woven together. Suggested next: `/feynman [top suggestion]`."

---

## Tone

Analytical. No filler. The synthesis agent does the heavy lifting — present its output cleanly.
