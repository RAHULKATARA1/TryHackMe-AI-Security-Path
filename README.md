# 🤖🔐 TryHackMe — AI Security Path

> **Hands-on notes, attack walkthroughs, and defensive implementations across 26 rooms of offensive & defensive AI security.**

<div align="center">

![TryHackMe](https://img.shields.io/badge/TryHackMe-AI%20Security%20Path-red?style=for-the-badge&logo=tryhackme)
![Rooms Done](https://img.shields.io/badge/Rooms%20Documented-26%2F26-brightgreen?style=for-the-badge)
![Sections](https://img.shields.io/badge/Sections%201--5-100%25%20Complete-success?style=for-the-badge)
![Difficulty](https://img.shields.io/badge/Difficulty-Medium%20%E2%80%93%20Hard-orange?style=for-the-badge)
![Focus](https://img.shields.io/badge/Focus-Red%20Team%20%7C%20Blue%20Team%20%7C%20MLSecOps-blueviolet?style=for-the-badge)

</div>

---

## 🎯 What This Repository Is

This is a **technical write-up repository** for the [TryHackMe AI Security Path](https://tryhackme.com/path/outline/aisecurity) — a 26-room learning path covering the full attack and defence surface of modern AI systems.

Every room in the path is documented with:
- **Deep-dive concept breakdowns** — not surface-level summaries, but first-principles analysis
- **Attack walkthroughs** — real payloads, tooling, and step-by-step methodology
- **Defensive implementations** — working code, architecture diagrams, and security patterns
- **CTF writeups** — complete multi-stage flag capture chains with attributed techniques

> ⚠️ **Disclaimer:** All techniques documented here are for educational purposes only, performed in authorized lab environments. Never apply offensive techniques outside of explicitly authorized targets.

---

## 🧠 Technical Skills Demonstrated

### 🔴 Offensive AI Security
| Skill | Rooms Practised |
|-------|----------------|
| **Prompt Injection** (direct & indirect, multi-modal, agentic tool hijacking) | Rooms 12, 15, 16 |
| **LLM Jailbreaking** (DAN, GCG adversarial suffixes, many-shot, encoding bypasses) | Room 13 |
| **RAG Poisoning** (disinformation, instruction injection, relevance hijacking) | Rooms 22, 23, 25 |
| **Sensitive Data Exfiltration from RAG** (semantic fuzzing, membership inference, embedding inversion) | Rooms 24, 25 |
| **ML Supply Chain Attacks** (Pickle RCE, backdoor triggers, dependency confusion) | Rooms 18, 20 |
| **MLOps Pipeline Compromise** (CI/CD abuse, registry poisoning, training data tampering) | Room 20 |
| **Multi-Agent Attack Chains** (cross-agent lateral movement, steganographic bypass) | Room 16 |
| **AI System Reconnaissance** (model fingerprinting, endpoint enumeration, metadata extraction) | Room 10 |

### 🔵 Defensive AI Security
| Skill | Rooms Practised |
|-------|----------------|
| **Prompt Defence Engineering** (hardened system prompts, canary tokens, dual-LLM architecture) | Room 14 |
| **Guardrails Implementation** (NeMo Guardrails, LlamaGuard, semantic anomaly detection) | Room 14 |
| **RAG Access Control** (document-level RBAC, metadata filtering, cross-tenant isolation) | Rooms 22, 26 |
| **Knowledge Base Sanitization** (semantic outlier detection, injection pattern scanning) | Room 26 |
| **Model Provenance & Signing** (Sigstore/Cosign, SafeTensors enforcement, SBOM) | Room 19 |
| **Backdoor Detection** (Neural Cleanse, activation clustering, spectral signature analysis) | Rooms 19, 21 |
| **AI Incident Response** (forensic attribution via S3/MLflow/GitHub Actions logs, rollback) | Room 21 |
| **AI Threat Modelling** (STRIDE for ML, MITRE ATLAS, trust boundary mapping) | Rooms 9, 11 |

### 🛠️ Tools & Frameworks Used
`modelscan` · `pickletools` · `NVIDIA NeMo Guardrails` · `LlamaGuard` · `Neural Cleanse` · `Sigstore/Cosign` · `MLflow` · `LangChain` · `Chroma` · `MITRE ATLAS` · `DVC` · `pip-audit` · `ffuf` · `cosign` · `Python` · `AWS CLI` · `GitHub Actions`

---

## 🗺️ Path Structure & Progress

### 📦 Section 1 — AI Fundamentals ✅

| # | Room | Type | Key Skills |
|---|------|------|-----------|
| 1 | [AI Security Path Ticketing Event](./Section-1-AI-Fundamentals/01-ai-security-path-ticketing-event/README.md) | Intro | Path introduction, lab environment setup |
| 2 | [AI/ML Security Threats](./Section-1-AI-Fundamentals/02-ai-ml-security-threats/README.md) | Theory | High-level threat taxonomy, attack surface overview |
| 3 | [AI Models & Data](./Section-1-AI-Fundamentals/03-ai-models-and-data/README.md) | Theory | ML lifecycle, data privacy, model architectures |
| 4 | [Prompt Engineering](./Section-1-AI-Fundamentals/04-prompt-engineering/README.md) | Theory | System prompts, in-context learning, few-shot prompting |
| 5 | [AI Forensics](./Section-1-AI-Fundamentals/05-ai-forensics/README.md) | Theory | AI incident response basics, audit logs, tracking |
| 6 | [ContAInment](./Section-1-AI-Fundamentals/06-containment/README.md) | Lab | Introductory prompt injection, basic defensive instructions |

---

### 🔒 Section 2 — Secure AI Systems ✅

| # | Room | Type | Key Skills |
|---|------|------|-----------|
| 7 | [Securing AI Systems](./Section-2-Secure-AI-Systems/01-securing-ai-systems/README.md) | Theory | AI security fundamentals, threat surface mapping |
| 8 | [LLM Security](./Section-2-Secure-AI-Systems/02-llm-security/README.md) | Theory | OWASP LLM Top 10, prompt injection mechanics, SSRF via plugins |
| 9 | [AI Threat Modelling](./Section-2-Secure-AI-Systems/03-ai-threat-modelling/README.md) | Theory | STRIDE for ML, MITRE ATLAS, trust boundary analysis |
| 10 | [AI System Reconnaissance](./Section-2-Secure-AI-Systems/04-ai-system-reconnaissance/README.md) | Lab | Model fingerprinting, API endpoint enumeration, metadata extraction |
| 11 | [AI Threat Modelling Assessment](./Section-2-Secure-AI-Systems/05-ai-threat-modelling-assessment/README.md) | Assessment | End-to-end threat model for a healthcare LLM deployment |

---

### 💉 Section 3 — Prompt Security ✅

| # | Room | Type | Key Skills |
|---|------|------|-----------|
| 12 | [Prompt Injection](./Section-3-Prompt-Security/01-prompt-injection/README.md) | Theory + Lab | Direct/indirect injection, tool hijacking, multi-modal attacks |
| 13 | [Jailbreaking](./Section-3-Prompt-Security/02-jailbreaking/README.md) | Theory + Lab | DAN, adversarial suffixes (GCG), many-shot, token-level bypasses |
| 14 | [Prompt Defence](./Section-3-Prompt-Security/03-prompt-defence/README.md) | Theory + Lab | NeMo Guardrails, dual-LLM architecture, semantic anomaly detection |
| 15 | [LLMborghini](./Section-3-Prompt-Security/04-llmborghini/README.md) | CTF | 5-stage agentic AI attack chain: prompt extraction → CRM exfiltration → tool hijack |
| 16 | [White Rabbit](./Section-3-Prompt-Security/05-white-rabbit/README.md) | CTF | 4-hop multi-agent chain: GatekeeperAI → OracleAI → ArchiVist → flag server |

---

### ⛓️ Section 4 — AI Supply Chain Security ✅

| # | Room | Type | Key Skills |
|---|------|------|-----------|
| 17 | [Understanding AI Supply Chains](./Section-4-AI-Supply-Chain-Security/01-understanding-ai-supply-chains/README.md) | Theory | Supply chain anatomy, Pickle/ONNX risks, ShadowRay case study |
| 18 | [Supply Chain Attack Vectors](./Section-4-AI-Supply-Chain-Security/02-supply-chain-attack-vectors/README.md) | Theory | Pickle RCE, backdoor triggers, dependency confusion, MLOps attacks |
| 19 | [Securing the AI Supply Chain](./Section-4-AI-Supply-Chain-Security/03-securing-the-ai-supply-chain/README.md) | Theory | Sigstore signing, SafeTensors, SBOM, Neural Cleanse, SLSA |
| 20 | [Payload](./Section-4-AI-Supply-Chain-Security/04-payload/README.md) | CTF | Phishing → cred extraction → Pickle RCE → training data poison → prod backdoor |
| 21 | [Checkpoint](./Section-4-AI-Supply-Chain-Security/05-checkpoint/README.md) | CTF | Full IR: backdoor triage, forensic attribution, remediation, pipeline hardening |

---

### ☠️ Section 5 — Data Poisoning ✅

| # | Room | Type | Key Skills |
|---|------|------|-----------|
| 22 | [RAG Security Fundamentals](./Section-5-Data-Poisoning/01-rag-security-fundamentals/README.md) | Theory | RAG pipeline anatomy, trust model breakdown, threat taxonomy |
| 23 | [Data Poisoning in RAG Systems](./Section-5-Data-Poisoning/02-data-poisoning-in-rag-systems/README.md) | Theory + Lab | Direct/indirect/behavioral poisoning, relevance hijacking, detection |
| 24 | [Sensitive Information Disclosure](./Section-5-Data-Poisoning/03-sensitive-information-disclosure/README.md) | Theory + Lab | Semantic fuzzing exfiltration, membership inference, embedding inversion |
| 25 | [UnIndexed](./Section-5-Data-Poisoning/04-unindexed/README.md) | CTF | Legal RAG breach: privilege extraction, API key leak, PDF injection |
| 26 | [Lockdown](./Section-5-Data-Poisoning/05-lockdown/README.md) | CTF | Full RAG hardening: RBAC, ingestion pipeline, guardrails, 22-test validation |

---

## 🏆 Progress Tracker

| Section | Rooms | Write-ups | Progress |
|---------|-------|-----------|----------|
| AI Fundamentals | 6 | ✅ Complete | 🟩🟩🟩🟩🟩🟩 |
| Secure AI Systems | 5 | ✅ Complete | 🟩🟩🟩🟩🟩 |
| Prompt Security | 5 | ✅ Complete | 🟩🟩🟩🟩🟩 |
| AI Supply Chain | 5 | ✅ Complete | 🟩🟩🟩🟩🟩 |
| Data Poisoning | 5 | ✅ Complete | 🟩🟩🟩🟩🟩 |
| **Total** | **26** | **26 documented** | **100% complete!** |

---

## 📁 Repository Structure

```
TryHackMe-AI-Security-Path/
│
├── README.md                               ← You are here
│
├── Section-1-AI-Fundamentals/              ✅ Fully documented
│   ├── 01-ai-security-path-ticketing-event/
│   ├── 02-ai-ml-security-threats/
│   ├── 03-ai-models-and-data/
│   ├── 04-prompt-engineering/
│   ├── 05-ai-forensics/
│   └── 06-containment/
│
├── Section-2-Secure-AI-Systems/            ✅ Fully documented
│   ├── 01-securing-ai-systems/
│   ├── 02-llm-security/
│   ├── 03-ai-threat-modelling/
│   ├── 04-ai-system-reconnaissance/
│   └── 05-ai-threat-modelling-assessment/
│
├── Section-3-Prompt-Security/              ✅ Fully documented
│   ├── 01-prompt-injection/
│   ├── 02-jailbreaking/
│   ├── 03-prompt-defence/
│   ├── 04-llmborghini/
│   └── 05-white-rabbit/
│
├── Section-4-AI-Supply-Chain-Security/     ✅ Fully documented
│   ├── 01-understanding-ai-supply-chains/
│   ├── 02-supply-chain-attack-vectors/
│   ├── 03-securing-the-ai-supply-chain/
│   ├── 04-payload/
│   └── 05-checkpoint/
│
└── Section-5-Data-Poisoning/              ✅ Fully documented
    ├── 01-rag-security-fundamentals/
    ├── 02-data-poisoning-in-rag-systems/
    ├── 03-sensitive-information-disclosure/
    ├── 04-unindexed/
    └── 05-lockdown/
```

---

## 🔗 Key References

| Resource | Relevance |
|----------|-----------|
| [OWASP LLM Top 10](https://owasp.org/www-project-top-10-for-large-language-model-applications/) | Primary vulnerability framework referenced throughout |
| [MITRE ATLAS](https://atlas.mitre.org/) | AI/ML adversarial tactics & techniques matrix |
| [NIST AI Risk Management Framework](https://www.nist.gov/artificial-intelligence/ai-risk-management-framework) | Governance and risk scoring methodology |
| [Zou et al. — GCG Adversarial Suffix Attack](https://arxiv.org/abs/2307.15043) | Core jailbreak research (Section 3) |
| [Greshake et al. — Indirect Prompt Injection](https://arxiv.org/abs/2302.12173) | Indirect injection & RAG attack foundation |
| [Protect AI — ModelScan](https://github.com/protectai/modelscan) | Pickle/ONNX malware scanning tool used in Section 4 |
| [Sigstore / Cosign](https://docs.sigstore.dev/) | ML artifact signing (Section 4) |
| [Hugging Face SafeTensors](https://github.com/huggingface/safetensors) | Secure serialization format (Section 4) |
| [Neural Cleanse](https://people.cs.uchicago.edu/~ravenben/publications/pdf/backdoor-sp19.pdf) | Backdoor detection method (Sections 4 & 5) |
| [Lakera — Prompt Injection](https://www.lakera.ai/blog/prompt-injection-attacks) | Practical injection examples |

---

## 📝 Write-up Format

Every room README follows this structure:

```
📋 Room Overview      — What the room covers and learning objectives
🧠 Key Concepts       — First-principles technical breakdown (not copy-paste)
📝 Task Walkthrough   — Step-by-step with real commands, payloads, and Q&A tables
🚩 Flags              — CTF flag captures with attribution
💡 Personal Takeaways — Original analysis: what matters, what surprised me, what to carry forward
🔗 Resources          — Academic papers, tools, and docs used
```

---

<div align="center">

*Built by a cybersecurity practitioner obsessed with the intersection of AI and offensive security.*  
*If this repo helped you — drop a ⭐. Open an issue if you spot an error or want to discuss a technique.*

**All 26 rooms fully documented!**

</div>
