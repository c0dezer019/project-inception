# SRS Template — IEEE 830

Use this template when `needs_srs` is true in the discovery context. Populate each
section using the `discovery` object. Sections marked [GENERATED] are auto-populated
from discovery data. Sections marked [USER] require input not yet collected — prompt
the user for these if missing.

---

# Software Requirements Specification
## Living Spec — [project_name]

**Version:** 0.1
**Last Modified:** [current date]
**Status:** Working Hypothesis

---

## Change Log

| Version | Date | Changes |
|---|---|---|
| 0.1 | [current date] | Initial spec generated from inception discovery |

---

> This is a living document. It reflects the current best understanding of
> the project and will be updated as the prototype reacts to reality.
> Use `inception:clarify` to revise assumptions and bump the version.

---

## Table of Contents

1. Introduction
2. Overall Description
3. Specific Requirements
4. External Interface Requirements
5. System Features
6. Non-Functional Requirements
7. Other Requirements

---

## 1. Introduction

### 1.1 Purpose [GENERATED]

This Software Requirements Specification describes the functional and non-functional
requirements for [project_name]. It is intended for [audience] and the development
team responsible for building the system.

### 1.2 Scope [GENERATED]

[project_name] is a [project_type] application that [problem_statement].

The system will provide the following core capabilities:
[bulleted list from core_features + custom_features]

The following are explicitly out of scope for this version:
[prompt user: "What is explicitly out of scope for v1?"]

### 1.3 Definitions, Acronyms, and Abbreviations [USER]

List any domain-specific terms, acronyms, or abbreviations used in this document.

| Term | Definition |
|---|---|
| [term] | [definition] |

### 1.4 References [USER]

List any external documents, standards, or systems referenced by this SRS.

### 1.5 Overview

The remainder of this document describes the overall system, specific requirements,
and constraints. Section 2 provides context. Section 3 details functional requirements.
Sections 4–6 cover interfaces, features, and non-functional requirements.

---

## 2. Overall Description

### 2.1 Product Perspective [GENERATED]

[project_name] is a [new standalone system / component of a larger system — clarify
with user if ambiguous]. It interfaces with [list any third-party services from
core_features — e.g., Stripe for payments, an email provider for notifications].

### 2.2 Product Functions [GENERATED]

At a high level, the system will:

[numbered list of core_features + custom_features, written as system capabilities]

### 2.3 User Classes and Characteristics [GENERATED + USER]

