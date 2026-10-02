# Continuous Research Intake

This document defines the upstream research-intake layer for NOETIDE. It extends the existing Research → Synthesize → Plan → Patch loop without changing its authority model: only validated Evidence can affect hypotheses, decisions, or plans, and changes still occur through partial invalidation and typed Strategy Patches.

## Purpose

NOETIDE needs two complementary research modes:

- **Ambient Research** continuously watches a deliberately managed set of sources and captures potentially relevant change.
- **Targeted Research** answers explicit Research Questions from the Research Backlog.

Ambient Research broadens awareness. Targeted Research resolves decision-relevant uncertainty. Neither mode bypasses provenance, validation, or human review.

## End-to-end flow

```text
Source Portfolio
      │
      ▼
Source Adapters / Scheduled Collection
      │
      ▼
Raw Item Cache ── deduplicate / normalize ──┐
      │                                     │
      ▼                                     │
Signal                                      │
      │                                     │
      ▼                                     │
Signal Inbox                                │
      │                                     │
      ├─ dismiss / defer / watch            │
      ├─ link to existing state             │
      └─ promote                            │
             │                              │
             ▼                              │
      Progressive Enrichment                │
             │                              │
             ├─ Evidence Candidate          │
             └─ Research Question           │
                      │                     │
                      ▼                     │
               Targeted Research            │
                      │                     │
                      ▼                     │
                   Evidence ◄───────────────┘
                      │
                      ▼
                Strategy Graph
                      │
                      ▼
          Partial Invalidation / Patch
```

A Signal is not Evidence. It is a triaged indication that a new item may matter. Promotion requires enough source context and provenance to create an Evidence Candidate or a Research Question; only validated Evidence enters the Strategy Graph's reasoning chain.

## Source Portfolio

The Source Portfolio is the governed set of sources NOETIDE monitors. It is not an unbounded list of feeds.

Each source profile should record:

- identity and adapter type, such as RSS, newsletter, API, repository, file, or web page
- domains, objectives, decisions, and research questions it may inform
- expected update frequency and collection cadence
- authority, reliability, and independence notes
- freshness expectations
- access constraints and collection cost
- enabled, paused, degraded, or retired status
- last successful retrieval and next scheduled review
- owner-supplied notes and exclusions

Portfolio management is part of research quality. Adding more sources is not automatically better; the portfolio should balance authority, diversity, timeliness, and cost.

## Signal

A Signal is a normalized, deduplicated pointer to a potentially relevant change.

Minimum fields include:

- stable identifier
- source profile and raw-item reference
- canonical URI and content hash
- title or concise description
- publication and retrieval timestamps
- detected topics and entities
- relevance and novelty scores
- likely related objectives, questions, hypotheses, or decisions
- triage status and rationale
- provenance needed to reopen the original material

Signals remain outside the authoritative Evidence → Hypothesis → Decision → Plan chain until promoted and validated.

## Signal Inbox

The Signal Inbox is the reviewable work queue for Signals. It separates high-volume collection from deliberate strategy updates.

Supported dispositions should include:

- **dismiss**: irrelevant or too weak; retain the decision for audit and future deduplication
- **defer**: potentially useful, but not worth current attention
- **watch**: update coverage or a topic watch without starting research
- **link**: attach to an existing Research Question or related graph node
- **promote**: create an Evidence Candidate or a new Research Question
- **merge**: combine duplicates or multiple reports of the same underlying event

Jev can perform first-pass classification and ranking. Human review or a higher-capability model is used when impact, ambiguity, or policy requires it. Inbox actions are events; they do not silently mutate Strategy State.

## Ambient Research

Ambient Research runs on source-specific cadences and budgets. Its job is to notice change cheaply and preserve enough context for later use.

An Ambient Research cycle should:

1. select due sources from the Source Portfolio
2. fetch incrementally using adapter checkpoints where possible
3. store or reference immutable raw material
4. normalize metadata and canonical identities
5. deduplicate by canonical URI, content hash, and semantic similarity
6. create or update Signals
7. perform low-cost relevance and novelty triage
8. place actionable Signals in the Signal Inbox
9. update source health and Coverage Model observations

Ambient Research does not synthesize a new strategy on every collection run. Most items should stop after caching, deduplication, or triage.

## Targeted Research

