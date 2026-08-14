# Trust

Trust is a research-stage, open-source infrastructure monitoring project for
professional IT teams operating private networks.

The goal is a secure, on-premises platform for monitoring endpoints, servers,
network services, and operational conditions without requiring a public cloud
service or internet access. Trust is inspired by hands-on experience with
Nagios, but it is not yet presented as a replacement for Nagios or any other
established product.

> [!IMPORTANT]
> Trust is currently in discovery. It is not ready for installation or
> production use, and no stable architecture or security boundary has been
> approved.

## Why Trust

Professional IT teams need to detect resource pressure, service failures, file
growth, and infrastructure changes early enough to act. Existing platforms are
powerful, but deployments can involve separate tools for agents, checks,
discovery, metrics, dashboards, and alert delivery.

Trust is investigating whether those workflows can become one coherent,
local-first system while retaining a simple extension model and strong
operational controls.

## Intended Capabilities

The following areas are under investigation and are not release commitments:

- Windows and Linux endpoint monitoring.
- CPU, memory, disk, process, service, and availability checks.
- Safe monitoring of selected file size and growth, including PST/OST files.
- Agentless checks for common services and ports.
- Authorized network and service discovery.
- History, dashboards, maintenance windows, acknowledgements, and audit trails.
- Actionable alerts delivered through internal infrastructure such as SMTP.
- Secure operation on restricted and air-gapped networks.
- A path from a simple deployment to distributed collectors and workers.
- Compatibility with existing monitoring checks where it is safe and useful.

## Design Principles

- **Local-first:** core operation must not depend on public internet services.
- **Secure by design:** the LAN is not treated as a trusted security boundary.
- **Professional operations:** clear ownership, auditability, and recovery.
- **Actionable alerts:** sustained symptoms and useful evidence over noise.
- **Progressive scale:** begin simply, then distribute components when needed.
- **Data restraint:** collect the minimum information required for each check.
- **Evidence before claims:** validate design decisions with tests and benchmarks.

## Project Status

The project is currently producing its product brief, competitive research,
threat model, scale model, and architecture decision records. Application code
will begin after the discovery exit criteria are satisfied.

Start with:

- [Discovery brief](docs/discovery/PROJECT_BRIEF.md)
- [Monitoring landscape](docs/research/MONITORING_LANDSCAPE.md)
- [Competitor research](docs/research/competitors.md)
- [Threat-model research](docs/THREAT_MODEL.md)
- [Scalability strategy](docs/SCALABILITY.md)
- [Minimum viable product](docs/product/MVP.md)
- [Proposed system architecture](docs/architecture/SYSTEM_ARCHITECTURE.md)
- [Roadmap](ROADMAP.md)

## Contributing

Trust welcomes careful research, corrections, use cases, design proposals, and
eventually code. Read [CONTRIBUTING.md](CONTRIBUTING.md) before opening an issue
or pull request. Major behavior and architecture changes should start as a
proposal rather than an implementation.

Please follow the [Code of Conduct](CODE_OF_CONDUCT.md). Potential security
issues must be reported according to [SECURITY.md](SECURITY.md), not in a public
issue.

## License

Trust is licensed under the [Apache License 2.0](LICENSE). The licensing rationale
is recorded in [ADR-0001](docs/decisions/0001-license.md).
