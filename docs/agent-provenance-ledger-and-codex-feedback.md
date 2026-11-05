# Agent Provenance Ledger and Codex Feedback Plan

Status: Proposed architecture direction  
Date: 2026-08-30  
Scope: Tabellio, agent-assisted software delivery, review evidence, and future Codex feedback

## Executive summary

Tabellio is an internal control and evidence layer for agent-assisted software delivery. It binds a task to the agent run, workspace, code changes, Git commits, Entire checkpoints, validation results, reviews, releases, and deployment receipts.

The central design question is not simply “Should the data be JSON?” The real question is how to preserve identity, causality, ordering, trust, and scope while keeping the evidence queryable and auditable.

The proposed direction is:

- Keep JSON as the interchange, fixture, export, and provider-payload format.
- Use PostgreSQL as the first primary query store.
- Store stable fields in typed relational columns.
- Store evolving provider-specific payloads in PostgreSQL `JSONB`.
- Store causal and evidence relationships as explicit typed edges or join tables.
- Keep Git and Entire as authoritative code/checkpoint receipts; index them rather than replacing them.
- Keep large raw logs and transcripts outside the relational database, referenced by digest and URI.
- Build graph, analytics, and semantic-search projections only when the workload proves they are needed.
- Feed only bounded, redacted, validated lessons back into future Codex runs.

This is a proposed architecture. It does not authorize a database migration, global Codex behavior change, model fine-tune, or external deployment.

Visual companion: [Tabellio system architecture diagrams](architecture-diagrams.md).

D2 model and structured architecture review: [provenance-ledger proposal](architecture/proposals/provenance-ledger/SYSTEM_DESIGN.md).

## 1. What Tabellio is

In simple terms, Tabellio is the ledger and gatekeeper around AI-assisted coding.

It does not replace:

- the coding agent;
- Git as the source of code history;
- GitHub as the pull-request and code-distribution surface;
- Entire as the checkpoint/session source;
- CI and product validators as execution evidence;
- humans as owners of product judgment and approvals.

It connects these systems and asks whether the evidence still refers to the exact code under review.

The current high-level flow is:

```text
task
  -> agent run
  -> isolated workspace
  -> file changes
  -> Git commits
  -> Entire checkpoint
  -> deterministic/product validation
  -> context and evidence packets
  -> stacked PRs
  -> Codex and security review
  -> merge
  -> release/deployment evidence
```

The key promise is not “the agent said this is correct.” The key promise is “this evidence is bound to this exact repository, commit, checkpoint, validation result, and review state.”

## 2. What the current review confirmed

The repository already contains a useful evidence foundation:

- [Workflow model](workflow-model.md) defines the task, runtime, repository, workspace, context packet, evidence envelope, validators, stack, review, and merge concepts.
- [Evidence schema](evidence-schema.md) defines versioned JSON envelopes, run IDs, Git bindings, commands, checks, approvals, artifacts, and default-deny external-action policy.
- [Agent run lifecycle](agent-run-lifecycle.md) defines run state and failure behavior.
- [Entire ledger provider](../scripts/providers/entire-ledger-provider.mjs) normalizes checkpoint/session metadata and links it to Git state.
- [Codex review](codex-review.md) defines the agent-review contract and its boundary relative to deterministic checks and human ownership.
- [Review loop](review-loop.md) keeps review feedback, triage, fixes, head remapping, and readiness under a durable review ledger.
- [Product validation](product-validation.md) binds validation decisions to exact commits and manifests.
- [Stack operations](stack-operations.md) models ordered PR chains, restacks, submissions, and merge operations.

The current implementation uses JSON records, Git refs/notes, Entire checkpoint references, and exact identifiers. It does not yet provide a central provenance database or automatically capture every model prompt, tool call, source body, and output.

That distinction matters:

- Current Tabellio: selective evidence capture and validation.
- Future provenance ledger: complete or intentionally bounded event capture with typed causal relationships.

Graphify is useful for navigating code relationships. It should not be treated as the runtime evidence database.

## 3. The data-shape problem

This system is not one tree.

| Data | Natural shape | Required operation |
| --- | --- | --- |
| PR stack | Tree or forest | Find parents, children, roots, and stack order |
| Git history | Directed acyclic graph | Walk ancestors, merge bases, and exact heads |
| Agent activity | Causal DAG | Connect inputs, tool calls, outputs, and edits |
| Evidence | Many-to-many graph | Show which records support which conclusion |
| Review findings | Linked lifecycle | Connect finding, disposition, fix, re-review, and validation |
| Lessons | Scoped reference graph | Connect rule, trigger, evidence, and later outcome |

Forcing every relationship into one nested JSON document makes correlation difficult. A single tool result can influence multiple edits. A merge commit can have multiple parents. One validation result can support multiple review decisions.

The correct mental model is:

```text
immutable facts + explicit relationships + derived projections
```

Sorting is part of the problem, but basic algorithms cover the first version:

- Git or a topological traversal orders commit DAGs.
- Parent/causal edges can be topologically ordered and checked for cycles.
- Database indexes and joins find exact records.
- Deterministic ranking selects relevant lessons.
- Graph traversal can be added as a read projection later.

No exotic algorithm or graph database is required for the first useful implementation.

## 4. Storage roles

JSON is a strong representation format. It is not the complete storage strategy.

| Storage | Recommended role | Do not use it as |
| --- | --- | --- |
| JSON | Portable event envelopes, fixtures, exports, provider payloads | The only cross-run query system |
| PostgreSQL | Canonical metadata, relationships, constraints, projections | The only location for large raw bodies |
| Git | Commits, branches, immutable code history | A high-volume event query database |
| Entire | Checkpoint/session receipts | A substitute for every runtime trace |
| Object storage or private artifact store | Large logs, transcripts, diffs, screenshots | The authority for relational state |
| SQLite | Local single-user prototype or test fixture store | A concurrent multi-project service |
| Neo4j or another graph store | Optional relationship-heavy read projection | The first system of record |
| DuckDB/Parquet | Offline analytics and reporting | The transactional evidence ledger |
| Vector index | Similarity retrieval for narratives and lessons | Correctness, security, or approval authority |

