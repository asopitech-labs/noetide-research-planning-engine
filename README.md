# NOETIDE

**Continuous Research and Planning Engine**

> Research changes the plan. The plan changes the research.

NOETIDE is a deliberation engine for continuously refining decisions through research and planning.

Most AI research tools follow a one-way process:

```text
Question
→ Research
→ Report
```

Planning agents usually follow another:

```text
Goal
→ Plan
→ Execute
```

NOETIDE connects these processes into a continuous loop.

```text
Research
   ↓
Evidence
   ↓
Hypothesis
   ↓
Planning
   ↓
Critique
   ↓
Unknowns / Assumptions / Contradictions
   ↓
Research Questions
   ↓
Targeted Research
   ↓
Plan Revision
   ↺
```

A plan is not the final output.

A plan is also a mechanism for discovering what still needs to be known.

## Core principles

### Plans create questions

Planning exposes assumptions, missing evidence, unresolved risks, and unknowns.

NOETIDE turns those gaps into explicit research questions and feeds them back into the research process.

### Evidence changes only what it touches

New information should not regenerate the entire strategy.

NOETIDE tracks dependencies between evidence, hypotheses, decisions, and plan steps so that only affected parts are reconsidered.

```text
Evidence
  ↓
Hypothesis
  ↓
Decision
  ↓
Plan Step
```

Unrelated decisions remain intact.

### State over sessions

LLM conversations are not the source of truth.

NOETIDE stores research and strategy as persistent structured state owned by the user.

```text
Strategy State
├─ Sources
├─ Evidence
├─ Facts
├─ Hypotheses
├─ Assumptions
├─ Research Questions
├─ Decisions
├─ Alternatives
├─ Risks
├─ Plans
├─ Experiments
└─ Observations
```

Model sessions can disappear without losing the accumulated reasoning state.

### Patches over regeneration

Planning changes are represented as explicit patches against the current strategy state.

A correction should update the affected decisions, not replace every previous conclusion.

### Deliberation before execution

NOETIDE is not primarily an execution agent.

Its responsibility is to determine what is known, what remains uncertain, what should be investigated next, and how the current plan should change.

Execution systems can consume an approved NOETIDE plan downstream.

```text
NOETIDE
   ↓
Approved Strategy / Plan
   ↓
Codex / Claude Code / CMS / CRM / Other Executors
```

## Architecture

NOETIDE is centered on a persistent Strategy Graph rather than an LLM conversation.

```text
                    User Interface
                         │
                         ▼
                Application Layer
                         │
                         ▼
                Deliberation Runtime
                         │
          ┌──────────────┼──────────────┐
          ▼              ▼              ▼
      Researcher       Critic        Planner
          │              │              │
          └──────────────┼──────────────┘
                         ▼
                  Strategy Patches
                         │
                         ▼
                   Strategy Graph
          ┌──────────────┼──────────────┐
          ▼              ▼              ▼
       Evidence      Event Log    Research Backlog
```

The main implementation is planned around:

- TypeScript
- Node.js
- XState
- PostgreSQL
- pgvector
- Zod
- Drizzle ORM
- React

LLMs are treated as replaceable reasoning providers rather than persistent state holders.

## Reasoning model roles

NOETIDE separates different kinds of model work.

### Jev

Used for fast, constrained semantic decisions such as:

- support / contradict / irrelevant
- unchanged / contested / invalid
- relevance classification
- research question prioritization
- patch validation

### High-capability reasoning models

Used for deeper tasks such as:

- hypothesis generation
- assumption discovery
- plan critique
- alternative generation
- synthesis
- plan patch proposals

Model integrations are adapters around the NOETIDE core. The Strategy Graph remains independent of any individual model or provider.

## Strategy Graph

NOETIDE represents reasoning explicitly.

Typical relationships include:

```text
Source
  └─ produces → Evidence

Evidence
  ├─ supports → Hypothesis
  └─ contradicts → Hypothesis

Hypothesis
  ├─ depends_on → Assumption
  └─ motivates → Decision

Decision
  ├─ selects → Alternative
  ├─ used_by → Plan
  └─ exposed_to → Risk

Plan
  ├─ contains → Plan Step
  └─ reveals → Research Question

Experiment
  └─ produces → Observation

Observation
  └─ produces → Evidence
```

This graph makes partial invalidation possible.

If new evidence contradicts one hypothesis, NOETIDE can identify which decisions and plan steps depend on it without discarding unrelated reasoning.

## Research backlog

Research questions are first-class persistent objects.

They can be prioritized by factors such as:

```text
priority =
  decision impact
  × uncertainty
  × dependency centrality
  ÷ research cost
```

The system therefore prioritizes questions whose answers are most likely to change important decisions.

## Intended use cases

NOETIDE is domain-independent.

Potential applications include:

- marketing strategy
- social media strategy
- editorial planning
- sales strategy
- product planning
- technical architecture
- engineering research
- competitive research
- long-running strategic investigations

The same deliberation loop applies across these domains:

```text
Research
↔ Hypothesis
↔ Decision
↔ Planning
```

## What NOETIDE is not

NOETIDE is not:

- a chat memory layer
- a report generator
- a generic autonomous agent
- a coding agent
- a marketing content generator
- a workflow automation platform

Those systems may operate upstream or downstream of NOETIDE, but the core responsibility of NOETIDE is continuous deliberation.

## Project status

NOETIDE is currently in the research and architecture phase.

The initial work focuses on:

- Strategy Graph schema
- evidence provenance
- research backlog
- partial invalidation
- patch-based planning
- research/planning loop orchestration
- reasoning-provider abstraction
- persistent user-owned state

## License

Apache License 2.0
