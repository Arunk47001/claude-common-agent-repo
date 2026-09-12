# claude-common-agent-repo

This repo holds a small, reusable Claude Code **squad pipeline**: a chain
of subagents that carry a raw idea from brainstorm to deployed code, each
agent reading the previous stage's output and writing its own stage's
output plus an append-only history, so every stage stays separate,
auditable, and independently re-runnable. It also includes a meta-agent
for designing further agents, and a skill that governs how implementation
code gets structured.

## Layout

```
.claude/
  agents/
    custom-agent-foundry.md  # meta-agent: designs/reviews other .claude/agents/*.md files
    specification.md         # brainstorms a raw idea into a written spec
    planner.md               # turns the latest spec into candidate build approaches
    task.md                  # breaks the latest plan into coder/deploy task lanes
    coder.md                 # implements the coder-lane tasks (writes real code)
    deploy.md                # executes the deploy-lane tasks (ships to a cloud target)
  skills/
    clean-architecture/
      SKILL.md               # layered, dependency-rule structure for implementation code
.squad/
  specification/<slug>.md    # current spec for one idea
  planner/<slug>.md          # current plan for that idea
  task/<slug>.md             # current task breakdown for that idea
  coder/<slug>.md            # current coder status report for that idea
  deploy/<slug>.md           # current deploy status report for that idea
.history/
  specification/<slug>.md    # append-only log of superseded spec versions
  planner/<slug>.md          # append-only log of superseded plan versions
  task/<slug>.md             # append-only log of superseded task-breakdown versions
  coder/<slug>.md            # append-only log of superseded coder-status versions
  deploy/<slug>.md           # append-only log of superseded deploy-status versions
```

`.squad/` and `.history/` are created on first use by each agent — they
won't exist in a fresh checkout until a stage has actually run.

## How the pieces fit together

- **`custom-agent-foundry`** (Read/Grep/Glob/Write/Edit/WebFetch/WebSearch)
  designs and writes new `.claude/agents/<name>.md` files, or reviews and
  tightens existing ones. It's the agent to use when extending this repo
  with another stage or a one-off agent — it is not itself a pipeline stage.
- **`specification`** (Read/Grep/Glob/Write/Edit) brainstorms a raw idea
  the user gives into a full spec (problem, proposed idea, scope, key
  components, open questions, status), written to
  `.squad/specification/<slug>.md`. Any existing version at that path is
  archived to `.history/specification/<slug>.md` first.
- **`planner`** (same tools) reads the most recently modified file in
  `.squad/specification/`, proposes two or more candidate build approaches
  — backend language/framework, frontend if one's needed, model choice
  when an LLM is involved, deployment target — each with trade-offs, names
  a recommendation, and lists anything the spec left undecided under
  "Decisions needed" instead of guessing. Output:
  `.squad/planner/<slug>.md` (+ history).
- **`task`** (same tools) reads the most recently modified file in
  `.squad/planner/`, and decomposes its recommended approach into two
  ordered lanes — **Coder tasks** and **Deploy tasks** — each task with an
  ID, dependencies, and acceptance criteria. Carries forward any unresolved
  "Decisions needed" as "Blocking decisions" rather than resolving them
  itself. Output: `.squad/task/<slug>.md` (+ history).
- **`coder`** (adds `Bash`) implements only the Coder-tasks lane from the
  most recently modified file in `.squad/task/`, in the language/framework
  that lane specifies, verifying each task's acceptance criteria with real
  build/test/lint commands. Writes a status report (completed / blocked /
  remaining) to `.squad/coder/<slug>.md` (+ history); the actual
  implementation code lives in the project's normal source layout, not
  under `.squad/`.
- **`deploy`** (adds `Bash`) executes only the Deploy-tasks lane from the
  same task file, shipping to the cloud target that lane specifies. It
  defaults to preview/staging and treats anything production-affecting or
  irreversible (resource deletion, prod secrets, prod domain/migration) as
  needing the user's explicit go-ahead in conversation, not something the
  task file alone authorizes. Writes a status report to
  `.squad/deploy/<slug>.md` (+ history).

Every stage keys its output off the **same kebab-case slug**: derived from
the idea at the `specification` stage, then carried forward unchanged as
each later stage's own output filename (it reuses the prior stage's
basename). This is what lets a single idea's spec, plan, task breakdown,
coder status, and deploy status all line up by filename across `.squad/`
and `.history/`.

Invoke the agents directly in sequence — `specification` → `planner` →
`task` → `coder`/`deploy` — for a one-shot "idea → shipped" request; to
cascade an update forward, re-invoke only the stage that changed (e.g. edit
the spec, then re-run `planner`, which re-runs `task`, and so on) rather
than redoing earlier stages that haven't changed.

## Squad conventions

- One idea = one slug = one filename, reused across every
  `.squad/<stage>/<slug>.md` and `.history/<stage>/<slug>.md`.
- Within a stage, `.squad/<stage>/<slug>.md` is always the **current**
  version; `.history/<stage>/<slug>.md` is an **append-only** log of every
  version it superseded, each entry under a dated
  `## Superseded <ISO date>` heading — never truncate or rewrite a
  `.history/` file, only add to it.
- Before any stage overwrites its current file, it archives the existing
  content to that stage's history file first — no version is ever lost
  silently.
- A stage never resolves what an earlier stage left open (a spec's open
  question, a plan's "Decisions needed", a task list's "Blocking
  decisions") — it carries the gap forward or stops, rather than guessing.
- `coder` and `deploy` each touch only their own lane (Coder tasks vs.
  Deploy tasks) from the task breakdown; neither crosses into the other's
  work.
- Reference code as `path/to/file:line` so links stay useful in an editor.
- Date anything time-sensitive (behavior snapshots, benchmarks, decisions,
  each stage's "Status" line).
- Never fabricate content to fill a gap — unresolved gaps get flagged in
  the relevant `.md` as open questions, or raised to the user, not
  guessed at.

## Writing implementation code

- The `clean-architecture` skill governs how implementation code gets
  structured (by `coder`, or by Claude directly): entities/domain → use
  cases → interface adapters → frameworks/drivers, with the Dependency
  Rule enforced — inner layers never depend on outer layers. It shapes
  internal structure only; it never overrides a language, framework, or
  deploy target a plan or task breakdown has already settled on.

## Adding a new agent or skill

- New agent: prefer using the `custom-agent-foundry` agent to design and
  write it. It adds `.claude/agents/<name>.md` with `name`, `description`
  (trigger phrases plus 1-2 `<example>` blocks — Claude Code uses this to
  decide when to invoke it), `model`, and the minimal `tools` list the job
  actually needs. Keep read-only agents read-only; a pipeline stage that
  writes to `.squad/`/`.history/` still gets no broader a tool list than
  that requires.
- New skill: add `.claude/skills/<name>/SKILL.md` with `name` and a
  `description` stating exactly when to trigger it. Keep the body to a
  concrete, ordered process plus explicit guardrails — skills are
  instructions Claude follows, not prose to summarize.
