# Roadmap

The roadmap describes outcomes, not delivery dates. Scope may change as research
and testing reveal better choices.

## Phase 0: Foundation

- Define primary users, workflows, and product boundaries.
- Complete competitor, security, privacy, and deployment research.
- Define scale tiers and a repeatable benchmark model.
- Select the project name, license, governance, and contribution terms.
- Write the initial architecture and threat model.
- Choose the first supported operating systems and technology stack.

Exit: discovery criteria in the project brief are satisfied and recorded.

## Phase 1: First Vertical Slice

- Enroll one approved endpoint with a unique identity.
- Collect a minimal set of host health signals.
- Evaluate a sustained threshold and record state history.
- Display the current state and supporting evidence.
- Deliver and recover one alert through internal SMTP.
- Install and operate without public internet access.

Exit: the workflow is tested end to end in a disposable lab environment.

## Phase 2: Team Operations

- Add roles, acknowledgements, maintenance windows, and audit history.
- Add policy-based configuration for groups of endpoints.
- Add safe file size and growth monitoring.
- Add backup, restore, retention, and offline update procedures.
- Publish administrator and troubleshooting documentation.

## Phase 3: Infrastructure Breadth

- Add Linux endpoint support and agentless service checks.
- Evaluate SNMP v3 and explicitly authorized network discovery.
- Add dependency and topology-aware alert behavior.
- Publish a stable, sandboxed extension interface.

## Phase 4: Distributed Operation

- Separate collection, scheduling, evaluation, and storage where required.
- Add remote collectors for segmented sites.
- Validate backpressure, retry, and partial-failure behavior.
- Benchmark documented scale tiers and publish reproducible results.
- Evaluate high availability and disaster-recovery modes.

## Not Yet Committed

- Remote control, patch deployment, or configuration enforcement.
- Packet capture or user-content inspection.
- A hosted cloud service.
- Unlimited retention or a universal SIEM replacement.
