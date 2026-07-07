![](banner.jpg)

# Orchestrate

A portable multi-agent orchestration skill that decomposes non-trivial tasks into parallel investigation, structured planning, council review, implementation, and verification phases - delivering reliable, well-reviewed outcomes for complex work across Codex, Claude Code, GLM, or any host with comparable delegation tools.

---

## Purpose

`orchestrate` gives an AI coding agent a structured playbook for tackling tasks too large or risky to handle in a single pass. It fans out investigation to specialist sub-agents, convenes a six-seat advisory council to stress-test the plan before a single file is touched, delegates implementation with strict file-ownership boundaries, and runs real verification gates before declaring anything done.

It is designed to be model-agnostic and host-agnostic: it maps logical roles (investigator, implementer, council seat, gate runner) to whatever models and delegation tools your environment provides.

---

## How to Use

Trigger orchestration with any of the following phrases:

| Trigger phrase | Effect |
|---|---|
| `/orchestrate` | Run full orchestration on the described task |
| `$orchestrate` | Alias for the above |
| `orchestrate this` | Orchestrate the task described in context |
| `run this through orchestration` | Same as above |

The agent will first evaluate whether orchestration is worthwhile. For tasks with fewer than roughly three meaningful subtasks - a single-file change, a simple lookup, a minor config edit - it will say so briefly and handle the work inline. If you want orchestration anyway, say so explicitly and a compact version of the workflow will run.

### Workflow Phases

Once triggered, orchestration proceeds through these phases automatically:

1. **Frame** - restate goal, success criteria, risks, and unknowns
2. **Investigate** - parallel sub-agent research with evidence requirements
3. **Plan** - full execution plan with file ownership and verification gates
4. **Council Review** - six-seat advisory panel reviews the plan in parallel
5. **Adjudicate** - synthesize council feedback into a revised plan
6. **Implement** - delegate code/doc edits with strict one-writer-per-file rules
7. **Verify** - run real deterministic checks (tests, lint, build, etc.)
8. **Review** - inspect final diff against every stated requirement
9. **Deliver** - commit, push, publish, or report proven outcomes

---

## Installation

Install it by placing the skill directory into your agent's skills folder. For Claude Code-style layouts, use:

```
~/.claude/skills/orchestrate/
```

Ensure the directory contains `SKILL.md`. No additional dependencies or configuration are required. The skill is automatically available in sessions that load local skills.

To verify the skill is loaded, start a new session and type:

```
/orchestrate help
```

---

## Examples of Usage

### Refactoring a module across many files

```
/orchestrate Refactor the authentication module to replace session tokens with JWTs. 
Update all call sites, add tests, and ensure nothing regresses.
```

The agent will investigate the current auth surface, plan the migration with file ownership assignments, have the council review the approach, implement with one agent per file partition, and run the test suite before reporting done.

---

### Auditing and fixing a bug with unclear root cause

```
orchestrate this - users are reporting intermittent 500 errors on /api/checkout. 
I don't know where it's coming from yet.
```

The agent will fan out investigation to locate the relevant code paths and logs, hypothesize root causes, plan a targeted fix, and verify it before closing.

---

### Building and publishing a new feature end-to-end

```
$orchestrate Add a dark mode toggle to the settings page. 
It should persist across sessions and apply immediately without a page reload.
```

The agent will investigate the current theming system, plan the full change including persistence and CSS strategy, run the council to surface risks, implement the feature, and verify it meets all stated criteria.

---

### Forcing orchestration on a small task

If you want the full orchestration structure even for a task that would normally be handled inline, say so explicitly:

```
/orchestrate Rename the `getUserById` function to `fetchUserById` everywhere. 
I know it's small - run full orchestration anyway.
```

---

## Council Seats

Every orchestrated plan is reviewed by six advisory perspectives before implementation begins:

| Seat | Focus |
|---|---|
| **Architect** | Long-term structure and abstractions |
| **Pragmatist** | Smallest real change that ships the outcome |
| **Skeptic** | Edge cases, security, failure modes |
| **Maintainer** | Operability, rollback, and on-call burden |
| **Contrarian** | A materially different approach, argued honestly |
| **Designer** | User experience and workflow ergonomics |

Council seats advise only - they do not edit files or run commands.

---

## Verification

Orchestration never declares a task done based on agent opinion alone. Every claim requires file-and-line evidence or command output. Deterministic checks (tests, lint, typecheck, build) must pass. Failed gates return to implementation; tests are never weakened to make a gate pass.

## License

This project is licensed under [CC BY-NC 4.0](https://darren-static.waft.dev/license) - free to use and modify, but no commercial use without permission.
