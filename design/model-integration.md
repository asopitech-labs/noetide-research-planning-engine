# Model Integration

NOETIDE treats models as replaceable compute providers. Model sessions never own Strategy State.

## Provider roles

### Jev

Jev is used for high-frequency, constrained semantic decisions.

Typical operations:

- Signal relevance, novelty, urgency, and routing
- Signal-to-question or Signal-to-node matching
- duplicate / related-item classification
- evidence relevance
- support / contradict / irrelevant
- unchanged / contested / invalid
- routing
- risk or impact bucket classification
- research-question ranking
- patch safety classification

Jev should receive narrow inputs and return narrow typed outputs.

### Codex / ChatGPT subscription

Codex is used as an API-like transport to high-capability reasoning available through the user's ChatGPT/Codex subscription.

NOETIDE must not adopt Codex's agent session model as application state.

Typical operations:

- generate hypotheses
- surface assumptions
- critique a plan
- generate alternatives
- synthesize conflicting evidence
- contextualize high-impact Signals
- propose Signal promotion or Targeted Research
- identify Coverage Model gaps
- propose research questions
- propose Strategy Patches

## Progressive Enrichment routing

Model work follows the intake ladder rather than running deep synthesis on every collected item:

```text
capture / normalize       deterministic application logic
triage                    Jev or another constrained classifier
contextualize / promote   bounded high-capability reasoning when needed
investigate               Targeted Research providers
integrate                 validated proposal + existing patch pipeline
```

Promotion decisions include explicit scores and rationale based on impact, uncertainty, novelty, source quality, coverage gap, and cost. A model may recommend a disposition, but an unpromoted Signal never enters the authoritative Evidence → Hypothesis → Decision → Plan chain.

Ambient collection can run without invoking Codex. Cache as Memory preserves earlier enrichment and triage so later Targeted Research can reuse work with provenance and freshness checks.

## Stateless Codex adapter

From the domain layer, Codex behaves like a function:

```ts
interface ReasoningProvider {
  invoke<I, O>(
    operation: ReasoningOperation<I, O>,
    input: I
  ): Promise<O>
}
```

Each reasoning request is constructed from the current persisted NOETIDE state.

```text
current AlopexDB state
  ↓
bounded projection
  ↓
new Codex invocation
  ↓
structured output
  ↓
validation
  ↓
proposal
```

NOETIDE must not require `resumeThread()` or previous Codex messages for correctness.

Thread/session identifiers are transport artifacts only.

## Input projection

Models do not receive the entire database.

For each operation, the application builds a bounded projection such as:

- objective
- affected hypotheses and decisions
- relevant validated evidence
- promoted Signals or Evidence Candidates when the operation requires them
- Coverage Model gaps relevant to the operation
- current plan fragment
- assumptions
- protected nodes
- requested operation

This keeps reasoning local to the actual change and reinforces patch-based planning.

## Structured output

State-changing or state-classifying model operations must return schema-validated structured output.

Zod schemas should be the application-level contract.

Free-form text may be stored as explanation, but must not be parsed heuristically to mutate Strategy State.

## Tool isolation

Reasoning calls should not implicitly use:

- repository files
- shell commands
- arbitrary network access
- git state

Research is performed through NOETIDE's Research Layer, where provenance can be captured.

The Codex adapter should therefore use the most restrictive available execution configuration appropriate to the SDK/CLI version.

## Provider-independent domain

The core domain must not contain Codex or Jev-specific types.

Preferred boundaries:

```text
domain
  ↓
ReasoningProvider
  ├─ CodexReasoningAdapter
  └─ future provider

domain
  ↓
SemanticDecisionProvider
  ├─ JevDecisionAdapter
  └─ future local classifier
```

## Failure behavior

A model failure must not corrupt current state.

Model outputs first become proposals. Proposals are:

1. schema validated
2. revision checked
3. policy checked
4. optionally human reviewed
5. only then applied

If validation fails, the previous Strategy State remains authoritative.
