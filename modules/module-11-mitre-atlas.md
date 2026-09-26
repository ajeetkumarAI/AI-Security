# Module 11 — MITRE ATLAS

[⬅ Back to README](../README.md)

## Overview

**MITRE ATLAS (Adversarial Threat Landscape for AI Systems)** provides a structured knowledge base for understanding adversary tactics and techniques targeting AI-enabled systems. It can be used for AI threat modeling, security assessments, attack simulation, and red-team planning — think of it as ATT&CK, but for AI.

## Topics

- ATLAS tactics & techniques
- AI reconnaissance & model access
- AI application / LLM-related attacks
- Adversarial data & training-data poisoning
- RAG-related attacks & AI agent attacks
- AI supply-chain threats & model manipulation
- Credential and data-access attacks
- AI-enabled exfiltration & impact techniques
- Mapping attacks to ATLAS techniques
- Building AI attack scenarios

## Using ATLAS in Practice

```text
1. Identify the AI component under test (LLM, RAG, agent, pipeline)
2. Browse the matching ATLAS tactic (Reconnaissance, ML Model Access, Exfiltration, Impact...)
3. Pick the technique that matches your hypothesis
4. Design a test case that proves/disproves that technique applies
5. Record the ATLAS technique ID in your finding for traceability
```

## Hands-on

- Analyze an AI application using MITRE ATLAS
- Map attack scenarios to ATLAS techniques
- Create an AI attack tree
- Develop an AI threat model
- Build and execute attack scenarios in a controlled AI red-team exercise
- Document attack paths
- Map defensive controls to identified threats

## Key Takeaways

1. ATLAS gives your findings a common vocabulary that maps to a recognized industry framework.
2. Use it to check *coverage* — did your assessment touch every relevant tactic, or only the obvious ones?

## References

- [MITRE ATLAS](https://atlas.mitre.org/)
- [MITRE ATT&CK](https://attack.mitre.org/)
