## Structure

- Plugin manifest: `.claude-plugin/plugin.json`
- Skills: `skills/inception:[phase]/SKILL.md` — colon in directory name is intentional
- Each skill has `references/` subdirectory for supporting docs the skill reads at runtime

## Skill Phases

Three phases, run in order for new projects:
1. `inception:discovery` — scope, audience, features, user stories → README draft
2. `inception:design` — stack, folder structure, ERD, data flow, UI/UX handoff prompt
3. `inception:feature-plan` — reentrant; plans feature deltas against existing codebases

Context flows forward: discovery produces a `discovery` object; design extends it into `design`; feature-plan discovers the target project from its filesystem.

## Conventions

- Skills are planning-only — never write to user's project files
- `inception:feature-plan` treats target project's `CLAUDE.md` as source of truth; README as fallback
- `AskUserQuestion` max 4 options per call (platform constraint) — follow up if "Other" selected
- State complexity is flagged in discovery/design but never prescribed — defer to dev phase
- ERD output: Mermaid format only
- SRS output: IEEE 830 format only

## Sensitive Areas

- `skills/inception:feature-plan/references/delta-planning.md` — governs risk assessment logic; changes affect all feature plans
- `skills/inception:design/references/stack-recommendations.md` — governs all stack recommendations; keep opinionated but context-aware
