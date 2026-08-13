# ADR-0001: Select The Project License

- Status: Accepted
- Date: 2026-08-10
- Owners: Project maintainers

## Context

Trust is intended to be publicly available open-source software. A public GitHub
repository alone does not grant permission to use, modify, or redistribute its
contents. A license must be selected before accepting code contributions or
advertising reusable releases.

The choice should consider adoption by professional IT teams, contributions,
commercial redistribution, hosted-service use, patent protection, compatibility
with dependencies, and any reuse of existing monitoring code or plugins.

## Options Considered

- Apache License 2.0: permissive, includes an explicit patent grant.
- GNU Affero General Public License v3: strong network copyleft.
- GNU General Public License v3: strong distribution copyleft.

MIT is also common and simple, but Apache-2.0 may better address patent terms for
a professional infrastructure project. This is not legal advice.

## Decision

Trust will use the Apache License 2.0. It permits commercial and private use,
modification, and redistribution while retaining copyright and license notices.
Its explicit patent grant is useful for a professional infrastructure project.

## Consequences

Trust can be adopted and integrated broadly, including by commercial users.
Modified or redistributed copies must preserve the required notices. The
license does not require downstream source disclosure, including for hosted or
modified deployments.

## Validation

- Inventory planned dependencies and compatibility constraints.
- Obtain qualified legal review if the project will be adopted by an employer.
- Review dependency and plugin compatibility as the architecture develops.
