# Module 1 — AI Security Fundamentals and Offensive AI

[⬅ Back to README](../README.md)

## Overview

This module introduces the security foundations required to analyze AI systems from an attacker's perspective. Every later module builds on the vocabulary and mental models introduced here: attack surfaces, trust boundaries, and threat modeling.

## Topics

- AI and machine learning security fundamentals
- AI application architecture
- AI attack surfaces
- AI trust boundaries
- Threat modeling for AI systems
- AI-specific threat categories
- Adversary techniques and attack paths
- MITRE ATT&CK fundamentals
- MITRE ATLAS fundamentals
- AI security risk assessment
- Offensive security methodology for AI systems
- Security boundaries and trust relationships
- AI attack taxonomy
- Security implications of AI adoption

## A Minimal AI Application, Annotated

```text
User
 |
 v
Application  <-- classic AppSec surface (auth, session, input validation)
 |
 v
AI Orchestrator
 |
 +----> LLM            <-- new surface: natural-language instructions as "code"
 |
 +----> RAG / Vector DB <-- new surface: untrusted retrieved content
 |
 +----> Agent / Tools   <-- new surface: the model can trigger real actions
 |
 v
External Systems
```

Each arrow above is a **trust boundary**: a point where information crosses from one level of trust to another. AI security work is largely the discipline of finding every arrow, deciding what should be trusted, and proving that trust is enforced.

## AI-Specific Threat Categories (Cheat Sheet)

| Category | Example | Where covered |
|---|---|---|
| Prompt-based attacks | Direct / indirect prompt injection, jailbreaks | Module 4 |
| Adversarial ML | Evasion, poisoning, extraction, inversion | Module 5 |
| Data / pipeline attacks | Poisoned training data, backdoors | Module 6 |
| Agentic risk | Excessive agency, tool poisoning, loops | Module 7, 13 |
| Infra / supply chain | Malicious dependencies, model tampering | Module 8 |
| RAG-specific | Document poisoning, cross-tenant leakage | Module 14 |
| Protocol-specific (MCP) | Malicious servers, tool poisoning | Module 15 |

## Hands-on

- Analyze the architecture of an AI application
- Identify potential attack surfaces
- Identify trust boundaries
- Create a basic AI threat model
- Map threats to relevant adversarial techniques
- Build a basic AI attack tree

### Exercise: Your First Attack Tree

1. Pick any AI feature you use (chat assistant, coding tool, customer-support bot).
2. Draw its architecture using the diagram style above.
3. Mark every trust boundary with an arrow annotation.
4. For each boundary, write one sentence: "If this boundary is *not* enforced, an attacker could ___."
5. That list is your first attack tree — refine it as you progress through later modules.

## Key Takeaways

1. AI security is fundamentally about **trust boundaries**, not new cryptography.
2. Natural language is an attack surface the moment it can influence behavior.
3. Threat model before you test — it tells you *what* to test and *why it matters*.

## References

- [MITRE ATLAS](https://atlas.mitre.org/)
- [MITRE ATT&CK](https://attack.mitre.org/)
- [OWASP GenAI Security Project](https://genai.owasp.org/)
