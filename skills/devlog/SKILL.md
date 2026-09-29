---
name: devlog
description: "Creates and maintains repository devlogs: ADRs for decisions, PLANs for tracking work, DIGs for research. Use when recording architecture, design rationale, implementation plans, progress, findings, investigations, or other devlog updates. Required in Plan mode."
---

# Devlog

Keep durable engineering context in a repository-local `devlog/`:

| Type | Stored in | Create when |
|---|---|---|
| ADR | `devlog/adr/` | The choice has lasting architectural, security, data-model, API, or operational impact; alternatives and trade-offs matter to future maintainers; reversing it needs migration or coordination. |
| PLAN | `devlog/plan/` | Work spans multiple steps, phases, subsystems, or releases; progress needs a stable summary above issue-level tasks; a migration or remediation effort needs tracked findings. |
| DIG | `devlog/dig/` | The work is researching a question: feasibility, option comparison, root cause, data collection; A spike. No decision is being made. Sources and evidence matter to future readers. The work is too substantial to be a footnote in an ADR. |

Read the repository's `AGENTS.md` and `devlog/<type>/000-template.md` before writing. If it ships none, use the bundled templates at `templates/<type>/000-template.md` relative to this skill.

Create an ADR *and* a PLAN when a lasting decision drives a substantial implementation: the ADR holds context, decision, alternatives, rationale, and consequences; the PLAN holds goal, phased work, progress, and findings. ADRs often supply DIGs to justify the decision. Link all records with `Related:`.

Create no record for routine implementation details that code and tests explain better.

## Numbers, Paths, and References

```text
devlog/adr/000-template.md
devlog/adr/NNN-short-kebab-case-title.md
```

The same layout applies to `plan/` and `dig/`. Numbers are independent, repository-local sequences, one per type.

To allocate `NNN`: list the target directory, take the highest number among names beginning with exactly three digits and `-`, and add one, keeping the three-digit padding. Never derive it from the file count and never reuse a retired or superseded number. Search filenames and H1 headings for the proposed number, and recheck immediately before writing so concurrent work cannot collide.

When referencing records, use the hyphenated identifier everywhere: `ADR-012`, `PLAN-008`, `DIG-003` — not `ADR 012` or a bare number, and link every reference with a relative path:

```markdown
[ADR-012](../adr/012-storage-layout.md)
```

For a decision spanning repositories, keep one canonical record in the repository that owns the decision and link to it from consumers.

## Header Fields

`Status:` is required on every record and takes a single value; never composites such as `Not Started (superseded)` or release-specific states such as `Deferred post-1.0`.

- **ADR** `Proposed` (under consideration) → `Accepted` (decided, not yet implemented) → `Implemented` (shipped and representative); or `Superseded by [ADR-NNN](./NNN-title.md)` (no longer relevant, retained for history).
- **PLAN** `Not Started` → `In Progress` → `Complete`; or `Abandoned` (stopped intentionally without completion); or `Superseded by [PLAN-NNN](./NNN-title.md)`.
- **DIG** `Open` (ongoing or inconclusive) → `Closed`, with the outcome stated in the body and linked to ADRs/PLANs where applicable.

Relationship fields take relative Markdown links and have distinct meanings.

- `Related:` Non-ordering context, especially the ADR/PLAN pair for one initiative.
- `Depends On:` Prerequisite decisions or work that must hold first.
- `Supercedes` / `Superseded By:` Replacement of prior work, revised architecture, new research. The reverse link goes on the amended record.

## Templates

Use `templates/<type>/000-template.md` relative to this skill when no alternative is supplied. Remove fields and sections that do not apply. Beyond the template:

- **ADR** — rationale must compare credible alternatives, not restate the decision; consequences must include costs and follow-up work as well as benefits.
- **PLAN** — `Goal` defines observable completion; keep `Work` at a high level and leave short-lived implementation detail to the project's issue tracker.
- **DIG** — `Question:` is required and `Sources` is strongly encouraged (URLs, papers, tickets, links to data). On closing, add an `Outcome:` header line or a final `## Outcome` section stating how the question was resolved and linking any ADR/PLAN it fed into.

## Artifacts

`## Sources` holds citations only; never commit files for them. Artifacts are files committed
alongside the `DIG-*.md` itself: data dumps, JSON or CSV blobs, screenshots, PDFs, raw notes.
Committing large or binary files permanently grows clone and history size, so prefer, in
order:

1. **Links.** Cite external sources in `## Sources` instead of vendoring them.
2. **Reproducible steps.** Record the commands or procedure that reproduce the data or
   result. When the project's own toolchain is not a safe assumption, prefer a small
   `uv run`-able script with PEP 723 inline dependencies over committing raw output.
3. **Project-external storage.** Use the object storage, data store, or dataset registry the
   project already relies on, and reference the exact location in the DIG.

If an artifact absolutely must be committed, put it in the part of the repository that owns
that kind of file (fixtures, test data, documentation) or under `devlog/artifacts/`, and list
every committed artifact by repository-root-relative path in `Artifacts:` — a field you omit
when there are none.

## Maintenance Workflow

When implementation changes a decision or plan:

1. Update progress and durable findings while the work is active; keep the record honest and never mark incomplete work complete.
2. Amend an ADR for a compatible partial change.
3. For a replacement, create a new ADR or PLAN, mark the old record superseded, and cross-link both directions.
4. Preserve history: do not rewrite an old record as though the new decision had always been true.
5. Update any repository-maintained devlog index or design-status summary.

## Verify

- [ ] `NNN` was rechecked against filenames and headings immediately before writing.
- [ ] Filename, H1, and every reference use the same identifier, linked by relative path.
- [ ] Status is one value from the documented lifecycle and matches reality.
- [ ] Supersessions and amendments are linked both ways; devlog indexes are updated.
