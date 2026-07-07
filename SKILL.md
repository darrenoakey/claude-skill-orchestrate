---
name: orchestrate
description: "Portable multi-agent orchestration for non-trivial tasks. Use when the user says \"$orchestrate\", \"/orchestrate\", \"orchestrate this\", \"run this through orchestration\", or asks for a planned multi-agent execution with investigation, council review, implementation, verification, commit, push, or publishing. Generalizes fable-style orchestration without depending on Claude/Fable: the current session model is the base orchestrator, cheaper/smaller models investigate, the strongest available coding model below the top/Fable tier writes code at medium reasoning, and an embedded six-seat council reviews plans before execution."
---

# Orchestrate

Use the current session model as the base orchestrator. Decompose, delegate, adjudicate, verify, and deliver; do not spend the main context on bulk exploration or mechanical implementation unless the task is too small to benefit from orchestration.

This skill is portable. Use whatever multi-agent, workflow, or delegation tool the host provides. If the host does not support explicit model selection, use the closest available defaults and state that limitation in the final result.

## Bypass Gate

First decide whether orchestration pays for itself. If the task has fewer than about three meaningful subtasks, or is a single-file/simple lookup/config change, say briefly that orchestration would add overhead and do it inline. If the user explicitly insists on orchestration, run a compact version of the workflow anyway.

## Model Roles

Map host-specific model names into these roles instead of hard-coding vendor names:

| Role | Use | Selection rule |
|---|---|---|
| Base orchestrator | Decomposition, synthesis, plan ownership, final review | The current session model |
| Mechanical investigator | File lists, bounded searches, log excerpts, simple summaries | Cheapest/smallest tool-using model |
| Interpretive investigator | Root-cause hypotheses, subsystem understanding, call graphs, relevance judgments | Mid-tier reasoning model |
| Council seat | Plan critique from one fixed perspective | Base orchestrator model, or best available reasoning model if seats can be model-selected |
| Coding implementer | Code/test/doc edits | Strongest available coding-capable model below the top/Fable tier, medium reasoning |
| Gate runner | Build, lint, tests, deterministic checks | Mid-tier model; no code authoring unless re-dispatched as a coding implementer |

If a platform exposes reasoning controls, set coding implementers to medium reasoning. Use lower reasoning for mechanical investigation and medium/high reasoning for contested design or synthesis. Never use a cheap model for code authoring when a stronger coding model is available.

## Workflow

1. **Frame the task** — restate the goal, success criteria, likely deliverables, risks, and concrete unknowns. Search durable memory first if available.
2. **Investigate** — fan out unknowns to mechanical or interpretive investigators in parallel. Keep prompts narrow and self-contained. Require file:line evidence or exact command/log evidence for claims.
3. **Plan** — synthesize a full execution plan yourself. Include file ownership boundaries, verification gates, deployment/publish steps, and what would count as done.
4. **Council review** — send the plan, not the whole conversation, to the embedded council. Run all six seats in parallel when the host supports it.
5. **Adjudicate** — revise the plan from council feedback. State internally which risks are real, which recommendations are rejected, and why. Do not delegate this synthesis.
6. **Implement** — dispatch coding work to implementers with explicit file partitions. One writer per file. If the edit is deeply entangled or judgment-heavy, do it in the base context.
7. **Verify** — run the real deterministic gates: tests, lint, typecheck, build, smoke checks, service restarts, persistence checks, publish checks, or equivalent. Failed gates return to implementation; never weaken tests.
8. **Review** — personally inspect the final diff/artifacts before delivery. Check that every user-requested outcome is complete.
9. **Deliver** — commit, push, publish, deploy, or open/update PRs when the surrounding rules require it. Report only proven outcomes and explicit blockers.

## Embedded Council

Use the council after the investigation-backed plan and before implementation whenever orchestration proceeds. The council advises only; it must not edit files, run commands that mutate state, or commit.

Run these six seats:

1. **Architect** — long-term structure, boundaries, abstractions, future change.
2. **Pragmatist** — smallest real change that ships the requested outcome.
3. **Skeptic** — edge cases, hidden assumptions, security, data loss, performance, failure modes.
4. **Maintainer** — operability, debugging, rollback, observability, on-call burden.
5. **Contrarian** — a materially different approach from the obvious one, argued honestly.
6. **Designer** — user experience, workflow ergonomics, polish, and visual/product coherence.

Council prompt template:

```text
You are "<SEAT NAME>" on an embedded orchestration council.

User goal:
<USER GOAL>

Current plan:
<PLAN>

Relevant evidence and constraints:
<ONLY THE CONTEXT THIS SEAT NEEDS>

Your role:
<ONE-PARAGRAPH SEAT DESCRIPTION>

Return under 300 words:
1. Recommendation: concrete change, approval, or rejection of the plan.
2. Top 3 reasons, priority ordered.
3. Biggest risk or weakness in your own recommendation.

Report only. Do not edit files. Do not run mutating commands. Do not hedge; other seats cover other angles.
```

Synthesize council results into the plan. Capture consensus, conflicts, and at least one recommendation you are deliberately not taking unless every seat truly agreed.

## Delegation Prompt Contract

Every investigator, implementer, and verifier prompt must be self-contained and end with this return format:

```text
Return format, maximum 300 words:
1. FINDINGS / RESULT — what you found or did, concrete.
2. EVIDENCE — file:line citations, command output excerpts, or artifact paths for every claim.
3. CONFIDENCE — high/medium/low, and what would change it.
4. OPEN QUESTIONS — anything unresolved or suspicious.
No file dumps. No transcripts. Your final message is the deliverable.
```

For implementers, add:

```text
You may modify ONLY these files:
<EXPLICIT FILE LIST OR OWNERSHIP BOUNDARY>

Use medium reasoning if the host supports reasoning controls. Do not commit, push, publish, deploy, or modify files outside your partition.
```

## Verification Rules

- Deterministic checks outrank agent opinion.
- Load-bearing claims from subagents need evidence. Spot-check at least one citation per critical finding.
- If two agents disagree or a result is low-confidence, re-run that subtask with a stronger model or inspect it yourself.
- Never let a subagent's summary dilute standing rules: no fabrication, no fake integrations, all tests pass, warnings are errors, persistence must be real.
- Do not declare done while known matching failures remain.
- Before final response, count the original requirements against completed, verified outcomes.

## Large Runs

For large fan-outs, audits, migrations, or many-stage work, prefer a workflow/batch orchestration tool if available so intermediate results stay out of the main context. Preserve the same role mapping, council step, one-writer rule, and verification gates.
