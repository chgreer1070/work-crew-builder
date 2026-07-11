# Crew Modification Playbook

This playbook governs every `/crew-builder` mutation, including initial
bootstrap, add, remove, modify, reassign, autonomy change, repair, rebuild, and
any separately authorized builder-package mutation. It is a transactional
safety contract: inspect first, preview the exact impact, obtain explicit
approval, apply only that preview, reconcile, validate, and hand off.
Operation-specific sections add requirements; they never narrow this lifecycle.
The active crew autonomy level never bypasses it.

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
- Read-only `/crew-builder` discovery and planning follow the staged Skill
  gates. Crew runtime autonomy does not authorize or replace those gates.
- Show the user an exact impact preview and receive explicit approval after the
  preview before any directory creation, file create, update, move, archive, or
  delete, including initial bootstrap.
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
Agent slugs must match `^[a-z][a-z0-9]*(?:-[a-z0-9]+)*$`; archive names must match
`[0-9]{8}T[0-9]{6}Z-<agent-slug>.md`.

Initial bootstrap and later crew mutations may touch only exact paths selected
from:

| Path | Allowed purpose |
|---|---|
| `.cursor/agents/` | Modern custom-subagent directory; create only when previewed |
| `.cursor/agents/<agent-slug>.md` | Modern Cursor custom-subagent definition |
| `AGENTS.md` | Canonical roster, task ownership, and active autonomy |
| `STATUS.md` | Active-work ownership and append-only status records |
| `MYCREW.md` | Human-facing projection of the crew |
| `project-context/` | Required crew context root; create only when previewed |
| `project-context/research/` | Required research output folder |
| `project-context/plans/` | Required planning output folder |
| `project-context/drafts/` | Required draft output folder |
| `project-context/final/` | Required final-output folder |
| `project-context/archive/`, `project-context/archive/agents/` | Archive parent directories; create only when previewed |
| `project-context/archive/agents/<archive-name>.md` | Retired agent archive outside agent discovery |

An exact legacy persona source at `.cursor/rules/<name>.md` may be moved only
as part of an explicitly previewed rebuild migration. It is never a modern
agent destination. `.cursor/rules/crew-sop.mdc` and builder skill files are
read-only inputs to bootstrap, add, remove, modify, reassign, autonomy, repair,
and rebuild operations. A separately authorized builder-package mutation still
follows this entire playbook and must declare its exact owned-path allowlist in
the preview; it cannot inherit the crew-file allowlist.

For every candidate path:

1. Inspect the path and each existing parent without following symlinks. Record
   available path identity, type, content hash or version, and regular-file link
   count.
2. Confirm its canonical location remains under the workspace root.
3. Stop if any component is a symlink, a regular file has multiple links where
   link-count metadata is available, or a path has an unexpected type.
4. Stop on case-insensitive slug duplication, an occupied destination, an
   existing archive name, or any other path collision.
5. Stop when an affected source, destination, or collision has unknown
   ownership. A file is crew-managed only when `AGENTS.md` names it or the user
   explicitly adopts it; filesystem ownership alone is not sufficient.

On a collision or unknown affected ownership, stop mutation, show the evidence,
ask the user to choose a safe disposition, rerun preflight, and issue a new
preview. Never overwrite, merge, rename, or choose a destination automatically.
An unrelated custom subagent not named in `AGENTS.md` is reported and left
untouched; it does not enter crew reconciliation unless it collides with or is
referenced by the proposed change.

### 1.3 Preservation Rules

- Treat content as user-authored unless a known builder baseline proves
  otherwise. Preserve it byte-for-byte outside explicitly approved hunks.
- Preserve all `STATUS.md` history. Reassignment may change owner fields in
  active rows and may append a new record, but must not regenerate, truncate,
  reorder, or scrub historical rows.