PostgreSQL is the best first database for Tabellio because the workload needs both strict relationships and evolving payloads. PostgreSQL supports indexed `JSONB`, relational columns beside JSON, foreign-key enforcement, and recursive queries for hierarchy traversal. See [PostgreSQL JSON types](https://www.postgresql.org/docs/current/datatype-json.html), [constraints](https://www.postgresql.org/docs/current/ddl-constraints.html), and [recursive queries](https://www.postgresql.org/docs/current/queries-with.html).

The recommendation is not “put everything into JSONB.” Use typed columns for fields that must be joined or validated:

- project and repository identity;
- run and event IDs;
- Git commit SHAs;
- Entire checkpoint IDs and digests;
- PR number and head SHA;
- validation status and manifest digest;
- review and finding IDs;
- trust, sensitivity, and lifecycle status.

Use `JSONB` for provider-specific or evolving details that do not yet justify a stable column.

## 5. Proposed logical schema

The following is a logical shape, not a migration script.

```text
projects
  id, key, name, sensitivity_policy

repositories
  id, project_id, canonical_url, default_branch

runs
  id, project_id, repository_id, task_ref, actor, runtime,
  base_sha, head_sha, started_at, ended_at, status

entities
  id, kind, canonical_key, display_name, sensitivity

events
  id, run_id, event_type, parent_event_id, caused_by_event_id,
  source, occurred_at, trust_level, payload_jsonb, payload_sha256

edges
  id, from_entity_id, relationship_type, to_entity_id,
  evidence_event_id, created_at

artifacts
  sha256, uri, media_type, byte_size, sensitivity, retention_class

commits
  repository_id, sha, parent_shas, author, committed_at

checkpoints
  provider, checkpoint_id, checkpoint_sha256, checkpoint_ref,
  repository_id, commit_sha, partial, created_at

validations
  id, run_id, repository_id, commit_sha, manifest_sha256,
  validator, decision, result_jsonb, created_at

reviews
  id, run_id, repository_id, pr_number, head_sha,
  reviewer, review_type, status, result_jsonb, created_at

findings
  id, review_id, subject_sha, severity, path, line,
  title, body, disposition, fix_sha

lessons
  id, scope_type, scope_id, trigger_jsonb, rule_text,
  status, version, created_at, retired_at

lesson_evidence
  lesson_id, evidence_entity_id, evidence_strength, outcome
```

Critical links should also have typed join tables with foreign keys. A free-form edge table is useful for flexible relationships, but it should not be the only protection for a commit, checkpoint, validation, or review link.

Every event should have:

- a stable event ID;
- a run ID;
- a typed event name;
- an observed timestamp;
- an actor/source;
- exact subject identifiers when known;
- a canonical payload digest;
- a trust level;
- causal parent or input references;
- sensitivity and retention classification.

Large content should be stored by digest and reference. The database should hold the metadata, not blindly duplicate every transcript or diff.

## 6. Correlation algorithm

The ingest path should be deterministic and append-oriented:

1. Create or resolve the `run_id` for the work session.
2. Receive an event from a bounded adapter: agent runtime, Git, Entire, GitHub, CI, validator, or review system.
3. Normalize it into the canonical event envelope.
4. Redact secrets and classify sensitivity before persistence.
5. Canonicalize the payload and compute its SHA-256 digest.
6. Insert the event and its known entities/edges in one transaction.
7. Enforce repository, commit, checkpoint, validation, and review references.
8. Reject impossible causal cycles; allow Git merge structure in the commit DAG.
9. Materialize query projections for stacks, review state, validation state, and lessons.
10. Generate a bounded context packet for the next review or agent run.

The primary join rule is exact identity, not timing:

```text
repository_id
  + exact head_sha
  + Entire checkpoint ID/digest
  + run_id
  + artifact digest
  + validation manifest digest
```

When the PR head changes, previous head-specific review and validation records become stale. They remain historical evidence, but they cannot authorize the new head without revalidation.

For a final integrated review, the system should:

1. resolve the exact final head;
2. enumerate the stack and commit ancestry;
3. resolve checkpoint references and event lineage;
4. collect validation and security evidence;
5. collect open and resolved findings;
6. reject stale or partial evidence;
7. provide Codex a bounded review packet;
8. require deterministic gates before treating the review as ready.

## 7. PR stacking, review, and security

Each stacked PR should produce its own evidence bundle. The final integrated head should receive a separate review and validation pass.

This prevents one child PR from inheriting false confidence from an earlier parent.

The review model should have two levels:

- Per-PR review: scope, changed files, local tests, findings, and checkpoint lineage.
- Final integration review: resolved stack, merge result, exact final head, security evidence, full validation, and release readiness.

Codex review is an interpretation and defect-finding layer. It is not the security authority. Deterministic schema, policy, dependency, static, operational, and exact-head checks remain first. Codex can explain risk, identify missing evidence, and propose focused fixes.

Security evidence needs the same identity model as ordinary review:

- exact repository and head SHA;
- validator version;
- manifest digest;
- commands and outputs or artifact references;
- policy decisions;
- reviewer disposition;
- known limitations.

“The agent said it was safe” is not security evidence.

## 8. Feedback loop into Codex

The feedback loop should improve future behavior through evidence, not hidden self-modification.

```text
agent run
  -> structured traces and artifacts
  -> outcome labels
  -> validated findings and fixes
  -> candidate lesson or evaluation case
  -> human/deterministic approval
  -> scoped retrieval packet
  -> next Codex run
  -> measured comparison
```

### Capture

Capture relevant events, not unlimited raw data. Useful events include:

- task and acceptance contract;
- model or agent invocation metadata;
- tool call name, bounded inputs, and bounded outputs;
- source/reference identifier and content digest;
- file read/write or patch artifact;
- command and exit status;
- Git commit and checkpoint;
- validation result;
- review finding and disposition;
- fix and re-review;
- release and deployment receipt.

If a custom Agents SDK runtime is used, OpenAI documents built-in tracing for model calls, tool calls, handoffs, guardrails, and custom spans. See [Agents SDK tracing and observability](https://developers.openai.com/api/docs/guides/agents/integrations-observability). Tabellio still needs adapters and its own evidence contract; tracing alone does not create Tabellio lineage.

### Label outcomes

A lesson should be based on what happened, not only what a model predicted. Useful signals include:

- deterministic validation pass or failure;
- human finding accepted, fixed, dismissed, or disputed;
- recurrence of the same defect;
- regression after merge;
- rollback or incident;
- security control result;
- review rework and time-to-resolution.

Model agreement is a supporting signal. It is not proof.

### Scope lessons

Lessons need explicit scope:

- Project-specific: private Tabellio rules and recurring repository patterns.
- Organization-wide: redacted patterns that are safe and useful across projects.
- General agent tendency: repeated, normalized behavior across many independent projects.

Project lessons should be retrieved first. Global lessons should require stronger evidence, privacy review, and versioning.

Example lesson:

```json
{
  "lessonId": "lesson-...",
  "scope": { "type": "repository", "id": "tabellio" },
  "appliesWhen": {
    "paths": ["scripts/providers/**", "schemas/**"],
    "riskTypes": ["lineage", "validation"]
  },
  "rule": "Revalidate the exact final head and checkpoint trailer after restacking or squashing.",
  "evidence": ["validation-...", "review-...", "commit-..."],
  "status": "candidate",
  "version": 1
}
```

### Retrieve bounded context

At the start of a run or review, a context broker should return only the relevant material:

1. Current repository, branch, PR, and exact head.
2. Applicable project rules.
3. Relevant validation and security requirements.
4. Repeated, evidence-backed failure patterns.
5. Similar accepted fixes, when useful.
6. Source references and digests for inspection.

Retrieval priority should be deterministic:

```text
exact project/repository match
  > path and risk match
  > validated evidence strength
  > repeated occurrence
  > recency as a tie-breaker
```

Semantic search can find similar narratives. It must not decide correctness, security, approval, or merge readiness.

### Evaluate before promotion

Candidate lessons should first become replayable evaluation cases. OpenAI’s evaluation guidance describes traces, graders, datasets, and evaluation runs as ways to identify workflow problems and measure changes over time. See [agent evaluations](https://developers.openai.com/api/docs/guides/agent-evals).

Promote a lesson or behavior change only when the comparison shows improvement without unacceptable regressions in:

- defect escape rate;
- security findings;
- validation pass rate;
- repeated review rework;
- cost and latency;
- false-positive burden;
- privacy and access-control boundaries.

Prompt, tool, policy, and retrieval changes should be versioned and reversible. Do not silently modify global Codex behavior from unreviewed local transcripts.

## 9. Privacy and trust boundaries

The ledger may contain source code, prompts, tool inputs, external references, and credentials-adjacent material. The design must preserve:

- project and tenant isolation;
- secret redaction before storage;
- explicit sensitivity classification;
- least-privilege retrieval;
- retention and deletion policy;
- immutable digests without exposing raw content;
- provenance for every derived lesson;
- separation between observed, derived, human-confirmed, and candidate data.

Raw transcripts should not automatically become global training material. A digest proves identity; it does not grant permission to read or redistribute the underlying content.

## 10. Phased implementation plan

### Phase 0 — Contract and capture boundary

Define the canonical event envelope and relationship vocabulary. Start with a small set of event types:

- `task.created`
- `run.started`
- `tool.called`
- `artifact.created`
- `git.commit.created`
- `entire.checkpoint.created`
- `validation.completed`
- `review.finding.created`
- `review.finding.disposed`
- `fix.created`
- `release.created`
- `deployment.receipted`

Done condition: the same event can be represented in JSON, linked to exact IDs, redacted, hashed, and replayed from a fixture.

### Phase 1 — PostgreSQL evidence projection

Add a PostgreSQL schema for runs, events, entities, edges, artifacts, commits, checkpoints, validations, reviews, findings, and lessons. Keep Git and Entire as upstream receipts.

Done condition: query one final PR head and recover its complete known lineage without joining on timestamps or titles.

### Phase 2 — Context broker

Build a read-only query surface that returns a bounded, evidence-linked context packet for a project, repository, exact head, or review.

Done condition: Codex can receive project-specific rules and current review facts without receiving unrelated project data or raw unbounded transcripts.

### Phase 3 — Evaluation and lesson promotion

Convert validated review outcomes into evaluation cases. Compare baseline and changed prompts, tools, policies, or retrieval rules.

Done condition: every promoted lesson has evidence references, scope, version, status, and a measured outcome.

### Phase 4 — Optional projections

Add only when usage proves the need:

- graph projection for deep path and dependency queries;
- vector index for semantic similarity;
- DuckDB/Parquet projection for trend analysis;
- OpenTelemetry or Agents SDK adapters for richer runtime traces.

## 11. Decisions and non-decisions

### Proposed decision

Use PostgreSQL plus typed relational fields, `JSONB` payloads, explicit evidence relationships, and external artifact references as Tabellio’s first central data architecture.

### Alternatives considered

- JSON files only: excellent portability; weak cross-run querying, constraints, concurrency, and relationship integrity.
- SQLite: strong local prototype; weaker fit for concurrent multi-project ingestion.
- Neo4j first: strong graph traversal; adds operational and synchronization complexity before the event contract is proven.
- Vector database first: useful for similarity; cannot serve as an authoritative ledger.
- Full event platform first: likely overbuilt until event volume and retention requirements are measured.

### Not decided yet

- expected event volume and retention;
- deployment location and operational ownership;
- tenant and project isolation model;
- permitted raw-content retention;
- whether a graph projection is needed;
- whether custom Agents SDK tracing is part of the runtime;
- exact human approval process for organization-wide lessons.

These questions can change the physical deployment. They do not change the core identity-and-relationship model.

## 12. Immediate next steps

- [ ] Treat this document as the working architecture note.
- [ ] Inventory current event producers and identify capture gaps.
- [ ] Define the first canonical event envelope and relationship vocabulary.
- [ ] Map each current JSON schema to either an event, entity, relation, projection, or artifact.
- [ ] Build fixtures for one complete agent-to-PR lineage.
- [ ] Decide retention, sensitivity, and redaction rules before collecting raw traces.
- [ ] Prototype the PostgreSQL schema against fixtures before migrating live workflows.
- [ ] Define the project-scoped context packet returned to Codex.
- [ ] Create evaluation cases from accepted findings and validated fixes.
- [ ] Keep global lesson promotion blocked until scope, privacy, and evidence rules are approved.

## Related repository contracts

- [Workflow model](workflow-model.md)
- [Evidence schema](evidence-schema.md)
- [Agent run lifecycle](agent-run-lifecycle.md)
- [Codex review](codex-review.md)
- [Durable review and fix loop](review-loop.md)
- [Product validation](product-validation.md)
- [Stack operations](stack-operations.md)
- [Research grounding](research-grounding.md)
