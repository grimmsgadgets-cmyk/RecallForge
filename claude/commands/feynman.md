# /feynman — Feynman Learning Session

**Usage:** `/feynman <concept>` or `/feynman <concept> --teach`
**Works for:** Anything — code, science, history, math, security, cooking, finance, music theory, anything you want to actually understand.

---

## What This Is

The Feynman Technique: if you can explain something simply, you understand it. If you can't, you've found the gap. This skill runs you through that loop in a structured conversation and saves the result to your knowledge vault.

---

## The Session

You said: `/feynman $ARGUMENTS`

If `$ARGUMENTS` ends with `--teach`, strip it and jump to **Teaching Mode** at the bottom.

Run the following sequence exactly. Do not skip phases. Do not rush to the next phase before the user responds.

---

### Phase 0 — Setup

**Step 1 — Goal + prior engagement (ask both in one message):**

Ask:

> "Two quick questions before we start:
> 1. What's your goal for this session? (e.g. understanding it for the first time, filling a specific gap, preparing to explain it to someone)
> 2. Have you already studied or engaged with this concept, or are you coming in completely fresh?"

Wait for the user's response.

---

**Step 2 — Notebook exercise (always run this, regardless of prior engagement):**

This is the core of Feynman's method: surface what you *think* you know and explicitly name what you don't. Ask:

> "Before anything else — write down:
> - What do you think you already know about [concept]? (Even vague impressions count.)
> - What specifically are you unsure or confused about?
> - What questions do you want to be able to answer by the end?"

Wait for the user's response. These known unknowns drive everything — use them to focus the study material and sharpen the Socratic questions later.

---

**Step 3 — Branch based on prior engagement:**

