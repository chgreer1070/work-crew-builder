# Work Crew Builder project plan and architecture

## Purpose

Work Crew Builder is a zero-code workspace package for Cursor. It helps a nontechnical professional describe their job, review an AI-suitable task map, and create a focused crew of custom subagents.

There is no application to build, package manager to run, or plugin manifest to install. The user copies the whole `.cursor` folder into a workspace and starts with `/crew-builder`.

## Design goals

- Keep installation and operation understandable without technical knowledge.
- Use one entrypoint, `/crew-builder`, for initial setup and later changes.
- Generate a small set of focused Cursor custom subagents.
- Ask the user to approve the task map, crew design, and file changes.
- Keep shared knowledge and durable outputs visible as ordinary files.
- Load detailed builder references only when needed.

## Shipped repository architecture

```text
work-crew-builder/
├── .cursor/
│   ├── skills/
│   │   └── crew-builder/
│   │       ├── SKILL.md
│   │       └── references/
│   └── rules/
│       └── crew-sop.mdc
├── INSTALL-GUIDE.md
├── README.md
├── project-context/
│   └── .gitkeep
└── wcb-project-plan.md
```

| Component | Responsibility |
|---|---|
| `.cursor/skills/crew-builder/SKILL.md` | Canonical discovery, design, preview, generation, and modification workflow |
| `.cursor/skills/crew-builder/references/` | O*NET guidance and supporting templates loaded by the skill when needed |
| `.cursor/rules/crew-sop.mdc` | Operating and coordination rules applied throughout the workspace |
| `README.md` | Overview, quick start, and repository inventory |
| `INSTALL-GUIDE.md` | Drag-and-drop setup and troubleshooting |
| `wcb-project-plan.md` | Architecture, contracts, and design rationale |
| `project-context/.gitkeep` | Preserves the repository's context folder |

The builder workflow belongs in the Skill. The public documentation explains how to install and use it without repeating its detailed internal instructions.

## Resulting workspace architecture

After installation and crew approval, the workspace has this combined structure:

```text
My-Work-Crew/
├── .cursor/
│   ├── agents/
│   │   ├── riley.md
│   │   ├── morgan.md
│   │   └── ...
│   ├── skills/
│   │   └── crew-builder/
│   │       ├── SKILL.md
│   │       └── references/
│   └── rules/
│       └── crew-sop.mdc
├── AGENTS.md
├── STATUS.md
├── MYCREW.md
└── project-context/
    ├── plans/
    ├── research/
    ├── drafts/
    └── final/
```

All four standard `project-context/` subfolders are required in every generated crew workspace.

### Generated artifact contract

| Artifact | Purpose |
|---|---|
| `.cursor/agents/<name>.md` | One rostered, project-level custom subagent with a focused role, clear delegation description, assigned tasks, autonomy behavior, and escalation triggers |
| `AGENTS.md` | Shared user context, priorities, standing instructions, constraints, and crew roster |
| `STATUS.md` | Task progress, handoffs, completed work, and questions for the user |
| `MYCREW.md` | Plain-language crew directory, task map, and usage guide |
| `project-context/plans/` | Strategies, roadmaps, and planning artifacts |
| `project-context/research/` | Research notes, evidence, and analysis |
| `project-context/drafts/` | Work in progress |
| `project-context/final/` | Finished, user-facing deliverables |

Crew members are invoked explicitly with `/name`. Cursor may also delegate to them automatically from a normal request, using each subagent's description to choose the right specialist.

The managed crew is the roster declared in `AGENTS.md`. Existing custom subagents that are not rostered are outside the managed crew; the builder reports and preserves them unless the user explicitly approves bringing them into the crew.

## User journey

1. **Install:** The user opens a dedicated folder in Cursor and copies in the complete `.cursor` folder.
2. **Start:** The user enters `/crew-builder Build a crew for my role`.
3. **Review:** The builder asks plain-language questions, proposes a task map and autonomy level, then presents a crew design.
4. **Approve:** The builder previews file additions, edits, and removals and asks before changing the workspace.
5. **Work:** The user invokes a specialist with `/name` or asks normally and lets Cursor delegate.
6. **Modify:** The user returns to `/crew-builder` to add, remove, or change crew members, adjust autonomy, or rebuild.