- Under an approved manifest, the builder may update affected active ownership
  fields, append schema-required `STATUS.md` state events, and append the
  corresponding `AGENTS.md` configuration-history record. Ordinary agents
  remain limited to their own task-owned current-state rows.
- Preserve stable task identifiers through rewording, reprioritization,
  reassignment, and rebuild; assign a new identifier only to genuinely new work.
- Preserve user-authored sections in `AGENTS.md`, `MYCREW.md`, and agent files.
  If managed boundaries cannot be identified safely, stop for user direction.
- Archive retired files byte-for-byte. Record provenance in the handoff rather
  than injecting metadata into the archived content.

## 2. Modification Lifecycle

Every operation, including initial bootstrap and repair, uses this order:

1. **Preflight** — inspect the current state without writing.
2. **Preview** — render the complete desired state and exact mutation manifest.
3. **Approve** — obtain explicit user approval of that manifest.
4. **Apply** — verify no drift, then perform only approved mutations.
5. **Reconcile** — align projections and references without expanding scope.
6. **Validate** — run every applicable deterministic check.
7. **Hand off** — report before/after state, preservation, and validation.

If reconciliation or validation discovers a needed write that was not
previewed, stop. Analyze the failure read-only, render a revised exact preview
from current state, obtain new approval, and only then apply. Old approval never
authorizes changed repair content.

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

Missing `AGENTS.md` and `STATUS.md` is valid only before an explicitly selected
initial bootstrap when no surviving evidence indicates an established crew. If
an established or partial crew is missing either file, stop the ordinary
operation. Reconstruction is permitted only through an explicit repair preview
derived from surviving files, hashes, history, and recorded user decisions;
never silently infer or reconstruct canonical state.

For every inspected file, record existence, file type, size, and SHA-256 hash.
Determine whether each agent file matches a known generated baseline; when no
baseline exists, classify it as user-modified.

Derive and compare these state maps:

1. agent identifier → definition path;
2. task identifier → exactly one active owner;
3. active `STATUS.md` item → current owner;
4. roster and task projections in `AGENTS.md` and `MYCREW.md`;
5. canonical autonomy declaration in `AGENTS.md`.

Stop preflight on a symlink or disallowed link, collision, malformed managed
structure, duplicate identifier, duplicate task owner, unknown affected
ownership, or unresolved active-work owner. Report the exact blocker, ask the
user for a safe disposition, then rerun preflight and issue a new preview; do
not infer a destructive fix.

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

| Order | Action ID | Row action | Workspace-relative path | Before SHA-256 | After SHA-256 | Exact impact |
|---:|---|---|---|---|---|---|
| 1 | `WRITE-01` | mkdir/create/update/delete | exact path | hash, `DIRECTORY`, or `ABSENT` | hash, `DIRECTORY`, or `ABSENT` | complete content or exact fields |

Show complete diffs for updates, complete content for creates, and the full path
plus prior hash for deletions.
Show every missing parent directory as its own `mkdir` row; directory creation
must never be an unlisted side effect.
List read-but-unchanged files separately so the user can distinguish inspection
from mutation.

An archive, rename, or move is always two path rows sharing one action ID,
never a single abstract row:

| Order | Action ID | Row action | Workspace-relative path | Before SHA-256 | After SHA-256 | Exact impact |
|---:|---|---|---|---|---|---|
| 1 | `MOVE-01` | create-destination | exact destination | `ABSENT` | exact previewed destination hash | complete destination content |
| 2 | `MOVE-01` | remove-source-after-verification | exact source | exact source hash | `ABSENT` | remove only after destination identity, containment, and hash verify |

The destination row executes first using the exact previewed bytes. Re-read and
verify its path identity, containment, type, and hash; only then may the source
row execute. The mutation log records both rows, their shared action ID, and
their exact order so validation can compare actual and approved path/action
sequences. Removing a verified archive source is not a permanent deletion, but
both rows still require approval; an unarchived delete retains the separate
permanent-deletion confirmation.

