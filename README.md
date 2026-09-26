# AI Security — A Practical Learning Repository

A hands-on, attacker-to-defender curriculum for understanding, testing, and securing modern AI systems: **LLM applications, RAG pipelines, AI agents, MCP integrations, machine learning models, data pipelines, and AI infrastructure.**

> **Approach:** For every topic, first learn how the system can be attacked, then learn how to find that weakness safely in a lab, and finally how to design and validate a control that stops it. Every module ends the same way: **Test → Mitigate → Re-Test.**

---

## Why This Matters

AI systems are being embedded into production software faster than security practices are catching up. Unlike classic web apps, AI systems introduce attack surfaces that traditional AppSec training doesn't cover:

| Traditional AppSec covers | AI systems additionally need |
|---|---|
| SQLi, XSS, auth, session management | Prompt injection (direct & indirect) |
| Input validation on structured data | Natural-language input that can *become* an instruction |
| Static roles/permissions | Agents that dynamically choose tools and actions |
| Trusted internal data | Untrusted retrieved documents, tool output, and model output |
| One request → one response | Multi-step reasoning loops, memory, and agent-to-agent chains |
| Known dependency CVEs | Model/dataset supply chain, weight tampering, poisoned training data |

A single missed trust boundary — e.g. treating a retrieved document or a tool's response as a trusted instruction — can lead to data exfiltration, unauthorized transactions, or full account compromise, even when the "code" has no traditional bug in it. Regulators, enterprises, and standards bodies (OWASP, MITRE, NIST) have responded with dedicated frameworks (OWASP GenAI LLM Top 10, OWASP Agentic AI Threats, MITRE ATLAS) because this class of risk is now considered its own discipline. This repository is built to teach that discipline practically, not just conceptually.

---

## What You Will Learn

By working through this repository you will be able to:

- Map the architecture, trust boundaries, and attack surface of an AI system
- Identify and exploit (safely, in a lab) direct and indirect prompt injection
- Reason about adversarial ML: evasion, poisoning, extraction, membership inference
- Secure a RAG pipeline end-to-end: ingestion, embeddings, vector DB, retrieval, output
- Threat-model and harden agentic AI systems: tool calling, memory, excessive agency
- Secure Model Context Protocol (MCP) integrations: servers, tools, resources, credentials
- Apply MITRE ATLAS and the OWASP GenAI LLM Top 10 to real assessments
- Run a structured AI red-team engagement from scope to final report
- Detect, investigate, and respond to AI-specific security incidents
- Write a defensible, evidence-backed security finding and validate its remediation

---

## Who This Is For

- **AppSec / Pentest engineers** extending their practice into AI systems
- **AI/ML engineers** who want to build with security controls from day one
- **Security researchers** studying the OWASP GenAI and MITRE ATLAS frameworks
- **Red teamers** building an AI-specific testing methodology
- **Engineering leads** who need a checklist-driven way to review AI features before ship

**Prerequisites:** comfort with basic AppSec concepts (auth, authorization, input validation) and enough Python to run small scripts. No prior ML background required — Module 1 and 5 build the concepts you need.

---

## Repository Structure

```text
AI_Security/
├── README.md                          <- you are here (overview + how to use this repo)
│
├── modules/                           <- one self-contained .md per topic
│   ├── module-01-fundamentals.md
│   ├── module-02-recon.md
│   ├── module-03-vulnerability-assessment.md
│   ├── module-04-prompt-injection.md
│   ├── module-05-adversarial-ml.md
│   ├── module-06-data-pipeline-security.md
│   ├── module-07-agentic-ai-security.md
│   ├── module-08-infrastructure-supplychain.md
│   ├── module-09-testing-redteam-hardening.md
│   ├── module-10-incident-response.md
│   ├── module-11-mitre-atlas.md
│   ├── module-12-owasp-genai-top10.md
│   ├── module-13-agentic-ai-security-owasp.md   <- expanded: tools, code, cheat sheet
│   ├── module-14-rag-security.md                <- expanded: tools, code, cheat sheet
│   ├── module-15-mcp-security.md                <- expanded: tools, code, cheat sheet
│   └── module-16-ai-red-teaming.md              <- expanded: tools, code, cheat sheet
│
├── resources/
│   ├── tool-landscape.md              <- open-source tools mapped to each module
│   ├── glossary.md                    <- every term used across modules, one place
│   └── frameworks-and-references.md   <- MITRE ATLAS, OWASP, NIST AI RMF, MCP spec links
│
└── templates/
    ├── finding-template.md            <- copy/paste security finding format
    ├── test-case-template.md          <- copy/paste test case format
    └── threat-model-canvas.md         <- one-page threat modeling worksheet
```

---

## Suggested Learning Path

```text
Foundations                Core AI Attack Surfaces           Specialized Frameworks         Capstone
────────────               ─────────────────────             ─────────────────────         ────────
Module 1  Fundamentals  →  Module 4  Prompt Injection     →   Module 11 MITRE ATLAS     →   Module 16
Module 2  Recon         →  Module 5  Adversarial ML       →   Module 12 OWASP Top 10        AI Red Teaming
Module 3  Vuln Assess.  →  Module 6  Data/Training Pipe.  →   Module 13 Agentic Security     (full assessment,
                        →  Module 7  Agentic AI           →   Module 14 RAG Security          scope → report)
                        →  Module 8  Infra/Supply Chain   →   Module 15 MCP Security
                        →  Module 9  Testing/Red Teaming
                        →  Module 10 Incident Response
```

Modules 13–16 are the deep-dive versions of Modules 7, 9, and the RAG/MCP topics introduced earlier — go through 1–12 first for the fundamentals, then treat 13–16 as your field manual when you actually build or assess a system.

---

## How Each Module Is Structured

Every module follows the same template so material is easy to scan and reuse:

1. **Overview** — what the component is and why it matters
2. **Architecture / Trust Boundaries** — ASCII diagrams of data and control flow
3. **Attack Surface** — the concrete list of things an attacker can touch
4. **Attack Techniques** — how each attack actually works, with examples
5. **Hands-On Exercises** — build it, break it, in a controlled lab
6. **Security Checklist** — a review checklist you can run against a real system
7. **Example Finding** — a fully worked finding in a consistent report format
8. **Tool Landscape** *(Modules 13–16)* — real open-source tools for that domain
9. **Key Takeaways / References**

---

## Ground Rules for Hands-On Work

- Only test systems and accounts you own or are explicitly authorized to test.
- Use isolated, disposable environments (containers, sandbox API keys, dummy data) — never production data or live credentials.
- Treat every "exploit" you build as a **regression test**: once you find it, write the check that fails until it's fixed, and keep it in CI.
- Document as you go using `templates/finding-template.md` — a finding you can't reproduce isn't a finding.

---

## Contributing

Additions are welcome as long as they follow the module template above. Please include: a working ASCII/architecture diagram, at least one hands-on exercise, and a checklist section. PRs that only add prose without a testable exercise will likely be asked for revisions.

---

## License

Use, adapt, and share for learning and internal security enablement. If you fork this for a course or workshop, a credit link back is appreciated but not required.
