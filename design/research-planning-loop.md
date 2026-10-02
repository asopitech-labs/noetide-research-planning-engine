# Research ↔ Planning Loop

The defining behavior of NOETIDE is a bidirectional research-planning loop. Continuous collection feeds this loop through an explicit intake boundary; it does not replace question-driven research or allow unvalidated items to change strategy.

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

## Two research modes

### Ambient Research

Ambient Research monitors the Source Portfolio on source-specific schedules or source events.

```text
Source Portfolio
→ collect
→ Raw Item Cache
→ normalize / deduplicate
→ Signal
→ Signal Inbox
→ dismiss / defer / watch / link / promote
```

Its output is a Signal, not Evidence. Most collected items should stop after caching, deduplication, or low-cost triage. A promoted Signal creates an Evidence Candidate, links to an existing Research Question, or opens a new Research Question.

### Targeted Research

Targeted Research starts from a persistent Research Question, not an ad-hoc prompt.

A Targeted Research operation should:

1. generate relevant perspectives
2. produce search/fetch tasks
3. collect sources
4. snapshot or reference source material
5. extract evidence candidates
6. classify relevance and polarity
7. store evidence with provenance
8. identify unresolved coverage gaps

STORM/Co-STORM are useful references for perspective-guided questioning and moderator-style discovery of unknown unknowns.

Targeted Research may reuse cached raw items and prior enrichment, but it must preserve original provenance and recheck freshness.

## Progressive Enrichment

Incoming material moves through a cost-aware ladder:

1. capture identity, timestamps, hashes, and raw references
2. normalize metadata, language, topics, and entities
3. triage relevance, novelty, urgency, and likely affected state
4. contextualize against objectives and Research Questions
5. promote to an Evidence Candidate or Research Question
6. investigate through Targeted Research
7. integrate validated Evidence through partial invalidation and a patch

Each stage can stop processing. Jev is suited to constrained triage and routing; deeper reasoning is reserved for ambiguous or high-impact items.

## Coverage Model

Coverage is tracked across objectives, decisions, hypotheses, risks, and Research Questions. Useful dimensions include source diversity and independence, authority, evidence polarity, freshness, geography or perspective, and collection health.

Coverage gaps may create or reprioritize Research Questions or suggest changes to the Source Portfolio. Coverage is an operational diagnostic, not Evidence and not a correctness score.

## Review Cadence

NOETIDE separates:

- source collection cadence
- Signal Inbox review cadence
- coverage review cadence
- high-impact strategy review
- Source Portfolio review cadence

Scheduled batching is the default for routine material. Urgent Signals may trigger event-driven review without forcing every source onto the same schedule.

## Cache as Memory

The Raw Item Cache is durable research memory. It retains content-addressed artifacts or references, adapter checkpoints, canonicalization and duplicate links, enrichment versions, triage outcomes, and links to downstream research objects.

Cache reuse saves collection and model work, but a cached item does not become current Evidence without provenance and freshness checks.

See [Continuous Research Intake](continuous-research-intake.md) for the complete intake design.

## Planning

Planning consumes the current Strategy Graph.

It must not directly consume untracked raw web output, Raw Item Cache entries, or unpromoted Signals.

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
