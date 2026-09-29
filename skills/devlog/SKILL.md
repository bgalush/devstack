---
name: devlog
description: "Creates and maintains repository devlogs: ADRs for decisions, PLANs for tracking work, DIGs for research. Use when recording architecture, design rationale, implementation plans, progress, findings, investigations, or other devlog updates. Required in Plan mode."
---

# Devlog

Keep durable engineering records in a repository-local `devlog/`:

| Type | Directory | Create when |
| :--- | :--- | :--- |
| ADR | `devlog/adr/` | The choice has lasting architectural, security, data-model, API, or operational impact; alternatives and trade-offs matter to future maintainers; reversing it needs migration or coordination. |
| PLAN | `devlog/plan/` | Work spans multiple steps, phases, subsystems, or releases; progress needs a stable summary above issue-level tasks; a migration or remediation effort needs tracked findings. |
| DIG | `devlog/dig/` | The work is researching a question: feasibility, option comparison, root cause, data collection, or a spike. No decision is being made and nothing is on a schedule. Sources and evidence matter to future readers, and the work is too substantial to be a footnote in an ADR. |

Records are always numbered `NNN` and named `short-kebab-case-title.md`, stored in the `devlog/` directory:

```text
devlog/adr/000-template.md
devlog/adr/NNN-short-kebab-case-title.md
devlog/plan/000-template.md
devlog/plan/NNN-short-kebab-case-title.md
devlog/dig/000-template.md
devlog/dig/NNN-short-kebab-case-title.md
devlog/artifacts/*
```

Sequences are independent per type, and local to the repository.

## Links and References

When referencing records in prose, use the hyphenated identifier everywhere: `ADR-012`, `PLAN-008`, `DIG-003` and link every reference with a relative path:

```markdown
[ADR-012](../adr/012-storage-layout.md)
```

Related records are linked via relationship fields in the record header. Use relative paths to link to related records.

| Field | Description |
| :--- | :--- |
| `Related:` | Non-ordering context, especially the ADR/PLAN pair for one initiative. |
| `Depends On:` | Prerequisite decisions or work that must hold first. |
| `Supersedes:` / `Superseded By:` | Revision of prior work, revised architecture, or new research. The reverse link belongs on the replaced record. |

## ADR

Short for Architecture Decision Record. Create an ADR *and* a PLAN when a lasting decision drives a substantial implementation: the ADR holds context, decision, alternatives, rationale, and consequences; the PLAN holds goal, phased work, progress, and findings. ADRs often rest on DIGs that justify the decision. Link all records with `Related:`.

**Template:** `<skill>/templates/adr/000-template.md`

| Status | Description |
| :--- | :--- |
| `Proposed` | Under consideration |
| `Accepted` | Decided, not yet implemented |
| `Declined` | Not accepted or related to cancelled work |
| `Implemented` | Shipped and representative of the system |

## PLAN

Plans track the progress of a high-level body of work, often associated with one or more ADRs. Do not create a PLAN to track a DIG, unless the research is substantial or requires supplementary work.

**Template:** `<skill>/templates/plan/000-template.md`

| Status | Description |
| :--- | :--- |
| `Not Started` | Work has not yet begun |
| `In Progress` | Work is currently being performed |
| `Complete` | Goal has been reached |
| `Abandoned` | Stopped intentionally without completion |

## DIG

DIGs capture research questions and findings. Do not create a new DIG for one-off web searches or simple data collection. When collecting research, prefer:

1. **Links:** Cite external sources (URLs, papers, tickets, links to data) in `## Sources` instead of vendoring them.
2. **Reproducible Steps:** Record the commands or procedure that reproduce the data or result. Prefer small `uv run`-able scripts with PEP 723 inline dependencies.
3. **Project-External Storage:** Use the object storage, data store, or dataset registry the project already relies on, and reference the exact location in the DIG.

Research may generate artifacts such as JSON or CSV blobs, screenshots, PDFs, visualizations, scripts, documentation, test results, or raw notes. Avoid committing large or binary files; if these absolutely must be committed, put them in the part of the repository that owns that kind of file, or under `devlog/artifacts/`. All files derived from or depended on by the DIG must be referenced in the DIG's `artifacts` frontmatter.

**Template:** `<skill>/templates/dig/000-template.md`

| Status | Description |
| :--- | :--- |
| `Open` | Not yet started, or is in progress |
| `Closed` | Research concluded, results recorded |

## Verify

- [ ] `NNN` was rechecked against filenames and headings immediately before writing.
- [ ] Filename, H1, and every reference use the same identifier, linked by relative path.
- [ ] Status is one value from the documented lifecycle and matches reality.
- [ ] Supersessions and amendments are linked both ways; devlog indexes are updated.
- [ ] Preserve history: Do not rewrite an old record as though the new state had always been true.
