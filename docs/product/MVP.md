# Minimum Viable Product

Status: Proposed
Target milestone: 0.1 lab preview

## Purpose

The Trust MVP must prove one complete professional monitoring workflow without
pretending to be a production-ready Nagios replacement. It will show that an IT
administrator can securely enroll a Windows endpoint, apply a monitoring policy,
detect a sustained problem, understand the evidence, receive an internal email,
acknowledge the incident, and observe recovery without public internet access.

## Primary Operator

A system administrator responsible for Windows workstations and servers on a
private organizational network.

The MVP is designed for a lab that models a professional environment. It must
not be deployed at a workplace until security, privacy, installation, rollback,
and support requirements have been reviewed by that organization.

## End-To-End Scenario

1. An administrator installs Trust Server on an internal Linux host.
2. The administrator creates a short-lived, single-use enrollment token.
3. Trust Agent is installed as a Windows service on an approved test endpoint.
4. The agent verifies the server identity and enrolls using the token.
5. The server approves or rejects the endpoint and issues a unique identity.
6. The administrator applies the built-in Windows baseline policy.
7. The agent reports health samples over an authenticated, encrypted connection.
8. A condition remains outside its threshold for the configured duration.
9. The server opens an alert with the samples and rule that caused it.
10. An internal SMTP server delivers one problem notification.
11. An operator acknowledges the alert with a note.
12. When the condition remains healthy for its recovery duration, the server
    closes the alert and sends one recovery notification.

## Included Capabilities

### Server

- Local administrator bootstrap with forced password creation.
- Authenticated web session and two initial roles: administrator and operator.
- Single-use, expiring endpoint enrollment tokens.
- Endpoint inventory with identity, operating system, agent version, first seen,
  last seen, and connection state.
- Built-in policies assigned to one or more endpoints.
- Durable sample, state-transition, alert, acknowledgement, and audit history.
- Sustained threshold evaluation with separate problem and recovery durations.
- Internal SMTP configuration, test message, problem message, and recovery
  message.
- A focused UI for endpoints, current problems, alert detail, and settings.
- Backup and restore documentation for all durable state.
- No required telemetry, CDN, hosted identity, or public internet service.

### Windows Agent

- Installation and removal as a Windows service.
- Explicit enrollment with server certificate verification.
- Outbound-only routine communication from agent to server.
- Unique revocable agent identity after enrollment.
- Bounded local buffering during temporary server unavailability.
- Heartbeat and agent self-health reporting.
- CPU utilization.
- Available and used physical memory.
- Per-volume disk capacity and free space.
- Selected Windows service running state.
- Selected file metadata: existence, size, modification time, and growth rate.
- No collection of file content, email content, usernames, or browsing activity.

### Initial Alert Rules

- Agent heartbeat missing for a sustained period.
- CPU utilization above a configured percentage for a sustained period.
- Available memory below a configured amount or percentage for a sustained
  period.
- Disk free space below a configured amount or percentage.
- Selected Windows service not running.
- Selected file exceeds a size threshold or configured growth rate.

Threshold values are policy settings, not universal claims about system health.

## Explicitly Excluded

- Arbitrary commands or scripts sent from server to agent.
- Remote repair, process termination, service restart, patching, or remediation.
- Reading PST/OST contents or other user documents.
- Linux agents, macOS agents, SNMP, WMI-over-network, or network discovery.
- Nagios plugin compatibility.
- SMS, chat, public email, or cloud notification integrations.
- SSO, Active Directory integration, or multi-organization tenancy.
- Custom dashboards or a general-purpose query language.
- Distributed collectors, horizontal scaling, or high availability.
- Mobile applications or a public status page.

These may be evaluated later; exclusion protects the first milestone from unsafe
or untestable scope.

## Quality Requirements

- **Secure defaults:** no default password, plaintext agent transport, implicit
  enrollment, or unauthenticated listening endpoint.
- **Resource restraint:** the idle agent target is under 1% average CPU and 100
  MiB resident memory on the documented reference endpoint, measured over one
  hour. These are initial test targets, not guarantees.
- **Predictable network use:** sample intervals are configurable within bounded
  limits, payloads are batched, and retries use exponential backoff with jitter.
- **Visible degradation:** delayed collection, a full local buffer, clock skew,
  rejected samples, and SMTP failure are shown as system health conditions.
- **Auditability:** enrollment, revocation, policy changes, acknowledgements, and
  security-sensitive settings changes are attributable and timestamped.
- **Accessibility:** core operator workflows are keyboard usable and do not rely
  on color alone.
- **Offline operation:** installation artifacts and documentation can be moved
  into a restricted lab and used without public network requests.

## MVP Acceptance Test

The milestone passes when a fresh isolated lab can reproduce the end-to-end
scenario with one server and three Windows test endpoints, including:

- A healthy endpoint.
- An endpoint that crosses and recovers from a resource threshold.
- An endpoint that loses connectivity long enough to alert and later reconnects.

The test must show no duplicate alert during retry/reconnect, no loss of audit
history, and no public DNS or HTTP dependency. Installation, backup, restore,
uninstallation, and identity revocation must also be demonstrated.

## Decisions Required Before Implementation

- Supported Windows client and server versions.
- Server operating systems and packaging format.
- Implementation languages and UI framework.
- Durable database and time-series representation.
- Agent/server protocol and certificate lifecycle.
- Password hashing and local secret storage.
- Exact retention defaults and resource reference hardware.
