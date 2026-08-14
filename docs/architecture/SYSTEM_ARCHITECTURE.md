# System Architecture

Status: Proposed logical architecture

## Goals

- Support the MVP as one understandable on-premises installation.
- Keep security and operational responsibilities explicit.
- Allow later separation of collection, evaluation, and storage without changing
  the endpoint policy model.
- Remain operable without public internet access.
- Fail visibly and preserve evidence needed to understand an incident.

## System Context

```text
                         Private organizational network

  +------------------+       authenticated TLS       +---------------------+
  | Windows endpoint | ----------------------------> | Trust Server        |
  | Trust Agent      |       outbound routine flow   |                     |
  +------------------+                               | API and enrollment  |
                                                     | Policy and schedule |
  +------------------+                               | Ingest and history  |
  | Administrator    | -------- HTTPS -------------> | State and alerts    |
  | Browser          | <---------------------------- | Web application     |
  +------------------+                               +----------+----------+
                                                               |
                                                    authenticated SMTP
                                                               |
                                                     +---------v----------+
                                                     | Internal mail      |
                                                     | infrastructure     |
                                                     +--------------------+
```

All monitored-host data is untrusted even after agent authentication. Identity
answers which agent sent data; it does not prove that the endpoint is healthy or
uncompromised.

## Compact Deployment

The MVP uses a modular monolith: one versioned Trust Server release with clear
internal module boundaries and one durable database. It may use multiple local
processes where isolation or reliability requires them, but the administrator
installs, upgrades, backs up, and diagnoses one product.

This is a deployment decision, not permission to mix responsibilities. Module
contracts should be explicit and testable so selected modules can become
separate workers later.

### Control API

- Authenticates browser users and agents.
- Manages endpoints, policies, enrollment, revocation, settings, and roles.
- Provides a versioned API to the web application and agent.
- Applies authorization and records security-sensitive actions.

### Enrollment And Identity

- Creates short-lived, single-use enrollment tokens.
- Requires the agent to verify the server identity before sending the token.
- Issues a unique credential or certificate per approved agent.
- Supports rotation, expiration, and individual revocation.
- Never uses one organization-wide agent secret.

The detailed certificate ceremony remains a separate security ADR.

### Scheduler And Policy

- Stores desired monitoring policy centrally.
- Produces immutable, versioned policy snapshots for each agent.
- Bounds intervals, timeouts, and allowed built-in checks.
- Does not send raw commands or shell fragments.

### Ingest

- Validates identity, protocol version, schema, timestamp bounds, payload size,
  and policy/check identity.
- Assigns server receipt time and an idempotency key.
- Rejects unknown, revoked, oversized, malformed, or disallowed results.
- Applies quotas and backpressure without silently accepting lost data.

### State Engine

- Converts ordered samples into OK, WARNING, CRITICAL, UNKNOWN, or stale state.
- Owns sustained problem and recovery durations.
- Produces explicit, idempotent state-transition events.
- Distinguishes endpoint absence, missing check data, collection error, and a
  measured unhealthy value.

### Alert Manager

- Opens one alert per active rule/resource condition.
- Tracks acknowledgement, recovery, and notification delivery.
- Prevents duplicate notifications during retries and reconnects.
- Leaves routing, escalation, dependency suppression, and maintenance behavior
  narrow in the MVP and extensible later.

### Notification Worker

- Consumes durable notification jobs.
- Sends through configured internal SMTP.
- Retries transient failures with bounded exponential backoff and jitter.
- Records attempts and terminal failures without blocking state evaluation.

### Storage

The logical model separates:

- Configuration and identity.
- Raw or summarized samples.
- Current state.
- Immutable state transitions.
- Alerts and acknowledgements.
- Notification delivery attempts.
- Administrative audit events.

The database technology is deliberately undecided. Retention and aggregation
must be designed before choosing a time-series extension or separate store.

### Web Application

- Uses the versioned Control API rather than direct database access.
- Treats all endpoint-supplied strings as hostile display data.
- Optimizes the first views for active problems, endpoint health, evidence, and
  repeated operator actions.
- Does not conceal delayed or stale data behind a last-known healthy state.

## Windows Agent

The agent is a small Windows service with four internal responsibilities:

### Collector

Collects only built-in, schema-defined signals permitted by the assigned policy.
The MVP exposes no remotely parameterized executable or scripting interface.

