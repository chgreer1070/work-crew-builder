# Work Crew Builder

Work Crew Builder is a zero-code way to create a small team of specialized AI helpers in Cursor. It is designed for professionals who do not write code: copy one folder, start one guided conversation, and review every proposed change before it is made.

The builder reviews all 41 activities in the U.S. Department of Labor's O*NET Generalized Work Activities framework and turns real duties into practical tasks. Each task remains visible as **Crew can do**, **Crew drafts—you decide**, or **Stays with you**, so Human-owned work is not filtered out.

## Quick start

1. Create a dedicated folder for your crew and open it in [Cursor](https://cursor.com).
2. Copy the **whole `.cursor` folder** from this repository into that workspace. See the [installation guide](INSTALL-GUIDE.md) for drag-and-drop steps.
3. Open Cursor Agent chat and enter:

   ```text
   /crew-builder Build a crew for my role
   ```

4. Answer the builder's questions, review its plan, and approve the files you want it to create.

Use `/crew-builder` again whenever you want to add, remove, or change a crew member, adjust autonomy, or rebuild the crew.

> The supplied `crew-sop.mdc` rule is always applied across the workspace. Install this package in a dedicated crew workspace so its operating procedures do not affect unrelated projects. Once a crew exists, keep and move that entire workspace together—not only `.cursor/`.

## Installed inputs and generated artifacts

### Copied during installation

```text
My-Work-Crew/
└── .cursor/
    ├── skills/
    │   └── crew-builder/
    │       ├── SKILL.md
    │       └── references/
    └── rules/
        └── crew-sop.mdc
```

### Created after you approve the crew

```text
My-Work-Crew/
├── .cursor/
│   └── agents/
│       ├── riley.md
│       └── ...
├── AGENTS.md
├── STATUS.md
├── MYCREW.md
└── project-context/
    ├── plans/
    ├── research/
    ├── drafts/
    └── final/
```

Crew members are Cursor **custom subagents** stored in `.cursor/agents/<name>.md`. Ask for one directly with `/name`, or describe the work normally and let Cursor delegate to the right crew member.

The builder manages only custom subagents listed in the `AGENTS.md` crew roster. Unrelated custom subagents already in `.cursor/agents/` stay outside that roster and are left untouched.

- `AGENTS.md` is the source of truth for durable context, priorities, configuration, and the managed crew roster.
- `STATUS.md` owns ordinary task and event state, including progress, handoffs, and questions that need your attention.
- `MYCREW.md` is the plain-language guide to your crew.
- All four standard `project-context/` subfolders keep source material and durable outputs out of the workspace root.

Use `/crew-builder` for lasting changes to context, priorities, standing instructions, tasks, roster, or autonomy. This keeps `AGENTS.md` and the related crew files reconciled.

## Using project context

Put briefs, specifications, examples, and other source material in `project-context/`. Crew members check it when relevant and save substantial work by type:

- `plans/` — strategies and plans
- `research/` — research and analysis
- `drafts/` — work in progress
- `final/` — finished, user-facing deliverables

All four standard folders are required. The builder creates and verifies them during crew setup.

## Repository contents

| Path | Purpose |
|---|---|
| `.cursor/skills/crew-builder/SKILL.md` | Canonical build and modification workflow |
| `.cursor/skills/crew-builder/references/` | O*NET, templates, and supporting builder guidance |
| `.cursor/rules/crew-sop.mdc` | Workspace-wide operating procedures for the generated crew |
| `README.md` | Project overview and quick start |
| `INSTALL-GUIDE.md` | Nontechnical installation and troubleshooting |
| `wcb-project-plan.md` | Architecture and design decisions |
| `project-context/.gitkeep` | Keeps the repository's context folder available |

## Troubleshooting

To start over, enter `/crew-builder Rebuild my crew`. Rebuild first previews the proposed changes and asks for approval before modifying files. Do not manually delete crew files.

## Learn more

- [Cursor Agent Skills](https://cursor.com/docs/skills)
- [Cursor Subagents](https://cursor.com/docs/subagents)

No coding, terminal, package manager, or plugin installation is required.