Targeted Research begins from a persistent Research Question. It may be triggered by planning critique, a promoted Signal, a stale assumption, a contradiction, an experiment, or a Coverage Model gap.

It uses bounded queries and source selection to gather evidence that could change a decision. Its output follows the existing NOETIDE path:

```text
Research Question
→ Research Tasks
→ Sources / Cached Material
→ Evidence Candidates
→ Validated Evidence
→ Partial Invalidation
→ Strategy Patch
```

Cached material may satisfy part of a question, but reuse must preserve provenance and freshness checks.

## Progressive Enrichment

NOETIDE should spend computation in stages and stop when an item is no longer worth further work.

A default enrichment ladder is:

1. **Capture** — URI, timestamps, source identity, content hash, and raw reference.
2. **Normalize** — canonical metadata, language, topics, entities, and duplicate links.
3. **Triage** — relevance, novelty, urgency, and likely affected state.
4. **Contextualize** — extract a bounded summary and connect the item to current objectives or Research Questions.
5. **Promote** — create an Evidence Candidate or Research Question with explicit rationale.
6. **Investigate** — run Targeted Research when the expected decision value justifies it.
7. **Integrate** — validate Evidence, traverse dependencies, and propose a Strategy Patch.

Promotion thresholds should depend on impact, uncertainty, novelty, source quality, coverage gap, and cost. They must be configurable and auditable rather than hidden in a prompt.

## Coverage Model

The Coverage Model describes how well the current Source Portfolio and research activity cover what the strategy needs to know.

Coverage dimensions may include:

- objective, decision, hypothesis, risk, or Research Question
- topic, entity, geography, market, technology, or stakeholder perspective
- source authority and independence
- evidence polarity, including supporting and contradicting views
- freshness window
- collection health and recent activity

Coverage is an operational assessment, not proof that a conclusion is correct. It should identify gaps such as:

- a high-impact decision supported by one source family
- a critical topic with stale evidence
- a Research Question with no credible source path
- overrepresentation of one geography or perspective
- a monitored source that has silently stopped updating

Coverage gaps can create or reprioritize Research Questions and can suggest Source Portfolio changes.

## Review Cadence

Cadence is explicit and layered:

- **collection cadence**: per source, based on expected update rate and cost
- **inbox cadence**: continuous for urgent Signals or batched daily/weekly review
- **coverage cadence**: periodic review of gaps, duplication, and source health
- **strategy cadence**: review when Evidence materially affects protected or high-impact nodes
- **portfolio cadence**: periodic addition, pausing, replacement, or retirement of sources

Cadence policies should support event-driven overrides. A critical Signal may trigger immediate review without forcing all routine sources into a high-frequency schedule.

## Cache as Memory

The raw-item cache is durable research memory, not disposable transport storage.

It should preserve:

- immutable or content-addressed raw artifacts when permitted
- retrieval metadata and adapter checkpoints
- canonical identities and duplicate relationships
- extraction and enrichment versions
- prior triage outcomes and rationales
- links to Signals, Evidence Candidates, Evidence, and Research Questions
- freshness, retention, access, and tombstone metadata

Cache reuse reduces repeated network work and allows later Research Questions to benefit from previously seen material. The cache is not authoritative reasoning state: stale or superseded content must not become current Evidence merely because it was stored.

## Boundaries and invariants

The following invariants preserve the existing NOETIDE design:

1. Signals never directly update hypotheses, decisions, or plans.
2. Ambient Research does not trigger whole-strategy regeneration.
3. Targeted Research remains question-driven and budgeted.
4. Evidence and interpretation remain separate objects.
5. Dependency traversal bounds the affected set before semantic reassessment.
6. Protected and high-impact nodes retain existing human-review requirements.
7. Models operate on bounded projections; persistent state remains user-owned.
8. Executors remain downstream and cannot write directly to Strategy State.

## Prior-art note

The Source Portfolio, inbox-oriented collection, scheduled review, and progressive processing concepts are informed by the operational information-collection workflow described in Matsuo Institute's article, [AI系の情報収集手法を紹介（ビジネス・開発・研究）【2025年版】](https://zenn.dev/mkj/articles/1357a7ea2970c4). NOETIDE adopts these ideas as an upstream research-intake layer while retaining its own Strategy Graph, Research Backlog, partial-invalidation, and patch-based planning model.