**If fresh (hasn't studied yet):**

Run the analogy transfer check silently (see below), then provide structured study material:

**One-sentence summary:** [what it is, plain language]

**Big idea:** [the core mechanism or insight — 2–3 sentences, zero jargon]

**What to be skeptical of:** [1–2 common traps — places the concept gets misapplied or misunderstood]

If the analogy check returned something relevant, include it here.

Then say:
> "Take a moment to read that. When you're ready, close it and explain it back to me in your own words — don't re-read, just explain from what you absorbed."

**If already studied:**

Skip the study material entirely — no priming. Go straight to:
> "Gut check, 0–5: how well do you think you understand this right now? Then teach it to me like I've never heard of it. Your own words."

In both cases, record their confidence score (or `"not given"` if skipped).

---

**Analogy transfer check (run silently during Step 3):**

Spawn an Agent with this prompt:
> "Scan the files in `$VAULT/Analogies/` (vault path from `cat $HOME/.recallforge/config 2>/dev/null | grep vault_path | cut -d= -f2`, fallback `$HOME/RecallVault`). List any analogies whose underlying structure could transfer to: `$ARGUMENTS`. Return: concept name + analogy text + why it transfers. If nothing relevant, return 'none'. Be brief."

---

### Phase 1 — Gap Analysis

Read the user's explanation carefully. Identify:
- What they got right (name it specifically)
- What they got wrong or oversimplified (name it specifically)
- What they left out entirely (name it specifically)
- **MISCONCEPTION:** Did the user assert something actively false — not just omit it, but state the wrong thing? If yes, flag it clearly. This gets separate treatment and its own vault file.

Respond with a short, honest, kind assessment:
> "Strong: [what they nailed]. Gaps: [what was missing or off]. You didn't mention [key concept]."

If a misconception was detected, add:
> "One thing to flag: you said [X] — that's actually [correct version]. Common trap. We'll lock this down."

Do not re-explain everything. Name the gaps and move on.

---

### Phase 2 — Socratic Questions

Generate 3 targeted questions that probe exactly the gaps from Phase 1. Not generic — questions that expose the specific misunderstanding.

Before finalizing, apply an adversarial lens: *What assumption in the user's explanation, if wrong, would break the concept entirely?* If that assumption isn't already covered by the 3 questions, replace the weakest question with it.

**Optional — Challenger Agent (for high-stakes or technically complex concepts):**

If the concept is in a domain where being wrong has real consequences (security, cryptography, distributed systems, data integrity, medical/financial), spawn a challenger subagent before asking the questions:

> Spawn an Agent with this prompt:
> "You are a skeptical expert reviewer. The user explained this concept: `[user's Phase 1 explanation]`. Concept: `$ARGUMENTS`. Find logical holes, missing edge cases, and false assumptions. Be specific — name the exact claim that breaks under scrutiny and why. Return 2–3 targeted challenges."

Use the challenger's output to sharpen or replace the weakest of the 3 questions. Do not mention the subagent to the user — just use its output to ask better questions.

Number them. Ask all 3 at once. Wait for the user to answer all 3 before continuing.

---

### Phase 3 — Responses

For each answer:
- Confirm what's right
- Correct what's wrong with a clear, simple explanation
- If there's still a gap, push back once with a follow-up

One short paragraph per question. No lectures.

---

### Phase 4 — Summary

**What you understand well:**
(2–3 bullet points, specific)

**Gaps to revisit:**
(1–3 concepts that need more work, one line each on why they matter)

**If you remember 3 things:**
(The 3 most important takeaways, prioritized — what should stick regardless of everything else)

**Recommended next step:**
One concrete action — read X, try Z, build W.

---

### Phase 5 — Save

After the summary, save to the vault automatically. Tell the user what you're doing.

**Detect the vault path:**
```
cat $HOME/.recallforge/config 2>/dev/null | grep vault_path | cut -d= -f2
```
If empty, fall back to `$HOME/RecallVault`. Store as VAULT.

**Create directories:**
```
mkdir -p "$VAULT/Feynman Sessions" "$VAULT/Misconceptions" "$VAULT/Analogies"
```

**Write the main note** to `$VAULT/Feynman Sessions/YYYY-MM-DD - $ARGUMENTS.md`:

```markdown
---
concept: $ARGUMENTS
date: YYYY-MM-DD
source_date: YYYY-MM-DD
tags: [feynman, learning]
sr_interval: 1
sr_repetitions: 0
sr_ease: 2.5
sr_due: YYYY-MM-DD
confidence_predicted: [0-5 or "not given"]
confidence_actual: [user's self-assessment from Phase 4, or "pending"]
has_misconceptions: [true/false]
---

# YYYY-MM-DD — $ARGUMENTS

---

## Distilled Understanding

[2–4 sentences: what this concept actually is, plain language.]

---

## What I Got Right

[Bullet points from Phase 1]

---

## Gaps Found

[Bullet points — what was missing or wrong]

---

## Socratic Questions & Answers

**Q1:** [question]
→ [user's answer + correction/confirmation]

**Q2:** [question]
→ [user's answer + correction/confirmation]

**Q3:** [question]
→ [user's answer + correction/confirmation]

---

## Compression Test

In 15 words or fewer:
→ [fill in from session]

If You Remember 3 Things:
→ 1. [most important]
→ 2.
→ 3.

Best analogy:
→ [from session, or generate one]

---

## Connections

- [[]]
- [[]]

---

## Gaps to Revisit

[From Phase 4]

## Next Step

[From Phase 4]
```

**If a misconception was detected in Phase 1**, write `$VAULT/Misconceptions/$ARGUMENTS.md`:

```markdown
---
concept: $ARGUMENTS
date: YYYY-MM-DD
misconception_resolved: false
---

# Misconception — $ARGUMENTS

Detected: YYYY-MM-DD

**What was said:** [the false assertion, verbatim or close]
**Why it's wrong:** [correct version, plain language]
**Why it's a common trap:** [1 sentence — what makes this confusion so easy to make]

---

*When reviewing `/feynman $ARGUMENTS`, check whether this reappears.*
```

**Extract the analogy** to `$VAULT/Analogies/$ARGUMENTS.md` if a strong one was used:

```markdown
---
concept: $ARGUMENTS
date: YYYY-MM-DD
---

# Analogy — $ARGUMENTS

[The analogy, 1–3 sentences]

Captured: YYYY-MM-DD
```

After saving, tell the user: "Saved to `Feynman Sessions/YYYY-MM-DD - $ARGUMENTS.md`. Due for review tomorrow." If a misconception was logged: "Misconception saved to `Misconceptions/$ARGUMENTS.md` — flag to confirm on next review."

---

## Reviewing an Existing Note

If a note already exists for this concept, load it before Phase 0. Use the documented gaps and next step to focus the session. If `has_misconceptions: true`, load the misconception file and watch whether that specific error reappears in Phase 1.

After Phase 4, update frontmatter with SM-2:

Ask: "How well did you recall this? 0–5 (0 = blank, 3 = recalled with effort, 5 = perfect)."

Apply SM-2:
- Score < 3: reset `sr_interval` to 1, keep `sr_ease`, `sr_due` = tomorrow
- Score 3: `sr_interval` = previous × `sr_ease`, round to nearest day
- Score 4–5: `sr_interval` = previous × (`sr_ease` + 0.1), `sr_ease` = min(sr_ease + 0.1, 2.5)
- Always: `sr_repetitions` += 1, `sr_due` = today + new interval

If the user gave a predicted score at Phase 0, note the gap:
> "You predicted [X], scored [Y]. Delta: [actual - predicted]."

A persistent positive delta (predict high, score low) is a blind spot. A persistent negative delta (undersell yourself) is fine.

If the misconception reappeared, keep `misconception_resolved: false`. If it was clean, set it to `true`.

Append a review section to the note:

```markdown
---

## Review — YYYY-MM-DD

Score: [0–5] | Predicted: [0–5 or "not given"] | Delta: [actual − predicted]
[2–3 sentences: what changed in understanding, what still feels shaky]
Misconception check: [reappeared / resolved / N/A]
```

---

## Teaching Mode — `/feynman $ARGUMENTS --teach`

Only available when `sr_repetitions >= 3` AND the most recent review score was `>= 4`.

If not eligible: "Not ready for teaching mode yet — review a few more times with solid recall first."

If eligible:

**You play a curious, slightly confused student.** The user teaches the concept to you. As the student you:
- Ask naïve but probing questions ("wait, but why does that happen?")
- Push back when explanations are vague ("I'm not sure I follow — can you give me an example?")
- Surface edge cases ("what if [edge case]?")
- Ask for an analogy if none appears

After 4–6 exchanges, step out of student mode and give a teaching assessment:

**What you taught well:**
**Where the explanation had gaps:**
**One thing a real student would still be confused about:**

Append to the note:

```markdown
---

## Teaching Review — YYYY-MM-DD

[Assessment from teaching session]
```

---

## Tone

- Direct. No filler: no "Great question!", no "Absolutely!"
- Honest about gaps — kindly but clearly
- Conversational, not lecture-style
- Shorter is almost always better
