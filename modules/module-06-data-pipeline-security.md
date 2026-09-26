# Module 6 — AI Data and Training Pipeline Security

[⬅ Back to README](../README.md)

## Overview

AI systems depend heavily on data and training pipelines. Compromising these components can affect model behavior, integrity, and trustworthiness long before the model ever reaches production.

## Topics

- AI data pipeline architecture & dataset security
- Training-data integrity & provenance
- Data validation
- Data poisoning & backdoor attacks
- Model trojans & malicious training samples
- Training pipeline attacks
- Model artifact security
- Training infrastructure security
- Model lifecycle security
- Secure model storage
- Model integrity validation

## Pipeline Attack Surface

```text
Raw Data Sources
      |
      v
Data Collection  <-- poisoning point: inject malicious samples
      |
      v
Data Validation
      |
      v
Training           <-- backdoor point: trigger pattern -> target label
      |
      v
Model Artifact      <-- tampering point: swap/modify weights at rest
      |
      v
Model Registry
      |
      v
Deployment
```

## Hands-on

- Analyze a sample AI training pipeline
- Simulate controlled data-poisoning scenarios
- Identify suspicious training data
- Explore backdoor behavior
- Analyze model artifacts
- Implement training-data validation
- Design controls for protecting training pipelines

### Exercise: Simulate a Backdoor Trigger

1. Take a small classifier and a clean dataset.
2. Add a fixed "trigger" pattern (e.g. a specific pixel patch or token) to 5% of samples, relabeled to a target class.
3. Train and confirm the model behaves normally on clean data but misclassifies whenever the trigger is present.
4. Implement a mitigation: dataset provenance checks, outlier/trigger detection, or training-data diffing against a trusted baseline — then verify the backdoor no longer activates.

## Key Takeaways

1. Data integrity controls (provenance, validation, hashing) must be enforced *before* training, not after.
2. Model artifacts need the same supply-chain rigor as any signed software artifact (checksum + signature + trusted registry).

## References

- NIST AI Risk Management Framework — https://www.nist.gov/itl/ai-risk-management-framework
- MITRE ATLAS (poisoning techniques) — https://atlas.mitre.org/
