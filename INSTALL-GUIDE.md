# Work Crew Builder installation guide

No coding experience is needed. Installation is a folder copy, and the builder guides you in plain language.

## Before you begin

You need:

- [Cursor](https://cursor.com) installed on your computer
- the downloaded Work Crew Builder repository
- a new or dedicated folder for your crew

The supplied `crew-sop.mdc` rule is always applied throughout the workspace where it is installed. A dedicated crew workspace prevents those operating procedures from affecting unrelated work.

## What you will copy

Copy the **whole `.cursor` folder**, including its subfolders:

```text
.cursor/
├── skills/
│   └── crew-builder/
│       ├── SKILL.md
│       └── references/
└── rules/
    └── crew-sop.mdc
```

Do not copy only `SKILL.md`. The skill uses the material in `references/`, and the crew relies on the workspace-wide SOP.

## Install in four steps

### 1. Create a crew workspace

1. Open Cursor.
2. Choose **File → Open Folder**.
3. Create and open a new folder, such as `My-Work-Crew`.

### 2. Copy the package

1. Open the Work Crew Builder download in your computer's file explorer.
2. Drag the entire `.cursor` folder into the top level of `My-Work-Crew`.
3. If your computer hides folders whose names begin with a dot, turn on **Show hidden files** first.

The result should include:

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

### 3. Start the builder

Open Cursor Agent chat and enter:

```text
/crew-builder Build a crew for my role
```

The builder asks about your work, maps suitable tasks using the O*NET Generalized Work Activities framework, and helps you choose how independently the crew may act. It shows you the proposed crew and file changes before asking permission to create them.

### 4. Review the result

After approval, your workspace should look like this:

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

Crew member names will be chosen for your role. Each file in `.cursor/agents/` defines a Cursor custom subagent.

## Work with your crew

- Invoke a crew member directly: `/riley Draft this week's status update`.
- Or describe the task normally and let Cursor delegate it to the appropriate subagent.
- Put useful briefs, examples, and source documents in `project-context/`.
- Check `STATUS.md` for progress and questions.
- Edit `AGENTS.md` when priorities or shared context change.
- Use `/crew-builder` to add, remove, or modify crew members or to change autonomy.

## Troubleshooting

### `/crew-builder` does not appear

- Confirm the file is at `.cursor/skills/crew-builder/SKILL.md`, with no extra folder level.
- Confirm the accompanying `references/` folder was copied.
- Reopen the workspace, then start a new Agent chat and type `/crew-builder`.

### A crew member does not appear

- Check that its file is under `.cursor/agents/`.
- Start a new Agent chat after the crew was created.
- Ask naturally for that subagent, or invoke it with `/name`.

### I want to start over

Enter:

```text
/crew-builder Rebuild my crew
```

Rebuild previews every proposed change and asks for approval before modifying files. Do not manually delete generated files; the builder keeps the crew roster and shared documents consistent.

### The crew rules affect unrelated work

The SOP is intentionally workspace-wide. Move the package to a dedicated crew workspace and use that workspace for crew tasks.

## File reference

| Path | Purpose |
|---|---|
| `.cursor/skills/crew-builder/SKILL.md` | Guided entrypoint for building and changing a crew |
| `.cursor/skills/crew-builder/references/` | O*NET and file-generation guidance used by the skill |
| `.cursor/rules/crew-sop.mdc` | Always-applied operating procedures for this workspace |
| `.cursor/agents/<name>.md` | Generated custom subagent definitions |
| `AGENTS.md` | Shared context, priorities, and crew roster |
| `STATUS.md` | Progress, handoffs, and questions |
| `MYCREW.md` | Plain-language crew guide |
| `project-context/` | Source material and durable crew outputs |

For Cursor terminology and behavior, see [Agent Skills](https://cursor.com/docs/skills) and [Subagents](https://cursor.com/docs/subagents).
