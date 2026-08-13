# Trust: Discovery Brief

Status: Draft 0.1

## Purpose

Trust is intended to be an open-source, on-premises monitoring platform for
professional IT teams. It should help a team observe endpoints, servers,
network services, and important operational conditions without depending on a
public cloud service or an internet connection.

The project is inspired by practical experience deploying Nagios and its agents
across a local network. Trust is not yet defined as a Nagios replacement. That
claim must be earned through research, user validation, and a focused first
release.

## Primary User

The initial user is a professional system administrator or IT operations team
responsible for a private organizational network.

These users need to:

- Know whether endpoints, servers, and services are available.
- Detect resource changes before they become incidents.
- Monitor CPU, memory, disks, processes, services, and network ports.
- Watch operationally important files, including growing Outlook PST/OST files.
- Receive actionable alerts through internal infrastructure such as an SMTP
  server.
- Operate the platform when the environment has no internet access.
- Understand what changed, when it changed, and what requires attention.

## Problem Statement

Professional IT teams need dependable infrastructure visibility, but a useful
monitoring deployment can require separate tools for checks, agents, discovery,
metrics history, configuration, dashboards, and alert delivery. Older systems
can be powerful while remaining difficult to deploy and operate. Newer systems
may assume cloud-native infrastructure, external services, or a level of
complexity that is not appropriate for a private office network.

Trust will investigate whether these workflows can be combined into a secure,
coherent, local-first system without losing the extensibility that makes Nagios
valuable.

## Confirmed Product Constraints

- Professional IT teams are the first audience.
- The core product must run on premises.
- Routine monitoring must not require internet access.
- Data must remain under the deploying organization's control.
- Internal email delivery must be supported.
- Endpoint resource and file-growth monitoring are first-class use cases.
- The project is intended to become publicly accessible open-source software.

## Working Principles

- Local-first, not cloud-dependent.
- Secure by design, not "secure because local."
- Useful defaults with explicit administrative control.
- Actionable alerts over noisy alerts.
- Explainable state changes and retained operational history.
- Extensible checks with a stable interface.
- Deployment and upgrades suitable for restricted networks.
- Conservative collection of endpoint data.

## Candidate Capabilities

These are candidates for research, not committed scope:

- Windows and Linux endpoint agents.
- Agentless checks for common network services and ports.
- CPU, memory, disk, process, service, and host availability checks.
- File and directory size/growth rules.
- Windows Event Log monitoring.
- Network and service discovery with explicit authorization and scope controls.
- Threshold, duration, trend, and change-based alert rules.
- Dependency-aware alert suppression.
- Internal SMTP notifications.
- Dashboards, incident timelines, maintenance windows, and acknowledgements.
- Role-based access control and administrative audit history.
- Offline installation, update bundles, backup, and restore.
- Compatibility with existing Nagios-style checks where practical.

## Security Position

Operating locally reduces reliance on external services, but does not guarantee
security. A monitoring platform receives sensitive inventory and health data,
and its agents may require privileged access to selected operating-system
information.

The design must address:

- Mutual authentication between servers and agents.
- Encryption in transit and protection of stored secrets.
- Least-privilege agent permissions.
- Role-based access for administrators and operators.
- Audit logging for configuration and privileged actions.
- Data minimization, retention, and deletion.
- Signed builds and verifiable offline update packages.
- Network segmentation and explicit listening ports.
- Safe plugin execution and resource limits.
- Secure enrollment, rotation, and revocation of agent identities.

## Definition of "Offline"

For this project, offline should mean that core monitoring, administration,
storage, dashboards, and alert evaluation work with no public internet access.
An installation may still use explicitly configured internal services such as
DNS, NTP, SMTP, directory services, package mirrors, and certificate services.

The eventual documentation must distinguish:

- Fully isolated or air-gapped deployments.
- Restricted networks with approved internal services.
- Internet-connected deployments that disable external telemetry.

## Initial Success Scenario

An IT administrator installs Trust on an internal server, enrolls approved
Windows endpoints, discovers selected checks, and applies a policy that monitors
availability, CPU, memory, disk space, key services, and selected PST/OST file
growth. The team receives a useful message through its internal SMTP server when
a sustained problem occurs, can inspect the history, acknowledge it, and see
when the condition recovers. No public internet service is required.

## Scale Position

Trust must be designed for progressive growth, but scale claims will be tied to
published benchmarks and operating envelopes. The proposed model begins with a
compact deployment and allows collection, evaluation, and storage workloads to
be separated as demand grows. See `docs/SCALABILITY.md`.

## Questions To Resolve

- Which operating systems and versions must the first release support?
- Which benchmark tiers should define supported deployment sizes?
- Is Windows endpoint monitoring the first vertical slice?
- Should the first release monitor network devices through SNMP?
- Which identity systems must be supported initially?
- Which endpoint data is forbidden or sensitive in the target workplace?
- Should compatibility with Nagios plugins be an explicit requirement?
- What deployment format best supports restricted networks?
- What open-source license and governance model fit the project's goals?
- Which product name is final, and is it legally and technically available?

## Discovery Exit Criteria

Coding should begin only after we have:

- Compared the relevant monitoring products and documented real gaps.
- Interviewed or modeled representative professional workflows.
- Defined a narrow, testable minimum viable product.
- Written an initial architecture and threat model.
- Chosen an initial platform and technology stack with recorded rationale.
- Selected a license and checked project-name availability.
- Defined repository governance, contribution, and security-reporting policies.
