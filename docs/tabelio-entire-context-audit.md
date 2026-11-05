# Tabellio and Entire Context Audit

**Audit date:** 2026-09-01  
**Repository:** IntelIP/Tabellio  
**Canonical repository spelling:** Tabellio (the requested file name uses “tabelio”)  
**Git baseline:** <code>main</code> at <code>979a81d07994f8024c3b071581165de94685d2ca</code>  
**Decision informed:** Whether Entire should remain a checkpoint/session layer or expand into Tabellio's Git transport, regional mirror, branch, or broader agent-infrastructure layer.

## Evidence labels

- **Fact** — directly observed in repository, Git, Plane, Notion, GitHub, Buildkite, Graphify, Entire CLI, or first-party Entire material.
- **Interpretation** — conclusion drawn from those facts.
- **Recommendation** — proposed decision or action.
- **Unknown** — evidence could not be accessed or does not exist.
- **Conflict** — two sources disagree or current state does not match the declared contract.

## 1. Executive summary

**Final position: Monitor and defer.**

This position applies to expanding Entire into Tabellio's Git hosting, regional mirroring, agent-oriented transport, or native branch platform. It does **not** mean removing Entire. Entire is already a required part of Tabellio's checkpoint and session design and should remain in that bounded role.

**Fact.** Tabellio is an internal, Git-native control and evidence product for agent-assisted software delivery. It is currently a dependency-free Node.js CLI/package and a set of schemas, providers, Git refs, tests, and CI workflows. It is not a hosted application, workflow database, Git host, or deployment platform. Repository sources define GitHub <code>origin</code> as canonical code and merge authority; Plane owns delivery state; Notion holds reasons and research; Entire owns session/checkpoint context; Tabellio owns the validated joins, review state, and evidence contracts.

**Fact.** Current Plane roadmap is a four-cycle rebuild from 2026-08-31 through 2026-10-25. Its twelve active stories, TAB-18 through TAB-29, are all in Backlog as of 2026-09-01. First cycle is date-current, but no story has started. Roadmap first proves data boundaries, local PostgreSQL, and one synthetic end-to-end lineage chain; only later does it connect sources, provide bounded Codex/security review packets, and prepare a release.

**Fact.** Entire already supplies:

- Codex lifecycle hooks for session capture;
- commit-linked 12-character checkpoint IDs;
- checkpoint/session explanation through Entire CLI;
- local resume/search state;
- a dedicated checkpoint branch;
- a separate private GitHub checkpoint remote;
- normalized checkpoint metadata through <code>EntireLedgerProvider</code>.

It does not currently supply Tabellio with a regional Git mirror, an <code>entire://</code> remote, Entire-native product branches, or a hosted source of canonical code.

**Fact.** Immediate Entire-related operational problem is not clone or fetch scale. It is checkpoint durability and synchronization:

- local <code>entire/checkpoints/v1</code> contains 157 checkpoint IDs and 376 commits;
- private <code>IntelIP/Tabellio-control</code> contains 78 checkpoint IDs;
- local branch is 256 commits ahead and zero behind the control branch;
- <code>push_sessions</code> is false;
- control repository's last GitHub push was 2026-07-27, while local checkpoints continued through 2026-07-31.

This means current recovery copy is materially behind local state.

**Fact.** No demonstrated need currently justifies Entire's distributed Git network:

- one active Entire Codex session was observed;
- two Git worktrees exist;
- no clone/fetch request volume, latency baseline, GitHub 429 response, rate-limit exhaustion, or multi-region agent-fleet record was found;
- hosted mirror inventory could not be read because Entire CLI is not logged in;
- repository has only GitHub <code>origin</code> and private GitHub <code>control</code> remotes.

**Interpretation.** Regional mirroring solves a future read-scaling problem, not Tabellio's current highest-risk problem. Entire-native branches would create a second branch authority and conflict with Tabellio's GitHub-canonical contract. Vendor throughput claims are not applicable to this repository without a controlled measurement, and Entire explicitly says GitHub-backed pushes remain bounded by GitHub.

**Recommendation.**

1. Retain Entire as checkpoint/session authority.
2. Restore and continuously measure checkpoint/control-ref durability before expanding scope.
3. Keep GitHub as canonical code and merge authority.
4. Do not put Entire mirrors or Entire-native branches in Tabellio's critical path now.
5. Treat Entire CLI 0.10.x and ref-based checkpoint storage as a compatibility pilot, not a drop-in upgrade: current adapter accepts only 12-character checkpoint IDs and hard-codes the branch backend.
6. Revisit a regional read-mirror pilot only after measured clone/fetch load, rate-limit pressure, or geographic latency crosses explicit thresholds.

## 2. What Tabellio is

### Problem it solves

**Fact.** Repository thesis is “GitHub-native context and evidence for agentic development” (<code>README.md:13-15</code>). AI-assisted changes otherwise leave reviewers with a patch but weak answers about why work started, which agent/runtime produced it, what commands ran, what failed, whether protected side effects occurred, and whether evidence matches the exact code under review.

**Plain-language explanation.** Tabellio is a flight recorder and gatekeeper for AI-assisted code changes. It connects a request, agent session, Git commits, validation, review, release, and deployment receipts without asking one system to own everything.

### Intended users and jobs to be done

| User | Core job |
| --- | --- |
| Developer or coding agent | Start from a frozen base, work in an isolated worktree, create small commits, and bind them to session checkpoints. |
| Reviewer | See why a change exists, exact candidate under review, commands and checks, failures/skips, findings, and protected side effects. |
| Delivery operator | Know whether a story, PR, validation, release, and deployment receipt refer to the same candidate. |
| Product owner | Preserve Plane roadmap intent and acceptance while preventing evidence proxies from being mistaken for shipment. |
| Security/release owner | Require explicit approvals, fresh exact-head evidence, bounded logs, and fail-closed publication. |

### Current capabilities

**Fact.** Current repository implements:

- native Git commit/diff/ref/worktree operations;
- isolated per-run worktrees and local run state;
- context packets and portable evidence envelopes;
- required Entire checkpoint metadata with Git-note migration mode;
- git-spice stack snapshots and approval-gated operations;
- GitHub PR/review/check readers;
- durable review and validation refs;
- exact-head product validation;
- approval-gated merge-ready status and release orchestration;
- private control-ref transport;
- Plane, Buildkite, GitHub release, Cloud Run, and Vercel evidence collectors;
- cross-repository analytics and deterministic wave admission.

Anchors: <code>README.md:17-94</code>, <code>docs/workflow-model.md:5-65</code>, <code>package.json</code>, and 104 tracked script files.

### Planned capabilities

**Fact.** Working-tree architecture proposal and Notion Decision propose:

