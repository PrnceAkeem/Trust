# Competitor Landscape Research — Trust (Local-First Infrastructure Monitoring)

## Introduction

The open-source infrastructure monitoring space is crowded but fragmented. Every mature tool here grew out of a specific era and use case — Icinga2 out of the Nagios lineage for enterprise IT ops, Checkmk as a Nagios-core wrapper for IT service shops, Zabbix as an all-in-one enterprise NOC platform, LibreNMS as a network-engineer-focused SNMP tool, Prometheus+Grafana as a cloud-native metrics stack for Kubernetes teams, and Uptime Kuma as a lightweight self-hosted uptime checker for hobbyists. Trust targets professional IT teams that want strong discovery, endpoint depth, security, and operational workflows without stitching together several products and data models. This document surveys six representative tools/stacks against that bar, closing with a comparison table and a gap analysis of the whitespace Trust can target.

## Icinga2

**Architecture.** Icinga2 is a fork of Nagios and, like its parent, is agentless-by-default: the master executes checks against remote hosts over the network (ICMP, SNMP, `check_by_ssh`, NRPE-style plugins) using the Nagios plugin ecosystem. An optional Icinga Agent can run on endpoints for local, low-latency checks in a master/satellite/agent hierarchy, so it supports both agent and agentless models depending on config ([Icinga docs — Agent-Based Monitoring](https://icinga.com/docs/icinga-2/2.10/doc/07-agent-based-monitoring/); [Icinga blog — agentless SSH monitoring](https://icinga.com/blog/how-to-do-agentless-monitoring-with-check_by_ssh/)). There is no built-in network discovery in core Icinga2 — host/service definitions are check-plugin-driven configuration, not auto-detected.

**Deployment.** Native packages (Debian/RHEL) plus official Docker images; no single-binary option. Initial setup requires configuring the core, Icinga Web 2 (a separate PHP app), and usually the Icinga Director module to get a usable UI-driven workflow — a multi-component stack, not a single install.

**Target User.** Enterprise/mid-market IT ops teams who outgrew plain Nagios but want to stay in the Nagios-plugin ecosystem with better performance and a REST API.

**Strengths.** Very mature core, strong config language and REST API, Icinga Director gives point-and-click config management with audit logging and rollback ([Icinga Director docs](https://icinga.com/docs/icinga-director/latest/doc/01-Introduction/)), scales well, large plugin ecosystem inherited from Nagios.

**Weaknesses.** No native discovery — a commonly requested gap ([Network World review](https://www.networkworld.com/article/965748/icinga-review-enterprise-grade-open-source-network-monitoring-that-scales.html)); UI (Icinga Web 2) is functional but dated; a modern experience requires bolting on Director and Icinga DB, effectively 3+ components; still a Nagios-style config-first tool under the hood, so "approachable" is not met out of the box.

**License.** GPLv2+ through v2.15, GPLv3+ from v2.16.0 onward (forced by a new Apache-2.0-licensed dependency); fully open-source, no open-core split ([Icinga on Wikipedia](https://en.wikipedia.org/wiki/Icinga); [Icinga2 GitHub](https://github.com/Icinga/icinga2)).

## Checkmk

**Architecture.** Hybrid: a lightweight agent (Linux/Windows/etc.) pushes data to the Checkmk site on request, but Checkmk also supports fully agentless checks (SNMP, APIs, "special agents" for cloud/piggyback data). Its signature feature is **Service Discovery**: check plugins probe a host, propose a list of detected services, and an admin accepts/rejects them in the UI rather than hand-writing config ([Checkmk service discovery docs](https://docs.checkmk.com/latest/en/wato_services.html); [ramsdata.com.pl explainer](https://ramsdata.com.pl/en/automatic-service-detection-in-checkmk-how-it-works-and-what-it-gives-administrators/)). Dynamic Host Configuration (DCD) can auto-register hosts from cloud/container inventories.

**Deployment.** Ships as an "appliance-style" all-in-one site (Apache + Nagios-derived core or the Checkmk Micro Core in Enterprise + RRD/DB + web UI) via native packages or a single Docker container, which is one of the easier "get a working monitoring server in one command" experiences in this list ([Checkmk Docker guide](https://oneuptime.com/blog/post/2026-02-08-how-to-run-checkmk-in-docker-for-infrastructure-monitoring/view)).

**Target User.** IT ops/MSPs monitoring heterogeneous server, network, and cloud estates who want auto-discovery and fast time-to-value; scales from small shops to large enterprise ([Checkmk vs Zabbix comparison](https://selfhosting.sh/compare/checkmk-vs-zabbix/)).

**Strengths.** Best-in-class auto-discovery UX among the traditional tools, large (2,000+) integration/check catalog, single coherent product (discovery + monitoring + alerting + UI in one site) rather than a bolted-together stack, good balance of ease-of-use and depth ([Checkmk homepage](https://checkmk.com/)).

**Weaknesses.** Raw (free) edition runs on the classic Nagios core with real performance ceilings — the vendor's own comparison shows CPU usage dropping from ~80% to <20% after upgrading to the proprietary Enterprise Micro Core ([Checkmk Raw vs Enterprise](https://checkmk.com/blog/raw-vs-enterprise)); HA, SLA reporting, tenant isolation, and the fast core are all Enterprise-only, so the genuinely "modern" version is commercial; UI, while better than Icinga/Zabbix, still reads as an IT-ops tool, not consumer-grade.

**License.** Open-core: Checkmk Raw Edition is GPLv2 open source (Nagios-core based); Checkmk Enterprise/Cloud/MSP editions are commercial, subscription-priced per-service (~€175/mo for 3,000 services) ([Checkmk pricing](https://www.siriusopensource.com/en-us/blog/how-much-does-checkmk-cost)).

## Zabbix

**Architecture.** Classic three-tier: Zabbix Server (central), optional Zabbix Proxy (for distributed/segmented networks, buffers data locally), and Zabbix Agent (push-style active checks or server-polled passive checks) — plus full agentless support via SNMP, IPMI, JMX, and generic scripts. Network Discovery scans IP ranges and auto-creates hosts/interfaces based on which protocols respond (agent, SNMP, ICMP, etc.) ([Zabbix Network Discovery docs](https://www.zabbix.com/documentation/8.0/en/manual/discovery/network_discovery); [Zabbix Proxy docs](https://www.zabbix.com/documentation/8.0/en/manual/concepts/proxy)).

**Deployment.** Native packages, official Docker images/Compose files, and appliance VM images; no single-binary. Setup involves standing up a database (MySQL/PostgreSQL/TimescaleDB), the server, and the PHP frontend as separate pieces.

**Target User.** Large enterprises and MSPs with dedicated SRE/NOC staff who need unlimited-scale, fully-featured monitoring at zero license cost ([Checkmk vs Zabbix comparison](https://selfhosting.sh/compare/checkmk-vs-zabbix/)).

**Strengths.** Everything (distributed proxies, discovery, auto-registration, event correlation, HA) is free with no feature gating — "true open source" is a marketing point for Zabbix ([Zabbix True Open Source](https://www.zabbix.com/true_open_source)); extremely capable templating and trigger/correlation engine; battle-tested at large scale.

**Weaknesses.** Consistently cited for a steep learning curve, complex initial configuration, and heavy resource use at scale ([Lemon.io — Zabbix disadvantages](https://lemon.io/answers/zabbix/what-are-the-main-disadvantages-of-zabbix/); [OpenMSP Zabbix review](https://www.openmsp.ai/blog/zabbix-review)); no built-in multi-tenancy/client portal; entirely self-managed (upgrades, DB scaling, patching); UI is dense and enterprise-oriented rather than approachable for a home-network user.

**License.** GPLv2 through 6.4; AGPLv3 from Zabbix 7.0 onward, driven partly by wanting to prevent unauthorized commercial forks while remaining OSI-approved open source ([Zabbix AGPLv3 announcement](https://blog.zabbix.com/striking-the-right-balance-zabbix-7-0-to-be-released-under-agplv3-license/27596/)).

## LibreNMS

**Architecture.** Fundamentally agentless and SNMP-centric: it auto-discovers network topology via CDP/FDP/LLDP/OSPF/BGP/SNMP/ARP and polls devices by SNMP (v1/v2c/v3) on a schedule (full discovery every 6 hours, new devices picked up within 5 minutes) ([LibreNMS Auto-Discovery docs](https://docs.librenms.org/Extensions/Auto-Discovery/)). An optional lightweight Unix agent exists for host-level metrics beyond SNMP, but it's disabled by default and has no discovery role of its own ([LibreNMS Discovery Support](https://docs.librenms.org/Support/Discovery%20Support/)).

**Deployment.** Traditional LAMP-style install (PHP/MySQL/RRDtool) or official Docker Compose setup, which is the recommended path for avoiding dependency headaches ([LibreNMS Docker](https://oneuptime.com/blog/post/2026-02-08-how-to-run-librenms-in-docker-for-network-monitoring/view)).

**Target User.** Network engineers/admins who need serious SNMP-based device monitoring (switches, routers, APs, UPS, printers) with a large out-of-the-box device library (10,000+ device definitions) ([LibreNMS homepage](https://www.librenms.org/)).

**Strengths.** Genuinely excellent, low-friction network auto-discovery — this is the closest any tool here comes to "point at a subnet and it figures out the topology"; huge community device library; free API for automation.

**Weaknesses.** SNMP-first design means it's weak at general server/application monitoring and cloud metrics compared to Zabbix/Checkmk/Prometheus; UI is community-built PHP and feels dated; alerting is present but less sophisticated than Zabbix's correlation engine; little host-based agent story for rich application-level metrics.

**License.** GPLv3 (community fork of the last GPL-licensed Observium release), with a linking exception for bundled third-party libraries ([LibreNMS GitHub](https://github.com/librenms/librenms)). Fully open, no commercial edition.

## Prometheus + Grafana (combined stack)

**Architecture.** Pure pull model: Prometheus scrapes `/metrics` HTTP endpoints (native app instrumentation or exporters like node_exporter/snmp_exporter) on a fixed interval and stores time series locally; Grafana is a separate visualization layer that queries Prometheus (and other data sources) via PromQL and renders dashboards ([Grafana + Prometheus getting-started docs](https://grafana.com/docs/grafana/latest/fundamentals/getting-started/first-dashboards/get-started-grafana-prometheus/)). There is no native device/service discovery for a non-Kubernetes network — target discovery in Prometheus is designed around Kubernetes/cloud service-discovery integrations, and on a flat home/office LAN, targets are essentially hand-listed or discovered via a separately-run tool.

**Deployment.** Each component is its own binary/container; Docker Compose is the common path to stand up Prometheus + Grafana (+ Alertmanager, + exporters) together ([Building a monitoring stack with Docker](https://dev.to/durrello/building-a-complete-monitoring-stack-with-prometheus-and-grafana-using-docker-36h8)). Nothing about it is "single binary" — it is explicitly a stack of independently-versioned services that must be wired together, and alerting/notification routing lives in yet another component (Alertmanager, or Grafana's own newer alerting engine, which overlaps and sometimes conflicts with Alertmanager).

**Target User.** Cloud-native/DevOps engineering teams instrumenting their own applications and Kubernetes clusters — metrics-first observability, not classical "is this host up / is this switch port down" IT monitoring.

**Strengths.** Massive exporter ecosystem, powerful query language, best-in-class dashboarding via Grafana, huge community and integration surface, both components are best-of-breed individually.

**Weaknesses.** The textbook example of "bolting together separate tools without a coherent product" — metrics (Prometheus), dashboards (Grafana), alerting (Alertmanager or Grafana Alerting), and typically logs/traces (Loki/Tempo) as separate systems with separate config languages; a 2025 observability survey found 39% of teams cite this stack's complexity/operational overhead as their #1 obstacle, juggling ~8 tools on average ([tech-insider.org](https://tech-insider.org/prometheus-vs-grafana-2026/)); no meaningful auto-discovery for non-cloud environments; steep setup for a home-lab user who just wants to know when a device goes down; alerting logic is comparatively primitive next to Zabbix's correlation engine ([Alertmanager vs Grafana Alerting](https://alexandre-vazquez.com/alertmanager-vs-grafana-alerting/)).

**License.** Prometheus: Apache License 2.0, fully open, CNCF-governed. Grafana: was Apache 2.0, relicensed core (Grafana, Loki, Tempo) to AGPLv3 in April 2021 specifically to deter cloud-vendor "strip-mining" ([Grafana relicensing announcement](https://grafana.com/blog/grafana-loki-tempo-relicensing-to-agplv3/)); Grafana Labs also sells commercial Grafana Enterprise/Cloud on top. So the "stack" mixes a permissive license (Prometheus) with a copyleft, commercially-backed one (Grafana).

## Uptime Kuma

**Architecture.** Simple push-free, pull/poll model: a single Node.js application actively probes configured targets (HTTP(S), TCP port, ping, DNS, Docker container status, database connections, Steam game servers, etc.) on a schedule and stores results in an embedded SQLite database ([Uptime Kuma GitHub](https://github.com/louislam/uptime-kuma)). No agents, no SNMP, and — critically — **no discovery of any kind**; every monitor is added manually.

**Deployment.** The easiest in this entire list: one Docker command with a persistent volume gets a fully working instance up; it runs comfortably on very modest hardware (2GB RAM handles 50+ monitors at 60s intervals with idle CPU) ([Pi My Life Up install guide](https://pimylifeup.com/uptime-kuma-docker/); [Uptime Kuma official install docs](https://uptimekuma.co/install-uptime-kuma-docker/)).

**Target User.** Self-hosters/homelab users who want a friendly uptime/status-page dashboard for a modest number of services — the direct spiritual ancestor of what a lot of home-network users reach for instead of Nagios today.

**Strengths.** By far the best UI/UX and lowest setup friction of any tool surveyed; genuinely "one binary, one command, works in minutes"; built-in status pages; broad notification integrations (Slack, Telegram, email, etc.) out of the box; this is the closest existing precedent for the *feel* Trust should aim for.

**Weaknesses.** No SNMP support despite years of open feature requests ([issue #4427](https://github.com/louislam/uptime-kuma/issues/4427); [issue #5899](https://github.com/louislam/uptime-kuma/issues/5899)), so it cannot do real network-device monitoring; no discovery mechanism, so it doesn't scale past a hand-maintained list; no multi-user/RBAC model (anyone with access can change or delete anything), a real problem for "secure by default"; no public API for automating monitor management; no built-in HA/failover; no infrastructure depth (no host-level CPU/disk/process metrics, no config templating).

**License.** MIT License — fully permissive, single maintainer/community project (louislam), no commercial edition ([Uptime Kuma LICENSE](https://github.com/louislam/uptime-kuma/blob/master/LICENSE)).

## Comparison Table

| Tool | Agent Model | Discovery | Ease of Local Setup | UI Quality | License |
|---|---|---|---|---|---|
| Icinga2 | Agentless (default) + optional agent | None built-in (plugin/Director-driven config) | Moderate — multi-component (core + Web2 + Director) | Dated, functional | GPLv3+ (fully open) |
| Checkmk | Hybrid (agent + agentless) | Strong (Service Discovery + DCD) | Easy–moderate (single Docker site) | Good, IT-ops styled | Open core (GPLv2 Raw / commercial Enterprise) |
| Zabbix | Hybrid (agent, proxy, agentless) | Strong (Network Discovery) | Moderate — server+DB+frontend to assemble | Dense, functional | AGPLv3 (fully open, no paid tier) |
| LibreNMS | Agentless (SNMP-first), optional minimal agent | Excellent for network topology (SNMP/LLDP/CDP/etc.) | Moderate (Docker Compose recommended) | Dated/community PHP | GPLv3 (fully open) |
| Prometheus + Grafana | Agentless/pull (exporters) | Weak outside Kubernetes/cloud | Moderate–hard (multi-service stack) | Excellent (Grafana) but fragmented across tools | Mixed: Apache 2.0 (Prometheus) / AGPLv3 + commercial (Grafana) |
| Uptime Kuma | Agentless/pull only | None | Very easy (single Docker command) | Excellent, consumer-grade | MIT (fully open) |

## Gap Analysis

Laid side by side, a clear pattern emerges: **discovery quality and UI quality are almost perfectly inversely correlated across the field.** LibreNMS and Zabbix have real discovery but old, dense UIs; Uptime Kuma has the best UI but zero discovery; Checkmk gets closest to balancing the two but locks its best engine and operational features behind a commercial edition, and even its Raw edition still feels like an IT-ops product. Nothing surveyed is simultaneously:

1. **A single coherent deployable unit**, not a stack of 2-4 services glued together (Prometheus+Grafana is the extreme case, but even Icinga2, Zabbix, and LibreNMS require assembling a database, a core/poller, and a separate web frontend).
2. **Capable of real network discovery *and* host/service depth** — SNMP tools like LibreNMS don't do rich app-level checks; app-metrics tools like Prometheus don't do network discovery.
3. **Secure by default out of the box** — every tool surveyed either has no access control model at all (Uptime Kuma), or ships with the sprawling default-open configuration and plugin/exec surface that mirrors the Nagios-era security assumptions this project should move past (arbitrary check-script execution, agents that implicitly trust the poller, web UIs not built with modern auth/RBAC as a first-class primitive).
4. **Straightforward enough for a professional team to deploy, govern, upgrade, back up, and troubleshoot** without unnecessary integration burden. Uptime Kuma sets a useful setup benchmark, but reaches it by limiting scope to simpler uptime checks.

The whitespace for Trust is the intersection nobody occupies: Uptime Kuma's zero-friction, single-binary setup and UI, combined with LibreNMS/Zabbix-grade automatic discovery and Checkmk-grade service auto-detection, wrapped in a security model that assumes a hostile local network from day one (authenticated/encrypted agent-to-server communication by default, least-privilege checks, no implicit trust of poller-supplied config) — delivered as one coherent product instead of a stack the user has to integrate themselves. That combination — discovery + monitoring + alerting + a good UI, local-first, secure by default, in one binary or one Compose file — does not currently exist in the open-source ecosystem; every serious contender trades off at least one of "easy," "capable," and "single product."
