# NOETIDE Architecture

NOETIDE is a continuous research and planning engine. Its core responsibility is not execution; it is to maintain a durable strategy state, repeatedly turn plans into research questions, and use new evidence to update only the parts of the strategy that are actually affected.

## Architectural principles

1. **Strategy state is the source of truth.** LLM conversations and sessions are transient.
2. **Patches over regeneration.** New evidence produces local changes, not full-plan rewrites.
3. **Research and planning share one state model.** Evidence, hypotheses, decisions, plans, and research questions live in the same graph.
4. **Execution is downstream.** NOETIDE hands approved plans to external executors.
5. **Models are replaceable compute providers.** Jev and Codex are adapters, not state owners.
6. **User-owned persistence.** Long-lived strategic state is stored locally or in user-controlled infrastructure.

## System overview

```text
┌─────────────────────────────────────────────┐
│                    UI                       │
│ Strategy / Research / Changes / History     │
└─────────────────────┬───────────────────────┘
                      │
                      ▼
┌─────────────────────────────────────────────┐
│            TypeScript Application           │
│ API + domain services + validation          │
└─────────────────────┬───────────────────────┘
                      │
                      ▼
┌─────────────────────────────────────────────┐
│          Deliberation Runtime               │
│                 XState                      │
│                                             │
│ critique → research → invalidate → replan   │
└─────────────┬──────────────┬────────────────┘
              │              │
              ▼              ▼
       Research layer   Reasoning layer
                          ├─ Jev
                          └─ Codex RPC adapter
              │              │
              └──────┬───────┘
                     ▼
               Proposal / Patch
                     │
              Validation + OCC
                     │
                     ▼
┌─────────────────────────────────────────────┐
│                  AlopexDB                   │
│ Strategy Graph / Event Log / Backlog        │
│ Sources / Evidence / Vector Retrieval       │
└─────────────────────────────────────────────┘
```

## Application stack

- **Language:** TypeScript
- **Runtime:** Node.js
- **Workflow/state machine:** XState
- **Validation:** Zod
- **Primary storage:** AlopexDB
- **Frontend:** React
- **Background work:** local worker queue initially; queue implementation remains replaceable
- **Object/blob storage:** filesystem initially, S3-compatible storage when remote deployment requires it

The domain layer must not depend directly on XState, Jev, Codex, or a specific AlopexDB transport. Those are adapters around domain interfaces.

## Core domain model

NOETIDE maintains a Strategy Graph with at least the following node types:

- Objective
- Source
- Evidence
- Fact
- Hypothesis
- Assumption
- Research Question
- Decision
- Alternative
- Risk
- Plan
- Plan Step
- Experiment
- Observation
- Outcome

Representative relations:

```text
Source ─produces→ Evidence

Evidence ─supports────→ Hypothesis
Evidence ─contradicts→ Hypothesis

Hypothesis ─depends_on→ Assumption
Hypothesis ─motivates─→ Decision
Hypothesis ─generates─→ Research Question

Decision ─selects──→ Alternative
Decision ─used_by──→ Plan
Decision ─exposed_to→ Risk

Plan ─contains→ Plan Step
Plan ─reveals─→ Research Question

Experiment ─tests───→ Hypothesis
Experiment ─produces→ Observation
Observation ─produces→ Evidence
```

## State transition model

The top-level XState machine is explicit and auditable:

```text
CRITIQUE
  ├─ gaps found → RESEARCH
  └─ stable     → REVIEW

RESEARCH
  → INGEST
  → INVALIDATE
  → REASSESS
  → REPLAN
  → CRITIQUE

REVIEW
  ├─ approve → DONE
  └─ revise  → CRITIQUE
```

Workflow state must remain small. Long-lived truth belongs in AlopexDB. XState should carry identifiers, iteration counters, budgets, and current transition context rather than entire plans or evidence corpora.

## Patch-based planning

Planner output is not a regenerated plan document. It is a typed patch proposal against a known revision.

A patch contains:

- base revision
- operations
- affected nodes
- explicitly preserved nodes
- rationale
- supporting evidence references
- newly generated research questions

The application validates the patch before applying it.

## Partial invalidation

When evidence changes, the system deterministically traverses dependency edges to find potentially affected nodes.

```text
Evidence
  ↓
Hypothesis
  ↓
Decision
  ↓
Plan Step
```

The graph traversal is deterministic application logic. Semantic reassessment is delegated to Jev or Codex only after the candidate impact set is bounded.

Each affected node is classified as:

- unchanged
- contested
- invalid

Unrelated nodes remain untouched.

## Human control

High-impact changes require explicit review. At minimum:

- changing or invalidating protected decisions
- merging branches
- rejecting critical assumptions
- changing high-impact objectives
- handing off a plan to an executor

Nodes can be user-protected. Models may propose changes to protected nodes but cannot apply them directly.

## Branching

Strategies may fork without overwriting the current path.

Examples:

- main
- conservative
- aggressive-growth
- editorial-focused

Branches share source and evidence data where valid, while decisions, assumptions, and plans may diverge.

## Executor boundary

NOETIDE produces an immutable execution handoff:

```text
NOETIDE
  ↓
Approved Strategy / Plan
  ↓
Execution Handoff
  ↓
Codex / Claude Code / CMS / CRM / other executor
```

Executors may return artifacts, results, observations, and metrics. They do not write directly to the Strategy Graph.

## OSS and prior-art reuse

NOETIDE should reuse ideas rather than inherit incompatible control models:

- **Co-STORM / STORM:** perspective generation, question generation, moderator-style exploration, unknown-unknown discovery.
- **Magentic:** facts/plan/progress ledger concepts and replanning triggers.
- **Deep Research implementations:** research decomposition, source gathering, gap detection, and citation patterns.

NOETIDE keeps its own state model, patch model, invalidation model, and research-planning loop.
