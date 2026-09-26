# Module 12 — OWASP GenAI LLM Top 10

[⬅ Back to README](../README.md)

## Overview

The **OWASP GenAI LLM Top 10** provides a structured approach for studying security risks affecting applications built with large language models and generative AI — the "OWASP Top 10" equivalent for LLM apps.

## Topics / The Top 10 at a Glance

| # | Risk | One-line description |
|---|---|---|
| 1 | Prompt injection | Untrusted input overrides intended instructions |
| 2 | Sensitive information disclosure | Model reveals secrets, PII, or internal data |
| 3 | Supply-chain risks | Compromised models, datasets, or plugins |
| 4 | Data / model poisoning | Training or fine-tuning data manipulated |
| 5 | Improper output handling | Unvalidated model output executed or rendered unsafely |
| 6 | Excessive agency | Agent has more permission/autonomy than needed |
| 7 | System prompt leakage | Instructions or secrets in the system prompt exposed |
| 8 | Vector & embedding weaknesses | RAG/vector-DB specific flaws (see Module 14) |
| 9 | Misinformation | Model produces confidently wrong, harmful output |
| 10 | Unbounded consumption | Resource exhaustion / denial-of-wallet |

## Security Testing Approach

Each applicable risk can be studied using:

```text
Threat
   ↓
Attack Scenario
   ↓
Vulnerable Application
   ↓
Security Test
   ↓
Impact Analysis
   ↓
Mitigation
   ↓
Security Validation
```

## Hands-on

- For each of the 10 risks, build one minimal vulnerable example and one test case that detects it.
- Track mitigations in a shared checklist and re-test after each fix.

## Key Takeaways

1. The Top 10 is a *checklist floor*, not a ceiling — pair it with MITRE ATLAS (Module 11) for full coverage.
2. Risks 1, 6, and 8 (prompt injection, excessive agency, vector/embedding weaknesses) are the ones most amplified by agentic and RAG architectures — see Modules 13 and 14.

## References

- OWASP GenAI LLM Top 10 — https://genai.owasp.org/resource/owasp-genai-llm-top-10-2026/
- OWASP Vector and Embedding Weaknesses — https://genai.owasp.org/llmrisk/llm082025-vector-and-embedding-weaknesses/
