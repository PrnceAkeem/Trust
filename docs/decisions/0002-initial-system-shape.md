# ADR-0002: Use A Modular Server With An Outbound Windows Agent

- Status: Proposed
- Date: 2026-08-13
- Owners: Project maintainers

## Context

Trust must prove a useful Windows endpoint monitoring workflow in a restricted
network while retaining a credible growth path. Beginning with independent
microservices, a distributed queue, and multiple databases would increase the
installation and failure surface before scale requirements are measured.

Pulling routine data from every endpoint requires inbound endpoint ports and
firewall rules. Allowing the server to send arbitrary commands creates an
especially dangerous control path. An agent-initiated flow works better for
segmented networks and permits a small, allow-listed collection surface.

Mature monitoring and telemetry systems commonly distinguish host agents,
central control/evaluation, and optional site-local collection. Their experience
also shows that buffering, backpressure, idempotency, and explicit registration
must be designed before distribution rather than added after data loss occurs.

## Decision

For the MVP, Trust will use:

- A Windows-first endpoint agent that initiates routine communication.
- Explicit enrollment and a unique revocable identity for every agent.
- Authenticated and encrypted transport after enrollment.
- Built-in, schema-defined collectors rather than remote arbitrary execution.
- A modular Trust Server deployed as one product with one durable database.
- Server-owned policy, state evaluation, alert lifecycle, audit, and notification.
- Bounded persistent buffering and idempotent sample ingestion.
- Internal module boundaries that permit later worker and collector separation.

The exact implementation stack and enrollment protocol are separate decisions.

## Alternatives Considered

### Agentless-Only Monitoring

Rejected for the first slice because workstation metrics and selected file
metadata would require remote credentials, firewall exposure, and platform-
specific administration. Agentless service checks remain a later capability.

### Server-Pull Agent

Deferred because it opens an endpoint listening surface and complicates
segmented networks. A future authenticated pull mode may be evaluated for cases
where administrators require server-controlled timing.

### Microservices From The First Release

Rejected because the operational cost is not justified by measured scale. Clear
module contracts and idempotent work provide a safer extraction path later.

### Reuse OpenTelemetry Collector As The Trust Agent

Not selected as the product agent. OpenTelemetry remains relevant for protocol
and operational patterns, but Trust requires endpoint enrollment, policy,
service/file checks, state semantics, and offline product lifecycle behavior
that are specific to Trust. Interoperability may be evaluated separately.

## Consequences

- A useful lab deployment remains understandable and supportable.
- Routine endpoint firewall configuration is simpler.
- Temporary disconnection can be tolerated within explicit buffer limits.
- Central alert truth is consistent across future collectors.
- Agent compromise still permits false data from that endpoint, so the server
  must treat results as untrusted evidence.
- Central ingest capacity and storage are initial scaling constraints.
- High availability and disconnected-site alerting are deferred.
- Agentless checks and plugins require later security designs.

## Security And Privacy

- Enrollment tokens are short-lived and single-use.
- Agent identities are individual and revocable.
- Server identity verification occurs before token submission.
- No routine unauthenticated or plaintext agent mode is supported.
- No arbitrary server-to-agent command execution is included.
- File checks collect metadata only and are opt-in by selected path/policy.
- The agent runs with the minimum privileges supported by its collectors.

## Validation

- Threat-model review of enrollment, transport, buffering, and spoofed data.
- MVP acceptance test with healthy, unhealthy, and disconnected endpoints.
- Duplicate, late, malformed, oversized, and revoked-agent result tests.
- One-hour idle resource measurement on the reference Windows endpoint.
- Restore test proving state, identity, and audit data recover coherently.
- Architecture review before changing this ADR to `Accepted`.
