---
name: devlog
description: "Creates and maintains repository devlogs with ADRs for durable decisions and PLANs for high-level bodies of work. Use when recording architecture, design rationale, implementation plans, progress, findings, supersession, or other devlog updates."
---

# Devlog

## Overview

Keep durable engineering context in a repository-local `devlog/`:

- `devlog/adr/` records architectural decision records: why a lasting choice was made.
- `devlog/plan/` records implementation plans: what a high-level body of work entails and
  how it progresses.

An ADR is not a work tracker, and a PLAN is not a substitute for design rationale. Significant
work often needs both, cross-linked to each other.

## Follow Repository-Local Rules First

Before creating or updating a record:

1. Read the repository's `AGENTS.md` and any more specific instructions.
2. Read `devlog/adr/000-template.md` or `devlog/plan/000-template.md`.
3. Inspect several recent records of the same type.
4. Follow repository-local fields, statuses, and terminology when they are stricter than this
   skill.

Use the fallback convention below only when the repository does not define one. Do not
silently migrate historical records to this convention.

## Choose the Record Type

Create an ADR when:

- A choice has lasting architectural, security, data-model, API, or operational impact.
- Alternatives and trade-offs are important to future maintainers.
- Reversing the choice would require meaningful migration or coordination.

Create a PLAN when:

- Work spans multiple steps, phases, subsystems, or releases.
- Progress and discoveries need a stable summary above issue-level tasks.
- A spike, migration, remediation effort, or conformance effort needs tracked findings.

Create both when a lasting decision drives a substantial implementation:

- ADR: context, decision, alternatives, rationale, consequences.
- PLAN: goal, phased work, progress, findings.
- Link each record to the other with `Related:`.

Do not create either record for routine implementation details that the code and tests explain
more clearly.

## Paths, Numbers, and References

Use:

```text
devlog/
  adr/000-template.md
  adr/NNN-short-kebab-case-title.md
  plan/000-template.md
  plan/NNN-short-kebab-case-title.md
```

ADR and PLAN numbers are independent, repository-local sequences.

To allocate `NNN`:

1. List filenames in the target directory.
2. Consider only names beginning with exactly three digits and `-`; ignore `000-template.md`.
3. Find the highest number and add one, preserving three-digit zero padding.
4. Search for the proposed number in filenames and H1 headings.
5. Recheck immediately before writing so concurrent work does not create a collision.

Never derive the next number from file count: gaps, templates, and existing collisions make
counts unreliable. Never reuse a retired or superseded number.

Use the hyphenated identifier everywhere: `ADR-NNN` and `PLAN-NNN`. Link references when a
target is available:

```markdown
[ADR-012](../adr/012-storage-layout.md)
[PLAN-008](../plan/008-storage-migration.md)
```

## Header Fields

Every record requires:

- `Status:` using the repository's lifecycle.
- `Version:` derived from the repository's current release/versioning convention.

Do not invent a version, use `unknown`, or assume `v0.0.0`. Determine it from tags, release
files, build configuration, or repository instructions. If it cannot be determined, ask.

Optional relationship fields have distinct meanings:

- `Depends on:` prerequisite decisions or work that must hold first.
- `Related:` non-ordering context, especially the ADR/PLAN pair for one initiative.
- `Amends:` a prior ADR modified in part while its unaffected decisions remain valid.
- `Amended by:` the reverse link added to the amended ADR when local practice uses it.
- `Superseded by:` complete replacement. Prefer the repository's established representation;
  the fallback templates put the linked replacement in `Status:`.

Use relative Markdown links. Do not use relationship fields as an unstructured list of vaguely
similar documents.

For a decision spanning repositories, keep one canonical record in the repository that owns
the decision and link to it from consumers. Do not copy the full record or reuse its number in
another repository.

## Fallback Status Lifecycles

When no local lifecycle exists, use:

ADR:

- `Proposed`: under consideration.
- `Accepted`: decided but not fully implemented.
- `Implemented`: shipped and representative of the system.
- `Deprecated`: retained for history but no longer recommended.
- `Superseded by [ADR-NNN](NNN-title.md)`: completely replaced.

PLAN:

- `Not Started`: scoped but execution has not begun.
- `In Progress`: active work remains.
- `Complete`: goal and required work are done.
- `Abandoned`: intentionally stopped without completion.
- `Superseded by [PLAN-NNN](NNN-title.md)`: replaced by another plan.

Use one status, not composites such as `Not Started (superseded)` or release-specific states
such as `Deferred post-1.0`. Explain scheduling details in the body.

## Fallback ADR Template

```markdown
# ADR-NNN: Title

Status: Proposed
Version: vX.Y.Z
Related: [PLAN-NNN](../plan/NNN-title.md)

## Context

## Decision

## Rationale

## Consequences
```

Remove optional fields that do not apply. The rationale should compare credible alternatives,
not merely restate the decision. Consequences should include costs and follow-up work as well
as benefits.

## Fallback PLAN Template

```markdown
# PLAN-NNN: Title

Status: Not Started
Version: vX.Y.Z
Related: [ADR-NNN](../adr/NNN-title.md)

## Goal

## Work

### Phase 1: Title

**Status:** Not Started

## Findings
```

Remove optional fields and an empty `Findings` section. Define observable completion in
`Goal`. Keep `Work` at a high level; use the project's issue tracker or task system for
short-lived implementation details.

## Maintenance Workflow

When implementation changes a decision or plan:

1. Update progress and durable findings while the work is active.
2. Keep the record honest when scope changes; do not mark incomplete work complete.
3. Amend an ADR for a compatible partial change.
4. Create a new ADR or PLAN for a replacement, mark the old record superseded, and cross-link
   both directions.
5. Preserve historical context. Do not rewrite an old record as though the new decision had
   always been true.
6. Update any repository-maintained devlog index or design-status summary.

## Common Pitfalls

1. Using one sequence across ADRs and PLANs instead of independent sequences.
2. Choosing the next number from record count without checking filenames and headings.
3. Mixing `ADR NNN`, `ADR-NNN`, and bare numbers in new references.
4. Treating `Accepted` and `Implemented` as synonyms.
5. Recording implementation checklists in an ADR or architectural rationale only in a PLAN.
6. Duplicating a cross-repository decision instead of linking to its canonical owner.
7. Adding relationships without relative links or clear semantics.
8. Updating code while leaving an active PLAN or design-status index stale.

## Verification Checklist

- [ ] Repository-local instructions and the relevant `000-template.md` were read.
- [ ] ADR versus PLAN choice matches the record's purpose.
- [ ] The number is the next available value in the correct independent sequence.
- [ ] Filename, H1, and all references use the same `ADR-NNN` or `PLAN-NNN`.
- [ ] Status accurately reflects the documented lifecycle.
- [ ] Version was derived from repository evidence.
- [ ] Relationship fields are meaningful, linked, and reciprocal where required.
- [ ] ADR rationale covers alternatives and trade-offs, or PLAN goal defines completion.
- [ ] Progress, findings, replacement records, and indexes were updated where applicable.
- [ ] Historical records were preserved unless migration was explicitly requested.
