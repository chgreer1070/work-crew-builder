---
name: crew-builder
description: Use when a user asks to create, build, rebuild, modify, add or remove crew members, reassign work, or change autonomy for a Work Crew.
---

# Work Crew Builder

Use this Skill as the canonical builder workflow. Treat `.cursor/rules/crew-sop.mdc` as immutable runtime policy: read it when present, but never create, edit, replace, or delete it.

Generate and maintain only these crew artifacts:

- Project custom subagents in `.cursor/agents/*.md`
- Root `AGENTS.md`, `STATUS.md`, and `MYCREW.md`
- Purposeful folders under root `project-context/`

## Non-negotiable invariants

1. Assign every approved Task ID to exactly one Owner. Allow Contributors, but never treat contribution as ownership.
2. Preserve existing user-authored content outside the exact approved change. Preserve all existing `STATUS.md` history byte-for-byte and in order; append approved events, but never rewrite history.
3. Write complete content. Never leave placeholders, empty required fields, invented facts, or unresolved template tokens.
4. Get explicit user approval before every builder file mutation, regardless of crew autonomy. Approval must cover the exact action and workspace-relative path. Reading and planning are not mutations.
5. Use only normalized, workspace-relative paths with forward slashes. Reject absolute paths, `..`, path escapes, and symlink escapes.
6. Keep one canonical active autonomy value in `AGENTS.md`: `LOW`, `MEDIUM`, or `HIGH`. Use `LOW` when the user has not chosen. Make other artifacts refer to or summarize that value without becoming another authority.
7. Change only the approved path list. Renew approval if a path, action, or design changes.

## Required reference routing

Read every reference whose trigger applies before proceeding.

| Reference | Read it when |
| --- | --- |
| [Discovery and crew design](references/discovery-and-crew-design.md) | Before discovery, every build or rebuild, and any task-scope change |
| [Agent persona template](references/agent-persona-template.md) | Before designing, creating, editing, or validating any `.cursor/agents/*.md` file |
| [Crew workspace templates](references/crew-workspace-templates.md) | Before creating, merging, editing, or validating `AGENTS.md`, `STATUS.md`, `MYCREW.md`, or `project-context/` |
| [Crew modification playbook](references/crew-modification-playbook.md) | Before a rebuild or any modify, add, remove, reassign, rename, tool, or change-autonomy request |

## Communicate plainly

- Ask one focused set of questions at a time.
- Say what you understood, what remains unknown, and what decision is needed.
- Refer to work by stable Task ID and short label.
- Present choices in plain language and recommend one when useful.
- Say “No files will change until you approve these exact changes” at the mutation gate.
- Treat silence, vague assent, and approval of an earlier design as no approval for a new mutation manifest.

## Route the request

- Use the build/rebuild workflow when no crew exists or the user asks for a fresh design or rebuild.
- Use the modification workflow for all changes to an existing crew.
- If state is partial or inconsistent, treat the request as a repair-aware rebuild. Preserve valid content and history; do not silently start over.

## Build or rebuild

1. **Inspect without writing.** Read applicable instructions and existing crew artifacts. Inventory current agent files, root files, context folders, Task IDs, ownership, autonomy, and tool claims. If `.cursor/rules/crew-sop.mdc` is missing, report the runtime-policy blocker; do not generate it.
2. **Complete discovery.** Follow the discovery reference. Review all 41 O*NET activities, normalize duties into stable tasks, record evidence, priority, and AI-involvement class, and identify sensitive boundaries.
3. **Gate task scope.** Present the discovery approval contract. Get explicit approval for the Task IDs, labels, dispositions, priorities, and involvement classes. Revise and present again until approved.
4. **Design the smallest coherent crew.** Follow the persona and workspace references. Assign exactly one Owner per approved Task ID, list any Contributors, choose realistic tools, and use the canonical autonomy value.
5. **Gate crew design.** Present the proposed roster, ownership map, contributors, tool gaps, autonomy, and rationale. Get explicit approval. Design approval still does not authorize file changes.
6. **Run preflight.** Normalize every proposed path and verify it resolves inside the workspace. Snapshot existing approved-path content and `STATUS.md` history for comparison. Recheck immediately before writing.
7. **Resolve collisions.** Classify each target as absent, builder-managed, user-owned, or ambiguous. Propose create, merge, preserve, rename, replace, or delete. Never overwrite an unknown file, follow an escaping symlink, or reuse a colliding agent name silently. For concurrent changes, stop and re-run preflight.
8. **Gate mutations.** Show an exact manifest with one row per action and path, the collision decision, and what existing content will be preserved. Get explicit approval for that manifest.
9. **Generate only the manifest.** Create or update `.cursor/agents/<name>.md`, merge root crew files, and create only needed `project-context/` folders. On rebuild, remove or rename an old agent file only when that exact action was approved.
10. **Validate, repair, and hand off.** Run every mechanical check below. Repair only within approved paths and actions, then rerun the full suite. Seek new approval before any broader repair.

