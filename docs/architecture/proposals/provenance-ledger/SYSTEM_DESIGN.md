# System Design

## Current State

Tabellio is a Node.js CLI and provider/adaptor system around a Git-native agent workflow. It currently binds selected task, workspace, commit, Entire checkpoint, validation, GitHub review, and control-ref evidence. It is not yet a central provenance database or an always-running microservice platform.

## Target Workflow

The proposal adds a provenance ingestion boundary, PostgreSQL evidence store, content-addressed artifact store, correlation/read projections, a scoped context broker, and an evaluation/lesson path. Git and Entire remain authorities for code history and checkpoint/session history.

## Architecture Track

proposal-target

## Review Tier

Deep: persistent store, queue, trust boundaries, agent context, privacy, and future service topology.

## Roles

- Author/DRI: Codex
- Consulted domain reviewers: repository maintainer, security reviewer, data/privacy reviewer
- Human approvers: to be assigned

## Evidence Snapshot

| Repository | Revision | Dirty | Observed at |
| --- | --- | --- | --- |
| /Users/hudson/Documents/GitHub/Tabellio | 979a81d07994f8024c3b071581165de94685d2ca | true | 2026-08-30T06:49:19Z |

## Architecture Views

- Baseline: generated from baseline.yaml.
- Target: generated from target.yaml.
- Focused views: context, topology, data, trust, failure, and causal scenarios.

## Evidence Table

| Claim | State | Layer | Source locator | Notes |
| --- | --- | --- | --- | --- |
| Current Git-native workflow exists | supported | implemented/intended | docs/tooling-stack.md; docs/native-git-foundation.md | Current repository contract |
| Current checkpoint adapter exists | supported | implemented | scripts/providers/entire-ledger-provider.mjs | Entire metadata normalization |
| Current stack and GitHub adapters exist | supported | implemented | scripts/providers/git-spice-stack-manager.mjs; scripts/providers/github-provider.mjs | Current provider boundaries |
| Proposed provenance ledger and service topology | proposed | intended | docs/agent-provenance-ledger-and-codex-feedback.md; docs/architecture-diagrams.md | Target design only |

## Options Considered

- Keep JSON files and Git refs only: portable, but weak for cross-run joins, constraints, concurrency, and scoped retrieval.
- Use SQLite first: useful for a local fixture prototype, but less suitable for concurrent multi-project ingestion.
- Use PostgreSQL first: selected proposal because it combines relational integrity, indexed JSONB, transactions, and recursive queries.
- Use a graph database first: deferred until graph traversal is proven to dominate the workload.
- Use a vector database first: rejected as an authority; semantic similarity cannot establish correctness or security.
- Split every logical boundary into a network service immediately: rejected; first prove contracts in a modular monolith and worker commands.

## Chosen Design

Use a modular Node.js implementation first. Add PostgreSQL as the first central provenance store, JSONB for evolving payloads, typed relationships for critical joins, content-addressed artifact references for large evidence, and read projections for lineage, review, and lessons.

Extract independent services only when measurements show a need for independent deployment, fault isolation, access control, or scaling.

## Components

- Source adapters
- Ingest and normalization worker
- Optional durable event queue
- PostgreSQL provenance ledger
- Private artifact store
- Correlation and read-model projector
- Validation and security workers
- Read-only context broker
- Codex review consumer
- Evaluation and lesson worker
- Human and deterministic approval boundary

## Integration Contracts

All integrations require an explicit protocol, contract, authentication method, delivery mode, timeout, retry policy, idempotency key, fallback, and failure propagation behavior. Exact repository, commit, checkpoint, PR-head, validation-manifest, and artifact-digest identities remain the primary correlation keys.

## Causal Scenarios

- Capture and correlate a bounded agent event.
- Resolve an exact final head for review.
- Promote an evidence-backed project lesson through evaluation and approval.

## Failure Modes

- Duplicate event delivery.
- Unknown or mismatched commit/checkpoint reference.
- Redaction failure.
- Queue backlog or poison event.
- Stale PR head.
- Partial validation or security evidence.
- Context broker returning out-of-scope project data.
- Candidate lesson promoted without sufficient evidence.

## Observability

Track ingest latency, queue depth, duplicate rate, rejected events, redaction failures, projection lag, stale-head rejections, context-packet scope, validation outcomes, review rework, lesson recurrence, and evaluation regressions. Raw content remains access-controlled and is not required for every metric.

## Prioritized Findings

D2 reviewed both `baseline.yaml` and `target.yaml` with recommendation `ready_for_human_review`.

- Baseline: no structural findings.
- Target: no structural findings.
- Human approval remains required; this result is not approval, implementation, or deployment evidence.

The review covers modeled ownership, contracts, timeout/fallback behavior, retries and idempotency, trust boundaries, failure propagation, shared-store write topology, and observable outcomes. It does not prove runtime behavior. Runtime receipts and implementation checks remain required during rollout.

## Decision Status

- Codex recommendation: pending D2 review
- Human decision: draft
- Decision owner: to be assigned
- Decision date: 2026-08-30

## Rollout

1. Define the event and relationship contract.
2. Build fixtures for one complete agent-to-PR lineage.
3. Prototype the PostgreSQL projection against fixtures.
4. Add a read-only project-scoped context broker.
5. Add evaluation and lesson promotion controls.
6. Split workers or services only after measured need.

## Post-Implementation Reconciliation

After implementation, re-scan the exact committed revision. Compare the observed current architecture with this proposal. Update a maintained current architecture model from fresh evidence; do not promote target.yaml directly.
