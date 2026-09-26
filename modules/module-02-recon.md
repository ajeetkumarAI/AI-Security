# Module 2 — AI Reconnaissance and Attack Surface Discovery

[⬅ Back to README](../README.md)

## Overview

Before testing an AI system, understand its architecture, exposed interfaces, dependencies, and potential entry points. Good recon turns a blind test into a targeted one.

## Topics

- AI reconnaissance fundamentals
- Open-source intelligence (OSINT)
- AI application discovery
- Publicly exposed AI endpoints
- API and service enumeration
- Model and model-provider identification
- LLM application discovery
- RAG architecture discovery
- Vector database discovery
- AI infrastructure discovery
- Training-data and dataset exposure
- Public model and dataset analysis
- Attack-surface documentation
- AI threat intelligence

## What to Look For

```text
Public Surface                Internal Surface (with access)
────────────────               ───────────────────────────────
API docs / OpenAPI specs       Model config files
Job postings (stack hints)     Environment variables
GitHub repos / commit history  Vector DB connection strings
Model cards / HF repos         Internal tool/plugin manifests
Error messages leaking stack   System prompts in logs
Rate-limit / header fingerprints  Agent orchestration configs
```

## Hands-on

- Perform reconnaissance against intentionally vulnerable AI applications
- Identify exposed APIs and services
- Identify AI-related infrastructure
- Analyze publicly available information
- Document an AI application's attack surface
- Create an attack-surface map

### Exercise: Fingerprint the Stack

1. Send a few edge-case prompts and see if the model reveals its provider/version.
2. Inspect HTTP response headers and error bodies for framework fingerprints.
3. Check for a public model card / weights repo if the app uses an open-source model.
4. Document findings as an attack-surface map (see Module 1's diagram style).

## Attack-Surface Documentation Template

```text
Component:        [e.g. "Customer Support Agent API"]
Entry Point:       [URL / endpoint / interface]
Data Handled:      [PII? credentials? business data?]
Authentication:    [none / API key / OAuth / session]
Dependencies:      [model provider, vector DB, plugins]
Notes:             [anything unusual observed]
```

## Key Takeaways

1. Recon for AI systems includes classic web recon *plus* model/provider/vector-DB fingerprinting.
2. Document everything — an attack-surface map is a living artifact used in every later module.

## References

- OWASP GenAI Security Project — https://genai.owasp.org/
- MITRE ATLAS Reconnaissance tactic — https://atlas.mitre.org/