## Modern custom subagents

- Store project subagents at `.cursor/agents/<name>.md`; do not place personas in `.cursor/rules/`.
- Start each file with valid YAML frontmatter. Require a unique lowercase-hyphen `name` matching the filename stem and a non-empty, delegation-focused `description`. Use only supported optional fields and valid value types.
- Use `model: inherit` unless the user approves an available model for a clear reason.
- Invoke explicitly with `/name request`, mention the subagent naturally, or allow Cursor to delegate from its description. Do not teach legacy `@name.md` invocation.
- Verify tool access in the current environment. Subagents inherit available parent tools; never promise unavailable local, MCP, account, or external-system access.

## Modify an existing crew

1. Read all current crew artifacts and the modification playbook. Establish the current Task ID register, owners, roster, canonical autonomy, history, and collisions before proposing a change.
2. Keep Task IDs stable across wording, priority, owner, contributor, or agent changes. Give a genuinely new task the next unused ID. Retire removed tasks; never renumber or reuse them.
3. Apply the relevant impact rule:
   - **Add an agent:** assign only approved tasks. Run discovery and task approval first for new work. Avoid duplicate ownership or a role created only to meet a quota.
   - **Remove an agent:** reassign or explicitly retire every owned task before approving deletion. Preserve historical references to that agent.
   - **Reassign work:** keep each Task ID and update its one Owner consistently everywhere; update Contributors separately.
   - **Add or remove work:** repeat discovery for the changed scope and approve the revised task register before crew design.
   - **Rename or change persona/tools:** preserve ownership, update every roster and invocation reference, and verify claimed tools.
   - **Change autonomy:** update the single canonical value in `AGENTS.md` and refresh derived guidance. Never modify `.cursor/rules/crew-sop.mdc`.
4. Present a plain-language before/after summary: Task IDs, owners, contributors, roster, autonomy, tool gaps, preserved content, and collisions.
5. Run preflight and present the exact mutation manifest. Get explicit approval even if the crew is `HIGH` autonomy.
6. Apply only approved actions. Validate the entire crew, not just edited files. Repair and revalidate before handoff.

## Mechanical validation

Parse and compare data; do not rely on visual review alone.

1. **Task IDs and owners:** Assert every approved active Task ID occurs once in the canonical ownership map with one Owner and valid Contributors. Assert the agent-owned ID set equals the union of agent task sections and its `MYCREW.md` projection. Assert Human-owned IDs occur in no agent-owned task section. Fail unknown, missing, or duplicate IDs or owners.
2. **Roster and files:** Assert one-to-one equality among active roster names, `.cursor/agents/*.md` filename stems, frontmatter names, and `MYCREW.md` entries. Assert invocation names resolve.
3. **Placeholders:** Scan generated artifacts for blank required values and unresolved markers such as `TODO`, `TBD`, `FIXME`, `PLACEHOLDER`, `[insert ...]`, and template braces. Fail on every unresolved match.
4. **Frontmatter:** Parse every active subagent file as YAML. Assert frontmatter begins on line 1, required values are non-empty, names are unique and safe, descriptions state delegation triggers, and optional fields use supported types.
5. **Safe paths:** Normalize and resolve every target. Assert it is workspace-relative, remains inside the workspace, uses an allowed artifact location, and does not escape through a symlink.
6. **Tools:** Compare every claimed tool and external dependency with tools actually available to the parent. Record limitations or approval needs; fail fabricated access claims.
7. **History:** Compare the preflight snapshot with the result. Assert every pre-existing `STATUS.md` historical entry remains byte-for-byte and in order; allow only approved appends and active-state updates outside historical entries.
8. **Approved paths:** Compare the workspace diff with the approved manifest. Assert the actual mutation path/action set equals the approved list; fail every extra, missing, or changed path or action.
9. **Autonomy and policy:** Assert exactly one canonical active autonomy field exists in `AGENTS.md`, its value is allowed, derived files agree, and `.cursor/rules/crew-sop.mdc` is unchanged.

If any check fails, do not hand off. Fix the cause without weakening the check or deleting user content. If the fix changes scope, design, action, or path, stop and request a revised approval. Repeat all checks until they pass.

## Handoff

Report the final roster, Task ID ownership, canonical autonomy, exact changed paths, preserved content, tool limitations, and validation result. Give copy-ready `/name` examples and explain that natural-language delegation also works. State any approved retirement or migration clearly.
