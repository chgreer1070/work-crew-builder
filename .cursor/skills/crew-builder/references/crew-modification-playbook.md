# Crew Modification Playbook

This playbook governs changes to an existing Cursor work crew. It is a
transactional safety contract: inspect first, preview the exact impact, obtain
explicit approval, apply only that preview, reconcile, validate, and hand off.
The active crew autonomy level never bypasses these steps.

## Contents

1. [Safety contract](#1-safety-contract)
2. [Modification lifecycle](#2-modification-lifecycle)
3. [Preflight inspection](#3-preflight-inspection)
4. [Exact impact preview and approval](#4-exact-impact-preview-and-approval)
5. [Apply](#5-apply)
6. [Reconcile](#6-reconcile)
7. [Operation workflows](#7-operation-workflows)
8. [Cross-file update matrix](#8-cross-file-update-matrix)
9. [Deterministic validation checklist](#9-deterministic-validation-checklist)
10. [Before/after handoff](#10-beforeafter-handoff)

## 1. Safety Contract

### 1.1 Approval Is Invariant

- Preflight and preview are read-only. Do not create directories, temporary
  files, backups, status entries, or any other artifact before approval.
- Show the user an exact impact preview and receive explicit approval after the
  preview before any create, update, move, archive, or delete.
- LOW, MEDIUM, and HIGH autonomy all use the same mutation gate. An autonomy
  choice, old approval, or broad instruction is not approval of a new preview.
- Approval covers only the paths, before-state hashes, after-state content, and
  actions in that preview. Any drift or scope change invalidates approval.
- Permanent deletion requires separate explicit confirmation naming the exact
  paths, even when the broader modification was approved.

### 1.2 Workspace-Relative Allowlist

Use normalized POSIX paths relative to the workspace root. Reject absolute
paths, `..`, empty path components, control characters, and paths that
normalize outside the workspace.
Agent slugs must match `[a-z0-9]+(?:-[a-z0-9]+)*`; archive names must match
`[0-9]{8}T[0-9]{6}Z-<agent-slug>.md`.

Normal crew modifications may touch only exact paths selected from:

| Path | Allowed purpose |
|---|---|
| `.cursor/agents/` | Modern custom-subagent directory; create only when previewed |
| `.cursor/agents/<agent-slug>.md` | Modern Cursor custom-subagent definition |
| `AGENTS.md` | Canonical roster, task ownership, and active autonomy |
| `STATUS.md` | Active-work ownership and append-only status records |
| `MYCREW.md` | Human-facing projection of the crew |
| `project-context/`, `project-context/archive/`, `project-context/archive/agents/` | Archive parent directories; create only when previewed |
| `project-context/archive/agents/<archive-name>.md` | Retired agent archive outside agent discovery |

An exact legacy persona source at `.cursor/rules/<name>.md` may be moved only
as part of an explicitly previewed rebuild migration. It is never a modern
agent destination. `.cursor/rules/crew-sop.mdc` and builder skill files are
read-only inputs to ordinary add, remove, modify, reassign, autonomy, and
rebuild operations; package upgrades are separate work with their own preview.

For every candidate path:

1. Inspect the path and each existing parent without following symlinks.
2. Confirm its canonical location remains under the workspace root.
3. Stop if any component is a symlink or a non-regular file where a regular
   file or directory is expected.
4. Stop on case-insensitive slug duplication, an occupied destination, an
   existing archive name, or any other path collision.
5. Stop when ownership is unknown. A file is crew-owned only when current crew
   records identify it or the user explicitly adopts it; filesystem ownership
   alone is not sufficient.

Never overwrite, merge, rename, or choose a new destination automatically to
work around a stop condition. Resolve it with the user and produce a new
preview.

### 1.3 Preservation Rules

- Treat content as user-authored unless a known builder baseline proves
  otherwise. Preserve it byte-for-byte outside explicitly approved hunks.
- Preserve all `STATUS.md` history. Reassignment may change owner fields in
  active rows and may append a new record, but must not regenerate, truncate,
  reorder, or scrub historical rows.
- Preserve stable task identifiers through rewording, reprioritization,
  reassignment, and rebuild; assign a new identifier only to genuinely new work.
- Preserve user-authored sections in `AGENTS.md`, `MYCREW.md`, and agent files.
  If managed boundaries cannot be identified safely, stop for user direction.
- Archive retired files byte-for-byte. Record provenance in the handoff rather
  than injecting metadata into the archived content.

## 2. Modification Lifecycle

Every operation uses this order:

1. **Preflight** — inspect the current state without writing.
2. **Preview** — render the complete desired state and exact mutation manifest.
3. **Approve** — obtain explicit user approval of that manifest.
4. **Apply** — verify no drift, then perform only approved mutations.
5. **Reconcile** — align projections and references without expanding scope.
6. **Validate** — run every applicable deterministic check.
7. **Hand off** — report before/after state, preservation, and validation.

If reconciliation discovers a needed write that was not previewed, stop. It is
a new change, not permission to expand the approved operation.

## 3. Preflight Inspection

Build a read-only inventory of:

- `AGENTS.md`: roster, stable agent identifiers, task ownership, standing
  instructions, user-authored sections, and every autonomy declaration.
- `STATUS.md`: complete content plus active, pending, notification, completed,
  and summary rows. Retain a byte-level snapshot for preservation checks.
- `MYCREW.md`: displayed roster, task map, and autonomy projection.
- Direct `.cursor/agents/*.md` files: path, custom-subagent metadata, role,
  tasks, references, and content hash.
- Legacy `.cursor/rules/*.md` persona files: report them as migration
  candidates; do not count them as modern custom subagents.
- `project-context/archive/agents/`, if present: destination collisions only.
- References to affected agents or tasks in all allowlisted crew files.

For every inspected file, record existence, file type, size, and SHA-256 hash.
Determine whether each agent file matches a known generated baseline; when no
baseline exists, classify it as user-modified.

Derive and compare these state maps:

1. agent identifier → definition path;
2. task identifier → exactly one active owner;
3. active `STATUS.md` item → current owner;
4. roster and task projections in `AGENTS.md` and `MYCREW.md`;
5. canonical autonomy declaration in `AGENTS.md`.

Stop preflight on a symlink, collision, malformed managed structure, duplicate
identifier, duplicate task owner, unknown file ownership, or an unresolved
active-work owner. Report the exact blocker; do not infer a destructive fix.

## 4. Exact Impact Preview and Approval

Render proposed after-content in memory, then show:

1. operation type and purpose;
2. a stable preview ID derived from the operation, ordered manifest, input
   hashes, and proposed after-content;
3. before/after roster and task-owner maps;
4. before/after active-work ownership;
5. before/after canonical autonomy value, when relevant;
6. user-authored sections and `STATUS.md` history that will remain unchanged;
7. warnings, assumptions, legacy files, and unresolved choices.

The mutation manifest must contain one row per exact path:

| Order | Action | Workspace-relative path | Before SHA-256 | After SHA-256 | Exact impact |
|---:|---|---|---|---|---|
| 1 | mkdir/create/update/archive/delete | exact path | hash, `DIRECTORY`, or `ABSENT` | hash, `DIRECTORY`, or `ABSENT` | fields, sections, or byte-preserving move |

Show complete diffs for updates, complete content for creates, source and
destination for archives, and the full path plus prior hash for deletions.
Show every missing parent directory as its own `mkdir` row; directory creation
must never be an unlisted side effect.
List read-but-unchanged files separately so the user can distinguish inspection
from mutation.

Ask the user to approve the named preview ID. For permanent deletion, ask for a
second confirmation that lists the delete rows. Before applying, re-read every
input and compare its type and hash with the preview. On any mismatch, discard
the approval and return to preflight.

## 5. Apply

After approval and drift verification:

1. Apply only actions in the ordered manifest and only at allowlisted paths.
2. Make operation-specific dependency changes first. In particular, reassign
   owned and in-progress work before retiring an agent.
3. Edit managed fields minimally; do not regenerate whole coordination files.
4. Update active `STATUS.md` ownership without altering historical entries.
5. Archive or explicitly confirmed delete actions run last.
6. Capture the resulting SHA-256 and file type for every manifest path.

If an action fails, stop immediately and report the successfully applied prefix
and current hashes. Do not improvise rollback writes; a rollback must already
be in the approved manifest or receive a new preview and approval.

## 6. Reconcile

Reconciliation checks and aligns only the approved operation:

- `AGENTS.md` is the canonical roster and task-owner map.
- `MYCREW.md` is a matching human-readable projection, not a second authority.
- Agent task lists agree with canonical ownership.
- Active `STATUS.md` rows name current owners; historical rows retain their
  original names and text.
- Retired agents have no active roster, task, pending, or in-progress
  references.
- Exactly one active autonomy value is stored in `AGENTS.md`; other files may
  display it but must not define a competing value.
- Archived agent files are under `project-context/archive/agents/`, outside
  `.cursor/agents/` discovery.

When a mismatch requires an unpreviewed mutation, reconciliation fails and a
new preview is required.

## 7. Operation Workflows

### 7.1 Add an Agent

1. Preflight the proposed slug, name, role, responsibilities, task identifiers,
   and output paths against all active and archived agents.
2. Reject responsibility overlap or duplicate task ownership until the user
   selects a single owner.
3. Preview the new `.cursor/agents/<slug>.md`, roster and task-map additions,
   `MYCREW.md` projection, and any optional status record.
4. After approval, create the custom-subagent definition before publishing its
   roster entry, then reconcile and validate.

Do not create an empty agent or placeholder tasks. The definition must satisfy
the current custom-subagent metadata contract.

### 7.2 Remove an Agent

1. Inventory every task, pending item, in-progress item, dependency, and active
   reference owned by the agent.
2. Ask the user to select a valid remaining owner for each owned or active item.
   Stop if any item remains unassigned.
3. Preview all task transfers, active `STATUS.md` owner changes, roster changes,
   target-agent updates, and source-file disposition.
4. After approval, apply and validate all reassignments first.
5. Only then remove the active roster entry and retire the source agent file.

Archival is the default disposition for retired agent files and specifically
for every user-modified agent file. Use a collision-free path such as
`project-context/archive/agents/<UTC-timestamp>-<agent-slug>.md`. This location
is intentionally outside `.cursor/agents/`, so archived definitions are not
discovered as active custom subagents.

Permanent deletion is never the default. It requires explicit confirmation of
the exact source path after the preview. Do not remove or rewrite historical
`STATUS.md` entries that mention the former agent.

### 7.3 Modify an Agent

1. Identify exact managed fields to change and classify all surrounding
   content as generated or user-authored.
2. Preview field-level diffs and every affected roster, task, status, and
   `MYCREW.md` projection.
3. Route ownership changes through the task-reassignment workflow.
4. Apply only approved hunks and verify preserved content byte-for-byte.

Treat a slug or path rename as create + reconcile + archive, with collision
checks and all three actions visible in the preview.

### 7.4 Reassign a Task

1. Identify the task by stable identifier and confirm its single current owner.
2. Select one valid target and check the current builder capacity and
   responsibility constraints.
3. Preview removal from the source, addition to the target, canonical
   `AGENTS.md` ownership, `MYCREW.md`, and active `STATUS.md` owner changes.
4. Apply source and target definitions plus canonical mappings as one approved
   change set.
5. Preserve completed and historical rows under their original owner.

The result must contain exactly one active owner—never zero and never two.

### 7.5 Change Autonomy

1. Read the canonical declaration in `AGENTS.md`. If absent, runtime behavior
   is LOW; if multiple or invalid declarations exist, stop for correction.
2. Preview one insertion or replacement so `AGENTS.md` contains exactly one
   ``**Active autonomy:** `<VALUE>` `` declaration, where `<VALUE>` is LOW,
   MEDIUM, or HIGH.
3. Update a `MYCREW.md` display value only as a matching projection.
4. Do not copy the active value or policy text into each agent definition.
5. Never rewrite LOW, MEDIUM, or HIGH policy definitions in
   `.cursor/rules/crew-sop.mdc` for an autonomy change.

The new autonomy takes effect only after the approved write validates.

### 7.6 Rebuild the Crew

Treat rebuild as a state migration, not a workspace reset:

1. Inventory all current crew files, user-authored content, active work,
   history, legacy personas, and archives.
2. Produce a complete before/after roster and task map with a disposition for
   every current agent and task.
3. Create or update modern `.cursor/agents/*.md` definitions. Never generate
   new persona files under `.cursor/rules/`.
4. Preserve `AGENTS.md` user context and keep exactly one canonical autonomy
   value.
5. Preserve `STATUS.md` in place; do not reinitialize, truncate, or reorder it.
6. Reassign all owned and in-progress work before retiring replaced agents.
7. Archive retired or superseded user-modified definitions by default.
8. Migrate a legacy `.cursor/rules/*.md` persona only when its exact source,
   modern destination, and archive action are in the approved preview.
9. Reconcile `MYCREW.md` after canonical state is valid.

A rebuild does not modify builder package files or crew policy definitions and
does not permanently delete files unless separately confirmed.

## 8. Cross-File Update Matrix

Legend: `C` create, `U` minimal managed-field update, `H` history-preserving
active-owner update, `A` byte-preserving archive, `R` read/validate only, `O`
optional only when shown in the preview, and `—` no write.

| File or state | Add | Remove | Modify | Reassign | Autonomy | Rebuild |
|---|---:|---:|---:|---:|---:|---:|
| `.cursor/agents/<slug>.md` | C | A last | U | U source/target | R | C/U/A |
| `AGENTS.md` roster/task map | U | U | U | U | R | U |
| `AGENTS.md` autonomy declaration | R | R | R | R | U | U/R |
| `STATUS.md` active rows | O | H | O | H | O | H/O |
| `STATUS.md` history | R | R | R | R | R | R |
| `MYCREW.md` projection | U | U | U | U | U | U |
| `project-context/archive/agents/` | — | C | O | — | — | C/O |
| `.cursor/rules/crew-sop.mdc` | R | R | R | R | R | R |
| Legacy `.cursor/rules/<persona>.md` | R | R | R | R | R | A only in approved migration |
| Builder skill/package files | R | R | R | R | R | R |

`O` never means implicit permission. If the path or action is absent from the
approved manifest, it is read-only.

## 9. Deterministic Validation Checklist

Run checks in this order and record `PASS` or `FAIL` for each applicable item:

1. The actual mutation path set and order equal the approved manifest.
2. Every mutation path is normalized, workspace-relative, and allowlisted.
3. No inspected or mutated path component is a symlink or unexpected file type.
4. Every before hash matched at apply time; every after hash matches the
   approved rendered content.
5. Every active custom-subagent file is under `.cursor/agents/`, parses, and
   satisfies the current required metadata contract.
6. Managed agent identifiers and slugs are unique, including
   case-insensitive comparison.
7. The active roster equals the set of crew-managed custom-subagent
   definitions; unknown custom subagents remain untouched and reported.
8. Every approved task identifier has exactly one active owner and appears in
   the matching agent task list.
9. Every active pending or in-progress row has an active owner.
10. Removed agents have no active references; historical references remain
    unchanged.
11. `STATUS.md` history has the same ordered content as preflight except for
    exact approved active-field edits and approved appended records.
12. User-authored content outside approved hunks is byte-for-byte unchanged.
13. `AGENTS.md` contains exactly one valid active autonomy declaration. If it is
    absent by approved design, effective runtime autonomy is LOW.
14. Any autonomy displayed in `MYCREW.md` equals the canonical `AGENTS.md`
    value; no agent file defines a competing active value.
15. The before and after hashes of `.cursor/rules/crew-sop.mdc` match for an
    autonomy change or ordinary crew modification.
16. `AGENTS.md`, agent task lists, active `STATUS.md` ownership, and
    `MYCREW.md` agree.
17. Every archive destination is outside `.cursor/agents/`, was absent during
    preflight, and has the source file's preflight hash.
18. No active roster or invocation guidance points to a legacy
    `.cursor/rules/*.md` persona.
19. No unapproved file changed, and no placeholder or unresolved task remains.

Any `FAIL` means the operation is not complete. Report the exact observed
state; do not claim success or perform unapproved repair writes.

## 10. Before/After Handoff

### Before Approval

Hand off a proposal containing:

- operation, preview ID, and explicit assumptions;
- current-state summary and blockers;
- exact ordered mutation manifest with complete diffs/content;
- before/after roster, tasks, active work, and autonomy;
- preservation statement for user-authored content and `STATUS.md` history;
- archive destinations and separately confirmable permanent deletes;
- deterministic validation plan.

### After Apply

Hand off:

- approved preview ID and whether any drift was detected;
- each applied action with path, before hash, and after hash;
- final roster, task-owner map, active-work owners, and autonomy;
- archive locations and confirmation that archived bytes match their sources;
- preservation results for user-authored content and `STATUS.md` history;
- the ordered validation checklist with `PASS`/`FAIL`;
- any unapplied action, partial state, warning, or required next decision.

The after handoff must distinguish completed work from partial work and must
never hide deviations from the approved preview.
