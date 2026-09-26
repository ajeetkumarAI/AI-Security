# Module 7 — Agentic AI Security (Introduction)

[⬅ Back to README](../README.md)

> This module introduces agentic AI risk. For the full deep-dive (architecture, 9 hands-on exercises, checklist, tool landscape), see **[Module 13 — OWASP Agentic AI Security](./module-13-agentic-ai-security-owasp.md)**.

## Overview

AI agents introduce additional security concerns because they can reason over multiple steps, use tools, access external systems, maintain state, and perform actions.

## Topics

- Agent architecture & attack surfaces
- Agent trust boundaries
- Tool calling & function execution
- Excessive agency & privilege escalation
- Tool poisoning
- Indirect prompt injection
- Agent memory security & context manipulation
- Multi-agent / agent-to-agent communication
- Model-to-model interactions
- Uncontrolled agent loops, resource exhaustion, denial-of-wallet, cost abuse
- Agent authorization
- Human-in-the-loop controls

## The Core Risk in One Diagram

```text
User
  |
  v
AI Agent
  |
  +----> LLM         (reasons about what to do next)
  |
  +----> Memory       (can be poisoned or leaked across users)
  |
  +----> Tools        (can execute real, possibly destructive, actions)
  |
  +----> Other Agents (trust can propagate incorrectly)
  |
  v
External Systems
```

## Hands-on

- Threat-model an AI agent
- Identify agent attack surfaces
- Test tool-use security
- Simulate malicious tool responses
- Analyze excessive permissions
- Test agent memory
- Test indirect prompt injection
- Implement authorization controls
- Add human approval checkpoints
- Build safer agent workflows

## Key Takeaways

1. An agent's authority should never exceed what its *specific task* requires — least privilege, always.
2. Tool output is untrusted data until validated, same as any retrieved document.
3. Continue to **[Module 13](./module-13-agentic-ai-security-owasp.md)** for the full checklist and 9 hands-on exercises.

## References

- OWASP Agentic AI Threats and Mitigations — https://genai.owasp.org/resource/agentic-ai-threats-and-mitigations/