- explicit event/entity/relationship vocabulary;
- local-first PostgreSQL, then a central projection if proven;
- source adapters for Git, Entire, GitHub, Plane, Buildkite, validators, and review;
- one reproducible task-to-review lineage fixture;
- stale/missing/conflicting evidence states;
- bounded Codex and security review packets;
- replay and recovery;
- evidence-backed, scoped lessons only after validation and approval.

These are proposals, not current runtime. Local proposal files are untracked or modified, and Notion marks the decision **Proposed**, **Confidence: Lead**, last reviewed 2026-08-30.

### Product maturity and active stage

**Fact.**

- Public Apache-2.0 repository began 2026-07-02.
- Main has 46 commits through 2026-07-31.
- Tags/releases exist for v0.1.0, v0.2.0, v0.5.0, and v0.6.0.
- Canonical main at <code>979a81d</code> passed GitHub product validation on 2026-08-01.
- Forty tracked test files and two GitHub Actions workflows exist.
- Buildkite pipeline defines Git toolchain, repository, Fallow, package, and product-validation gates.
- No GitHub deployment or environment exists; Plane reports <code>is_deployed: false</code>.

**Conflict.** GitHub labels v0.6.0 latest, but its target <code>615733b807a0c1105f2410fd933a99c97057218f</code> is not an ancestor of main. It is contained only by <code>origin/codex/intb-279-v060-release</code>. PR #44 from that branch is still open and conflicted, while current main's <code>package.json</code> says 0.5.0. Therefore v0.6.0 is a published off-main release, not proof that canonical main contains that candidate.

**Interpretation.** Tabellio has substantial control-plane implementation and release machinery, but product operating model is still early and is now being rebuilt around a provenance projection. “Mature controls” and “mature deployed product” are different claims; only the first is supported.

### Major constraints

- GitHub remains canonical code/merge authority.
- Plane remains execution-state authority.
- Source systems remain authoritative; Tabellio stores derived, rebuildable joins.
- Exact candidate identity is mandatory; new commits invalidate old evidence.
- External actions default to deny.
- Worktrees isolate files but are not security sandboxes.
- Private prompts/transcripts must not enter public code history.
- Current runtime is local/CI Node.js, standard Git, and Git refs; no service database exists.
- Human approval remains required for merge, publication, deployment, spend, and destructive work.

### What “done” means now

**Fact.** Current roadmap's terminal story, TAB-29 “Release the first useful version,” defines done as:

- known exact release candidate;
- required checks passing;
- end-to-end demo and limitations recorded;
- human approval before publication;
- deployment treated as a separate decision.

Roadmap-wide done also requires deterministic replay, explicit missing/stale/conflicting evidence, safe review packets, separate security review, recovery tests, and no provider mutation during source collection.

## 3. Current product goals and roadmap

### Roadmap shape

| Cycle | Dates | Plane stories | Outcome |
| --- | --- | --- | --- |
| Rebuild 1: Make the evidence trail | 2026-08-31 to 2026-09-13 | TAB-18, TAB-19, TAB-20 | Decide retention/privacy, prove local PostgreSQL, record one synthetic full change. |
| Rebuild 2: Connect the sources | 2026-09-14 to 2026-09-27 | TAB-21, TAB-22, TAB-23 | Read trusted sources, replay records, expose missing/stale/conflicting proof. |
| Rebuild 3: Make reviews useful | 2026-09-28 to 2026-10-11 | TAB-24, TAB-25, TAB-26 | Resolve exact candidate, create bounded Codex packet, run separate security review. |
| Rebuild 4: Prepare the release | 2026-10-12 to 2026-10-25 | TAB-27, TAB-28, TAB-29 | Show clear results, test failure/recovery, release first useful version. |

### Active stories

| ID | Name | Type in description | Priority | Points | Current state |
| --- | --- | --- | --- | --- | --- |
| TAB-18 | Decide what Tabellio should remember | human-decision | medium | 3 | Backlog |
| TAB-19 | Prove local storage works | prototype | high | 5 | Backlog |
| TAB-20 | Save one change from start to finish | prototype | high | 5 | Backlog |
| TAB-21 | Connect the trusted tools | implementation | high | 5 | Backlog |
| TAB-22 | Rebuild the record from the original tools | prototype | high | 5 | Backlog |
| TAB-23 | Show when proof is missing or wrong | implementation | high | 5 | Backlog |
| TAB-24 | Find the exact code being reviewed | implementation | high | 3 | Backlog |
| TAB-25 | Give Codex a safe review packet | implementation | high | 5 | Backlog |
| TAB-26 | Run a separate security review | implementation | high | 5 | Backlog |
| TAB-27 | Show clear review results | implementation | high | 5 | Backlog |
| TAB-28 | Test failure and recovery | implementation | high | 5 | Backlog |
| TAB-29 | Release the first useful version | manual-task | high | 5 | Backlog |

### Roadmap interpretation

**Fact.** Stories form a text-described dependency chain. First three deliberately exclude cloud provisioning, broad source adapters, semantic search, dashboards, queues, and automatic learning.

**Interpretation.** Roadmap is correctly sequencing proof before infrastructure. Entire transport adoption would jump ahead of TAB-18 through TAB-23 and introduce a new source/transport before data boundaries, replay, and failure semantics are proven.

## 4. Current architecture and stack

### Plain-language topology

~~~mermaid
flowchart LR
    notion["Notion: why, research, decisions"] --> plane["Plane: roadmap and execution state"]
    plane --> codex["Human or Codex work"]
    codex --> wt["Git worktree and branch"]
    codex --> entire["Entire: session and checkpoints"]
    wt --> tab["Tabellio CLI: join, validate, review, release gates"]
    entire --> tab
    tab --> origin["GitHub origin: canonical code, PR, tag"]
    tab --> control["Private GitHub control repo: reviews, validations, checkpoints"]
    gha["GitHub Actions"] --> tab
    bk["Buildkite"] --> tab
    deploy["Release/deployment receipts"] --> tab
~~~

### Components

| Component | Current implementation | State/authority |
| --- | --- | --- |
| CLI/package | <code>@intelip/tabellio</code>, Node.js 20+, ES modules, no runtime dependencies | Current product runtime |
| Git engine | <code>NativeGitStore</code>, worktrees, merge-tree, update-ref/CAS | Git objects are code truth |
| Run lifecycle | <code>tabellio-run</code>, local <code>.tabellio/</code> state | Mutable local execution state |
| Checkpoint adapter | <code>EntireLedgerProvider</code> | Entire is session/checkpoint authority |
| Stack adapter | git-spice | Stack topology and approved operations |
| GitHub adapter | Read-only PR/review/status/check normalization | GitHub is code/PR/merge authority |
| Evidence contracts | JSON Schema plus versioned JSON/JSONL records | Portable contract |
| Validation | <code>ValidationRunner</code>, exact commit, bounded output | Tabellio validation authority |
| Review ledger | <code>refs/tabellio/reviews</code> | Tabellio durable review state |
| Validation ledger | <code>refs/tabellio/validations</code> | Tabellio durable validation state |
| Control transport | Atomic, approval-gated standard Git pushes to <code>control</code> | Private external state |
| Analytics | Plane/Buildkite/GitHub/release/deployment collectors and reports | Derived evidence, not source authority |
| Proposed provenance store | PostgreSQL + JSONB + relationship rows | Not implemented |

