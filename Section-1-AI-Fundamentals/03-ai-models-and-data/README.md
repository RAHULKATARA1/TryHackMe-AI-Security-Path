# 🗄️ Room 03 — AI Models & Data

> **Section:** AI Fundamentals (Section 1 of 5)
> **Difficulty:** Medium
> **Type:** Theory + Practical Lab
> **Room Link:** [https://tryhackme.com/room/aimodelsdata](https://tryhackme.com/room/aimodelsdata)
> **Completed:** 19/05/2026

---

## 📋 Room Overview

A deep dive into where AI training data comes from, why it matters for security, and what risks are baked into models before they're ever deployed. Covers data provenance, PII in training data, key model-building decisions, the inheritance problem with fine-tuning, and why trained models are opaque black boxes.

**What you'll learn:**
- Why poor data provenance is a serious security risk
- How PII and live credentials end up permanently embedded in model weights
- How overfitting, quantisation, and federated learning each introduce security trade-offs
- Why fine-tuning a pre-trained model inherits all its hidden risks
- What model cards are and why their absence is a red flag

---

## 🧠 Key Concepts

### Data Provenance
The ability to answer three questions about any piece of training data: where did it come from, when was it collected, and has it been modified? In practice, most organisations deploying AI today cannot fully answer any of these. The Data Provenance Initiative audited 1,800+ datasets and found over 70% of licences listed as "Unspecified."

### Common Crawl
The most widely used public corpus underpinning essentially every major model family. It's a massive web scrape — 400TB+ — and Truffle Security found nearly **12,000 live, verified API keys and passwords** in the December 2024 archive alone. Once in the training data, credentials can be surfaced by prompting the model — and no patch fixes it post-deployment.

### ML-BOM (Machine Learning Bill of Materials)
The AI equivalent of a Software Bill of Materials (SBOM). A documented inventory of dataset sources, licences, PII categories, and filtering decisions. Adoption is still early; most organisations have nothing close to one.

### Key Model-Building Concepts
| Concept | Definition | Security Risk |
|---------|-----------|---------------|
| Epoch | One complete pass of training data through the algorithm | More epochs → more overfitting risk |
| Overfitting | Model memorises training data instead of learning patterns | Can reproduce sensitive training data verbatim when prompted |
| Quantisation | Reduces numerical precision of weights to cut memory/compute | Security trade-offs rarely documented; inherits unknown behaviour |
| Federated Learning | Trains across decentralised devices; only weight updates sent centrally | Participants can submit poisoned gradient updates — very hard to detect |
| Validation Set | Held-back data never used in training, used to catch overfitting | Skipping it = unknown real-world behaviour = security risk |

### The Inheritance Problem
Fine-tuning a pre-trained model means inheriting **everything** beneath it:
- Biases baked in during pre-training persist
- Safety alignment erodes — Stanford/Princeton found it can be broken with as few as **10 adversarially crafted fine-tuning examples** at under $0.20
- Fine-tuned models are **measurably more susceptible to prompt injection** than their base models (Cisco research)
- Fine-tuning always targets a specific checkpoint — if that checkpoint had a backdoor, every derivative inherits it

### The Black Box Problem
Trained model weights are billions of floating-point numbers with no human-readable record of what shaped them. You cannot audit a model the way you audit code. Security testing can only **sample behaviour** — it cannot audit the full attack surface. Model cards are the primary transparency mechanism, but they remain voluntary, frequently incomplete, or entirely absent.

---

## 📝 Task Walkthrough

### Task 1 — Introduction

| Question | Answer |
|----------|--------|
| I understand the learning objectives and am ready to learn about AI models and data! | `No answer needed` |

---

### Task 2 — Training Data

| Question | Answer |
|----------|--------|
| What term describes the ability to answer where data came from, when it was collected, and whether it has been modified? | `Data Provenance` |
| What is the name of the most widely used public corpus that underpins essentially every major model family? | `Common Crawl` |
| What is the AI equivalent of a Software Bill of Materials (SBOM), used to document dataset sources, licenses, and filtering decisions? | `ML-BOM` |

---

### Task 3 — Building the Model

| Question | Answer |
|----------|--------|
| What term describes one complete pass of the training algorithm through the entire dataset? | `Epoch` |
| What problem occurs when a model memorises training data rather than learning general patterns? | `Overfitting` |
| What post-training optimisation technique reduces the numerical precision of model weights to cut memory and compute requirements? | `Quantisation` |
| What training approach trains a model across decentralised devices, sending only weight updates rather than raw data to a central server? | `Federated Learning` |

---

### Task 4 — The Inheritance Problem

| Question | Answer |
|----------|--------|
| What is the process of taking a pre-trained model and continuing to train it on a smaller, task-specific dataset? | `Fine-tuning` |
| What term describes a model that has already been trained on a large general-purpose dataset? | `Pre-trained Model` |

---

### Task 5 — The Black Box Problem

| Question | Answer |
|----------|--------|
| What documentation artifact accompanies a model to describe what it is, how it was built, and where it falls short? | `Model Card` |
| What are the billions of floating-point numbers that make up a trained model collectively referred to as? | `Weights` |

---

### Task 6 — Practical (Model Card Audit)

A simulated HuggingFace-style model repository. Perform a security audit of a model card, identifying red flags across metadata, file listings, and training details. Each finding is rated by severity.

**Red flags found and their severities:**

| Finding | Severity |
|---------|----------|
| Training data from publicly available web sources | High |
| Training data includes forums and Q&A sites | High |
| Base model: `enterprise-base-v1.1` (unverified) | Medium |
| Macro-averaged F1 score of 0.91 only (limited eval) | Medium |
| Custom licence — contact vendor for terms | Medium |
| Model file size: 268 MB (unexpectedly small) | Medium |

| Question | Answer |
|----------|--------|
| Complete the exercise to get the flag! | `THM{A_m0del_Stud3nt}` |

---

### Task 7 — Conclusion

| Question | Answer |
|----------|--------|
| All Done! | `No answer needed` |

---

## 🚩 Flags

| Flag | Value |
|------|-------|
| Task 6 — Model Card Audit | `THM{A_m0del_Stud3nt}` |

---

## 💡 Personal Takeaways

- "The risks don't begin when a model is deployed. They begin long before." — the data supply chain is as real as the software supply chain
- A missing or vague model card is a security warning, not a minor inconvenience
- Federated learning solves the data privacy problem but creates a new gradient poisoning problem — security trade-offs always stack
- Fine-tuning is powerful but doesn't sanitise what's beneath it; you inherit the base model's entire history
- *(Add your own thoughts after completing the room)*

---

## 🔗 Additional Resources

- [Data Provenance Initiative](https://www.dataprovenance.org/)
- [Truffle Security — Credentials in Common Crawl](https://trufflesecurity.com/blog/research-finds-12-000-live-api-keys-and-passwords-in-deepseek-s-training-data)
- [Stanford/Princeton — Fine-tuning breaks safety alignment](https://arxiv.org/abs/2310.03693)
- [Cisco — Fine-tuned models & prompt injection](https://blogs.cisco.com/security/fine-tuning-llms-breaks-their-safety-and-security-alignment)
- [HuggingFace Model Cards](https://huggingface.co/docs/hub/model-cards)

---

> ⬅️ [Previous Room](../02-ai-ml-security-threats/README.md) | [Back to Main](../../README.md) | [Next Room → Prompt Engineering](../04-prompt-engineering/README.md) ➡️
