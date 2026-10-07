# Assessment Overview

This directory supplements the [assessment report](../report.md) with source-based architecture and security analysis. The application source and deployment configuration were assessed without modification.

## Supplementary Documents

- [Architecture diagram](architecture-diagram.md): Application layers, runtime storage, component relationships and technology inventory.
- [Dependency map](dependency-map.md): Declared production and test dependencies, versions and compatibility considerations.
- [API and service contracts](api-service-contracts.md): Razor Pages handlers, authentication, request/response contracts and communication sequence.
- [Data architecture](data-architecture.md): Photo metadata model, database environments, persistence boundaries and data sensitivity.
- [Configuration inventory](configuration-inventory.md): Runtime settings, deployment inputs, resource requirements and masked secret references.
- [Business workflows](business-workflows.md): Upload, browsing, login and deletion flows, including validation and compensating actions.
- [Security assessment](security-assessment.md): CVE query coverage, 59 CWE checklist rules, three confirmed checklist findings and assessment limitations.

## Assessment Coverage

The core analysis covered both solution projects and reported eight issue types across 28 incidents. AppCAT 1.0.1127 ran with restricted privacy and all available compute targets. It logged an MSBuild discovery warning on Linux, but produced source-located incidents for both projects; a separate solution build succeeded with no warnings or errors. Interpret automated findings as assessment candidates, not verified deployment failures.

Architecture analysis distinguishes provisioned Azure resources from integrations actually used by application code. Security results use a minimum CVE severity of **high**; no matching advisories were returned by the dependency queries. Full machine-readable security evidence is available in [the security directory](../security/). NOT_FOUND results are not a guarantee of security.