### Runtime and deployment

**Fact.** Runtime is local developer/agent process or CI worker. There is no tracked application server, database migration set, container deployment, Terraform stack, GitHub environment, or GitHub deployment. GitHub Actions use Ubuntu runners; Buildkite uses a <code>macos-medium</code> queue. Deployment-related CLIs collect and validate Cloud Run/Vercel receipts; they do not prove a Tabellio service is deployed.

### CI/CD and observability

**GitHub Actions**

- <code>Quality</code>: tests, Fallow changed-code gate, package dry run.
- <code>product-validation</code>: exact candidate validation on PR and main push, with artifacts.
- Latest main run observed: success at <code>979a81d</code> on 2026-08-01.

**Buildkite**

- required strict branch-protection check: <code>buildkite/tabellio</code>;
- pipeline steps: supported Git, repository check, Fallow, package dry run, product validation;
- PR #44 reports successful Buildkite build #151 from 2026-07-30;
- live <code>bk build list --pipeline intelip/tabellio --limit 10 --summary --json</code> returned an empty list, so current Buildkite inventory was not independently confirmed.

**Observability**

- command stdout/stderr hashes plus bounded 16 KiB tails;
- validation/result refs and one-use receipts;
- analytics reports over Plane/GitHub/Buildkite/release/deployment snapshots;
- explicit failure and blocked states;
- proposed OpenTelemetry only; no deployed tracing/logging backend found.

### Authority map

| Domain | Authority | Tabellio's role |
| --- | --- | --- |
| Code commits, branches, tags, PRs, checks, merge | GitHub <code>origin</code> | Read, bind, validate, and gate |
| Roadmap, state, dates, assignee, priority | Plane TAB project | Read and correlate |
| Research, rationale, decisions, learning | Notion / repository Markdown | Cite and scope |
| Agent session, transcript, checkpoint, resume | Entire | Read normalized metadata; do not copy transcript bytes |
| Stack topology | git-spice and Git | Normalize and bind |
| Validation and durable review | Tabellio Git refs | Own result state |
| CI execution | GitHub Actions and Buildkite | Collect receipts |
| Release/deployment truth | GitHub release plus exact runtime receipt | Correlate without inferring deployment |
| Code relationships | Graphify advisory index | Navigation only, never runtime authority |

## 5. Current delivery and agent workflow

### Evidence-backed intended flow

1. **Idea/research.** Rationale belongs in Notion or durable repository Markdown.
2. **Roadmap item.** Plane item defines objective, user story, priority, dates/cycle, acceptance, dependencies, and state.
3. **Work admission.** Exact Plane item, repository, branch/worktree, and base candidate are selected.
4. **Session start.** Codex hooks call Entire; Tabellio or Codex uses an isolated worktree.
5. **Implementation.** Agent/human creates small commits. Entire records session state and creates checkpoint metadata linked by <code>Entire-Checkpoint</code> trailers.
6. **Context and evidence.** Tabellio resolves immutable base/head/merge-base, changed files, checkpoint IDs, validation, approvals, and side-effect policy.
7. **Validation.** Local or CI workers validate exact commit; stale head invalidates prior evidence.
8. **Review.** GitHub provides thin PR surface; Tabellio review ref retains detailed state; Codex/security findings can be imported.
9. **Merge.** Approved git-spice/GitHub operation advances canonical code; no second merge authority.
10. **Release.** Release orchestration binds exact merged main, tag, control refs, and GitHub release.
11. **Deployment.** Separate runtime receipt required; release or green CI alone is not deployment.
12. **Recovery/learning.** Entire resumes sessions; Git refs and source replay rebuild evidence; only validated, redacted, scoped lessons may inform later work.

### Evidence of actual use

- Commit <code>28d3d15</code> added mandatory Entire integration.
- Twenty-five main commits contain Entire checkpoint trailers, spanning 19 unique checkpoint IDs.
- One active Entire Codex session was visible during audit.
- Twenty-six local Entire refs exist: one checkpoint branch and 25 session/shadow refs.
- Two Git worktrees exist: canonical main and one validator-security repair worktree.
- Many archived/local feature branches remain, but only one open GitHub PR exists.
- Plane roadmap was rebuilt on 2026-08-30; all new work remains Backlog.

### Parallelism and isolation

**Fact.** Tabellio supports per-run worktrees, stack branches, cross-project wave admission, CAS refs, and bounded concurrent provider reads. These are architectural capabilities.

**Unknown.** No source records current clone/fetch rate, peak concurrent agents, geographic agent distribution, or GitHub rate-limit incidents. Branch count and old Entire refs do not prove active fleet concurrency.

### Recovery boundaries

- Git worktree cleanup is explicit.
- CAS ref updates reject stale writers.
- Validation is replayable from committed manifests.
- Entire can explain/resume checkpointed sessions.
- Private control repo is intended recovery copy for checkpoint/review/validation refs.
- Current checkpoint remote lag means intended recovery RPO is not being met.

## 6. Current Entire integration

### Exact integration points

| Integration | Evidence | Current behavior |
| --- | --- | --- |
| Project enablement | <code>.entire/settings.json</code> | Entire enabled; checkpoint remote is <code>IntelIP/Tabellio-control</code>; automatic session pushes disabled. |
| Codex capture | <code>.codex/hooks.json</code> | SessionStart, UserPromptSubmit, PostToolUse, and Stop invoke Entire hooks. |
| History search | <code>.codex/agents/entire-search.toml</code> | Dedicated read-only Entire search agent configuration. |
| Commit linkage | Git trailers and checkpoint branch | Main has 25 checkpoint-linked commits / 19 IDs. |
| Metadata adapter | <code>scripts/providers/entire-ledger-provider.mjs:12</code>, <code>EntireLedgerProvider</code> | Calls <code>entire version</code> and <code>entire checkpoint explain --json</code>; normalizes metadata and hashes response. |
| Context capture | <code>scripts/capture-tabellio-context.mjs</code> | Default ledger is Entire; Git notes are migration mode. |
| Preflight | <code>scripts/lib/preflight.mjs:22</code> | Requires Entire CLI 0.7.7 or later and trusted hooks. |
| Validation | <code>scripts/lib/validation-runner.mjs:917</code> and manifests | Requires checkpoint IDs for configured change ranges. |
| Review repair | <code>scripts/lib/review-cycle.mjs:256-275</code> | Uses unique checkpoint ID to remap commits after git-spice rewrites. |
| Private publication | <code>scripts/lib/control-ref-transport.mjs:14</code> | Publishes checkpoint/review/validation refs to private <code>control</code>, never <code>origin</code>. |

