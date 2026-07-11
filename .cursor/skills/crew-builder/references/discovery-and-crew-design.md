# Discovery and Crew Design

Use this reference to turn a user's real duties into an approved, traceable task register and the smallest coherent crew. Do not use it as an agent-persona or workspace-file template.

## Contents

- [Source authority](#source-authority)
- [Discovery completeness](#discovery-completeness)
- [Normalize duties into tasks](#normalize-duties-into-tasks)
- [Stable Task IDs](#stable-task-ids)
- [Prioritize tasks](#prioritize-tasks)
- [Classify AI involvement](#classify-ai-involvement)
- [Review all O\*NET Generalized Work Activities](#review-all-onet-generalized-work-activities)
- [Canonical 41-activity register](#canonical-41-activity-register)
- [Turn activity evidence into task candidates](#turn-activity-evidence-into-task-candidates)
- [Size and compose the crew](#size-and-compose-the-crew)
- [Approval presentation contracts](#approval-presentation-contracts)
- [Deterministic design checks](#deterministic-design-checks)

## Source authority

Use O\*NET as a completeness aid, not as a substitute for discovery.

Official sources:

- O\*NET 30.3 current database release: https://www.onetcenter.org/database.html
- O\*NET Content Model: https://www.onetcenter.org/content.html
- O\*NET 30.3 Work Activities data dictionary: https://www.onetcenter.org/dictionary/30.3/excel/work_activities.html
- O\*NET 30.3 Work Activities workbook linked by that dictionary: https://www.onetcenter.org/dl_files/database/db_30_3_excel/Work%20Activities.xlsx
- O\*NET 30.3 Content Model Reference: https://www.onetcenter.org/dl_files/database/db_30_3_text/Content%20Model%20Reference.txt

The register below reproduces the 41 generalized Work Activity ID and name pairs in the official O\*NET 30.3 Content Model Reference and cross-checks them against the 41 unique `4.A` Element ID and Element Name pairs in the 30.3 Work Activities workbook. Preserve the official IDs and names when recording coverage.

Required attribution for the verbatim O\*NET activity IDs and names below:

> This page includes information from the O\*NET 30.3 Database by the U.S. Department of Labor, Employment and Training Administration (USDOL/ETA). Used under the [CC BY 4.0 license](https://creativecommons.org/licenses/by/4.0/). O\*NET® is a trademark of USDOL/ETA.

Treat the user's current duties as authoritative. Generic occupational data may suggest a question or candidate, but it never proves that the user performs a duty. When the user's account conflicts with an occupation profile or rating, follow the user and record the conflict as evidence. Do not use O\*NET importance ratings as the user's priority.

## Discovery completeness

Do not design a crew until all six areas have a concrete answer or the user explicitly confirms “none,” “unknown,” or “not applicable.”

1. **Role and context:** Capture the user's role, scope, industry or operating context, team relationships, and material constraints.
2. **Desired outcomes:** Capture the results the user is accountable for and how they recognize acceptable work.
3. **Recurring deliverables:** Capture at least one real recurring deliverable. Record its cadence, audience, inputs, approval path, and definition of done.
4. **Tools and access:** Capture systems, file locations, data sources, available Cursor tools or MCP servers, permission level, and access gaps. Distinguish claimed access from verified access.
5. **Stakeholders and approvals:** Capture recipients, collaborators, decision owners, external audiences, and required review or sign-off.
6. **Sensitive and high-stakes boundaries:** Capture confidential data, regulated work, legal or safety implications, financial authority, personnel decisions, external publishing, irreversible actions, and duties that must remain human.

Ask focused questions for missing areas. Do not fill gaps with plausible details. Keep the user's wording as evidence even when a normalized task label is shorter.

Discovery is complete only when:

- all six areas have an explicit answer;
- at least one recurring deliverable is described;
- every proposed task traces to evidence;
- all 41 O\*NET activities have a disposition and evidence;
- unknown tool or approval dependencies are visible;
- the user has reviewed the task register.

## Normalize duties into tasks

Build a candidate list from the user's statements, existing workspace evidence, recurring deliverables, and the 41-activity review.

Normalize each duty as follows:

1. Write one observable action and outcome in plain language.
2. Start with a specific verb and name the object or deliverable.
3. Include cadence, audience, system, or approval boundary only when it changes ownership or completion.
4. Split compound duties when the parts can have different owners, AI classes, approvals, or definitions of done.
5. Merge true duplicates while preserving every evidence source and important qualifier.
6. Separate preparation from final authority. For example, normalize drafting and approving as different tasks when the approval must stay human.
7. Keep physical execution, sensitive judgment, and external commitment visible; do not disguise them as general “support.”
8. Reject a candidate supported only by a generic occupation profile until the user confirms it.

Record at least these fields for every normalized task: Task ID, short label, outcome or definition of done, evidence, frequency, impact, time burden, priority, AI-involvement class, Owner, Contributors, tools/access, approver, and sensitive boundary.

## Stable Task IDs

Use `TASK-001`, `TASK-002`, and so on for a new register. Use at least three digits and continue monotonically beyond `TASK-999` if needed. Preserve an existing safe, unique Task ID exactly, including a semantic ID such as `TASK-RESEARCH-02`.

- Normalize and deduplicate candidates before assigning an ID.
- Assign the ID when the task first appears in a user-facing approval register.
- Never change an ID because its wording, priority, owner, contributor, cadence, or agent changes.
- Give each genuinely new task the next unused sequential ID in the register's established convention.
- Never renumber tasks to close gaps and never reuse a retired ID.
- Mark removed work `Retired` and preserve its label and history.
- When merging approved tasks, retain the earliest suitable ID and record the retired IDs as aliases after user approval.
- When splitting a task, retain the original ID for the scope that best preserves its meaning and assign new IDs to the other scopes.
- Resolve ambiguous merge or split identity with the user before changing the register.

Use IDs for every approval, ownership map, agent assignment, modification, and validation check. Labels help people read the register; IDs preserve identity.

## Prioritize tasks

Score frequency, impact, and time burden from `0` to `3`. Ask the user when evidence does not support a score.

| Score | Frequency | Impact | Time burden |
| --- | --- | --- | --- |
| 0 | Less than quarterly or exceptional | Convenience only | Less than 1 hour per month |
| 1 | Monthly or quarterly | Individual and readily reversible | 1–4 hours per month |
| 2 | Weekly | Team, customer, or deadline effect | 1–4 hours per week |
| 3 | Daily or several times weekly | Legal, safety, financial, executive, or material external effect | More than 4 hours per week |

Add the three scores:

- `7–9`: Critical
- `4–6`: Important
- `0–3`: Optional

Treat this score as a consistent starting point, not an authority. Let the user override any score or priority. Record the override and reason without arguing or silently recalculating it. When work is seasonal, record the period used for the score.

Use priority to order design effort, not to decide whether a real duty exists. Do not drop low-priority duties from the approved register.

## Classify AI involvement

Assign exactly one of these classes to every normalized task:

| Class | Meaning | Ownership rule |
| --- | --- | --- |
| `Crew can do` | A crew agent can complete the stated outcome with verified tools, access, and runtime approvals. | Assign one agent Owner. |
| `Crew drafts—you decide` | A crew agent can prepare, analyze, or recommend, but the user or named human makes the final judgment, approval, send, or commitment. | Word the task around the crew's draft or analysis, assign one agent Owner, and name the human approver. |
| `Stays with you` | The duty requires physical action, personal accountability, reserved authority, inaccessible systems, or judgment the user will not delegate. | Assign the Human as Owner; do not assign an agent as Owner. |

Allow Contributors without weakening the one-Owner rule. A Contributor may research, review, or supply inputs, but cannot become an implicit second Owner.

Classify the normalized outcome, not the broad subject. A crew may own “Draft the hiring recommendation,” while “Make the hiring decision” stays with the user.

Default sensitive, irreversible, regulated, personnel, safety, legal, financial-commitment, and external-publication work to `Crew drafts—you decide` or `Stays with you` until the user explicitly defines a safe boundary. Runtime autonomy never transfers authority the user did not grant.

## Review all O\*NET Generalized Work Activities

Review every activity in the register below. Never filter activities by ordinal position, numeric range, assumptions about office work, or AI suitability. Physical and equipment-related activities still require review because the crew may support planning, instructions, monitoring, records, procurement, or analysis.

For the runtime discovery output, reproduce all 41 rows and add `Disposition` and `Evidence` columns. Mark every row with exactly one disposition:

- **Included:** The activity is part of the user's role and yields at least one confirmed task candidate.
- **Assist-only:** The activity is part of the user's role, but crew support is limited to preparation, monitoring, documentation, analysis, or another explicit boundary.
- **Not applicable:** The activity is not part of the user's current role and yields no task.

Never leave a row unmarked. Give each row specific evidence, such as a direct user statement, a recurring deliverable, an existing workflow or artifact, or the user's explicit rejection. An O\*NET occupation match may justify asking, but it is not sufficient evidence by itself.

Do not preclassify the reference register: disposition depends on the user's actual duties. Do not mark an activity `Not applicable` merely because AI cannot perform it; use the AI-involvement class at task level to express that boundary.

## Canonical 41-activity register

| 4.A ID | Official O\*NET 30.3 Generalized Work Activity |
| --- | --- |
| 4.A.1.a.1 | Getting Information |
| 4.A.1.a.2 | Monitoring Processes, Materials, or Surroundings |
| 4.A.1.b.1 | Identifying Objects, Actions, and Events |
| 4.A.1.b.2 | Inspecting Equipment, Structures, or Materials |
| 4.A.1.b.3 | Estimating the Quantifiable Characteristics of Products, Events, or Information |
| 4.A.2.a.1 | Judging the Qualities of Objects, Services, or People |
| 4.A.2.a.2 | Processing Information |
| 4.A.2.a.3 | Evaluating Information to Determine Compliance with Standards |
| 4.A.2.a.4 | Analyzing Data or Information |
| 4.A.2.b.1 | Making Decisions and Solving Problems |
| 4.A.2.b.2 | Thinking Creatively |
| 4.A.2.b.3 | Updating and Using Relevant Knowledge |
| 4.A.2.b.4 | Developing Objectives and Strategies |
| 4.A.2.b.5 | Scheduling Work and Activities |
| 4.A.2.b.6 | Organizing, Planning, and Prioritizing Work |
| 4.A.3.a.1 | Performing General Physical Activities |
| 4.A.3.a.2 | Handling and Moving Objects |
| 4.A.3.a.3 | Controlling Machines and Processes |
| 4.A.3.a.4 | Operating Vehicles, Mechanized Devices, or Equipment |
| 4.A.3.b.1 | Working with Computers |
| 4.A.3.b.2 | Drafting, Laying Out, and Specifying Technical Devices, Parts, and Equipment |
| 4.A.3.b.4 | Repairing and Maintaining Mechanical Equipment |
| 4.A.3.b.5 | Repairing and Maintaining Electronic Equipment |
| 4.A.3.b.6 | Documenting/Recording Information |
| 4.A.4.a.1 | Interpreting the Meaning of Information for Others |
| 4.A.4.a.2 | Communicating with Supervisors, Peers, or Subordinates |
| 4.A.4.a.3 | Communicating with People Outside the Organization |
| 4.A.4.a.4 | Establishing and Maintaining Interpersonal Relationships |
| 4.A.4.a.5 | Assisting and Caring for Others |
| 4.A.4.a.6 | Selling or Influencing Others |
| 4.A.4.a.7 | Resolving Conflicts and Negotiating with Others |
| 4.A.4.a.8 | Performing for or Working Directly with the Public |
| 4.A.4.b.1 | Coordinating the Work and Activities of Others |
| 4.A.4.b.2 | Developing and Building Teams |
| 4.A.4.b.3 | Training and Teaching Others |
| 4.A.4.b.4 | Guiding, Directing, and Motivating Subordinates |
| 4.A.4.b.5 | Coaching and Developing Others |
| 4.A.4.b.6 | Providing Consultation and Advice to Others |
| 4.A.4.c.1 | Performing Administrative Activities |
| 4.A.4.c.2 | Staffing Organizational Units |
| 4.A.4.c.3 | Monitoring and Controlling Resources |

## Turn activity evidence into task candidates

1. Review the complete activity register with the user.
2. For each `Included` or `Assist-only` activity, ask for concrete examples, recurrence, outputs, recipients, systems, and approval boundaries.
3. Normalize examples into tasks; do not turn the broad O\*NET label itself into a task.
4. Link each task to one or more source activity IDs without changing its stable Task ID.
5. Deduplicate across activities. One real task may evidence several activities but still needs only one Task ID and one Owner.
6. Apply priority and AI-involvement rules.
7. Keep `Stays with you` duties visible in the register so crew scope is honest.
8. Ask the user to correct omissions, invented scope, or language that changes accountability.

## Size and compose the crew

Choose the smallest number of agents that creates coherent ownership and useful specialization.

- Prefer `3–5` agents for a typical multi-domain scope.
- Allow `1–2` agents for a small or tightly related scope.
- Use at most `7` agents unless the user explicitly approves an exception or a phased crew.
- Target `2–5` owned tasks per agent. Treat this as a design target, not a quota or hard validity rule.
- Never invent, split, or duplicate tasks merely to reach an agent or task-count target.
- Allow one-task specialists or agents with more than five tasks when the approved work genuinely warrants it; explain the exception.
- Give every active approved task exactly one Owner. Allow zero or more named Contributors.
- Keep `Stays with you` tasks owned by the Human.
- Do not create two agents with materially overlapping responsibility unless the owner/contributor boundary is explicit.
- Group by shared outcome, workflow, expertise, data, tools, or stakeholder—not by equal task counts.
- Separate roles when sensitive approval boundaries, conflicting duties, or materially different tool access require it.

Start with the approved tasks, form coherent clusters, and then choose agent count. Never start with a desired roster and manufacture work for it.

## Approval presentation contracts

Use short sentences and familiar words. Explain any technical term the user must act on.

### Task-scope approval

Present:

1. A six-part summary of role, outcomes, recurring deliverables, tools/access, stakeholders/approvals, and sensitive boundaries.
2. The complete 41-row O\*NET review with a disposition and evidence on every row.
3. The normalized task register with Task ID, label, evidence, three scores, priority, AI class, and human approval boundary.
4. A short list of unresolved questions or tool gaps.

End with: “Please approve these tasks and labels, or list changes by Task ID. No crew files will change yet.”

Do not treat corrections as approval. Revise the complete affected view and ask again.

### Crew-design approval

Present:

1. Each proposed agent and the outcome it owns.
2. Every Task ID with exactly one Owner and any Contributors.
3. Human-owned tasks and decision points.
4. Proposed agent count, any sizing exception, verified tools, access gaps, and canonical autonomy (`LOW` by default).
5. Existing-file collisions known at this stage.

End with: “Please approve this crew design or list changes by Task ID or agent name. Design approval does not change files.”

### File-mutation approval

After preflight, present each workspace-relative path, action, collision decision, and preservation note. End with: “No files will change until you approve these exact changes. Do you approve this path list?”

Only an explicit approval of that manifest authorizes mutation. Re-present it when any action or path changes.

## Deterministic design checks

Run these checks before presenting a design:

1. Assert all six discovery areas are explicitly answered and at least one recurring deliverable exists.
2. Assert the O\*NET review contains exactly the 41 unique IDs above, no extra `4.A` IDs, one allowed disposition per row, and non-empty evidence per row.
3. Assert each proposed task has user or workspace evidence; reject generic-data-only tasks.
4. Assert new default IDs match `^TASK-[0-9]{3,}$`; assert every preserved ID matches `^TASK-[A-Z0-9]+(?:-[A-Z0-9]+)*$`. Assert all IDs are unique, new IDs are monotonic in the established convention, and retired IDs are not reused.
5. Assert each task contains one action/outcome and has non-empty completion, tools/access, stakeholder, and boundary fields.
6. Assert frequency, impact, and time scores are each `0–3`; calculated priority matches the sum unless a user override and reason are recorded.
7. Assert every task has exactly one of the three AI-involvement classes.
8. Assert every active approved Task ID has exactly one Owner; `Stays with you` has Human as Owner; Contributors never count as additional Owners.
9. Assert the proposed agent roster owns the same set of agent-owned active Task IDs as the task register—no missing, unknown, or duplicate IDs.
10. Assert the crew has `1–7` agents, or records explicit user approval for an exception or phase.
11. Flag, but do not automatically fail, agents outside the `2–5` owned-task target; require a real-work rationale and never add quota tasks.
12. Assert role clusters are coherent and overlapping agents have an explicit owner/contributor boundary.
13. Assert every claimed tool or external access path is verified or clearly marked unavailable, approval-gated, or user-provided.
14. Assert sensitive and high-stakes tasks retain their named human decision or execution boundary.
15. Assert the user explicitly approved the current task scope and crew design versions before file preflight.

If a check fails, repair the discovery or design and repeat the affected approval. Do not weaken a check, invent evidence, or proceed with partial approval.