Ask the user to approve the named preview ID. For permanent deletion, ask for a
second confirmation that lists the delete rows. Before applying, re-read every
input and compare its identity, type, hash or version, and available link count
with the preview. On mismatch, discard the approval, merge the requested intent
with the latest state in memory, rerun preflight, and issue a revised preview
for new approval.

## 5. Apply

After approval and drift verification:

1. Apply only rows in the ordered manifest and only at allowlisted paths.
2. Immediately before each row, revalidate path identity and canonical
   containment without following links. Reject symlinks, unexpected types,
   changed identities, and regular files with multiple links when that metadata
   is available. For an absent target, revalidate every existing parent and
   confirm the target is still absent.
3. Use hash/version-conditional updates. For new destinations, require the path
   to remain absent and use no-clobber creation where available. On drift, stop;
   retry only after read-only merge, a revised exact preview, and new approval.
4. Prefer no-follow access and same-directory atomic replacement primitives
   where available. If an equivalent containment and identity check cannot be
   established, abort any destructive or overwrite action rather than weakening
   safety; no particular platform-specific primitive is mandatory.
5. Make operation-specific dependency changes first. In particular, reassign
   owned and in-progress work before retiring an agent.
6. Edit managed fields minimally; do not regenerate whole coordination files.
7. The builder may update affected active `STATUS.md` ownership fields and
   append required events only when those exact changes are in the manifest.
   Preserve every historical row and unrelated active row.
8. For archive/move actions, create and verify the destination row before the
   source-removal row. Unarchived, explicitly confirmed deletes run last.
9. Capture the resulting identity, SHA-256 or version, and file type for every
   manifest row.

If an action fails, stop immediately and report the successfully applied prefix
and current hashes. Do not improvise rollback or repair writes. Analyze
read-only; any changed content requires a revised exact preview and new approval
before it is written.

## 6. Reconcile

Reconciliation checks and aligns only the approved operation:

- `AGENTS.md` is the canonical roster and task-owner map.
- `MYCREW.md` is a matching human-readable projection, not a second authority.
- Agent task lists agree with canonical ownership.
- Builder-approved active `STATUS.md` ownership fields name current owners;
  historical and unrelated active rows retain their original names and text.
- Retired agents have no active roster, task, pending, or in-progress
  references.
- A built crew has exactly one active autonomy value in `AGENTS.md`; other files
  may display it but must not define a competing value.
- Archived agent files are under `project-context/archive/agents/`, outside
  `.cursor/agents/` discovery.
- Crew reconciliation compares `AGENTS.md` only with the crew-managed agent
  files it names. Unknown unrelated custom subagents are reported and untouched.

When a mismatch requires an unpreviewed mutation, reconciliation fails and a
new preflight, preview, and approval are required.

## 7. Operation Workflows

### 7.1 Initial Bootstrap

1. Confirm the operation is initial bootstrap rather than repair. Missing
   `AGENTS.md` and `STATUS.md` is expected only when no surviving file indicates
   an established crew.
2. Inventory all existing target paths, partial artifacts, unrelated custom
   subagents, and collisions without writing.
3. Preview every required directory and file creation, including
   `.cursor/agents/`, the four standard `project-context/` folders,
   `AGENTS.md`, `STATUS.md`, and `MYCREW.md`.
4. Render `AGENTS.md` with exactly one active autonomy declaration and render
   `STATUS.md` with the current generated schema before approval.
5. Obtain approval of the complete bootstrap manifest before creating even the
   first directory, then apply, reconcile, and validate normally.

If surviving evidence indicates a prior or partial crew, use an explicit repair
preview based on that evidence and the user's ownership decisions; do not
bootstrap over it.

### 7.2 Add an Agent

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

### 7.3 Remove an Agent

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
`project-context/archive/agents/20260711T145700Z-<agent-slug>.md`. Represent the
archive as the required two-row move action: create and verify the destination,
then remove the source. This location is intentionally outside
`.cursor/agents/`, so archived definitions are not discovered as active custom
subagents.

