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
- `devlog/dig/` records investigations: open questions, research, data collection, and the
  evidence gathered around them — with no decision required and no deliverable to ship.

An ADR is not a work tracker, and a PLAN is not a substitute for design rationale. Significant
work often needs both, cross-linked to each other. A DIG is the most open-ended of the three:
it answers (or fails to answer) a question, and inconclusive is a legitimate outcome.

## Follow Repository-Local Rules First

Before creating or updating a record:

1. Read the repository's `AGENTS.md` and any more specific instructions.
2. Read the repository's `devlog/adr/000-template.md`, `devlog/plan/000-template.md`, or
   `devlog/dig/000-template.md`. If the repository does not ship one, use the bundled
   fallback template at `templates/adr/000-template.md`, `templates/plan/000-template.md`,
   or `templates/dig/000-template.md` relative to this skill.
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
- A migration, remediation, or conformance effort needs tracked findings.

Create a DIG when:

- The primary work is answering a question: feasibility research, option comparison,
  root-cause investigation, data collection, or a spike that needs findings recorded.
- No decision is being made and no deliverable is on a schedule.
- Sources, evidence, or collected data matter to future readers.

Create both when a lasting decision drives a substantial implementation:

- ADR: context, decision, alternatives, rationale, consequences.
- PLAN: goal, phased work, progress, findings.
- Link each record to the other with `Related:`.

A DIG often precedes an ADR or PLAN it informs. Link it with `Related:` and, when the
investigation converts into a decision or plan, say so in its outcome and link both ways.

Do not create any record for routine implementation details that the code and tests explain
more clearly.

## Paths, Numbers, and References

Use:

```text
devlog/
  adr/000-template.md
  adr/NNN-short-kebab-case-title.md
  plan/000-template.md
  plan/NNN-short-kebab-case-title.md
  dig/000-template.md
  dig/NNN-short-kebab-case-title.md
```

ADR, PLAN, and DIG numbers are independent, repository-local sequences.

To allocate `NNN`:

1. List filenames in the target directory.
2. Consider only names beginning with exactly three digits and `-`; ignore `000-template.md`.
3. Find the highest number and add one, preserving three-digit zero padding.
4. Search for the proposed number in filenames and H1 headings.
5. Recheck immediately before writing so concurrent work does not create a collision.

Never derive the next number from file count: gaps, templates, and existing collisions make
counts unreliable. Never reuse a retired or superseded number.

Use the hyphenated identifier everywhere: `ADR-NNN`, `PLAN-NNN`, and `DIG-NNN`. Link
references when a target is available:

```markdown
[ADR-012](../adr/012-storage-layout.md)
[PLAN-008](../plan/008-storage-migration.md)
[DIG-003](../dig/003-postgres-index-survey.md)
```

## Header Fields

Every record requires `Status:` using the repository's lifecycle.

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

DIG:

- `Open`: investigation is ongoing or inconclusive.
- `Closed`: investigation has concluded. The outcome — resolved, inconclusive, superseded,
  or converted into an ADR/PLAN — is stated in the body and linked where applicable.

Use one status, not composites such as `Not Started (superseded)` or release-specific states
such as `Deferred post-1.0`. Explain scheduling details in the body.

## Fallback ADR Template

The fallback ADR template is `templates/adr/000-template.md` relative to this skill; it is
the single source of truth for the fallback shape. Remove optional fields that do not
apply. The rationale should compare credible alternatives,
not merely restate the decision. Consequences should include costs and follow-up work as well
as benefits.

## Fallback DIG Template

The fallback DIG template is `templates/dig/000-template.md` relative to this skill; it is
the single source of truth for the fallback shape. Remove sections that do not apply;
`Question:` is required and `Sources` is strongly encouraged (URLs, papers, tickets, links to data). On closing, add an `Outcome:` line to
the header or a final `## Outcome` section stating how the question was resolved and
linking any ADR/PLAN it fed into.

## DIG Artifacts

DIG evidence falls into two distinct categories:

- `## Sources` are citations: URLs, papers, tickets, external references. They are never
  committed as files; they are references.
- **Artifacts** are files, data, and scripts that must be committed alongside the
  `DIG-*.md` itself: data dumps, JSON or CSV blobs, screenshots, PDFs, raw notes.

Committing large or binary files to a repository is a lasting cost
(clone size, history size, review noise), so the decision to do so must be made carefully,
not by default. Prefer, in order:

1. **Links.** Cite external sources in `## Sources` instead of vendoring them.
2. **Reproducible steps.** Record the commands or procedure that reproduce the data or
   result. When the project's own toolchain is not a safe assumption, prefer a small
   `uv run`-able script with PEP 723 inline dependencies over committing raw output.
3. **Project-external storage.** Use the object storage, data store, or dataset registry
   the project already relies on, and reference the exact location in the DIG.

If an artifact absolutely must be committed, place it either in the part of the repository
that owns it (fixtures, test data, documentation) or under `devlog/artifacts/`, and
reference it explicitly in the DIG via the `Artifacts:` field, which lists the
repository-root-relative path of every committed artifact. Omit `Artifacts:` when the DIG
has no committed artifacts.

## Fallback PLAN Template

The fallback PLAN template is `templates/plan/000-template.md` relative to this skill; it
is the single source of truth for the fallback shape. Remove optional fields and an empty
`Findings` section. Define observable completion in
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

1. Using one sequence across ADRs, PLANs, and DIGs instead of independent sequences.
2. Choosing the next number from record count without checking filenames and headings.
3. Mixing `ADR NNN`, `ADR-NNN`, and bare numbers in new references.
4. Treating `Accepted` and `Implemented` as synonyms.
5. Recording implementation checklists in an ADR or architectural rationale only in a PLAN.
6. Forcing an undecided question into an ADR instead of opening a DIG, or closing a DIG
   without stating its outcome.
7. Duplicating a cross-repository decision instead of linking to its canonical owner.
8. Adding relationships without relative links or clear semantics.
9. Updating code while leaving an active PLAN or design-status index stale.

## Verification Checklist

- [ ] Repository-local instructions and the relevant `000-template.md` were read.
- [ ] ADR versus PLAN versus DIG choice matches the record's purpose.
- [ ] The number is the next available value in the correct independent sequence.
- [ ] Filename, H1, and all references use the same `ADR-NNN` or `PLAN-NNN`.
- [ ] Status accurately reflects the documented lifecycle.
- [ ] Relationship fields are meaningful, linked, and reciprocal where required.
- [ ] ADR rationale covers alternatives and trade-offs, PLAN goal defines completion, or DIG
      states its question and, if closed, its outcome with sources cited.
- [ ] DIG evidence prefers links, reproducible steps, or external storage; any committed
      artifacts live in an owned location and are listed in `Artifacts:`.
- [ ] Progress, findings, replacement records, and indexes were updated where applicable.
- [ ] Historical records were preserved unless migration was explicitly requested.
