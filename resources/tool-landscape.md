# Tool Landscape — Cross-Module Index

[⬅ Back to README](../README.md)

A consolidated index of the practical tools referenced across modules, grouped by function. See the "Practical Tool Landscape" section inside Modules 13–16 for full context.

| Function | Tool | Module(s) |
|---|---|---|
| Automated LLM/agent red-teaming | PyRIT, garak, promptfoo | 13, 16 |
| Guardrails / input-output filtering | NeMo Guardrails, LLM Guard, Rebuff | 13, 16 |
| Agent tracing & observability | LangSmith, Langfuse, Arize Phoenix | 13, 16 |
| LLM/agent test suites (CI) | Giskard, DeepEval | 13, 16 |
| Sandboxing tool execution | Firecracker, gVisor, Docker+seccomp, E2B | 13 |
| Vector DB access control | Pinecone namespaces, Weaviate multi-tenancy, Milvus RBAC, pgvector+RLS | 14 |
| PII / secrets scanning | Presidio, detect-secrets | 14 |
| RAG evaluation | RAGAS, TruLens | 14, 16 |
| Embedding-inversion research | vec2text | 14 |
| MCP server scanning | mcp-scan (community) | 15 |
| Server isolation | Docker+AppArmor/seccomp, Firecracker microVMs | 15 |
| Credential brokering | HashiCorp Vault, cloud KMS, OAuth token exchange | 15 |
| Adversarial ML testing | IBM ART, Microsoft Counterfit | 5, 16 |
| Supply-chain scanning | pip-audit, trivy, syft/grype, HF model scanning | 8, 16 |

> Tools change fast — treat this as a starting point for evaluation, not an endorsement. Always vet a tool's own supply chain (Module 8) before adding it to your pipeline.
