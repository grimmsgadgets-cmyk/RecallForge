# /calibration-report — RecallForge Confidence Calibration Analysis

Analyzes all notes with prediction vs actual score data to surface where you consistently overestimate or underestimate your understanding. Turns raw frontmatter data into an actionable feedback loop.

---

## Steps

**1. Detect the vault path**

Run `cat $HOME/.recallforge/config 2>/dev/null | grep vault_path | cut -d= -f2`. If empty, fall back to `$HOME/RecallVault`.

**2. Collect calibration data**

Scan all `.md` files in `$VAULT/Feynman Sessions/` for notes containing both `confidence_predicted` and `confidence_actual` in frontmatter. Skip notes where either field is `"not given"`, `"pending"`, or missing.

Also parse `## Review` sections for lines matching `Score: [N] | Predicted: [N] | Delta: [N]`.

Build a dataset: `[concept, predicted, actual, delta, tags, date]`.

If fewer than 5 data points: "Not enough calibration data yet — need at least 5 scored sessions. Keep reviewing." Stop.

**3. Spawn calibration analysis agent**

Spawn an Agent with this prompt:

> "You are an analyst reviewing a learner's self-assessment accuracy. You have been given a dataset of concepts with predicted recall scores (0–5) and actual recall scores (0–5).
>
> Dataset:
> [paste the dataset]
>
> Analyze:
> 1. **Overall calibration** — are they systematically overconfident, underconfident, or well-calibrated?
> 2. **Domain-level patterns** — which topic tags show the largest consistent gaps between predicted and actual?
> 3. **Overconfidence zones** — specific concepts or domain tags where predicted > actual by 2+ consistently
> 4. **Underconfidence zones** — where they undersell themselves (fine, but worth noting)
> 5. **Trend over time** — is calibration improving (delta shrinking over time) or stable?
> 6. **Recommendation** — one concrete change to how they should approach learning in their overconfidence zones
>
> Be specific about domain patterns. 'You tend to overestimate security concepts' is useful. 'You are somewhat miscalibrated' is not."

**4. Write the report**

Run `mkdir -p "$VAULT/Session Logs"`

Write to `$VAULT/Session Logs/calibration-YYYY-MM-DD.md`:

```markdown
---
date: YYYY-MM-DD
data_points: [N]
tags: [calibration, meta-learning]
---

# Calibration Report — YYYY-MM-DD

Data points analyzed: [N] scored sessions

---

## Overall Calibration

[From analysis agent]

---

## Overconfidence Zones

[Domains/concepts where predicted consistently exceeds actual]

---

## Underconfidence Zones

[Where you undersell yourself — less critical but noted]

---

## Domain Breakdown

[Table or list: domain tag | avg predicted | avg actual | avg delta]

---

## Trend

[Is calibration improving over time?]

---

## Recommendation

[One concrete change based on the data]
```

**5. Done**

Say: "Calibration report saved to `Session Logs/calibration-YYYY-MM-DD.md`. [N] sessions analyzed." Surface the top overconfidence zone inline: "Watch out for [domain] — you're consistently rating yourself higher than you perform there."

---

## Tone

Data-first. No cheerleading. The point is to surface uncomfortable patterns, not validate the learner.
