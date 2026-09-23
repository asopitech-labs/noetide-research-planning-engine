# AlopexDB Storage Design

AlopexDB is the standard persistence layer for NOETIDE.


## Why AlopexDB

NOETIDE needs one durable state layer for:

- structured strategy data
- graph-style dependencies
- event history
- research backlog
- source metadata
- vector retrieval
- experiments and analytical history

AlopexDB already targets a unified SQL + Vector + Graph-ready model and supports embedded, single-node, and distributed deployment modes. That matches NOETIDE's requirement to begin local-first without changing its logical state model as deployment grows.

## Storage responsibilities

```text
AlopexDB
├─ Strategy Graph
│  ├─ nodes
│  └─ edges
├─ Event Log
├─ Research Backlog
├─ Branch metadata
├─ Source metadata
├─ Evidence
├─ Vector retrieval
└─ Experiment / observation history
```

Large immutable raw artifacts such as PDFs, screenshots, or full HTML snapshots may remain in filesystem/S3-compatible blob storage, referenced from AlopexDB.

## Logical tables

The physical schema may evolve with AlopexDB capabilities, but the logical model starts with:

### strategy_nodes

Fields should include:

- id
- workspace_id
- branch_id
- node_type
- title
- body
- status
- confidence
- revision
- created_at
- updated_at

### strategy_edges

Fields should include:

- id
- workspace_id
- branch_id
- src_id
- relation
- dst_id
- metadata
- created_at

### strategy_events

Append-only audit history:

- sequence/id
- workspace_id
- branch_id
- actor
- event_type
- entity_id
- payload
- causation_id
- correlation_id
- created_at

### research_questions

- id
- workspace_id
- branch_id
- question
- trigger_node_id
- status
- impact
- uncertainty
- dependency_centrality
- estimated_cost
- priority
- created_at
- updated_at

### sources

- id
- canonical_uri
- source_type
- retrieved_at
- freshness metadata
- content hash
- blob reference

### evidence

Evidence is separate from interpretation.

- id
- source_id
- source fragment/location
- claim
- extraction metadata
- confidence
- created_at

### embeddings / vector-bearing records

Vectors may be attached to source fragments, evidence, hypotheses, and other retrieval candidates as AlopexDB vector types/indexes allow.

## Graph traversal

NOETIDE does not require a separate graph database at the outset.

Strategy dependencies are represented through `strategy_edges`. Partial invalidation traverses these edges deterministically to bound the semantic reassessment set.

Typical path:

```text
Evidence
→ Hypothesis
→ Decision
→ Plan Step
```

AlopexDB's graph-ready direction should be used where stable and useful, but NOETIDE must not require speculative graph features that are not available in the selected AlopexDB release.

## Vector retrieval

Vector retrieval is used for candidate discovery, not as the authority for relationships.

Suitable uses:

- similar source fragments
- related evidence
- duplicate research questions
- prior hypotheses
- relevant historical decisions

Authoritative dependency edges remain explicit records.

## Transaction and revision requirements

NOETIDE requires optimistic concurrency or equivalent revision-safe updates.

Each mutable strategy node carries a revision. Patch application must verify the expected revision before update.

If an update races with another research or user change, the patch is rejected for reassessment rather than silently overwriting newer state.

## Deployment modes

### Local

```text
NOETIDE
  ↓
AlopexDB embedded or local server
```

Local-first operation is preferred when the JavaScript integration path is stable.

### Server

```text
TypeScript NOETIDE
  ↓
AlopexDB adapter
  ↓
alopex-server
```

For the initial TypeScript implementation, the server path is acceptable and may be preferable where direct embedded Node bindings are not yet available.

### Distributed

The logical Strategy Graph and domain interfaces must remain unchanged when AlopexDB moves to a distributed deployment.

## Adapter boundary

The domain layer depends on a storage interface, not AlopexDB-specific transport code.

Example responsibilities:

- load nodes/edges
- append event
- apply revision-checked patch
- query dependent nodes
- store evidence
- vector search
- manage branch metadata

AlopexDB-specific SQL, HTTP, gRPC, embedded bindings, or future Node bindings belong behind the adapter.

## Validation work

Before production use, NOETIDE must validate the selected AlopexDB version for:

- transaction semantics required by patch application
- concurrent revision-safe updates
- recursive/dependency traversal performance
- JSON/semi-structured payload handling
- append-heavy event workloads
- high-frequency small updates
- vector search quality and index behavior
- backup/recovery expectations
- local single-process restrictions where embedded mode is used

These are implementation verification items, not reasons to revert the architecture to PostgreSQL by default.
