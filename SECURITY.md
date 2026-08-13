# Security Policy

Trust is in pre-release research and must not be deployed in production.

## Supported Versions

No released version is currently supported. This table will be updated with the
first security-supported release.

| Version | Supported |
|---|---|
| Pre-release research | No |

## Reporting A Vulnerability

Do not open a public issue, discussion, or pull request containing vulnerability
details, credentials, internal addresses, logs, or proof-of-concept exploits.

Once the GitHub repository enables private vulnerability reporting, use
**Security > Advisories > Report a vulnerability**. Before public launch, the
maintainers must also publish a monitored security contact as a fallback.

Include, when possible:

- The affected component and revision.
- Preconditions and a minimal reproduction.
- The security impact and likely attack path.
- Suggested mitigations.
- Whether the issue is already public or under active exploitation.

The project will acknowledge valid reports, coordinate a fix and disclosure
timeline, and credit reporters who want attribution. Formal response targets
will be published before the first supported release.

## Security Expectations

- Never commit secrets or real organizational data.
- Use synthetic hostnames, addresses, logs, and identities in examples.
- Treat agent and monitored-host output as untrusted input.
- Document changes to authentication, authorization, encryption, execution,
  secrets, storage, and network exposure.
- Add negative and abuse-case tests for security-sensitive behavior.

See [docs/THREAT_MODEL.md](docs/THREAT_MODEL.md) for ongoing threat research.
