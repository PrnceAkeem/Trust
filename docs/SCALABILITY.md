# Scalability Strategy

Status: Proposed direction

## Principle

Trust should grow without requiring a redesign of its public model, but no
system scales "with no issue." Capacity depends on check frequency, metric
volume, retention, endpoint latency, hardware, and failure conditions. Trust
will publish tested operating envelopes instead of an unbounded scale claim.

## Progressive Deployment Model

### Tier A: Compact

A single deployable installation provides the control plane, scheduler,
collection, evaluation, storage, and UI. This tier optimizes installation,
backup, and operation for a modest environment.

### Tier B: Separated Workloads

Stateless or independently scalable workers perform checks and evaluation while
the control plane and durable storage remain centralized. Queues and explicit
backpressure prevent slow endpoints from blocking unrelated work.

### Tier C: Distributed Sites

Authenticated collectors operate near endpoints in segmented networks or remote
sites. They buffer bounded data during central-server interruptions and resume
without creating duplicate state transitions or alert storms.

### Tier D: Resilient Control Plane

The system may support redundant control-plane and storage components where the
operational cost is justified. High availability is a later capability and must
not complicate the first useful deployment unnecessarily.

## Architectural Requirements

- Stable identities for tenants, sites, hosts, services, checks, and events.
- Idempotent result ingestion and explicit event ordering rules.
- Work partitioning that does not leak into user-facing configuration.
- Bounded queues, timeouts, concurrency, retries, and resource use.
- Per-site and per-agent authentication and revocation.
- Clear behavior during clock drift, partitions, duplicates, and delayed data.
- Independent retention for raw samples, state transitions, alerts, and audits.
- Observable health and capacity of Trust itself.
- Backward-compatible protocols with version negotiation.

## Measurement Model

Benchmarks must state:

- Host and service count.
- Checks and samples per second.
- Check duration and failure distribution.
- Collection interval and timeout.
- Metric cardinality and payload size.
- Retention and query window.
- Alert-rule count and evaluation interval.
- Hardware, operating system, and storage configuration.
- Steady-state resource use and recovery after interruption.
- p50, p95, and p99 ingest, evaluation, and UI query latency.

Initial tier targets will be selected after the first vertical slice produces
real measurements. Candidate validation points may include tens, hundreds, and
thousands of endpoints, but they are not promises until reproduced and
published.

## Scale Failure Policy

Trust must degrade visibly and predictably. When capacity is exceeded it should
report delayed or dropped work, protect durable state, prioritize critical
operations, and avoid presenting stale data as current. Silent data loss is not
acceptable.