### Policy Cache

Stores the last authenticated, versioned policy and its validity period. A
policy outside its validity rules stops or restricts collection according to a
documented fail-safe behavior.

### Local Buffer

Persists a bounded queue so a short server interruption does not immediately
lose samples. The queue has explicit byte/time limits and reports overflow. It
is not a permanent monitoring database.

### Transport And Identity

Maintains the agent identity, verifies the server, batches samples, retries with
backoff, and prevents concurrent duplicate submission. Routine operation is
agent-initiated so endpoints do not require an inbound monitoring port.

The service runs with the minimum Windows rights required for the enabled
collectors. Checks needing additional rights must document and isolate them;
the default agent must not assume LocalSystem is acceptable.

## Core Data Flow

1. Agent loads an authenticated policy snapshot.
2. Built-in collectors sample due signals.
3. Agent creates stable sample identifiers and appends them to its bounded queue.
4. Transport sends a bounded batch to the ingest API.
5. Server validates and durably accepts or explicitly rejects each batch.
6. Agent removes only acknowledged samples from its queue.
7. State engine evaluates accepted samples in resource order.
8. A change produces one durable state-transition event.
9. Alert manager reconciles the transition with the active alert.
10. Notification worker delivers a durable SMTP job and records its result.

## Failure Semantics

| Failure | Required behavior |
|---|---|
| Agent cannot reach server | Buffer within limits, retry with jitter, expose queue health after reconnect |
| Agent buffer fills | Apply documented eviction policy and report exact loss; never silently discard |
| Duplicate batch | Acknowledge idempotently without duplicate state transitions |
| Samples arrive late/out of order | Preserve receipt/source times and apply explicit ordering window |
| Agent credential revoked | Reject new data and display endpoint as revoked, not merely offline |
| Database unavailable | Stop durable acknowledgement, apply backpressure, and surface server health |
| State worker restarts | Reconcile from durable input/events without duplicate alerts |
| SMTP unavailable | Preserve monitoring state, retry boundedly, and show notification failure |
| Clock skew detected | Mark affected evidence and avoid misleading duration evaluation |
| Unsupported protocol | Reject with actionable version information; never guess schema meaning |

## Growth Path

### Stage 1: Compact

All server modules and storage are deployed together.

### Stage 2: Worker Separation

Notification, state evaluation, and agentless check execution may move behind a
durable work interface. Horizontal workers require partition keys and
idempotency already present in Stage 1.

### Stage 3: Site Collectors

Site-local collectors accept agent traffic, buffer bounded data, and forward to
the central ingest tier. They do not become independent sources of policy,
identity, state truth, or alerts.

### Stage 4: Resilient Services

Control, ingest, evaluation, and storage may be replicated with documented
consistency and failover guarantees. This stage is justified by measured demand,
not included by default.

## Trust Boundaries

- Browser to control API.
- Agent to enrollment service.
- Enrolled agent to ingest API.
- Server modules to durable storage and secret material.
- Notification worker to internal SMTP.
- Installation/update artifacts to operating-system trust stores.
- Future collector/plugin processes to the host operating system.

Each boundary requires authentication, authorization, input limits, auditability,
and a documented failure policy appropriate to its risk.

## Decisions Still Open

- Programming languages and frameworks.
- Server and agent process layout.
- API transport and serialization.
- Certificate authority, enrollment, rotation, and recovery procedures.
- Database, time-series schema, and retention tiers.
- Windows service identity and per-collector permissions.
- Server packaging and supported Linux distributions.
- Browser authentication and recovery controls.
- Update signing and offline distribution.

## Research Basis

- [Zabbix architecture](https://www.zabbix.com/documentation/current/en/manual/introduction/overview)
- [Zabbix distributed monitoring](https://www.zabbix.com/documentation/current/en/manual/distributed_monitoring)
- [Checkmk monitoring agents and registration](https://docs.checkmk.com/latest/en/wato_monitoringagents.html)
- [OpenTelemetry Collector deployment patterns](https://opentelemetry.io/docs/collector/deploy/)
- [Prometheus remote-write queues and backpressure](https://prometheus.io/docs/practices/remote_write/)
- [Microsoft service logon guidance](https://learn.microsoft.com/en-us/windows/win32/ad/guidelines-for-selecting-a-service-logon-account)
