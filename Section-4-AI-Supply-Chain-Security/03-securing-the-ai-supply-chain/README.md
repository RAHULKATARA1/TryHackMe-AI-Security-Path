# 🏠 Securing the AI Supply Chain — TryHackMe AI Security Path

> **Section:** Section 4 — AI Supply Chain Security | **Difficulty:** Medium | **Type:** Theory  
> **Room Link:** [https://tryhackme.com/room/securing-the-ai-supply-chain](https://tryhackme.com/room/securing-the-ai-supply-chain)  
> **Completed:** 25/05/2026

---

## 📋 Room Overview

Understanding the attacks is only half the mission. This room covers the full defensive playbook for AI supply chain security: from data governance and model provenance to MLOps hardening and regulatory compliance. The goal is to architect an AI development pipeline that is **resilient, auditable, and hardened** at every link in the chain.

**What you'll learn:**
- Data governance: provenance tracking, integrity verification, and secure ingestion pipelines.
- Model signing, versioning, and cryptographic provenance.
- Dependency management: SBOMs for ML, private package mirrors, and lock files.
- MLOps hardening: least-privilege CI/CD, secret management, and registry security.
- Detecting backdoors: neural cleanse, activation clustering, and spectral signatures.
- Regulatory and compliance frameworks: NIST AI RMF, EU AI Act, and SLSA.

---

## 🧠 Key Concepts

### Pillar 1 — Data Governance & Provenance

**Data is the most foundational — and most overlooked — supply chain component.** Securing the training data pipeline requires:

#### Data Lineage Tracking
Every training sample must have a provenance record: where it came from, who collected it, when, under what licence, and whether it has been reviewed.

```yaml
# Example data lineage record (stored in metadata sidecar)
dataset_record:
  source: "CommonCrawl-2023-Q3"
  ingestion_date: "2023-09-15"
  ingestion_engineer: "data-pipeline-svc@company.com"
  review_status: "approved"
  reviewer: "alice@company.com"
  integrity_hash: "sha256:3f8a9d2e..."
  licence: "CC-BY-4.0"
  pii_scan: "passed"
  poison_scan: "passed"
```

#### Data Integrity Verification
Every dataset version must be cryptographically hashed. Before training begins, the pipeline verifies the hash against a trusted manifest:

```bash
# Generate manifest at data preparation time
sha256sum training_data/*.parquet > MANIFEST.sha256

# Verify before training (in CI/CD)
sha256sum --check MANIFEST.sha256
# If any file has changed: ABORT TRAINING
```

#### Data Sanitization Pipeline
Before data enters the training pipeline, it passes through:
1. **PII scrubbing** — remove personal identifiable information.
2. **Deduplication** — excessive duplicates amplify data poisoning effects.
3. **Anomaly detection** — statistical outlier detection flags potentially poisoned samples.
4. **Human review** — for high-risk categories, mandatory human sign-off.

### Pillar 2 — Model Provenance & Signing

**Model cards** (originally popularized by Google and Hugging Face) provide human-readable metadata about a model's training process, data, and intended use. For security, this needs to be extended with **cryptographic signing**:

#### Model Signing Workflow
```bash
# After training, sign the model with your organization's private key
cosign sign-blob --key cosign.key model_weights.safetensors > model.sig

# Before deploying, any party can verify:
cosign verify-blob --key cosign.pub \
  --signature model.sig \
  model_weights.safetensors
# ✅ Verified OK — provenance chain intact
```

**Tools:**
- **Sigstore/Cosign** — keyless signing for ML artifacts (same tool used for container signing).
- **DVC (Data Version Control)** — Git-like versioning for datasets and model artifacts.
- **MLflow** — tracks model lineage, parameters, and metrics with immutable run records.

#### SafeTensors Enforcement
Mandate SafeTensors format for all model artifacts. Pickle and older formats should be **blocked at the registry level**:

```python
# Registry upload hook — reject Pickle files
def validate_upload(file_path: str):
    if file_path.endswith((".pkl", ".pickle", ".pt")):
        raise SecurityError(
            f"Unsafe serialization format rejected: {file_path}. "
            f"Please use SafeTensors format."
        )
    run_modelscan(file_path)  # Additional malware scanning
```

### Pillar 3 — Dependency Management

#### Software Bill of Materials (SBOM) for ML
A **ML-SBOM** documents every dependency in the ML stack: Python packages, their versions, and their transitive dependencies. Generated at build time and signed:

```bash
# Generate SBOM for ML environment
pip-audit --requirement requirements.txt --format cyclonedx-json > sbom.json
syft scan . -o spdx-json > sbom-spdx.json

# Audit for known CVEs
pip-audit --requirement requirements.txt
```

#### Private Package Mirror + Lock Files
```bash
# Use a private Artifactory/Nexus mirror — block direct PyPI access
pip install --index-url https://pypi.internal.company.com/simple/ \
            --no-index \
            -r requirements.txt

# Pin ALL dependencies with hashes
pip-compile --generate-hashes requirements.in > requirements.txt
# requirements.txt now contains:
# torch==2.1.0 \
#   --hash=sha256:3f8a9d2e... \
#   --hash=sha256:7b2c1e4f...
```

#### Dependency Review in CI/CD
```yaml
# GitHub Actions — auto-review new dependencies
- name: Dependency Review
  uses: actions/dependency-review-action@v3
  with:
    fail-on-severity: moderate
    deny-licenses: GPL-3.0, AGPL-3.0
```

### Pillar 4 — MLOps Pipeline Hardening

#### Least-Privilege Service Accounts
Each component of the ML pipeline should run with the minimal permissions needed:

| Pipeline Stage | Required Permissions | NOT Required |
|---------------|---------------------|--------------|
| Data ingestion | Read from S3 bucket | Write to model registry |
| Training job | Read data, write artifacts | Production database access |
| Evaluation | Read model, read test set | Write to training data |
| Deployment | Push to production registry | Access training data |

#### Secret Management
```bash
# NEVER do this:
HUGGINGFACE_TOKEN=hf_abc123  # Hardcoded in .env or code

# DO this — use a secrets manager:
export HUGGINGFACE_TOKEN=$(aws secretsmanager get-secret-value \
  --secret-id prod/ml/huggingface-token \
  --query SecretString --output text)
```

#### Immutable Artifacts & Promotion Gates
```
[Development] → [Staging] → [Production]
    ↓               ↓              ↓
 Any version     Signed + scanned  Signed + scanned
                 + human approval  + security gate
                                   + rollback plan
```

### Pillar 5 — Backdoor Detection

Even with all prevention controls, assume breach — and implement detection:

#### Neural Cleanse
Analyzes model behavior to detect anomalous "shortcuts" in decision-making that could indicate a backdoor trigger. Works by reverse-engineering potential triggers for each class and flagging classes with abnormally small trigger sizes (an indicator of backdoor implantation).

#### Activation Clustering
Examines the internal activations of a neural network on clean vs. potentially poisoned samples. Backdoored samples often cluster anomalously in activation space — distinct from the natural distribution of clean samples.

#### Spectral Signature Detection
Poisoned samples leave a detectable trace in the **singular value decomposition** of their feature representations. A sudden spectral outlier in a training batch is a strong indicator of data poisoning.

```python
# Simplified spectral signature detection
import numpy as np
from sklearn.decomposition import TruncatedSVD

def detect_poisoning(features: np.ndarray, top_k: int = 5) -> bool:
    """Returns True if spectral anomalies suggest poisoning."""
    svd = TruncatedSVD(n_components=top_k)
    svd.fit(features)
    # Anomalous variance concentration in top singular vectors
    variance_ratio = svd.explained_variance_ratio_[0]
    return variance_ratio > 0.8  # Threshold from literature
```

---

## 📝 Task Walkthrough

### Task 1 — Data Governance Implementation

**Summary:** Designing a secure data ingestion pipeline for a medical AI system that processes patient records.

**Questions & Answers:**

| Question | Answer |
|----------|--------|
| What cryptographic mechanism ensures that a training dataset has not been tampered with between collection and training? | `Cryptographic hashing (SHA-256) with a signed manifest` |
| In a secure ML pipeline, at what stage should PII scrubbing occur relative to training? | `Before data enters the training pipeline — in the data sanitization stage` |
| What standard document describes every dependency in an ML software environment, including transitive packages? | `Software Bill of Materials (SBOM)` |

**Notes:**
> Data governance is a people + process + technology problem. The most sophisticated technical controls fail if a single data engineer can bypass them. Separation of duties — requiring two engineers to approve any bulk dataset change — is as important as any cryptographic control.

---

### Task 2 — Model Signing & Registry Security

**Summary:** Implementing a model signing workflow using Sigstore/Cosign and configuring a registry to reject unsigned or unsafe-format artifacts.

**Lab Commands:**
```bash
# Step 1: Generate signing keypair
cosign generate-key-pair

# Step 2: Train model, export as SafeTensors
python train.py --output-format safetensors

# Step 3: Sign the artifact
cosign sign-blob --key cosign.key \
  model_weights.safetensors \
  > model_weights.sig

# Step 4: Verify before deployment (automated in CI/CD)
cosign verify-blob --key cosign.pub \
  --signature model_weights.sig \
  model_weights.safetensors
```

**Questions & Answers:**

| Question | Answer |
|----------|--------|
| What open-source project provides keyless artifact signing and is used for both container and ML model provenance? | `Sigstore (Cosign)` |
| What serialization format should be mandated for ML model distribution to prevent Pickle RCE attacks? | `SafeTensors` |
| What event should trigger an automatic deployment rollback in a production ML system? | `Failed signature verification on the deployed model artifact` |

**Notes:**
> Model signing is the ML equivalent of code signing for executables. Remarkably, it's almost non-existent in practice — the vast majority of Hugging Face models have no cryptographic provenance. This is changing slowly with initiatives like Hugging Face's model signing feature, but adoption is still very low.

---

### Task 3 — Backdoor Detection Lab

**Summary:** Running Neural Cleanse against a suspicious classifier to identify a backdoor trigger.

**Lab Execution:**
```python
from neural_cleanse import NeuralCleanse

# Load the suspicious model
model = load_model("suspicious_classifier.h5")

# Run Neural Cleanse
nc = NeuralCleanse(model, num_classes=10)
results = nc.detect()

for class_id, result in results.items():
    print(f"Class {class_id}: L1 norm = {result['l1_norm']:.4f}")

# Output:
# Class 0: L1 norm = 1423.2
# Class 1: L1 norm = 1389.7
# Class 2: L1 norm = 156.3   ← ANOMALY: suspiciously small trigger
# Class 3: L1 norm = 1512.8
# ...

# Class 2 trigger reconstructed — a small 5x5 yellow patch
```

**Questions & Answers:**

| Question | Answer |
|----------|--------|
| Which class was identified as the backdoor target by Neural Cleanse? | `Class 2` |
| What metric does Neural Cleanse use to identify backdoored classes? | `L1 norm of the minimal trigger pattern — backdoored classes have anomalously small (efficient) triggers` |
| What does the Anomaly Index (AI) score from Neural Cleanse indicate when it exceeds 2.0? | `Strong statistical evidence of a backdoor trigger for that class` |

---

## 🚩 Flags / Final Answers

| Flag # | Value |
|--------|-------|
| Flag 1 (Data Governance) | `THM{d4t4_pr0v3n4nc3_s3cur3d}` |
| Flag 2 (Model Signing) | `THM{c0s1gn_m0d3l_s1gn3d_s4f3}` |
| Flag 3 (Backdoor Detection) | `THM{n3ur4l_cl34ns3_b4ckd00r_cl4ss2}` |

---

## 💡 Personal Takeaways

- **Secure AI supply chain = Secure traditional supply chain + 3 new layers**: data provenance, model signing, and backdoor detection. Organizations that have mature DevSecOps can adapt those controls with relatively modest additional effort — the tooling is increasingly available.
- **SafeTensors adoption should be a hard organizational mandate**, not a recommendation. The risk-reward calculation is simple: Pickle saves nothing, and costs everything if a poisoned model gets loaded. There is no legitimate reason to use Pickle for model distribution in 2025.
- **Backdoor detection should be part of every model evaluation pipeline** — especially for externally sourced or fine-tuned models. Neural Cleanse and Activation Clustering are computationally cheap compared to the cost of shipping a backdoored model to production.
- The **regulatory landscape is converging** on supply chain requirements. The EU AI Act mandates technical documentation and traceability. NIST AI RMF requires governance over training data. SLSA (Supply Chain Levels for Software Artifacts) is increasingly being applied to ML. Proactive compliance with these frameworks is both a security and business imperative.

---

## 🔗 Additional Resources

- [Sigstore / Cosign](https://docs.sigstore.dev/)
- [DVC — Data Version Control](https://dvc.org/)
- [Neural Cleanse — Backdoor Detection](https://github.com/bolunwang/backdoor)
- [NIST AI Risk Management Framework](https://www.nist.gov/artificial-intelligence/ai-risk-management-framework)
- [SLSA — Supply Chain Security Framework](https://slsa.dev/)
- [Protect AI — Security Tools for MLOps](https://protectai.com/)

---

> ⬅️ [Previous Room](../02-supply-chain-attack-vectors/README.md) | [Back to Main](../../README.md) | [Next Room](../04-payload/README.md) ➡️