For a rebuild, the supported request is `/crew-builder Rebuild my crew`. Rebuild previews the proposed changes and asks for approval before modifying files. Users should not manually delete generated files because the builder must keep the agent definitions, roster, task map, and shared state consistent.

## O*NET task analysis

The [O*NET Generalized Work Activities](https://www.onetonline.org/find/descriptor/browse/Work_Activities/) framework provides a common vocabulary for work across occupations. It prevents the builder from relying on an unstructured list of generic AI ideas.

The builder uses that framework to:

1. review all 41 activities and record how each relates to the user's actual role;
2. translate real duties into specific tasks in the user's language;
3. classify every task as **Crew can do**, **Crew drafts—you decide**, or **Stays with you**, keeping physical, access-constrained, and human-judgment duties visible;
4. prioritize tasks by impact and frequency; and
5. ask the user to correct and approve the result before crew design.

The user's domain knowledge remains authoritative. O*NET is a structured starting point, not a substitute for the user's judgment.

## Crew design

- Prefer the smallest crew that gives each role a distinct responsibility.
- Assign each approved task to exactly one owner; keep **Stays with you** tasks visible and owned by the Human.
- Give every subagent a specific description so Cursor can delegate reliably.
- Configure LOW, MEDIUM, or HIGH autonomy in terms the user can understand.
- Make escalation triggers concrete, especially for external communication, sensitive data, financial decisions, and irreversible actions.
- Keep generated files complete and consistent with `AGENTS.md`, `STATUS.md`, and `MYCREW.md`.

## File-based context and coordination

`AGENTS.md` is the source of truth for durable user context, priorities, standing instructions, tasks, roster, constraints, and active autonomy. Durable configuration changes go through `/crew-builder` so the agent definitions, roster, task map, and user guide remain reconciled.

`STATUS.md` is the source of truth for mutable task and event state. Ordinary progress, handoffs, blockers, notifications, and completion history remain STATUS-owned rather than becoming crew configuration.

`project-context/` serves a different purpose: it stores the source material and substantial artifacts used or produced by the crew. Crew members inspect only relevant folders, place new work in the matching subfolder, and link important outputs from `STATUS.md`.

This separation keeps the workspace root readable:

- coordination stays in `AGENTS.md` and `STATUS.md`;
- the user guide stays in `MYCREW.md`; and
- working knowledge and deliverables stay in `project-context/`.

## Workspace scope

`crew-sop.mdc` is an always-applied Cursor rule. Its context, autonomy, notification, and file-coordination procedures therefore apply to every Agent task in the workspace, not only tasks delegated to generated crew members.

For that reason, the package is best installed in a dedicated crew workspace. Installing it in an unrelated project would also apply the SOP there.

After crew generation, relocation must keep the entire dedicated workspace together: `.cursor/`, `AGENTS.md`, `STATUS.md`, `MYCREW.md`, and `project-context/`. Moving only the installed package would separate the runtime policy from the crew's configuration, history, and outputs.

## Safety and consistency requirements

- Preview planned file changes and receive user approval before modifying the crew.
- Treat rebuild and removal as coordinated updates, not isolated file deletion.
- Never overwrite user-authored context without showing what will change.
- Keep the roster, task ownership, invocation names, and generated files synchronized.
- Preserve existing source material and crew outputs unless the user explicitly approves their removal.
- Preserve unrelated custom subagents outside the managed roster.
- Use `/crew-builder` as the single lifecycle entrypoint.

## Cursor integration

The architecture uses two native Cursor mechanisms:

- [Agent Skills](https://cursor.com/docs/skills) for the discoverable, reusable `/crew-builder` workflow and its progressively loaded references.
- [Subagents](https://cursor.com/docs/subagents) for project-level specialists in `.cursor/agents/`, explicit `/name` invocation, and automatic delegation.

The always-applied SOP remains a rule because it must govern the whole workspace. Crew generation produces subagents, not rules.