Permanent deletion is never the default. It requires explicit confirmation of
the exact source path after the preview. Do not remove or rewrite historical
`STATUS.md` entries that mention the former agent.

### 7.4 Modify an Agent

1. Identify exact managed fields to change and classify all surrounding
   content as generated or user-authored.
2. Preview field-level diffs and every affected roster, task, status, and
   `MYCREW.md` projection.
3. Route ownership changes through the task-reassignment workflow.
4. Apply only approved hunks and verify preserved content byte-for-byte.

Treat a slug or path rename as a two-row move plus reconciliation, with the
shared action ID, both paths, collision checks, and exact content visible in the
preview.

### 7.5 Reassign a Task

1. Identify the task by stable identifier and confirm its single current owner.
2. Select one valid target and check the current builder capacity and
   responsibility constraints.
3. Preview removal from the source, addition to the target, canonical
   `AGENTS.md` ownership, `MYCREW.md`, and active `STATUS.md` owner changes.
4. Apply source and target definitions plus canonical mappings as one approved
   change set.
5. Preserve completed and historical rows under their original owner.

The result must contain exactly one active owner—never zero and never two.

### 7.6 Change Autonomy

1. Read the canonical declaration in `AGENTS.md`. Absence is valid only before
   initial bootstrap, with LOW runtime constraints. In an established crew,
   absence, duplication, or an invalid value requires the explicit repair flow.
2. Preview one insertion or replacement so `AGENTS.md` contains exactly one
   ``**Active autonomy:** `<VALUE>` `` declaration, where `<VALUE>` is LOW,
   MEDIUM, or HIGH.
3. Update a `MYCREW.md` display value only as a matching projection.
4. Do not copy the active value or policy text into each agent definition.
5. Never rewrite LOW, MEDIUM, or HIGH policy definitions in
   `.cursor/rules/crew-sop.mdc` for an autonomy change.

The new autonomy takes effect only after the approved write validates.

### 7.7 Rebuild the Crew

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
   modern destination, and the two ordered rows of each move/archive action are
   in the approved preview.
9. Reconcile `MYCREW.md` after canonical state is valid.

A rebuild does not modify builder package files or crew policy definitions and
does not permanently delete files unless separately confirmed.

## 8. Cross-File Update Matrix

Legend: `C` create, `U` minimal managed-field update, `H` approved
history-preserving active-owner update, `M2` two-row move/archive, `R`
read/validate only, `O` optional only when shown in the preview, and `—` no
write.

| File or state | Bootstrap | Add | Remove | Modify | Reassign | Autonomy | Repair/Rebuild |
|---|---:|---:|---:|---:|---:|---:|---:|
| `.cursor/agents/<slug>.md` | C | C | M2 last | U/M2 | U source/target | R | C/U/M2 |
| `AGENTS.md` roster/task map | C | U | U | U | U | R | C/U |
| `AGENTS.md` autonomy declaration | C | R | R | R | R | U | C/U |
| `STATUS.md` active rows | C | O | H | O | H | O | C/H/O |
| `STATUS.md` history/events | C | O | O | O | O | O | C/O |
| `MYCREW.md` projection | C | U | U | U | U | U | C/U |
| Four standard `project-context/` folders | C | R | R | R | R | R | C/R |
| `project-context/archive/agents/` | — | — | C | O | — | — | C/O |
| `.cursor/rules/crew-sop.mdc` | R | R | R | R | R | R | R |
| Legacy `.cursor/rules/<persona>.md` | R | R | R | R | R | R | M2 only in approved migration |
| Builder skill/package files | R | R | R | R | R | R | R |

`O` never means implicit permission. If the path or action is absent from the
approved manifest, it is read-only.
This matrix covers crew-state operations. A separately authorized builder
package mutation uses a dedicated exact allowlist and manifest but follows the
same lifecycle and safety checks.