### Features actually used today

- local session capture;
- prompt/tool/transcript capture owned by Entire;
- commit-time checkpoints;
- checkpoint explanations and normalized summaries/token metadata;
- commit/checkpoint linkage;
- session resume/search configuration;
- local shadow/session branches;
- branch-based <code>entire/checkpoints/v1</code> persistence;
- separate private checkpoint remote;
- release/review/validation integrity joins.

### Features not evidenced today

- regional repository mirror;
- <code>entire://</code> Git remote helper in this repository;
- mirror-backed clones/fetches;
- multi-region repository placement;
- Entire-native or <code>entire/unmirrored/</code> product branches;
- hosted Entire as canonical code store;
- ref-based checkpoint backend;
- hosted cross-repository search dependency.

Hosted mirror listing failed with: <code>connect to Entire control plane: resolve control-plane target: not logged in; run entire login</code>. No login was attempted.

### Data and state ownership

**Entire owns**

- session/transcript history;
- local active session metadata;
- shadow/session refs;
- persistent checkpoints;
- checkpoint search/resume semantics.

**Tabellio owns**

- normalized checkpoint IDs, commit bindings, summaries, token totals, and digests;
- context/evidence envelopes;
- validation/review/release joins.

**GitHub owns**

- canonical code;
- private remote copy of checkpoint/control refs, when published.

### Removal impact

**If Entire CLI/hooks were removed today:**

- new Codex sessions would no longer produce expected checkpoints/trailers;
- default context capture and Entire preflight would fail;
- workflows requiring checkpoint evidence would block;
- resume/search/transcript recovery would disappear;
- git-spice rewrite remapping by checkpoint would lose its stable key.

Legacy Git-note mode could preserve limited migration compatibility, but it is not equivalent to current session/transcript capability.

**If Entire regional hosting/mirroring were removed today:** no repository evidence shows a break, because no Entire Git remote or mirror is configured locally.

### Version and backend compatibility

**Fact.**

- installed CLI: 0.7.7;
- latest offered stable release during audit: 0.10.3;
- local backend: shared 12-character-ID branch;
- current provider validates only 12-character hexadecimal IDs and hard-codes <code>entire/checkpoints/v1</code>;
- first-party 0.10.x material supports one-ref-per-checkpoint storage and 26-character time-sortable IDs.

**Interpretation.** A CLI/backend upgrade can cross an application compatibility boundary. Ref-based checkpoint storage, subagent durability, zombie-session recovery, and newer redaction improvements are valuable, but enabling them without updating Tabellio's schemas/provider/tests could break checkpoint capture and review remapping.

### Current durability state

| Measure | Local | Private control repo |
| --- | ---: | ---: |
| Checkpoint IDs | 157 | 78 |
| Checkpoint branch tip | <code>5e7afa34352b...</code> | <code>79518b510121...</code> |
| Branch comparison | 256 commits ahead | 0 commits unique |
| Latest observed local checkpoint | 2026-07-31 | GitHub repo pushed 2026-07-27 |

**Interpretation.** Entire data exists and matters, but configured remote durability is not current. This should be fixed before considering a new transport layer.

## 7. Plane roadmap and user-story findings

### Project

- Name/identifier: Tabellio / TAB
- Project UUID: <code>ea3ee64d-b148-4493-adfe-6bb640e02982</code>
- Created: 2026-08-21
- Updated: 2026-08-30
- Owner/lead/default assignee: Hudson
- Cycles: enabled, four active definitions
- Modules: enabled, zero modules
- Milestones: disabled/zero
- Epics/work-item types: disabled; all current items have native type <code>null</code>
- Deployment flag: false

### Workflow

Backlog → In Progress → Review → Completed, with Blocked and Cancelled states. Blocked is a started-state group and counts against WIP.

### Findings

**Fact.** Twelve active stories are complete business-first descriptions with objectives, user stories, success criteria, exclusions, risks, test protocols, demo contracts, and dates.

**Conflict (at audit time; corrected 2026-09-02).** Each story's description named “Blocked by” dependencies, but the 2026-09-01 native Plane relation queries returned no dependency or custom relation. On 2026-09-02 the direct blocking edges were added and read back for TAB-18 through TAB-29. Plane now carries the dependency graph; the descriptions remain the human-readable contract.

**Conflict.** Rebuild Cycle 1 is date-current on 2026-09-01, but all three cycle stories and all later stories remain Backlog. Plane does not show active implementation.

**Fact.** Previous TAB-1 through TAB-17 items are Cancelled and archived. They covered broader merge ownership, local coordinator, provider-neutral identity, subagent provenance, observability, and outage work. New roadmap intentionally narrows first release to provenance foundation and review usefulness.

**Unavailable.** Plane Initiatives call returned HTTP 403 “Payment required.” Initiative membership could not be verified. No module or milestone substitutes exist.

### Roadmap implication for Entire

Entire is explicitly an upstream source in TAB-20, TAB-21, and later review packets. No story asks Tabellio to replace GitHub with Entire hosting. TAB-22 requires source replay and source independence; that favors a bounded adapter, portable normalized records, and GitHub fallback.

## 8. Notion and research findings

### Tabellio-specific decision

