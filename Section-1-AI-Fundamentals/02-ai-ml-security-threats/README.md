# 🤖 Room 02 — AI/ML Security Threats

> **Section:** AI Fundamentals (Section 1 of 5)
> **Difficulty:** Easy
> **Type:** Theory
> **Room Link:** [https://tryhackme.com/room/aimlsecuritythreats](https://tryhackme.com/room/aimlsecuritythreats)
> **Completed:** 19/05/2026

---

## 📋 Room Overview

An entry-level introduction to AI, Machine Learning, and Deep Learning, and how these technologies are being exploited by attackers and leveraged by defenders. No prior AI knowledge required, but basic cybersecurity understanding is assumed.

**What you'll learn:**
- The building blocks of AI: ML, DL, neural networks, and LLMs
- How adversaries exploit AI models (prompt injection, data poisoning, model theft, deepfakes)
- How defenders use AI for log analysis, phishing detection, and threat hunting
- The MITRE ATLAS framework for mapping AI-specific threats

---

## 🧠 Key Concepts

### AI → ML → DL → LLMs (The Stack)
AI is the broad goal of making machines reason like humans. ML is a subset where computers learn from data without explicit instructions. Deep Learning (DL) is a subset of ML using multi-layered neural networks that can self-teach from raw, unlabelled data. Large Language Models (LLMs) like ChatGPT, LLaMA, and DeepSeek sit at the top — they're DL models using **transformer** architectures trained on massive datasets to predict and generate text. Training involves three stages: pre-training → fine-tuning → RLHF (Reinforcement Learning from Human Feedback).

### ML Algorithm Types
| Type | How it learns |
|------|--------------|
| Supervised | Labelled data — learns input→output mappings |
| Unsupervised | Unlabelled data — finds hidden patterns |
| Semi-supervised | Mix of both labelled and unlabelled data |
| Reinforcement | Learns through reward/penalty feedback loops |
| Deep Learning | No human-labelled data needed; extracts features from raw unstructured input |

### AI Security Threats (Two Categories)

**1. Vulnerabilities in AI Models (new threats):**
| Threat | Description |
|--------|-------------|
| Prompt Injection | Overriding original model instructions to leak info or produce harmful output |
| Data Poisoning | Corrupting training data to bias or break the model |
| Model Theft | Cloning a model by querying its API to replicate its behaviour |
| Privacy Leakage | Model unintentionally reveals sensitive data from training |
| Model Drift | Accuracy degrades over time as real-world data shifts away from training data |

**2. AI-enhanced existing attacks:**
| Attack | AI's Role |
|--------|-----------|
| Malware | AI generates polymorphic, evasion-capable malicious code instantly |
| Deepfakes | Realistic fake audio/video used for fraud, social engineering, CEO scams |
| Phishing | AI generates fluent, personalised, error-free phishing emails at scale |

### MITRE ATLAS
The framework developed by MITRE specifically for mapping adversary tactics and techniques against AI/ML systems. Think of it as ATT&CK but for AI.

### Defensive AI
- **Analyse:** Detect anomalies in logs and network traffic using ML
- **Predict:** Flag phishing emails and block threats before they land
- **Summarise:** Digest incident reports and threat intelligence rapidly
- **Investigate:** Use LLMs for log triage, threat hunting ideas, and detection rule generation
- According to IBM, AI adoption can **save $2.2M** and cut breach response time by **108 days**
- Explainability tools like **SHAP** and **LIME** are used for model monitoring

---

## 📝 Task Walkthrough

### Task 1 — Introduction

| Question | Answer |
|----------|--------|
| I'm ready to learn about AI/ML security threats! | `No answer needed` |

---

### Task 2 — The Building Blocks of AI

| Question | Answer |
|----------|--------|
| What category of machine learning combines both labelled and unlabelled data? | `semi-supervised learning` |
| What is the first layer in a neural network that handles incoming raw data? | `input layer` |
| Which learning method does not require human-labeled data and can extract features from raw, unstructured input? | `deep learning` |
| What are the weighted connections between nodes in a neural network meant to simulate in the human brain? | `synapses` |

---

### Task 3 — LLMs

| Question | Answer |
|----------|--------|
| What type of AI model enabled major advancements in ChatGPT and similar tools? | `large language models` |
| What is the first training stage where an LLM processes massive amounts of data? | `pre-training` |
| What type of neural network introduced by Google in 2017 powers modern LLMs? | `transformer` |

---

### Task 4 — AI Security Threats

| Question | Answer |
|----------|--------|
| What framework was developed by MITRE to guide the understanding of AI-specific cyber threats? | `ATLAS` |
| What type of attack involves cloning an AI model by interacting with its API? | `model theft` |
| What generative AI technique can replicate a person's voice or appearance with high realism? | `deepfake` |
| What common social engineering attack has become harder to detect due to AI-generated fluent messages? | `phishing` |

---

### Task 5 — Defensive AI

| Question | Answer |
|----------|--------|
| According to IBM, how many days faster does AI help identify and contain breaches? | `108` |
| What cybersecurity task benefits from AI helping to imagine attacker behavior we might not consider? | `threat hunting` |
| Explainability tools such as SHAP and LIME help with what? | `model monitoring` |

---

### Task 6 — Practical (AI Cybersecurity Sidekick)

Use the in-room AI assistant to answer questions about networking values. The flag format is `thm{DoH_port/SYN_flood_timeout/ephemeral_port_range_size}`.

| Question | Answer |
|----------|--------|
| What's the flag? | `thm{443/60/16384}` |

**Notes:**
> DNS over HTTPS (DoH) uses port **443**. SYN flood timeout is **60** seconds. Windows ephemeral port range size is **16384**.

---

## 🚩 Flags

| Flag | Value |
|------|-------|
| Task 6 Flag | `thm{443/60/16384}` |

---

## 💡 Personal Takeaways

- The AI security threat landscape splits cleanly into: attacks *on* AI models vs. attacks *enhanced by* AI — keep this mental model
- MITRE ATLAS is to AI security what ATT&CK is to traditional security — learn it early
- Only 24% of generative AI initiatives are properly secured — massive opportunity for security professionals
- AI is both the weapon and the shield; the goal is to adopt it faster and more securely than attackers do
- *(Add your own thoughts after completing the room)*

---

## 🔗 Additional Resources

- [MITRE ATLAS](https://atlas.mitre.org/) — AI/ML adversary tactics matrix
- [IBM Cost of a Data Breach Report](https://www.ibm.com/reports/data-breach)
- [SHAP (Explainability)](https://shap.readthedocs.io/en/latest/)
- [LIME (Explainability)](https://github.com/marcotcr/lime)
- [TryHackMe — Demystifying LLMs Room](https://tryhackme.com/room/demystifyingllms)

---

> ⬅️ [Previous Room](../01-ai-security-path-ticketing-event/README.md) | [Back to Main](../../README.md) | [Next Room → AI Models & Data](../03-ai-models-and-data/README.md) ➡️
