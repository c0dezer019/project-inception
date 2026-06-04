# User Story Formats

Reference for inception:discovery when generating or gathering user stories.

## Preferred Format: Free-Form Narrative

Free-form stories read naturally and communicate intent without forcing artificial
structure. They are easier to write, easier to read, and capture nuance that rigid
templates often lose.

### Guidelines for free-form stories

- Write from the perspective of the user, not the system
- Describe what the user wants to accomplish and why it matters
- Keep each story to 1-3 sentences
- Focus on outcomes, not implementation details
- Group related stories under a feature heading

### Examples

**Authentication**
- A new visitor can create an account using their email address so they can save
  their progress and return later.
- A returning user can log in without re-entering their full credentials if they
  choose to stay signed in.
- A user who forgets their password can request a reset link sent to their email.

**Dashboard**
- A user can see an overview of their recent activity as soon as they log in,
  giving them a quick sense of where things stand.
- A user can filter their dashboard view by date range to focus on a specific period.

**Admin**
- An admin can suspend a user account without permanently deleting it, preserving
  the data for potential review.
- An admin can export a CSV of all user activity for a given month.

---

## Alternative Format: "As a [Role]"

Use only when the user explicitly requests this format, or when the project involves
multiple distinct user roles that need clear delineation (e.g., Admin vs. End User
vs. Guest have fundamentally different capability sets).

### Structure
```
As a [role], I want to [action] so that [benefit/outcome].
```

### When it adds value
- Projects with 3+ distinct user roles with non-overlapping permissions
- Enterprise or regulated contexts where role-based traceability is required
- Teams that already use this format in their existing workflow

### When it adds noise
- Simple single-user apps (forces artificial "As a user" prefix on every story)
- Internal tools where all users have the same role
- Early-stage exploration where roles aren't yet defined

### Examples
```
As an admin, I want to export user activity logs so that I can audit access
for compliance reporting.

As a subscriber, I want to pause my subscription so that I'm not charged
during months I don't need the service.
```

---

## Story Quality Checklist

Before finalizing stories, verify:

- [ ] Each story represents a single user intent (not a multi-step process)
- [ ] Stories describe outcomes, not UI interactions ("see an overview" not "click the dashboard tab")
- [ ] No implementation details leak in ("via a REST endpoint", "using SQL query")
- [ ] Stories are testable — you could write an acceptance test for each one
- [ ] Custom features from the user are represented
- [ ] Edge cases and error states have stories where they matter (e.g., "A user who enters an invalid email sees a clear error message")

---

## Volume Guidelines

| Project Scale | Story Count |
|---|---|
| Personal/Hobby | 5–8 |
| Small Team / Startup | 8–15 |
| Production | 15–25 |
| Enterprise | 25+ (may require separate story mapping session) |

For Enterprise scale, note to the user that a full story mapping workshop may be
more appropriate than generating stories in this session.
