# 🏠 AI System Reconnaissance — TryHackMe AI Security Path

> **Section:** Section 2 — Secure AI Systems | **Difficulty:** Medium | **Type:** Lab  
> **Room Link:** [https://tryhackme.com/room/ai-system-reconnaissance](https://tryhackme.com/room/ai-system-reconnaissance)  
> **Completed:** 24/05/2026

---

## 📋 Room Overview

This highly interactive lab room focuses on the attacker's perspective: how do threat actors identify, enumerate, and fingerprint Artificial Intelligence systems in the wild? We step into the shoes of a red teamer tasked with mapping out a target company's exposed ML infrastructure, identifying API endpoints, and extracting metadata to determine exactly which models are running under the hood.

**What you'll learn:**
- Passive and Active reconnaissance techniques for ML systems.
- Enumerating exposed AI APIs and undocumented endpoints.
- Model fingerprinting: Analyzing token limits, output formatting, and specific hallucination patterns to identify the underlying LLM or ML architecture.
- Extracting model metadata and sensitive configuration details.

---

## 🧠 Key Concepts

### ML Endpoint Enumeration
AI applications are frequently deployed rapidly, leaving poorly secured API endpoints exposed. Reconnaissance involves scanning for common endpoints (e.g., `/api/v1/predict`, `/v1/chat/completions`, `/generate`) and identifying the frameworks in use (like TensorFlow Serving, TorchServe, or FastAPI LLM wrappers).

### Model Fingerprinting
Different LLMs and ML models exhibit distinct characteristics. By sending specific test payloads, an attacker can analyze the response latency, error messages, token limits, and exact phrasing to deduce whether the backend is using OpenAI's GPT-4, Anthropic's Claude, a Llama 3 variant, or a custom Hugging Face deployment.

### Metadata Extraction
Misconfigured ML endpoints often leak critical metadata. This can include the exact framework versions, internal model paths, hardware architecture (GPU types), and sometimes even fragments of the system prompt or training data references in error traces.

---

## 📝 Task Walkthrough

### Task 1 — Passive Recon & OSINT

**Summary:** Using OSINT to map the target's AI footprint before sending a single packet.

**Questions & Answers:**

| Question | Answer |
|----------|--------|
| What public repository platform is frequently used to hunt for exposed model weights and ML code? | `Hugging Face` |
| What search dork can be used to find exposed Jupyter Notebooks containing ML pipeline configurations? | `inurl:8888/tree` |

**Notes:**
> Never underestimate OSINT. Developers often discuss their ML tech stack on GitHub issues, StackOverflow, or even LinkedIn. A job posting asking for "Experience with TorchServe and Llama 2" gives away the entire backend architecture.

---

### Task 2 — Active Enumeration of ML APIs

**Summary:** Using `ffuf` and custom wordlists to discover hidden prediction endpoints on the target server.

**Questions & Answers:**

| Question | Answer |
|----------|--------|
| What is the hidden API endpoint discovered during the directory brute-force? | `/api/v2/model_predict` |
| Which HTTP method is predominantly used to interact with inference endpoints? | `POST` |

**Notes:**
> Standard web wordlists are often insufficient for AI recon. Custom wordlists containing terms like `inference`, `predict`, `embed`, `tokenize`, and `weights` are essential for modern bug bounty hunting.

---

### Task 3 — Fingerprinting the Target Model

**Summary:** Interacting with the discovered API to determine the exact model architecture in use.

**Questions & Answers:**

| Question | Answer |
|----------|--------|
| By analyzing the exact system prompt leakage, what base model is the target utilizing? | `Llama-3-8B-Instruct` |
| What is the value of the `x-model-backend` header found in the verbose error response? | `vLLM` |

**Notes:**
> Prompting a model with extreme edge cases (like asking it to output a specific, obscure mathematical constant or utilizing highly specific formatting instructions) can quickly differentiate between closed-source API wrappers and open-weight models.

---

## 🚩 Flags / Final Answers

| Flag # | Value |
|--------|-------|
| Flag 1 (Hidden Endpoint) | `THM{recon_m1_api_d1sc0v3ry}` |
| Flag 2 (Model Extraction) | `THM{llm_f1ng3rpr1nt_succ3ss}` |
| Flag 3 (Metadata Leak) | `THM{v3rb0s3_3rr0rs_l3ak_data}` |

---

## 💡 Personal Takeaways

- The sheer amount of information you can glean just from error messages and response headers on an ML API is staggering. Developers often leave debug modes on in production AI deployments.
- Model fingerprinting is an art form. Understanding the specific 'quirks' of different LLM families allows an attacker to perfectly tailor their subsequent prompt injection or jailbreak payloads.
- AI Reconnaissance requires a blend of traditional web application penetration testing and deep knowledge of modern ML deployment stacks (like vLLM, Ollama, and Triton Inference Server).

---

## 🔗 Additional Resources

- [SecLists - API Wordlists](https://github.com/danielmiessler/SecLists/tree/master/Discovery/Web-Content)
- [Hugging Face Security Guidelines](https://huggingface.co/docs/hub/security)

---

> ⬅️ [Previous Room](../03-ai-threat-modelling/README.md) | [Back to Main](../../README.md) | [Next Room](../05-ai-threat-modelling-assessment/README.md) ➡️
