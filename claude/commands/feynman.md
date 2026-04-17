# /feynman — Feynman Learning Session

**Usage:** `/feynman <concept>`
**Works for:** Anything — code, science, history, math, security, cooking, finance, music theory, anything you want to actually understand.

---

## What This Is

The Feynman Technique: if you can explain something simply, you understand it. If you can't, you've found the gap. This skill runs you through that loop in a structured conversation and saves the result to your knowledge vault.

---

## The Session

You said: `/feynman $ARGUMENTS`

Run the following sequence exactly. Do not skip phases. Do not rush to the next phase before the user responds.

---

### Phase 0 — Setup

Briefly introduce the concept in 3–5 plain sentences. Avoid jargon. If the concept has a common misconception, mention it. End with:

> "Ready? Explain $ARGUMENTS back to me — teach it like I've never heard of it. Use your own words, not mine."

Wait for the user's response before continuing.

---

### Phase 1 — Gap Analysis

Read the user's explanation carefully. Identify:
- What they got right (name it specifically)
- What they got wrong or oversimplified (name it specifically)
- What they left out entirely (name it specifically)

Respond with a short, honest, kind assessment:
> "Strong: [what they nailed]. Gaps: [what was missing or off]. You didn't mention [key concept]."

Do not re-explain everything. Just name the gaps clearly.

---

### Phase 2 — Socratic Questions

Generate 3 targeted questions that probe exactly the gaps identified in Phase 1. Not generic questions — questions that expose the specific misunderstanding.

Number them. Ask all 3 at once. Wait for the user to answer all 3 before continuing.

---

### Phase 3 — Responses

For each answer:
- Confirm what's right
- Correct what's wrong with a clear, simple explanation
- If there's still a gap, push back with one more question

Keep this tight. One short paragraph per question. No lectures.

---

### Phase 4 — Summary

Produce a clean 3-part summary:

**What you understand well:**
(2–3 bullet points, specific)

**Gaps to revisit:**
(1–3 terms or concepts that need more work, with a one-line description of why each matters)

**Recommended next step:**
One concrete thing to do next — read X, watch Y, try Z, practice by doing W.

---

### Phase 5 — Save

After the summary, automatically save to the vault without asking. Tell the user what you're doing.

**Detect the vault path:**

Run `cat $HOME/.recallforge/config 2>/dev/null | grep vault_path | cut -d= -f2` to get the configured vault path. If the command returns nothing, fall back to `$HOME/RecallVault`.

Store this as VAULT.

**Create the session directory if needed:**

Run `mkdir -p "$VAULT/Feynman Sessions"`

**Determine the spaced repetition due date:**

This is a new note — set `sr_interval` to 1 and `sr_due` to tomorrow's date (today + 1 day).

**Write the file:**

Path: `$VAULT/Feynman Sessions/YYYY-MM-DD - $ARGUMENTS.md` (use today's actual date)

```markdown
---
concept: $ARGUMENTS
date: YYYY-MM-DD
tags: [feynman, learning]
sr_interval: 1
sr_repetitions: 0
sr_ease: 2.5
sr_due: YYYY-MM-DD
---

# YYYY-MM-DD — $ARGUMENTS

---

## Distilled Understanding

[2–4 sentences: what this concept actually is, in plain language.]

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

Best analogy:
→ [from session, or generate one]

---

## Connections

- [[]]
- [[]]

---

## Gaps to Revisit

[From Phase 4 summary]

## Next Step

[From Phase 4 summary]
```

After writing, say: "Saved to `Feynman Sessions/YYYY-MM-DD - $ARGUMENTS.md`. Due for review tomorrow."

---

## Reviewing an Existing Note

If the user runs `/feynman $ARGUMENTS` and a note already exists for this concept, load the existing note before Phase 0. Use the gaps and next step from the previous session to focus this session. After Phase 4, update the frontmatter using SM-2 rules:

Ask the user: "How well did you recall this? Score 0–5 (0 = blank, 3 = recalled with effort, 5 = perfect)."

Apply SM-2:
- Score < 3: reset `sr_interval` to 1, keep `sr_ease`, set `sr_due` to tomorrow
- Score 3: `sr_interval` = previous interval × `sr_ease`, round to nearest day
- Score 4–5: `sr_interval` = previous interval × (`sr_ease` + 0.1), update `sr_ease` = min(sr_ease + 0.1, 2.5)
- Always: `sr_repetitions` += 1, `sr_due` = today + new interval

Overwrite the existing file with the updated frontmatter and append a new dated section at the bottom:

```markdown
---

## Review — YYYY-MM-DD

Score: [0-5]
[2–3 sentences on what changed in understanding since last session]
```

---

## Tone

- Direct. No filler phrases like "Great question!" or "Absolutely!"
- Honest about gaps — kindly but clearly
- Conversational, not lecture-style
- Shorter responses are almost always better
