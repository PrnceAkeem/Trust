# Monitoring Landscape: Research Notebook

Status: Working notes
Last reviewed: 2026-08-10

This notebook records evidence and separates sourced observations from product
decisions. Inclusion here is not an endorsement or a final design choice.

## Nagios Core

Nagios Core schedules active checks, accepts passive results, tracks host and
service states, and performs actions such as notifications and event handlers.
The check itself is normally an external executable or script. A plugin reports
text and exits with a conventional status code: 0 OK, 1 WARNING, 2 CRITICAL, or
3 UNKNOWN. This interface is simple and language-independent.

The same abstraction has a limitation: Core understands state, but not the
meaning or schema of the thing a plugin measured. Rich metrics, discovery,
configuration management, and visualization therefore require conventions or
additional components.

Nagios Core is offered under GPL v2. Nagios XI is a separate commercial product
that adds capabilities including graphical configuration, reporting,
dashboards, a database, and APIs. We must avoid describing commercial XI
features as limitations of the entire Nagios product family.

Sources:

- [Nagios plugin architecture](https://assets.nagios.com/downloads/nagioscore/docs/nagioscore/4/en/plugins.html)
- [Nagios plugin API](https://assets.nagios.com/downloads/nagioscore/docs/nagioscore/4/en/pluginapi.html)
- [Active checks](https://assets.nagios.com/downloads/nagioscore/docs/nagioscore/4/en/activechecks.html)
- [Passive checks](https://assets.nagios.com/downloads/nagioscore/docs/nagioscore/4/en/passivechecks.html)
- [Nagios Core product and license overview](https://www.nagios.org/projects/nagios-core/)
- [Nagios Core and XI comparison](https://www.nagios.org/downloads/nagios-core-/)

## Checkmk

Checkmk models hosts and services and uses an endpoint agent for local system
information. Its setup flow can discover services from agent data and present
them for an administrator to accept or configure. This makes service discovery
and lifecycle management an important comparison point for Trust.

Source:

- [Checkmk monitoring setup and service discovery](https://docs.checkmk.com/latest/en/intro_setup_monitor.html)

## Prometheus

Prometheus emphasizes a dimensional time-series model, service discovery, and a
large cloud-native ecosystem. Its operational guidance recommends alerting on
actionable symptoms, allowing tolerance for brief disturbances, and linking
alerts to diagnostic views. Those principles are relevant even if Trust uses a
different collection or storage model.

Sources:

- [Prometheus FAQ and system overview](https://prometheus.io/docs/introduction/faq/)
- [Prometheus alerting practices](https://prometheus.io/docs/practices/alerting/)
- [Prometheus file-based service discovery](https://prometheus.io/docs/guides/file-sd/)

## LibreNMS

LibreNMS provides network-oriented discovery and polling, including SNMP-based
device discovery and distributed polling. It is a useful comparison for network
equipment, topology, and discovery workflows rather than only workstation
agents.

Sources:

- [LibreNMS auto-discovery](https://docs.librenms.org/Extensions/Auto-Discovery/)
- [LibreNMS dispatcher and distributed polling](https://docs.librenms.org/Extensions/Dispatcher-Service/)

## Windows Endpoint Signals

Windows Performance Counters expose a consistent interface for processor,
memory, disk, process, and other system data. Microsoft notes that they are
intended for administrative and diagnostic collection rather than high-frequency
profiling. Collection intervals and performance cost therefore need deliberate
testing.

Modern Windows versions also expose Windows Event Log APIs for querying the
centralized operating-system and application event channels.

Microsoft documents configurable size limits for Outlook PST and OST files. At
warning or maximum conditions, Outlook operations such as sending, copying,
moving, or auto-archiving can be affected. Trust should initially monitor file
metadata and growth, not inspect message content. Workplace policy and privacy
requirements must be established before implementing this check.

Sources:

- [Windows Performance Counters](https://learn.microsoft.com/en-us/windows/win32/perfctrs/performance-counters-portal)
- [Windows performance troubleshooting and counter examples](https://learn.microsoft.com/en-us/troubleshoot/windows-server/performance/troubleshoot-performance-problems-in-windows)
- [Windows Event Log](https://learn.microsoft.com/en-us/windows/win32/eventlog/event-logging)
- [Outlook PST and OST size-limit behavior](https://learn.microsoft.com/en-us/microsoft-365-apps/outlook/data-files/configure-size-limit-outlook-data-files)

## Early Opportunity Areas

These are hypotheses to validate, not conclusions:

- A guided on-premises deployment that remains usable without internet access.
- First-class Windows workstation monitoring alongside servers and services.
- Safe file-growth monitoring for operational files without collecting content.
- One integrated workflow from enrollment and discovery to alert and recovery.
- Clear explanations of why a state changed and which evidence caused an alert.
- Strong defaults for sustained thresholds, dependencies, and noise reduction.
- A modern extension interface plus a compatibility path for existing checks.
- Documented offline updates, backup, restore, and disaster recovery.

## Research Backlog

- Zabbix architecture, agent security, discovery, templates, and licensing.
- Icinga distributed architecture and Nagios compatibility.
- Checkmk editions, licensing, rule model, and agent security.
- OpenNMS and enterprise network-management workflows.
- Netdata and real-time troubleshooting workflows.
- Uptime Kuma and the expectations set by simple setup experiences.
- OpenTelemetry applicability to endpoint and service signals.
- Windows agent APIs, service accounts, permissions, and deployment mechanisms.
- Linux agent collection mechanisms and privilege boundaries.
- SNMP v3, network discovery safety, and topology modeling.
- Alert routing, escalation, acknowledgement, maintenance, and dependency models.
- Offline packaging, signing, software bills of materials, and update verification.
- Data storage options, retention, cardinality, and capacity estimates.
- Threat model and secure plugin sandboxing.
- License compatibility if Nagios-style plugins or other components are reused.