## 9. Deterministic Validation Checklist

Completion validation is post-mutation. Absence of `AGENTS.md` is valid while
inspecting a pre-bootstrap workspace, but every built or repaired crew must pass
the single-autonomy check below. Run checks in order and record `PASS` or `FAIL`:

1. The actual ordered mutation rows—including action IDs, row actions, and
   paths—exactly equal the approved manifest.
2. Every mutation path is normalized, workspace-relative, and allowlisted.
3. Immediately before every mutation, identity and containment were rechecked;
   no path component was a symlink or unexpected type, and no regular file had
   multiple links when link-count metadata was available.
4. Every hash/version conditional matched at write time; drift caused an abort,
   not an overwrite.
5. Every resulting hash/version and type matches the approved rendered state.
6. Every crew-managed custom-subagent file is under `.cursor/agents/`, parses,
   satisfies the current metadata contract, and uses a slug matching
   `^[a-z][a-z0-9]*(?:-[a-z0-9]+)*$`.
7. `AGENTS.md` names the complete crew-managed agent set. Unknown unrelated
   custom subagents are reported, excluded from equality, and untouched.
8. Every approved task identifier has exactly one active owner and appears in
   the matching managed-agent task list.
9. Every active pending or in-progress row has an active owner; builder changes
   are limited to approved affected ownership fields and required appended
   events.
10. Removed agents have no active references; historical references remain
    unchanged.
11. `STATUS.md` history has the same ordered content as preflight except for
    exact approved active-field edits and schema-required appended records.
12. User-authored content outside approved hunks is byte-for-byte unchanged.
13. `AGENTS.md` contains exactly one valid active autonomy declaration.
14. Any autonomy displayed in `MYCREW.md` equals the canonical `AGENTS.md`
    value; no agent file defines a competing active value.
15. The before and after hashes of `.cursor/rules/crew-sop.mdc` match for an
    autonomy change or ordinary crew-state mutation.
16. `AGENTS.md`, managed-agent task lists, active `STATUS.md` ownership, and
    `MYCREW.md` agree.
17. `project-context/research/`, `project-context/plans/`,
    `project-context/drafts/`, and `project-context/final/` exist as real
    directories inside the workspace.
18. Every move/archive has exactly two ordered rows with one action ID; its
    destination was absent, verified to the approved hash, and contained safely
    before its source was removed. For a byte-preserving archive, the verified
    destination hash equals the source preflight hash.
19. No active roster or invocation guidance points to a legacy
    `.cursor/rules/*.md` persona.
20. No unapproved path or content changed.
21. No unresolved builder template token such as `{{...}}` or required managed
    field remains. Ordinary `TODO` text in preserved user-authored or historical
    prose is not a validation failure.

Any `FAIL` means the operation is incomplete. Analyze the failure read-only and
report exact current state. A repair must render a revised exact preview from
that state, obtain new approval, and then apply; validation never writes changed
content under the old approval.

## 10. Before/After Handoff

### Before Approval

Hand off a proposal containing:

- operation, preview ID, and explicit assumptions;
- current-state summary and blockers;
- exact ordered mutation manifest with action IDs and complete diffs/content;
- before/after roster, tasks, active work, and autonomy;
- preservation statement for user-authored content and `STATUS.md` history;
- archive destinations and separately confirmable permanent deletes;
- deterministic validation plan.

### After Apply

Hand off:

- approved preview ID and whether any drift was detected;
- each applied manifest row with action ID, path, before hash, and after hash;
- final roster, task-owner map, active-work owners, and autonomy;
- archive locations and confirmation that archived bytes match their sources;
- preservation results for user-authored content and `STATUS.md` history;
- the ordered validation checklist with `PASS`/`FAIL`;
- any unapplied action, partial state, warning, or required next decision.

The after handoff must distinguish completed work from partial work and must
never hide deviations from the approved preview.
