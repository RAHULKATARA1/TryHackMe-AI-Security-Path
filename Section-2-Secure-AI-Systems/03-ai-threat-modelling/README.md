# 🏠 AI Threat Modelling — TryHackMe AI Security Path

> **Section:** Section 2 — Secure AI Systems | **Difficulty:** Medium | **Type:** Theory  
> **Room Link:** [https://tryhackme.com/room/ai-threat-modelling](https://tryhackme.com/room/ai-threat-modelling)  
> **Completed:** 24/05/2026

---

## 📋 Room Overview

This room tackles the crucial engineering practice of AI Threat Modelling. We explore how to systematically identify, categorize, and mitigate threats before an AI system is even built. The room bridges traditional methodologies like STRIDE with AI-specific frameworks like MITRE ATLAS, providing a comprehensive toolkit for secure AI architecture design.

**What you'll learn:**
- Adapting traditional threat modelling (STRIDE, PASTA) for Machine Learning systems.
- Navigating and utilizing the MITRE ATLAS (Adversarial Threat Landscape for AI Systems) matrix.
- Identifying trust boundaries, data flows, and assets unique to AI infrastructures.
- Developing threat scenarios and actionable mitigation strategies.

---

## 🧠 Key Concepts

### Adapting STRIDE for AI
STRIDE (Spoofing, Tampering, Repudiation, Information Disclosure, Denial of Service, Elevation of Privilege) must be adapted for AI. 
- **Tampering** now includes Data Poisoning.
- **Information Disclosure** encompasses Model Inversion and Training Data Extraction.
- **Denial of Service** involves Sponge Attacks (complex inputs designed to exhaust GPU/CPU resources during inference).

### MITRE ATLAS
MITRE ATLAS is a knowledge base of adversary tactics, techniques, and case studies for machine learning systems, analogous to MITRE ATT&CK. It provides a taxonomy for AI-specific attacks, spanning from Initial Access (e.g., compromising the ML supply chain) to Impact (e.g., Evading ML models).

### Trust Boundaries in ML
In AI systems, trust boundaries are complex. You must consider the boundaries between the data engineering pipeline, the model training environment (often requiring high-compute clusters with broad access), the model registry, and the inference API. The connection to external data sources (APIs, web scraping) represents a massive, often untrusted boundary.

---

## 📝 Task Walkthrough

### Task 1 — Principles of AI Threat Modelling

**Summary:** Understanding why AI requires specialized threat modelling approaches.

**Questions & Answers:**

| Question | Answer |
|----------|--------|
| Which threat modelling methodology focuses on evaluating threats from an attacker-centric perspective using the acronym Spoofing, Tampering, etc.? | `STRIDE` |
| In the context of AI, an attacker modifying the weights of a compiled model is an example of which STRIDE category? | `Tampering` |

**Notes:**
> The most critical step in AI threat modelling is creating an accurate Data Flow Diagram (DFD). If you don't map out exactly where the training data comes from and where the telemetry data goes, you will miss critical attack vectors.

---

### Task 2 — Navigating MITRE ATLAS

**Summary:** Using the ATLAS matrix to categorize specific AI attacks.

**Questions & Answers:**

| Question | Answer |
|----------|--------|
| According to MITRE ATLAS, acquiring public ML models to analyze them for vulnerabilities falls under which tactic? | `Reconnaissance` |
| What ATLAS technique involves an attacker slightly altering an image so a facial recognition system misidentifies them? | `Evade ML Model` |

**Notes:**
> MITRE ATLAS is invaluable for communicating AI risks to traditional SOC teams. By mapping AI attacks to ATT&CK-style tactics, it makes the abstract nature of ML vulnerabilities concrete and actionable.

---

### Task 3 — Building a Threat Model

**Summary:** A practical exercise in mapping out a GenAI chatbot's architecture and identifying its weak points.

**Questions & Answers:**

| Question | Answer |
|----------|--------|
| If an AI system logs all user interactions, including potential PII, without encryption, this is a violation of which security principle? | `Confidentiality` |
| What is the primary mitigation against an attacker exploiting the model inference API to cause a Denial of Service via complex prompts? | `Rate Limiting and Input Truncation` |

**Notes:**
> Threat modelling should be an iterative process. As the model updates and new data sources are integrated, the threat model must be revised to account for the expanded attack surface.

---

## 💡 Personal Takeaways

- Threat modelling for AI is fundamentally an exercise in mapping out the supply chain and data provenance. The model itself is often just the tip of the iceberg.
- Integrating MITRE ATLAS alongside traditional frameworks like STRIDE provides a perfect blend of high-level architectural security and deep, ML-specific adversarial analysis.
- Sponge attacks (resource exhaustion via computationally expensive inputs) are a highly underrated threat. AI inference is expensive; unmitigated access can lead to massive financial DoS.

---

## 🔗 Additional Resources

- [MITRE ATLAS](https://atlas.mitre.org/)
- [Threat Modeling Manifesto](https://www.threatmodelingmanifesto.org/)

---

> ⬅️ [Previous Room](../02-llm-security/README.md) | [Back to Main](../../README.md) | [Next Room](../04-ai-system-reconnaissance/README.md) ➡️
