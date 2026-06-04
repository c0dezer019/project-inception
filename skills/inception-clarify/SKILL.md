---
name: inception-clarify
description: >
  Assumption refinement tool. Use when the developer wants to revisit, challenge,
  or update inferences Jordan made during discovery. Can be run at any point —
  post-discovery, mid-development, after Alex's research returns new signals,
  or after a prototype reveals something unexpected. Updates the living spec and
  assumptions log in place. Trigger phrases: "revisit assumptions", "refine
  requirements", "update the spec", "Jordan inferred wrong", "I want to change X",
  "the prototype showed something different", "rethink the scope".
metadata:
  version: "0.6.0"
---

# inception-clarify — Assumption Refinement

Revisit and update Jordan's inferences. Update living spec and assumptions log.
Never a blocker — always an iterator.

## Purpose

Jordan infers confidently during discovery to keep momentum. Those inferences
are working hypotheses, not permanent decisions. inception-clarify is how the
developer refines those hypotheses as understanding improves — from prototype
feedback, new research, changed scope, or simply a clearer idea.

## Input

Accepts any of:
- Specific assumption to revisit ("the inference about refund flow is wrong")
- New information to incorporate ("the prototype showed users want X not Y")
- Research findings (Alex's market report surfaced something relevant)
- Scope change ("we're dropping [feature]" or "adding [feature]")
- Full re-review ("walk me through all assumptions")

If no context object present, ask:
"Give me a quick summary of where the project stands and what you want to revisit."

## Refinement Modes

### Mode 1 — Targeted update
User names a specific assumption or requirement to change.

1. Show the current assumption:
   ```
   Current: [assumption]
   Inferred from: [original reasoning]
   ```
2. Ask: "What should it be instead? Or tell me what you learned."
3. Accept free-text. Apply inference engine if still vague.
4. Update assumption in place, log the change:
   ```
   Updated: [new interpretation]
   Reason for change: [what prompted the update]
   Previous: [old assumption — kept for reference]
   ```
5. Flag downstream impact: "This change affects [ERD / stack / folder structure].
   Riley should revisit [specific section] when you're ready."

### Mode 2 — Research incorporation
Alex's findings prompt assumption updates.

1. Scan research findings for signals that contradict or strengthen current assumptions
2. For each relevant signal, propose an update:
   > "Alex found that [research finding]. This suggests [current assumption]
   > might need updating to [refined version]. Want to apply that?"
3. Apply on confirmation. Log as research-driven update.

### Mode 3 — Prototype feedback
Post-build iteration based on what the prototype revealed.

1. Ask: "What did the prototype show that was different from what we assumed?"
2. Accept free-text description of the delta
3. Map each delta back to affected assumptions
4. Update each one, flag downstream impact
5. If scope changes are significant, suggest re-running inception-design
   for affected sections

### Mode 4 — Full review
Walk through all assumptions one by one.

Present each assumption:
```
[#] [assumption]
    From: [original reasoning]
    Keep / Update / Remove?
```
Process each response. Apply inference engine to any vague updates.
Produce updated assumptions log at the end.

## Living Spec Update

After any refinement, update the relevant sections of the living spec:
- Bump the spec version (v0.1 → v0.2, etc.)
- Update last-modified date
- Add entry to the change log section:
  ```
  v[N] — [date]: [what changed + why]
  ```
- Update affected sections in place

## Output

After refinement session, show:
```
REFINEMENT COMPLETE
─────────────────────────────────────────
Assumptions updated: [count]
Assumptions removed: [count]
Spec version:        [new version]
Downstream impact:   [list of artifacts that may need updating]
─────────────────────────────────────────
```

If downstream impact is significant (schema changes, stack changes, folder
structure changes), prompt:
> "These changes are significant enough that you may want to re-run
> inception-design for [affected sections]. Want to do that now?"
