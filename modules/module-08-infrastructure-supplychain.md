# Module 8 — AI Infrastructure and Supply Chain Security

[⬅ Back to README](../README.md)

## Overview

AI applications depend on frameworks, models, libraries, APIs, containers, cloud services, and third-party components. Each dependency introduces potential security risk.

## Topics

- AI infrastructure architecture & cloud AI security
- Container security
- Model artifact security
- Dependency vulnerabilities
- Open-source AI frameworks & third-party model risks
- Plugins and external tools
- API security
- AI software supply chain / model supply-chain attacks
- Dependency & model integrity
- Secure deployment practices & infrastructure hardening

## Supply Chain Map

```text
Model Source (HF Hub / vendor)  --->  Model Registry  --->  Serving Infra  --->  App
        |                                    |                    |
        v                                    v                    v
  Weight tampering?              Unsigned artifacts?      Container CVEs?

Python Packages  --->  Dependency Confusion  --->  Build Pipeline  --->  Runtime
```

## Hands-on

- Analyze dependencies in an AI project
- Identify vulnerable components
- Review container configurations
- Review AI deployment configurations
- Evaluate third-party model risks
- Analyze model artifacts
- Apply infrastructure hardening
- Implement dependency security controls

### Exercise: Model Provenance Check

1. Pick a model your app depends on (Hugging Face, vendor API, or self-hosted).
2. Verify: publisher identity, checksum/signature, license, and known CVEs against the serving framework.
3. Confirm the deployment pins an exact model version/hash rather than "latest".
4. Document any gap as a supply-chain finding.

## Key Takeaways

1. Treat models and datasets as first-class supply-chain artifacts — pin, hash, and sign them.
2. Container and dependency hygiene still matters; AI stacks don't get a pass on classic AppSec hardening.

## References

- NIST AI Risk Management Framework — https://www.nist.gov/itl/ai-risk-management-framework
