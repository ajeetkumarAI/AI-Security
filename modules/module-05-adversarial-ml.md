# Module 5 — Adversarial Machine Learning and Model Privacy

[⬅ Back to README](../README.md)

## Overview

This module covers attacks that manipulate machine-learning behavior, influence model predictions, or attempt to extract sensitive information from models.

## Topics

- Adversarial examples & evasion attacks
- Data manipulation & model robustness
- Model extraction
- Membership inference
- Model inversion
- Privacy attacks / training-data leakage
- Model confidentiality
- Robustness evaluation
- Defensive strategies

## Attack Family Overview

| Attack | Goal | Attacker Access Needed |
|---|---|---|
| Evasion (adversarial example) | Make the model misclassify a specific input | Query access (black-box) or gradients (white-box) |
| Model extraction | Steal a functional copy of the model | Repeated query access |
| Membership inference | Determine if a record was in the training set | Query access + confidence scores |
| Model inversion | Reconstruct training data from outputs | Query access + auxiliary data |
| Data poisoning | Corrupt training data to plant a backdoor | Write access to training pipeline (see Module 6) |

## Hands-on

- Generate adversarial inputs in controlled environments
- Evaluate model behavior under manipulated inputs
- Explore model extraction concepts
- Analyze membership-inference scenarios
- Study model privacy risks
- Evaluate model robustness
- Implement basic defensive controls

### Exercise: Confidence-Score Leakage Check

1. Query a classification model with a known training-set sample and a known non-member sample.
2. Compare confidence scores / log-probabilities.
3. If members consistently score higher, the model may be leaking membership information.
4. Mitigation: reduce output granularity (return labels, not raw probabilities), add differential privacy noise, or rate-limit repeated queries on the same input class.

## Key Takeaways

1. Query access alone is often enough to extract meaningful information — treat inference APIs as sensitive.
2. Defenses (rate limiting, output rounding, DP noise) trade off usability against leakage — pick deliberately.

## References

- NIST AI Risk Management Framework — https://www.nist.gov/itl/ai-risk-management-framework
- MITRE ATLAS — https://atlas.mitre.org/
