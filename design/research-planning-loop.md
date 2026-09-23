# Research ↔ Planning Loop

The defining behavior of NOETIDE is a bidirectional research-planning loop.

## Core loop

```text
Research
  ↓
Evidence ingestion
  ↓
Strategy Graph update
  ↓
Planning
  ↓
Critique
  ↓
Gap detection
  ↓
Research Backlog
  ↓
Targeted Research
  ↓
Partial invalidation
  ↓
Plan patch
  ↺
```

## Research

Research starts from a persistent Research Question, not an ad-hoc prompt.

A research operation should:

1. generate relevant perspectives
2. produce search/fetch tasks
3. collect sources
4. snapshot or reference source material
5. extract evidence candidates
6. classify relevance and polarity
7. store evidence with provenance
8. identify unresolved coverage gaps

STORM/Co-STORM are useful references for perspective-guided questioning and moderator-style discovery of unknown unknowns.

## Planning

Planning consumes the current Strategy Graph.

It must not directly consume untracked raw web output.

Planner output is a patch proposal containing:

- changed decisions
- changed plan steps
- newly introduced assumptions
- alternatives considered
- explicitly preserved nodes
- newly generated research questions

## Critique

Critique is separated into focused checks rather than one generic "critic".

### Evidence critic

Finds decisions with weak, stale, missing, or contradictory evidence.

### Assumption critic

Finds explicit and implicit assumptions that materially affect the plan.

### Alternative critic

Finds important alternatives that have not been considered.

### Dependency critic

Finds where one uncertain decision can invalidate many downstream plan steps.

## Research backlog generation

Critique findings become Research Questions.

A baseline priority formula may be:

```text
priority =
  impact
  × uncertainty
  × dependency centrality
  ÷ estimated research cost
```

The exact formula is tunable, but priority must not be based only on model preference.

## Partial invalidation

New evidence does not trigger a full rewrite.

The application first traverses explicit Strategy Graph dependencies to bound the affected set.

Example:

```text
Evidence E42
  └─ contradicts → Hypothesis H7
                       ↓
                    Decision D3
                       ↓
                    Plan Step P5
```

Only H7, D3, P5, and their dependents are candidates for reassessment.

## Convergence

The loop must not end because a model says "done".

Possible convergence criteria include:

- no unresolved critical contradiction
- no unsupported high-impact assumption
- no open high-impact research question above a threshold
- plan change rate below a threshold across recent cycles
- research/model budget exhausted
- explicit user approval

## Experiments as research

Execution can create new evidence.

```text
Experiment
→ Observation
→ Evidence
→ Hypothesis update
→ Decision reassessment
```

For example, a social-media posting experiment produces observations. Those observations become evidence; they do not directly overwrite strategy.

## Execution boundary

When a branch reaches an approved state, NOETIDE emits an Execution Handoff containing:

- strategy revision
- objective
- approved decisions
- constraints
- plan steps
- protected nodes
- remaining open questions

Execution results return as observations, restarting the loop when appropriate.
