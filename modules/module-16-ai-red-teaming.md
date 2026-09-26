# Module 16 — AI Red Teaming

[⬅ Back to README](../README.md) · Capstone module — draws on Modules 1–15

## Overview

AI Red Teaming is a structured security testing approach used to identify weaknesses in AI systems before attackers can exploit them — spanning LLM applications, RAG systems, AI agents, MCP integrations, ML models, AI APIs, infrastructure, supply chains, and data pipelines. The goal is not just to find vulnerabilities, but to **verify controls actually prevent the tested scenario** and stay effective after remediation.

## 1. Objectives

Identify AI-specific attack surfaces · discover weaknesses · test security controls · simulate realistic attacks · evaluate impact · validate authN/authZ · test prompt-injection defenses · test RAG/agent/tool/MCP security · identify sensitive-data exposure · evaluate supply-chain risk · validate mitigations.

## 2. Scope

Define explicitly before testing: systems in/out of scope, environment, allowed techniques, test accounts/data, duration, rate limits, evidence requirements, reporting requirements. **Only test systems and accounts you are authorized to test.**

```text
AI Application
 +---- LLM
 +---- RAG / Vector Database
 +---- AI Agent
 +---- Tools
 +---- MCP
 +---- APIs
 +---- Infrastructure
```

## 3. Methodology

```text
Define Scope → Understand Architecture → Identify Assets → Identify Trust Boundaries
   → Reconnaissance → Attack Surface Mapping → Threat Modeling → Attack Scenarios
   → Test Cases → Execute Controlled Tests → Collect Evidence → Analyze Impact
   → Recommend Mitigation → Implement Controls → Re-Test → Final Security Report
```

## 4–6. Architecture, Assets, Trust Boundaries

```text
User → Web App → API → AI Orchestrator
                        +--> LLM
                        +--> RAG → Vector DB
                        +--> Agent → Tools, MCP, External APIs
```

Assets to classify by sensitivity: user/customer data, financial info, business documents, source code, system prompts, API/cloud credentials, model artifacts, training data, vector DBs, agent memory, internal APIs.

## 7–9. Reconnaissance & Attack Surface Mapping

Reconnaissance (see also Module 2) reviews public endpoints, APIs, AI endpoints, model info, RAG/tool/MCP interfaces, auth mechanisms, docs, cloud services, dependencies — before any test is executed. Build a full attack-surface map covering LLM, RAG/Vector DB, Agent/Tools/MCP, Model API, and External APIs.

## 10–21. Testing Areas (Summary Table)

| Area | Key tests | Related module |
|---|---|---|
| LLM application | Prompt injection, jailbreaks, system-prompt exposure, unsafe output | 4 |
| Indirect prompt injection | Malicious docs/websites/emails/tool responses | 4, 13, 14, 15 |
| RAG | Poisoning, unauthorized/cross-tenant retrieval, metadata manipulation, embedding security | 14 |
| AI agent | Excessive agency/permissions, tool poisoning, memory manipulation, agent-to-agent, loops/cost abuse | 7, 13 |
| Tool use | Unauthorized invocation, input/output validation, permission boundaries | 13 |
| MCP | Untrusted servers, authN/authZ failures, tool poisoning, credential exposure, isolation failures | 15 |
| AuthN/AuthZ | Cross-user access, role enforcement, tenant isolation | all |
| Sensitive-data exposure | Direct and indirect extraction of PII/credentials/secrets | 4, 14 |
| Supply chain | Dependency vulns, malicious packages, compromised models, dependency confusion | 8 |
| Infrastructure | Cloud config, containers, secrets management, network exposure | 8 |
| Adversarial ML | Evasion, poisoning, backdoors, extraction, membership inference, inversion | 5, 6 |

## 22–26. Scenario, Test Case, and Finding Formats

**Attack scenario:** `Target → Attacker Capability → Attack Vector → Expected Control → Test → Observed Behavior → Potential Impact`

**Test case template:**

```text
Test Case ID:      AI-RED-001
Title:              Indirect Prompt Injection Through RAG Document
Target:             RAG Application
Preconditions:      Authenticated test account
Attack Scenario:    A malicious instruction is inserted into a controlled document.
Expected Behavior:  The system treats the document as untrusted content.
Test Steps:         1. Create test document 2. Insert instruction 3. Ingest
                    4. Query to retrieve it 5. Observe behavior
Observed Behavior:  Document content influences the LLM response.
Security Impact:    Potential manipulation of AI behavior.
Recommended Mitigation: Context validation + instruction/data separation.
Retest:             Pending
```

**Finding template:** see `templates/finding-template.md` for the full copy/paste version used throughout every module in this repo.

## 27. Risk Analysis

Assess likelihood, impact, exploitability, required privileges/user interaction, data sensitivity, business impact, and control effectiveness — use your organization's approved risk methodology rather than assigning severity arbitrarily.

## 28–29. Security Control Validation & Re-Testing

```text
Finding → Mitigation → Security Control → Re-Test → Validation
```

After remediation, verify: the original attack no longer succeeds, the control is actually enforced (not just present), no bypass exists, related attack paths are considered, functionality and logging still work.

## 30. Hands-On Capstone Project

Build an intentionally vulnerable AI application (LLM + RAG/Vector DB + Agent + Tools + MCP) and run a full assessment:

1. **Architecture Review** — components, data flows, trust boundaries, dependencies
2. **Attack Surface Mapping** — APIs, LLM, RAG, agent, tools, MCP, auth
3. **Threat Modeling** — threat model, attack tree, attack scenarios
4. **Security Testing** — prompt injection, RAG poisoning, agent permissions, tool/MCP authorization, resource abuse
5. **Evidence Collection** — inputs, behavior, logs, tool calls, model responses
6. **Findings** — consistent finding format for every issue
7. **Remediation** — implement controls
8. **Re-Test** — repeat original tests, confirm controls hold

## 31–32. Deliverables & Final Report Structure

Deliverables: architecture review, attack-surface map, threat model, attack scenarios, test cases, evidence, findings, risk analysis, mitigations, re-test results, final report.

```text
1. Executive Summary        6. Testing Methodology      11. Recommended Mitigations
2. Assessment Scope         7. Security Test Cases      12. Re-Test Results
3. System Architecture      8. Findings                 13. Security Control Validation
4. Attack Surface           9. Risk Analysis             14. Residual Risks
5. Threat Model             10. Evidence                 15. Conclusion
```

## 33. AI Red Teaming Checklist

**Scope:** defined · authorization confirmed · test environment/accounts ready · rules of engagement documented
**Architecture:** reviewed · data flows/trust boundaries/dependencies/assets identified
**LLM:** prompt injection (direct + indirect), system-prompt exposure, sensitive-data exposure, unsafe output all tested
**RAG:** poisoning, unauthorized/cross-tenant retrieval, vector DB access, metadata manipulation tested
**Agent:** excessive agency/permissions, tool authorization/poisoning, memory security, agent-to-agent, resource abuse tested
**MCP:** server trust, authN/authZ, tool/resource security, credential protection, prompt injection tested
**Infrastructure:** API security, cloud config, containers, secrets management, network exposure, dependencies reviewed
**Reporting:** evidence collected · findings documented · risk assessed · mitigations documented · re-testing completed · final report prepared

---

## 34. Practical Tool Landscape for AI Red Teaming *(new)*

| Category | Tools | Notes |
|---|---|---|
| Automated LLM/agent red-teaming | [Microsoft PyRIT](https://github.com/Azure/PyRIT), [`garak`](https://github.com/leondz/garak), [`promptfoo`](https://github.com/promptfoo/promptfoo) | Probe libraries for jailbreaks, injection, data leakage, toxicity |
| Continuous eval in CI | [Giskard](https://github.com/Giskard-AI/giskard), [DeepEval](https://github.com/confident-ai/deepeval), [RAGAS](https://github.com/explodinggradients/ragas) (RAG-specific) | Wire red-team probes into your pipeline as regression tests |
| Adversarial ML testing | [IBM ART (Adversarial Robustness Toolbox)](https://github.com/Trusted-AI/adversarial-robustness-toolbox), [Microsoft Counterfit](https://github.com/Azure/counterfit) | Evasion, extraction, poisoning research tooling |
| Guardrail / detector testing | [LLM Guard](https://github.com/protectai/llm-guard), [Rebuff](https://github.com/protectai/rebuff), [NeMo Guardrails](https://github.com/NVIDIA/NeMo-Guardrails) | Test your own guardrails *against* the probes above, don't just deploy them blind |
| Supply-chain / dependency scanning | `pip-audit`, `trivy`, `syft`/`grype` (SBOM), Hugging Face model scanning | Extend classic AppSec supply-chain tooling to models/datasets |
| Observability for evidence collection | OpenTelemetry, Langfuse, LangSmith, Arize Phoenix | Capture the full reasoning/tool-call trace as red-team evidence |

## 35. Automation vs. Manual Testing — When to Use Which *(new)*

| Situation | Prefer |
|---|---|
| Regression testing known injection patterns after every deploy | Automated (`promptfoo`/`garak` in CI) |
| Discovering novel jailbreaks / creative attack chains | Manual, human-led red team |
| Coverage across a large prompt/persona surface | Automated fuzzing |
| Business-logic-specific abuse (e.g. "can this agent be tricked into refunding twice?") | Manual scenario-based testing |
| Continuous drift detection (model/provider updates silently changing behavior) | Automated, scheduled |

A mature program runs **both**: automated suites catch regressions continuously; periodic manual engagements find what automation can't yet imagine.

## 36. Continuous / CI-Integrated Red Teaming *(new)*

```text
Code / Prompt / Model Change
        |
        v
CI Pipeline
        |
        +--> Unit tests (app logic)
        +--> Automated red-team probe suite (promptfoo / garak / custom)
        +--> RAG groundedness eval (RAGAS)
        +--> Guardrail regression tests
        |
        v
   Pass → Deploy        Fail → Block deploy, file finding, notify owner
```

Treat every confirmed finding in this module as a permanent addition to this suite — a vulnerability that isn't turned into a regression test will eventually resurface.

---

## References

* MITRE ATLAS — https://atlas.mitre.org/
* MITRE ATT&CK — https://attack.mitre.org/
* OWASP GenAI Security Project — https://genai.owasp.org/
* OWASP GenAI LLM Top 10 — https://genai.owasp.org/resource/owasp-genai-llm-top-10-2026/
* OWASP Agentic AI Security — https://genai.owasp.org/resource/agentic-ai-threats-and-mitigations/
* NIST AI Risk Management Framework — https://www.nist.gov/itl/ai-risk-management-framework
* Model Context Protocol — https://modelcontextprotocol.io/
