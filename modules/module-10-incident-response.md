# Module 10 — AI Incident Response and Forensics

[⬅ Back to README](../README.md)

## Overview

AI systems require security monitoring and incident-response processes that account for model behavior, prompts, tools, data, and application telemetry — not just server logs.

## Topics

- AI security incident lifecycle & detection
- Security / prompt / response telemetry
- Agent activity & tool-call monitoring
- Model & application logs
- Evidence collection & incident investigation
- Root-cause analysis & attack-path reconstruction
- Containment & recovery
- Post-incident analysis

## What to Log (Minimum Viable Telemetry)

```text
Request:   user id, timestamp, raw prompt, session/conversation id
Reasoning: model version, system prompt hash, retrieved doc IDs
Action:    tool called, parameters, authorization decision
Result:    tool output, model final response, approval decision
```

## Hands-on

- Analyze simulated AI security incidents
- Investigate suspicious prompts
- Analyze suspicious model responses
- Review agent activity & tool calls
- Identify the attack path
- Perform root-cause analysis
- Create an incident report
- Recommend preventive controls

### Exercise: Reconstruct an Attack Path

1. Given a log bundle (prompt, retrieved docs, tool calls, final output), find the point where untrusted content became a trusted instruction.
2. Reconstruct the path: entry point → trust boundary crossed → action taken → impact.
3. Write the incident report using the finding format in `templates/finding-template.md`, adapted with a timeline section.

## Key Takeaways

1. If you can't reconstruct *why* the model did something, you can't contain or prevent the next incident.
2. Prompt/tool-call telemetry is as important as network logs for AI systems.

## References

- MITRE ATLAS — https://atlas.mitre.org/
