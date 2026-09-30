# NOETIDE Requirements

This document defines product and implementation requirements for NOETIDE.

## Product objective

NOETIDE must help a user deepen strategy through repeated cycles of research and planning.

The required loop is:

```text
Research
→ Evidence
→ Hypothesis
→ Plan
→ Critique
→ Gap / Assumption / Contradiction
→ Research Question
→ Targeted Research
→ Partial Plan Revision
↺
```

Ambient Research must be able to feed this loop without changing its authority model:

```text
Source Portfolio
→ Ambient Research
→ Raw Item Cache
→ Signal Inbox
→ Progressive Enrichment
→ Evidence Candidate / Research Question
→ Targeted Research
→ Evidence
```

A Signal is not Evidence and must not directly invalidate or update strategy.

A plan is an intermediate reasoning artifact, not the terminal output.

## Functional requirements

### FR-1 Persistent strategy state

The system must persist research and strategy independently of any model conversation or session.

The persisted state must include:

- sources
- evidence
- facts
- hypotheses
- assumptions
- questions
- decisions
- alternatives
- risks
- plans
- experiments
- observations
- outcomes
- source profiles
- raw-item cache entries
- signals and review outcomes
- coverage snapshots
- review cadences

### FR-2 Research-to-planning traceability

Every decision and plan step must be traceable back to its supporting and contradicting evidence.

### FR-3 Planning-to-research generation

The system must detect gaps revealed by planning and turn them into explicit research questions.

Gap classes must include at least:

- unsupported assumption
- weak evidence
- contradiction
- missing alternative
- unresolved risk
- stale evidence

### FR-4 Research backlog

Research questions must be first-class persistent objects with status, origin, impact, uncertainty, estimated cost, and priority.

### FR-5 Patch-based plan updates

The system must update strategies through explicit patches rather than regenerate an entire strategy by default.

Every patch must identify:

- base revision
- changed nodes
- preserved nodes
- supporting evidence
- reason for change

### FR-6 Partial invalidation

When evidence changes, only dependent hypotheses, decisions, and plan steps may be reconsidered automatically.

Unrelated nodes must remain unchanged.

### FR-7 Branching

Users must be able to fork a strategy and explore alternatives without overwriting the source branch.

### FR-8 Protected decisions

Users must be able to protect nodes from automatic modification.

### FR-9 Human review

The system must support review before applying high-impact patches and before execution handoff.

### FR-10 Research provenance

Evidence must retain source provenance including source identity, location/fragment, retrieval time, extraction metadata, and freshness information where available.

### FR-11 Evidence / interpretation separation

Observed evidence and model-generated interpretation must be stored as different object types.

### FR-12 Experiments and observations

Real-world execution results must be ingestible as observations and converted into evidence without bypassing the evidence/hypothesis layer.

### FR-13 Model abstraction

The core domain must not depend on one LLM provider or one session format.

### FR-14 Jev integration

Jev must be usable for constrained semantic decisions such as relevance, support/contradiction, impact classification, routing, ranking, and validation.

### FR-15 Codex integration

Codex must be exposed to the core as an API-like stateless reasoning provider.

The application must not depend on Codex thread history for correctness.

### FR-16 Research provider abstraction

Search, files, GitHub, CRM, analytics, and future source systems must enter through research-provider adapters.

### FR-17 Executor isolation

Execution systems must not mutate Strategy State directly.

### FR-18 Source Portfolio

The system must maintain a governed Source Portfolio with adapter type, topical scope, expected update frequency, collection cadence, quality notes, freshness expectations, cost/access constraints, health, and lifecycle status.

### FR-19 Signal and Signal Inbox

Ambient collection must create normalized, deduplicated Signals in a reviewable Signal Inbox.

A Signal must preserve its raw-item reference and provenance. Supported dispositions must include dismiss, defer, watch, link, promote, and merge. Inbox actions must be auditable events and must not directly mutate Strategy State.

### FR-20 Ambient and Targeted Research

The system must distinguish:

- Ambient Research, triggered by source cadence or source events to notice relevant change
- Targeted Research, triggered by a persistent Research Question to resolve decision-relevant uncertainty

Both modes must use the same provenance rules, budgets, provider interfaces, and evidence-validation boundary.

### FR-21 Progressive Enrichment

Research intake must support staged enrichment: capture, normalize, triage, contextualize, promote, investigate, and integrate.

Each stage must be able to stop further processing. Promotion thresholds and rationales must be configurable and auditable.

### FR-22 Coverage Model

The system must assess coverage across high-impact objectives, decisions, hypotheses, risks, and Research Questions using dimensions including source diversity, authority, independence, evidence polarity, freshness, and collection health.

Coverage gaps may create or reprioritize Research Questions and suggest Source Portfolio changes. A coverage score must not be treated as evidence or correctness.

### FR-23 Review Cadence

The system must represent collection, Signal Inbox, coverage, strategy, and Source Portfolio review cadences explicitly.

Cadence policy must support both scheduled batches and event-driven urgent review.

### FR-24 Cache as Memory

The system must retain reusable raw research material, canonical identities, content hashes, adapter checkpoints, deduplication relationships, enrichment versions, prior triage outcomes, provenance links, and freshness metadata subject to retention and access policy.

Cached material must undergo freshness and provenance checks before reuse as an Evidence Candidate.

## Non-functional requirements

### NFR-1 User-owned state

A user must be able to retain Strategy State independently of an LLM subscription or conversation history.

### NFR-2 Auditability

Every material strategy change must be explainable from:

- prior revision
- triggering event
- evidence
- patch
- resulting revision

### NFR-3 Reproducibility

Given the same persisted state and bounded reasoning input, the system must preserve enough metadata to explain how a result was produced even when the underlying model is nondeterministic.

### NFR-4 Concurrency safety

Node updates must use revision checks or equivalent optimistic concurrency control.

### NFR-5 Replaceable providers

Reasoning, search, embedding, storage transport, and executor integrations must be replaceable through interfaces.

### NFR-6 Local-first operation

The architecture must support local execution with user-controlled persistence.

### NFR-7 Incremental scalability

The same logical data model should remain usable from a single local NOETIDE process through server-backed and distributed AlopexDB deployments.

### NFR-8 Structured outputs

Model-facing operations that mutate or classify state must use typed structured output validated by Zod or equivalent schemas.

### NFR-9 Bounded deliberation

The system must support budgets for:

- iterations
- research queries
- source fetches
- model calls
- elapsed time or operator-defined limits

### NFR-10 Idempotent intake

Repeated collection of the same item must not create duplicate actionable Signals. Ingestion must support adapter checkpoints, canonicalization, content hashing, and safe retries.

### NFR-11 Explicit convergence criteria

A deliberation cycle must not terminate merely because a model says it is done.

Convergence may consider:

- unresolved high-impact questions
- critical contradictions
- unsupported high-impact assumptions
- plan churn across recent iterations
- user approval

## Initial use-case requirements

NOETIDE is domain-independent, but the first design must support these without changing the core state model:

- social media strategy
- editorial/article planning
- marketing strategy
- sales strategy
- product planning
- technical architecture and engineering research

Domain-specific adapters may add vocabulary, metrics, and source types, but must not bypass the common Evidence → Hypothesis → Decision → Plan model.

## Explicit non-goals

NOETIDE is not primarily:

- a chat memory product
- a report generator
- a coding agent
- a generic autonomous executor
- a marketing content generator
- a workflow automation platform

Those systems may integrate with NOETIDE upstream or downstream.
