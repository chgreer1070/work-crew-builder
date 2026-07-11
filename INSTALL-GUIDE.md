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

The builder asks about your work, reviews all 41 O*NET Generalized Work Activities, and classifies each real task as **Crew can do**, **Crew drafts—you decide**, or **Stays with you**. Human-owned duties remain visible. The builder then helps you choose how independently the crew may act and shows the proposed crew and file changes before asking permission to create them.

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

All four standard `project-context/` subfolders are required. Crew member names will be chosen for your role, and each managed crew member has a custom-subagent file in `.cursor/agents/`. Existing custom subagents that are not listed in the `AGENTS.md` roster remain outside the managed crew and are left untouched.

## Work with your crew

- Invoke a crew member directly: `/riley Draft this week's status update`.
- Or describe the task normally and let Cursor delegate it to the appropriate subagent.
- Put useful briefs, examples, and source documents in `project-context/`.
- Check `STATUS.md`, the source of truth for ordinary task and event state, for progress and questions.
- Use `/crew-builder` for durable changes to context, priorities, standing instructions, tasks, roster, or autonomy. The builder reconciles those changes across `AGENTS.md` and the related crew files.

## Troubleshooting

### `/crew-builder` does not appear

- Confirm the file is at `.cursor/skills/crew-builder/SKILL.md`, with no extra folder level.
- Confirm `SKILL.md` begins with valid YAML frontmatter containing `name: crew-builder` and a non-empty `description`.
- Reload the workspace window, or close and reopen the folder, so Cursor discovers the Skill again. Then start a new Agent chat and type `/crew-builder`.

A missing `references/` folder affects execution after invocation; it does not determine whether `/crew-builder` appears in the slash-command list. If the command appears but cannot complete its workflow, confirm the whole `references/` folder was copied.

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

The SOP is intentionally workspace-wide. After a crew exists, do not move only the installed package or `.cursor/` folder. Move or open the entire dedicated crew workspace as one unit, including `.cursor/`, `AGENTS.md`, `STATUS.md`, `MYCREW.md`, and `project-context/`. This keeps crew configuration, task history, and outputs together.

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