**Primary Users:** [audience from discovery]
[prompt if multiple user roles exist: "Are there multiple user roles with different
permission levels? e.g., Admin, Standard User, Guest"]

| User Class | Description | Technical Proficiency |
|---|---|---|
| [role] | [description] | [low/medium/high] |

### 2.4 Operating Environment [USER]

Describe the environment in which the software will operate:
- Target platforms (web browsers, mobile OS versions, desktop OS)
- Hardware requirements (if applicable)
- Dependencies on other software or systems

### 2.5 Design and Implementation Constraints [GENERATED + USER]

Known constraints at discovery time:
- Scale target: [scale]
- Team experience: [team_experience]
- Caching requirements: [caching_needs]

[prompt: "Are there any regulatory, compliance, or technology constraints we should
document? e.g., GDPR, HIPAA, must use existing company infrastructure"]

### 2.6 Assumptions and Dependencies [USER]

List assumptions made during requirements gathering and any external dependencies
the system relies on.

---

## 3. Specific Requirements

### 3.1 Functional Requirements [GENERATED from user stories]

Organize by feature area. For each feature, list requirements as numbered statements.

**Format:**
- REQ-[FEATURE]-[NUM]: [The system shall / The user shall be able to] [requirement]

**Example (Authentication):**
- REQ-AUTH-001: The system shall allow users to register with a valid email address and password.
- REQ-AUTH-002: The system shall send a verification email upon registration.
- REQ-AUTH-003: The system shall allow users to reset their password via email.

[Generate one section per feature in core_features + custom_features. Derive
requirements from user stories captured in discovery.]

### 3.2 User Story Traceability [GENERATED]

| Story | Requirement(s) |
|---|---|
| [story summary] | REQ-[FEATURE]-[NUM] |

---

## 4. External Interface Requirements

### 4.1 User Interfaces [USER]

Describe any UI requirements at a high level. Leave detailed UI specification
to the design phase.

> Note: Detailed UI/UX design will be addressed separately. A design handoff
> prompt will be generated at the end of the Design & Architecture phase.

### 4.2 Hardware Interfaces

Not applicable unless hardware integration is a stated requirement.

### 4.3 Software Interfaces [GENERATED]

List third-party software the system must integrate with, derived from core_features:

| Integration | Purpose | Notes |
|---|---|---|
| [e.g., Stripe] | Payments | Required for billing feature |
| [e.g., SendGrid] | Email | Required for transactional email |

### 4.4 Communication Interfaces [GENERATED]

- API Style: [to be determined in Design phase]
- Real-time: [WebSockets / SSE — if real-time is a core feature]
- Caching layer: [based on caching_needs]

---

## 5. System Features

For each feature in `core_features + custom_features`, create a subsection:

### 5.X [Feature Name]

**5.X.1 Description and Priority**
[Brief description]. Priority: [High / Medium / Low — prompt user if unclear]

**5.X.2 Stimulus/Response Sequences**
Describe the trigger and expected system response for primary interactions.

**5.X.3 Functional Requirements**
[Reference REQ-[FEATURE]-NNN items from Section 3]

---

## 6. Non-Functional Requirements

### 6.1 Performance [GENERATED + USER]

- Scale target: [scale]
- [prompt: "Are there specific performance benchmarks? e.g., page load < 2s,
  API response < 200ms, support N concurrent users"]

### 6.2 Security [USER]

- Authentication required: [yes/no based on Auth feature]
- [prompt: "Are there specific security requirements? e.g., OWASP compliance,
  data encryption at rest, penetration testing, RBAC"]

### 6.3 Reliability & Availability [USER]

- [prompt: "What is the acceptable downtime? e.g., 99.9% uptime SLA, maintenance
  windows allowed, disaster recovery required"]

### 6.4 Maintainability [GENERATED]

- Team experience level: [team_experience]
- Codebase should be structured to support onboarding at this level.

### 6.5 Portability [USER]

- [prompt: "Does the system need to run in multiple environments? e.g., on-premise
  + cloud, multiple cloud providers, specific OS requirements"]

---

## 7. Other Requirements

### 7.1 Legal and Compliance [USER]

- [prompt: "Are there regulatory requirements? e.g., GDPR, HIPAA, SOC 2, CCPA,
  accessibility (WCAG 2.1 AA)"]

### 7.2 Internationalization [USER]

- [prompt: "Does the product need to support multiple languages or locales?"]

---

## Appendix A: User Stories

[Full list of user stories captured in discovery, verbatim]

## Appendix B: Assumptions

Populated from Jordan's ambiguity detection. Each item was flagged as vague or
unresolvable during discovery and recorded with a concrete interpretation.
Include this appendix only if `assumptions[]` is non-empty.

| # | Assumption | Based On | Impact If Wrong | Revisit Trigger |
|---|---|---|---|---|
| 1 | [assumption] | [rationale] | [what changes] | [trigger event] |

## Appendix C: Open Questions

Populated from Jordan's ambiguity detection and gap-filling pass. These items
require stakeholder input before they can be resolved. Include only if
`open_questions[]` is non-empty.

Use `inception:clarify` to generate a prioritized stakeholder question list
from this appendix.

| # | Question | Priority | Blocks |
|---|---|---|---|
| 1 | [question] | High/Medium/Low | [what it blocks] |

---

*This document was generated during the Project Inception discovery phase using
the project-inception plugin. It should be reviewed and updated as requirements
evolve.*
