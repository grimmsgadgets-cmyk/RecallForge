# /deep-feynman — Research-Backed Feynman Session

**Usage:** `/deep-feynman <concept>`
**Use when:** The concept has external depth worth anchoring to — a spec, a paper, a CVE, official docs, an RFC. Runs the standard Feynman session but spawns a researcher agent first to gather authoritative source material and a challenger agent after Phase 1 to stress-test your explanation.

---

## When to Use This vs `/feynman`

Use `/feynman` for most things — code patterns, architectural decisions, things learned in session.

Use `/deep-feynman` when:
- The concept has an authoritative external source (RFC, spec, paper, CVE, official docs)
- You want to verify your understanding against source material, not just against Claude's training
- The concept has real-world consequences if misunderstood (security, cryptography, compliance, infrastructure)

---

## The Session

You said: `/deep-feynman $ARGUMENTS`

---

### Phase 0 — Research

Before introducing the concept to the user, spawn a **researcher subagent** to gather source material:

> Spawn an Agent with this prompt:
> "You are a research assistant. Find 1–3 authoritative sources for the concept: `$ARGUMENTS`. This could be an RFC, official docs page, academic paper, CVE entry, or specification — whatever is most canonical for this concept. For each source: extract the 3–5 most important facts, any common misconceptions explicitly called out in the source, and the URL. Return a structured summary. Do not editorialize. Stick to what the sources say."

Wait for the researcher to return. Use its findings to:
1. Anchor the Phase 0 introduction to what the sources actually say
2. Identify misconceptions the sources flag explicitly
3. Build stronger Phase 2 questions based on source-documented edge cases

---

### Phase 0 — Setup (source-anchored)

Introduce the concept using the researcher's findings:

**One-sentence summary:** [from source material]
**Big idea:** [2–3 sentences grounded in what the sources say]
**What to be skeptical of:** [misconceptions explicitly called out in sources — these are the ones worth knowing about]
**Source:** [link or name the canonical source]

Then ask:

> "Before you explain — gut check, 0–5: how well do you think you understand this right now? Then teach it to me like I've never heard of it."

Wait for the user's response. Record their predicted score.

---

### Phase 1 — Gap Analysis

Same as `/feynman` Phase 1. Identify what they got right, wrong, left out, and any misconceptions.

Additionally: cross-check the user's explanation against the researcher's source summary. Note any divergence from what the spec/docs actually say — even if the user's explanation "sounds" right, if it contradicts the source, flag it.

---

### Phase 2 — Challenger

After standard gap analysis, spawn a **challenger subagent**:

> Spawn an Agent with this prompt:
> "You are a skeptical expert reviewer. The user explained: `[paste user's Phase 1 explanation]`. The concept is: `$ARGUMENTS`. Source summary: `[paste researcher summary]`. Your job: find logical holes, missing edge cases, and false assumptions in the user's explanation. Do not be gentle. Be specific — name the exact claim that breaks under scrutiny and why. Return 2–3 specific challenges."

Use the challenger's output to strengthen or replace the weakest of the 3 Socratic questions.

---

### Phases 3–5 — Standard

Run Phases 3, 4, and 5 exactly as in `/feynman`.

In Phase 5, add to the saved note:

```markdown
## Source Anchors

[List of sources from researcher, with URLs and key facts]
```

And add to frontmatter:
```yaml
source_anchored: true
sources: [list of URLs]
```

---

## Tone

Same as `/feynman` — direct, honest, no filler. The added research depth is invisible to the user except in output quality.