**Page:** [Tabellio Provenance Ledger and Codex Feedback Architecture](https://app.notion.com/p/3cc27d53272481889ca3d496e4a1727b?pvs=204)  
**Last edited:** 2026-08-30T06:58:40Z  
**Page status:** Proposed  
**Confidence:** Lead  
**Connector verification:** unverified

Key findings:

- Tabellio is an internal control/evidence layer, not a replacement for Git, GitHub, Entire, CI, validators, or approval.
- Current integration does not capture every prompt/tool/source/output.
- Git remains code authority; Entire supplies checkpoint/session lineage.
- Graphify is navigation, not runtime evidence storage.
- No central provenance database exists.
- PostgreSQL + JSONB + explicit relationships is proposed.
- Raw content boundaries, retention, tenant isolation, and lesson approval remain open.
- First implementation should be one complete lineage fixture, not a full event platform.

### Knowledge authority decision

**Page:** [Notion is the IntelIP knowledge layer](https://app.notion.com/p/3cc27d5327248155b480f98913c07bee?pvs=204)  
**Last edited:** 2026-08-30T05:30:22Z  
**Status/confidence:** Active / Confirmed

It states:

- Plane owns execution state.
- GitHub owns code and PR state.
- Notion explains why work matters and what was learned.
- repository Markdown and Codex skills own durable operating rules.

### Research coverage

Targeted Notion searches for Tabellio roadmap, agent provenance architecture, Entire hosting/mirroring, and competitive research returned one directly relevant Tabellio Decision. Other hits were generic IntelIP governance or old product/marketing notes. No dedicated Notion page validating Entire's new hosting performance, costs, SLAs, regions, failure behavior, or competitive position was found.

**Interpretation.** Notion aligns with repository authority boundaries but does not provide independent support for adopting Entire's hosting network.

## 9. Cross-source contradictions and stale assumptions

| Conflict | Evidence | Impact |
| --- | --- | --- |
| v0.6.0 “latest” vs canonical main | GitHub release/tag points to off-main <code>615733b</code>; PR #44 remains open/conflicted; main package is 0.5.0. | Release label must not be treated as merged canonical product state. |
| Git-note lifecycle doc vs mandatory Entire default | <code>docs/agent-run-lifecycle.md:81</code> says checkpoints use Git notes; <code>docs/workflow-model.md:27-38</code>, tooling docs, and code make Entire default. | Agent lifecycle doc is stale or describes legacy path. |
| “Current Unreleased Scope” vs released repository | <code>docs/tooling-stack.md:59</code> labels a broad current scope unreleased; several releases exist. | Documentation maturity labels need revision. |
| Tabellio “keeps transcripts” vs source ownership | README wording at line 15 can imply Tabellio stores transcripts; provider stores metadata only and Entire owns transcript bytes. | Security/data-ownership wording should be tightened. |
| Plane blockers vs native dependency graph | 2026-09-01 relation reads were empty; 2026-09-02 direct blocking edges were added and read back. | Native ordering is now queryable; future work must keep relations and prose aligned. |
| Cycle started by date vs execution state | Cycle 1 dates began 2026-08-31; all stories remain Backlog. | Schedule is not implementation status. |
| PostgreSQL architecture vs current runtime | Notion and untracked docs propose PostgreSQL; no database runtime/migrations/deployment exist. | Proposal must not be described as deployed architecture. |
| Checkpoint remote contract vs actual sync | Private control remote is configured, but local checkpoint branch is 256 commits ahead. | Current durability/recovery objective is unmet. |
| v0.7.7 branch backend vs v0.10.x ref backend | Tabellio adapter accepts 12-hex branch checkpoints; current Entire supports ULID refs. | Upgrade requires compatibility work and migration tests. |
| Entire region statements | Launch blog names US/EU/Australia; current repository overview names US/EU. | Availability is changing; verify live before any region decision. |
| Entire checkpoint default wording | First-party checkpoint guide calls branch backend default, while current CLI architecture says new setups preselect refs and old repos fall back to branch. | Documentation reflects transition; do not infer backend from version alone. |
| Graphify vs working tree | Graphify returned 7,859 nodes/11,892 edges; repository has uncommitted proposal docs and changed files. | Graph is advisory and cannot establish current implementation. |
| Buildkite API surfaces | GitHub check history links build #151, while current <code>bk build list</code> returned empty. | Live Buildkite inventory remains unverified; historical GitHub receipt is usable. |
| Notion proposal vs implementation | Notion page says repository note should be linked after commit; local architecture files remain untracked. | Decision record is ahead of canonical repository state. |

## 10. Capability-to-need matrix for Entire

| Entire capability | Tabellio workflow/requirement | Supporting evidence | Pain | Expected benefit | Effort | Operational risk | Maturity/availability | Recommendation |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Codex session hooks | Capture agent run context | <code>.codex/hooks.json</code>; one active session | High if lost | Preserves why/how work occurred | Existing | Sensitive transcript capture | In use on CLI 0.7.7 | **Adopt now** (retain) |
| Commit checkpoints and trailers | Bind commits to agent sessions | 25 main commits / 19 checkpoint IDs; provider code | High | Stable join across commit rewrites/review | Existing | Missing/stale checkpoint blocks work | In use | **Adopt now** (retain) |
| Explain/search/resume | Recovery and prior-work inspection | Entire CLI commands; search-agent config | Medium | Faster recovery and context lookup | Existing | Search privacy and version drift | In use locally | **Adopt now** (retain) |
| Separate checkpoint remote | Keep private context out of public origin | <code>.entire/settings.json</code>; private control repo | Critical | Durable private recovery copy | Low to repair | Data exposure if target wrong; current lag | Supported, currently stale | **Adopt now** and repair |
| Ref-based checkpoint backend | Concurrent checkpoint writes and selective fetch | Entire 0.10.x architecture; current branch is serialization point | Low current, future medium | Removes shared checkpoint-tip contention | Medium/high | Current adapter/schema incompatibility | Shipped in newer CLI | **Pilot** after adapter support |
| Durable subagent records | Trace parallel agent contributions | Entire 0.10.3 release; archived TAB-7 had this goal | Low current | Better parent/subagent lineage | Medium | Agent-specific hook gaps and sensitive task text | Shipped for some agents in 0.10.3 | **Pilot** with CLI upgrade |
| Zombie-session sweep and hook metrics | Recovery/observability | Entire 0.10.3 release | Medium | Less stuck state; measurable hook latency | Medium | Upgrade regression | Shipped in 0.10.3 | **Pilot** |
| GitHub regional read mirror | High-volume clone/fetch | Entire blog/docs; no local volume evidence | Low/unproven | Lower regional read latency and origin load | Medium | New auth, sync, vendor, data-placement path | Preview/waitlist history; live entitlement unknown | **Monitor and defer** |
| <code>entire://</code> Git transport helper | Agent clone/fetch routing | First-party blog; no configured remote | Low/unproven | Simple regional endpoint | Medium | Tool availability and fallback complexity | Shipped with CLI for mirrors | **Defer** |
| GitHub-backed push through Entire | Preserve GitHub-backed branches | Blog says push remains as fast as GitHub allows | None demonstrated | Unified remote interface, not extra write throughput | Medium | Sync ambiguity; no proven gain | Available with mirror | **Reject now** |
| Entire-native/unmirrored branches | Very high concurrent writes | ForgeMark path; Tabellio requires GitHub canonical merge | None | High write throughput under synthetic fleet load | High | Second authority, regional-only state, recovery/PR mismatch | Vendor preview/native path | **Reject** for product branches |
| Multi-region placement/replication | Resilience/data residency | Entire blog/docs; no Tabellio geo requirement | Low/unproven | Regional redundancy and locality | High | Policy, residency, replication, cost, vendor lock-in | Region list inconsistent; SLA not verified | **Monitor and defer** |
| Entire as broad agent infrastructure | Unified code + sessions + branches | Vendor product direction | None demonstrated | Fewer conceptual vendors | Very high | Concentrated failure/lock-in and authority collapse | Evolving | **Reject** |

## 11. Current problems versus hypothetical future benefits

### Demonstrated current problems

| Problem | Evidence | Priority |
| --- | --- | --- |
| Checkpoint recovery copy lags local state | 157 local vs 78 remote checkpoint IDs; 256 commits ahead | Immediate |
| Canonical release truth is inconsistent | v0.6 off-main; open conflicted release PR; main package 0.5.0 | Immediate |
| Plane dependency graph was prose-only | Twelve relation queries were empty on 2026-09-01; native blocking graph repaired on 2026-09-02 | Completed roadmap hygiene; preserve on future edits |
| First cycle has no started work | All TAB-18–TAB-29 Backlog | Product decision |
| Entire adapter is behind backend evolution | 0.7.7 and 12-hex branch assumption vs 0.10.3 refs/ULIDs | Near-term compatibility |
| Central provenance and replay do not exist | Notion and working-tree proposal; no DB | Roadmap |
| Buildkite live inventory not confirmed | CLI returned empty; only GitHub receipt observed | Operational visibility |

### Hypothetical future benefits

| Benefit | Missing evidence required before adoption |
| --- | --- |
| Faster agent clones/fetches | p50/p95 latency, clone/fetch counts, concurrency, cache hit rate |
| Avoid GitHub rate limits | actual 429/403 rate-limit events and rate-limit headroom |
| Regional agent locality | agent runtime regions and latency impact |
| Higher branch write throughput | real simultaneous push workload and GitHub bottleneck |
| GitHub outage resilience | tested mirror staleness/fallback behavior and RPO/RTO |
| Data residency | approved jurisdiction, retention, encryption, subprocess, and deletion requirements |
| Lower cost | Entire pricing/contract plus current GitHub/CI traffic cost baseline |

**Interpretation.** Current benefits are primarily checkpoints and recovery. Hosting benefits remain unmeasured hypotheses.

## 12. Recommended role for Entire in the stack

### Role decision

| Possible role | Decision | Reason |
| --- | --- | --- |
| Session and checkpoint layer | **Keep** | Demonstrated dependency and useful stable join. |
| Checkpoint transport to private control repo | **Keep and repair** | Correct privacy boundary; current synchronization is behind. |
| Git transport adapter for normal work | **Do not adopt now** | No read-scale problem measured; adds critical-path dependency. |
| Regional GitHub mirror | **Monitor and defer** | Potential future read optimization only. |
| Entire-native branch platform | **Reject for canonical work** | Conflicts with GitHub merge authority and benchmark isolation. |
| Canonical code store | **Reject** | No evidence supports replacing GitHub. |
| Broad agent infrastructure dependency | **Reject** | Concentrates code, context, identity, and transport failure. |

### Target boundary

~~~text
GitHub origin
  owns code, PRs, merge, tags

Entire
  owns session/transcript/checkpoint history
  exports stable IDs and normalized metadata

Tabellio
  owns evidence joins, freshness, validation, review, release gates

Plane
  owns delivery state

Notion / Markdown
  owns rationale, research, durable rules
~~~

**Recommendation.** Preserve this federated authority model. Entire may optimize read transport later, but must remain replaceable and must not become the only route to canonical code.

## 13. Risks, dependencies, and fallback design

### Risks

| Risk | Current/future exposure | Control |
| --- | --- | --- |
| Checkpoint loss | Current remote lag | Define checkpoint RPO; reconcile private remote; restore drill. |
| Sensitive transcript exposure | Entire captures prompts, tools, file/MCP content | Private remote, redaction, least access, no origin publication. |
| Raw code secrets on shadow branches | Entire documents raw code snapshots are not redacted | Never manually push shadow refs; secret scanning; clean lifecycle. |
| PII leakage | PII redaction is opt-in in current Entire docs | Decide TAB-18 policy before broader capture. |
| Vendor lock-in | Resume/search/backend semantics are Entire-specific | Keep normalized metadata, Git objects, exports, and source replay. |
| Backend migration break | 12-hex branch adapter vs ULID/ref backend | Compatibility fixture and dual-backend reads before migration. |
| Mirror staleness | Future GitHub mirror can lag/fail | GitHub direct fallback, lag metric, never merge from uncertain mirror state. |
| Second branch authority | Entire-native branches can diverge from GitHub | Forbid for canonical product branches. |
| Region/contract drift | Official region and availability statements differ | Verify live entitlement, DPA, regions, SLA, deletion before pilot. |
| Concentrated outage | Hosted code + checkpoint platform failure | Keep GitHub direct remote and private Git backup paths. |

### Fallback design

1. <code>origin</code> remains direct GitHub URL and canonical fetch/push target.
2. Any future Entire mirror is read-optional. Failure routes clones/fetches to GitHub without changing branch identity.
3. Canonical pushes, PRs, tags, and merges go to GitHub, not Entire-native branches.
4. Checkpoints remain Git-backed and are exported to private <code>control</code>.
5. Tabellio stores normalized checkpoint metadata/digests, never requires hosted transcript bytes for deterministic validation.
6. Replay reconstructs projections from Plane, Git, Entire, GitHub, Buildkite, and committed evidence.
7. Backend migration keeps branch reads until ref-based checkpoint parity and restore tests pass.
8. A mirror outage can slow work but cannot change merge truth.

## 14. Proposed bounded pilot, if justified

### Current decision

An Entire regional mirror pilot is **not justified now**. No qualifying load or failure signal exists.

### Trigger-gated future pilot

Run this only if one of these is observed for at least two weeks:

- peak concurrent fresh clones/fetches ≥ 20;
- GitHub clone/fetch p95 ≥ 30 seconds for repositories of comparable size;
- at least two GitHub rate-limit or read-availability incidents affecting agents;
- agents operate in at least two regions and cross-region Git latency materially delays delivery;
- GitHub read traffic becomes a measured CI/agent cost or reliability bottleneck.

**Smallest non-critical workflow**

- one synthetic Tabellio fixture repository or disposable read-only mirror;
- one agent pool in one region;
- clone, fetch, merge-base, diff, and history reads only;
- no release branch, no private production code, no Entire-native branch, no canonical pushes.

**Baseline**

- direct GitHub clone/fetch p50/p95/p99;
- request and byte counts;
- GitHub rate-limit/5xx count;
- agent startup time;
- failed/retried Git operations;
- mirror freshness lag;
- operator time and cost.

**Success thresholds**

- ≥ 30% lower p95 clone/fetch time under representative concurrency;
- zero missing objects or incorrect refs;
- mirror freshness p95 ≤ 60 seconds, or tighter if vendor contract supports it;
- 100% successful automatic/manual fallback to GitHub in failure drills;
- zero canonical branch divergence;
- zero unauthorized data placement or transcript/code exposure;
- no material increase in operating cost per completed agent task.

**Failure/stop thresholds**

- any incorrect ref, missing commit, unauthorized region, or secret exposure;
- any canonical push routed to an unapproved destination;
- fallback failure;
- mirror lag exceeds accepted RPO in two tests;
- < 10% p95 improvement;
- no real rate-limit/latency problem after baseline period.

**Tests**

1. GitHub available / mirror available.
2. Mirror unavailable / GitHub fallback.
3. GitHub temporarily unavailable / mirror read behavior.
4. Delayed synchronization and stale-ref detection.
5. Concurrent shallow and full clones.
6. Fetch, unshallow, merge-base, and object-integrity checks.
7. Credential revocation.
8. Region placement and deletion verification.
9. Recovery from local clone using GitHub only.

**Duration**

Fourteen days after a seven-day baseline.

**Expansion decision**

Expand to one real but non-critical agent pool only if every safety threshold passes and performance gain exceeds 30%. Otherwise stop and keep GitHub direct.

### Separate checkpoint-backend compatibility pilot

This pilot is more relevant than a regional mirror:

- clone repository into a disposable local test copy;
- install approved Entire CLI version without changing current repository;
- exercise old branch checkpoints and new ref checkpoints;
- update/test adapter support for both ID forms;
- verify commit trailer resolution, git-spice rewrite remapping, control remote publication, resume, export, and restore;
- stop if any existing checkpoint becomes unreadable or any transcript/ref is pushed to <code>origin</code>.

This is a compatibility experiment, not authorization to upgrade or migrate.

## 15. Metrics and acceptance criteria

### Current Entire/session layer

| Metric | Acceptance |
| --- | --- |
| Commits requiring checkpoints with a valid checkpoint | 100% |
| Checkpoint explain success for linked commits | 100% |
| Local-to-control checkpoint count delta | Zero outside documented active-session RPO |
| Control-ref sync lag | Explicit target; recommended ≤ 15 minutes after approved publication |
| Restore drill | Recover checkpoint metadata and one session from private remote with no local refs |
| Checkpoint data on public origin | Zero |
| Hook p95 and timeout rate | Measured; zero 30-second hook timeouts in normal workload |
| Redaction failures | Fail closed; zero pushed under-scanned transcripts |
| Orphan/zombie sessions | Zero older than defined cleanup window |
| Backend compatibility | Old and new checkpoint formats readable during migration |

### Tabellio roadmap

| Stage | Acceptance |
| --- | --- |
| TAB-18 | Approved include/exclude/retention/redaction rules |
| TAB-19 | Empty DB migration, save/read/restart, idempotency, no cloud |
| TAB-20 | One deterministic task → run → commit → Entire → PR → validation → review chain |
| TAB-21 | Source reads expose auth/permission/outage/malformed states |
| TAB-22 | Delete derived state and replay same canonical result |
| TAB-23 | Missing/stale/conflicting/inferred evidence cannot become false green |
| TAB-24–26 | Exact candidate, bounded packet, separate security decision |
| TAB-27–29 | Clear operator status, failure/recovery demo, exact approved release |

### Revisit criteria for Entire hosting

Revisit only when at least one trigger in Section 14 is observed and data owner, region, SLA, pricing, fallback, and deletion terms are verified from current first-party/contract sources.

## 16. Open questions

1. Is private control-repository lag intentional because publication awaits a release approval, or is the 157-local/78-remote checkpoint gap an operational failure? What checkpoint RPO is acceptable?
2. Was off-main v0.6.0 publication intentional? Which artifact should users treat as canonical: main 0.5.0, off-main v0.6.0, or a future reconciled release?
3. Plane blocking relations were added on 2026-09-02. Why does the cycle list include the rebuild stories while each retrieved work item reports <code>cycle_id: null</code>? Verify whether this is an API projection issue or missing cycle membership.
4. What measured agent concurrency, clone/fetch volume, GitHub rate-limit pressure, and runtime regions are expected over the next two cycles?
5. Does an Entire hosted organization/mirror exist under another login, and what regions, retention, deletion, encryption, SLA, and pricing have been approved?
6. Is Tabellio's near-term product still an internal CLI/control layer, or is a hosted multi-tenant product now intended? That decision changes storage and mirror requirements materially.

## 17. Evidence index

### Repository and Git

Retrieved 2026-09-01 unless noted.

- <code>README.md:13-94</code> — product problem, evidence packet, stack, authorities, core files.
- <code>docs/workflow-model.md:5-125</code> — core objects, standard flow, evidence/review boundaries, future extensions.
- <code>docs/tooling-stack.md:9-126</code> — stack map, Entire/GitHub boundaries, current/proposed scope.
- <code>docs/agent-run-lifecycle.md:5-84</code> — worktree lifecycle, validation/promotion, stale Git-note wording.
- <code>docs/github-code-storage-boundary.md:5-29</code> — public code vs private control-state placement.
- <code>docs/operations-hardening.md:5-46</code> — concurrency, publication, release, recovery controls.
- <code>docs/research-grounding.md:7-81</code> — product thesis, prior art, allowed/forbidden claims.
- <code>docs/review-loop.md</code> — checkpoint-bound repair and durable review state.
- <code>docs/product-validation.md</code> — exact-candidate decision semantics.
- <code>tabellio.platform.json</code> — GitHub origin, Entire ledger, private control refs.
- <code>.entire/settings.json</code> — Entire enabled, private checkpoint remote, <code>push_sessions: false</code>.
- <code>.codex/hooks.json</code> — Entire Codex lifecycle hooks.
- <code>.codex/agents/entire-search.toml</code> — read-only Entire search configuration.
- <code>scripts/providers/entire-ledger-provider.mjs:12</code>, symbol <code>EntireLedgerProvider</code> — version check, checkpoint explain, normalized metadata.
- <code>scripts/lib/capture-context.mjs:6</code>, symbol <code>captureContext</code> — checkpoint-bound context.
- <code>scripts/lib/validation-runner.mjs:917</code>, symbol <code>checkpointIds</code> — validation checkpoint requirement.
- <code>scripts/lib/control-ref-transport.mjs:14</code>, <code>CONTROL_REFS</code> — private atomic ref transport.
- <code>.github/workflows/quality.yml</code> and <code>product-validation.yml</code> — GitHub CI.
- <code>.buildkite/pipeline.yml</code> — macOS Buildkite gates.
- <code>package.json</code> — package 0.5.0, CLI inventory, test/check commands.
- Git commits: <code>fb70542</code> initial extraction; <code>9060164</code> rebrand; <code>23b2797</code> native Git; <code>28d3d15</code> mandatory Entire; <code>24cf171</code> GitHub-only hosted migration; <code>617b89a</code> release automation; <code>c9759d7</code> durable review readiness; <code>37d42ad</code> Plane collector; <code>979a81d</code> wave admission.
- Git tag target: v0.6.0 → <code>615733b807a0c1105f2410fd933a99c97057218f</code>, not ancestor of main.
- Working-tree proposals: <code>docs/agent-provenance-ledger-and-codex-feedback.md</code>, <code>docs/architecture-diagrams.md</code>, and <code>docs/architecture/proposals/provenance-ledger/</code>; untracked during audit.

### Plane

Live read 2026-09-01; dependency repair and registry refresh 2026-09-02.

- Project TAB / <code>ea3ee64d-b148-4493-adfe-6bb640e02982</code>.
- Active work: TAB-18 through TAB-29.
- Archived/cancelled prior roadmap: TAB-1 through TAB-17.
- Cycles: <code>5c0a767e-e3d8-4e6a-a0b5-423ef9feea49</code>, <code>8a4dcb55-d687-43a9-91f0-238a44aadfb8</code>, <code>cf81ca54-6ab4-4ae4-9cdf-ef3c61b3937f</code>, <code>98004b58-ed89-4dba-a57f-3e6ca9ac5695</code>.
- Native relation reads were empty during the audit. On 2026-09-02, direct <code>blocking</code> relations matching each story's “Blocked by” text were added and read back for TAB-18 through TAB-29.
- Initiatives unavailable: HTTP 403 “Payment required.”

### Notion

- [Tabellio Provenance Ledger and Codex Feedback Architecture](https://app.notion.com/p/3cc27d53272481889ca3d496e4a1727b?pvs=204), last edited 2026-08-30T06:58:40Z.
- [Notion is the IntelIP knowledge layer](https://app.notion.com/p/3cc27d5327248155b480f98913c07bee?pvs=204), last edited 2026-08-30T05:30:22Z.
- Targeted searches: “Tabellio,” “Tabellio roadmap product goals users,” “agent provenance evidence control plane architecture,” and “Entire GitHub mirroring regional repository native branches.”

### GitHub and Buildkite

- [IntelIP/Tabellio](https://github.com/IntelIP/Tabellio) — public canonical repository.
- [PR #44](https://github.com/IntelIP/Tabellio/pull/44) — open, conflicted v0.6 release branch.
- [Main product-validation run](https://github.com/IntelIP/Tabellio/actions/runs/30679078339) — success at <code>979a81d</code>, 2026-08-01.
- [IntelIP/Tabellio-control](https://github.com/IntelIP/Tabellio-control) — private control repository; contents not linked publicly.
- GitHub API returned no deployments and no environments.
- Branch protection requires strict <code>buildkite/tabellio</code>.
- Buildkite CLI 3.54.1 available; recent summary query returned <code>[]</code>.

### Entire local state

- Entire CLI 0.7.7, update offered to 0.10.3.
- <code>entire status --json</code>: enabled, Codex agent, one active session.
- Hosted mirror list unavailable because CLI is not logged in.
- Local checkpoint branch: 376 commits, 157 checkpoint IDs.
- Private control checkpoint branch: 78 checkpoint IDs.
- Branch comparison: local 256 commits ahead / zero behind.
- Code <code>origin</code> has no <code>entire/checkpoints/v1</code> branch.

### Entire first-party material

Retrieved 2026-09-01.

- [An Entirely New Git Hosting Network](https://entire.io/blog/an-entirely-new-git-hosting-network) — mirroring, regional cells, GitHub-backed push limit, unmirrored/native benchmark path, architecture, preview/waitlist language.
- [Entire Repositories overview](https://docs.entire.io/guides/repositories/overview) — regional read mirror and EntireDB model.
- [Store checkpoints with your code](https://docs.entire.io/guides/checkpoints/store-checkpoints-with-your-code) — branch and ref backends.
- [Ref-based checkpoint backend](https://github.com/entireio/cli/blob/main/docs/architecture/ref-checkpoint-backend.md) — one-ref-per-checkpoint, push/fetch, migration/coexistence.
- [Sessions and checkpoints](https://github.com/entireio/cli/blob/main/docs/architecture/sessions-and-checkpoints.md) — domain model and local/persistent state.
- [Security and privacy](https://github.com/entireio/cli/blob/main/docs/security-and-privacy.md) — transcript scope, redaction, PII, raw shadow-branch code risk.
- [Entire CLI v0.10.3](https://github.com/entireio/cli/releases/tag/v0.10.3) — subagent durability, zombie session recovery, hook metrics, redaction/security changes.
- [2026-07-08 launch announcement](https://entire.io/news/entire-launches-distributed-git-network-for-the-agent-era) — preview status and vendor-measured performance.

Vendor throughput numbers were treated as vendor claims. Push benchmarks described as Entire-native were not applied to GitHub mirrors.

### Graphify

- Live API returned 7,859 nodes, 11,892 edges, 530 communities, 99% extracted.
- Query located <code>EntireLedgerProvider</code>, capture, validation, control, review, and release nodes.
- Graphify was used only for relationship orientation. Targeted repository and Git reads supplied decision evidence.

### Access limits and exclusions

- Entire hosted organization/mirror inventory: unavailable without login; no authentication change attempted.
- Plane Initiatives: unavailable on current plan (HTTP 403).
- Buildkite recent build inventory: CLI query returned no rows; GitHub check receipt used instead.
- No application code, application configuration, Notion/GitHub state, integration, commit, push, login, or deployment was changed. Plane native dependency relations were updated on 2026-09-02; the external Codex project registry was corrected only for Tabellio's active backlog range.

## Follow-up execution record (2026-09-02)

**Fact.** The live Plane dependency graph now matches the direct blockers in the TAB-18 through TAB-29 descriptions: TAB-18 blocks TAB-19 and TAB-20; TAB-19 blocks TAB-20; TAB-20 blocks TAB-21; TAB-21 blocks TAB-22; TAB-22 blocks TAB-23; TAB-23 blocks TAB-24 and TAB-28; TAB-24 blocks TAB-25; TAB-25 blocks TAB-26 and TAB-27; TAB-26 blocks TAB-27; and TAB-27 plus TAB-28 block TAB-29. Every edge was read back from both directions.

**Fact.** The current Codex session is bound to <code>TAB-18</code>, repository <code>/Users/hudson/Documents/GitHub/Tabellio</code>, branch <code>main</code>, exact head <code>979a81d07994f8024c3b071581165de94685d2ca</code>. TAB-18 remains in Backlog; no lifecycle transition was performed.

**Fact.** The external registry at <code>/Users/hudson/Documents/GitHub/Tools/CodexSkills/workspace/plane-project-registry.md</code> previously described cancelled TAB-1 through TAB-17 as the active backlog. Its Tabellio mapping now names TAB-18 through TAB-29 as the active rebuild backlog and retains TAB-1 through TAB-17 as history.

**Operational note.** The installed Plane receipt command currently raises a <code>NameError</code> for an undefined <code>load_payload</code> symbol. The existing in-process receipt function was used instead, recording secret-free before/after fingerprints for the relation writes. The plugin defect remains a tooling follow-up; no plugin source was edited.

**Decision boundary.** TAB-18 still requires Hudson's data-boundary decision before TAB-19 can start. Entire hosting/mirror expansion remains deferred; only checkpoint durability and compatibility work remain justified next.
