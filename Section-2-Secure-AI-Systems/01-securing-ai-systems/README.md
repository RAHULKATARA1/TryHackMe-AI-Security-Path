# 🏠 Securing AI Systems — TryHackMe AI Security Path

> **Section:** Section 2 — Secure AI Systems | **Difficulty:** Medium | **Type:** Theory  
> **Room Link:** [https://tryhackme.com/room/securing-ai-systems](https://tryhackme.com/room/securing-ai-systems)  
> **Completed:** 24/05/2026

---

## 📋 Room Overview

This room serves as the foundational pillar for understanding how to secure Artificial Intelligence and Machine Learning pipelines. Unlike traditional software, AI systems introduce non-deterministic behaviors, reliance on massive datasets, and complex supply chains that require a paradigm shift in security thinking. We explore the NIST AI Risk Management Framework (RMF) and the core tenets of AI defense.

**What you'll learn:**
- The fundamental differences between IT security and AI security.
- The AI development lifecycle (from data collection to model deployment) and its inherent risks.
- Key attack vectors against ML systems including evasion, poisoning, and model inversion.
- Implementing defense-in-depth strategies for AI architectures.

---

## 🧠 Key Concepts

### The AI Lifecycle & Attack Surface
AI security isn't just about protecting the deployed API; it requires securing the entire pipeline. The lifecycle includes Data Collection, Data Preprocessing, Model Training, Evaluation, and Deployment. Each phase has unique vulnerabilities: training data can be poisoned, evaluation metrics can be manipulated, and deployed models can be subjected to adversarial inputs.

### Adversarial Machine Learning (AML)
AML focuses on the vulnerabilities of ML algorithms. The main categories include:
- **Evasion Attacks:** Modifying input data subtly (e.g., changing pixels in an image) so the model misclassifies it, without human detection.
- **Data Poisoning:** Injecting malicious data into the training set to create backdoors or degrade overall performance.
- **Model Inversion/Extraction:** Querying the model repeatedly to reverse-engineer its training data or steal the model weights.

### AI Risk Management Frameworks
Frameworks like the NIST AI RMF provide structured approaches to managing AI risks. It emphasizes four core functions: Map, Measure, Manage, and Govern. Security teams must adapt these frameworks to systematically identify and mitigate risks specific to their AI deployments.

---

## 📝 Task Walkthrough

### Task 1 — Introduction to AI Security

**Summary:** An overview of why AI introduces novel security challenges compared to deterministic software.

**Questions & Answers:**

| Question | Answer |
|----------|--------|
| What property of AI systems makes them difficult to secure using traditional rules-based firewalls? | `Non-determinism` |
| Which NIST framework is explicitly designed to help organizations manage AI-related risks? | `NIST AI RMF` |

**Notes:**
> Traditional security relies on predictable inputs and outputs. AI's probabilistic nature means defenses must focus on input validation, behavior monitoring, and robust training rather than just signature-based blocking.

---

### Task 2 — The Machine Learning Pipeline

**Summary:** Breaking down the ML pipeline to understand where vulnerabilities can be introduced.

**Questions & Answers:**

| Question | Answer |
|----------|--------|
| In which phase of the ML pipeline is a data poisoning attack executed? | `Data Collection` |
| What type of attack occurs when an adversary queries a deployed model to replicate its functionality? | `Model Extraction` |

**Notes:**
> Securing the pipeline means implementing zero-trust principles at every stage. Data provenance is critical—if you can't trust the source of your training data, you can't trust the model.

---

### Task 3 — Defensive Strategies

**Summary:** Exploring how to protect AI systems against adversarial attacks.

**Questions & Answers:**

| Question | Answer |
|----------|--------|
| What defensive technique involves training a model with adversarial examples to make it more robust? | `Adversarial Training` |
| Which principle dictates that AI systems should only be given access to the data they strictly need to function? | `Least Privilege` |

**Notes:**
> Defense-in-depth is crucial. Adversarial training hardens the model, but network-level API rate limiting prevents model extraction, and robust access controls protect the training data.

---

## 💡 Personal Takeaways

- The realization that an AI model is essentially a reflection of its training data; securing the data pipeline is just as critical as securing the application logic.
- Adversarial examples are fascinating—the mathematical precision required to flip a model's classification while remaining invisible to the human eye highlights the fragility of deep neural networks.
- The NIST AI RMF is a highly practical tool for standardizing AI security assessments, shifting the focus from ad-hoc patching to systemic risk management.

---

## 🔗 Additional Resources

- [NIST AI Risk Management Framework](https://www.nist.gov/itl/ai-risk-management-framework)
- [Adversarial Robustness Toolbox (ART)](https://github.com/Trusted-AI/adversarial-robustness-toolbox)

---

> ⬅️ [Previous Room](../../Section-1-AI-Fundamentals/06-containment/README.md) | [Back to Main](../../README.md) | [Next Room](../02-llm-security/README.md) ➡️
