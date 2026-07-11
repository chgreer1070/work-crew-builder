# Work Crew Builder

Work Crew Builder is a zero-code way to create a small team of specialized AI helpers in Cursor. It is designed for professionals who do not write code: copy one folder, start one guided conversation, and review every proposed change before it is made.

The builder uses the U.S. Department of Labor's O*NET Generalized Work Activities framework to turn a job into practical tasks, then groups those tasks into focused crew roles.

## Quick start

1. Create a dedicated folder for your crew and open it in [Cursor](https://cursor.com).
2. Copy the **whole `.cursor` folder** from this repository into that workspace. See the [installation guide](INSTALL-GUIDE.md) for drag-and-drop steps.
3. Open Cursor Agent chat and enter:

   ```text
   /crew-builder Build a crew for my role
   ```

4. Answer the builder's questions, review its plan, and approve the files you want it to create.

Use `/crew-builder` again whenever you want to add, remove, or change a crew member, adjust autonomy, or rebuild the crew.

> The supplied `crew-sop.mdc` rule is always applied across the workspace. Install this package in a dedicated crew workspace so its operating procedures do not affect unrelated projects.

## What the builder creates

Crew members are Cursor **custom subagents** stored in `.cursor/agents/<name>.md`. Ask for one directly with `/name`, or describe the work normally and let Cursor delegate to the right crew member.

```text
My-Work-Crew/
├── .cursor/
│   ├── agents/
│   │   ├── riley.md
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

- `AGENTS.md` holds shared context, priorities, and the crew roster.
- `STATUS.md` records progress, handoffs, and questions that need your attention.
- `MYCREW.md` is the plain-language guide to your crew.
- `project-context/` keeps source material and durable outputs out of the workspace root.

## Using project context

Put briefs, specifications, examples, and other source material in `project-context/`. Crew members check it when relevant and save substantial work by type:

- `plans/` — strategies and plans
- `research/` — research and analysis
- `drafts/` — work in progress
- `final/` — finished, user-facing deliverables

The builder creates these folders during crew setup.

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
