# 🤖🔐 TryHackMe — AI Security Path

> **Documenting my journey through the TryHackMe AI Security learning path.**  
> Notes, walkthroughs, key concepts, and answers for every room.

![TryHackMe](https://img.shields.io/badge/TryHackMe-AI%20Security%20Path-red?style=for-the-badge&logo=tryhackme)
![Status](https://img.shields.io/badge/Status-In%20Progress-yellow?style=for-the-badge)
![Rooms](https://img.shields.io/badge/Rooms-26-blue?style=for-the-badge)
![Difficulty](https://img.shields.io/badge/Difficulty-Medium-orange?style=for-the-badge)

---

## 📌 About This Repo

This repository contains my personal notes and write-ups for the [TryHackMe AI Security Path](https://tryhackme.com/path/outline/aisecurity).

The path covers:
- How AI systems are built and where they break
- Attacking and defending LLMs via prompt injection & jailbreaking
- Securing AI supply chains and training data
- Exploiting OWASP LLM vulnerabilities against real agents

> ⚠️ **Disclaimer:** These notes are for educational purposes only. All content is based on the TryHackMe platform rooms. Never apply these techniques outside of authorized environments.

---

## 🗺️ Path Structure & Progress

### 📦 Section 1 — AI Fundamentals

| # | Room | Type | Status |
|---|------|------|--------|
| 1 | [AI Security Path Ticketing Event](./Section-1-AI-Fundamentals/01-ai-security-path-ticketing-event/README.md) | Intro | ⬜ Not Started |
| 2 | [AI/ML Security Threats](./Section-1-AI-Fundamentals/02-ai-ml-security-threats/README.md) | Theory | ⬜ Not Started |
| 3 | [AI Models & Data](./Section-1-AI-Fundamentals/03-ai-models-and-data/README.md) | Theory | ⬜ Not Started |
| 4 | [Prompt Engineering](./Section-1-AI-Fundamentals/04-prompt-engineering/README.md) | Theory | ⬜ Not Started |
| 5 | [AI Forensics](./Section-1-AI-Fundamentals/05-ai-forensics/README.md) | Theory | ⬜ Not Started |
| 6 | [ContAInment](./Section-1-AI-Fundamentals/06-containment/README.md) | Lab | ⬜ Not Started |

---

### 🔒 Section 2 — Secure AI Systems

| # | Room | Type | Status |
|---|------|------|--------|
| 7 | [Securing AI Systems](./Section-2-Secure-AI-Systems/01-securing-ai-systems/README.md) | Theory | ⬜ Not Started |
| 8 | [LLM Security](./Section-2-Secure-AI-Systems/02-llm-security/README.md) | Theory | ⬜ Not Started |
| 9 | [AI Threat Modelling](./Section-2-Secure-AI-Systems/03-ai-threat-modelling/README.md) | Theory | ⬜ Not Started |
| 10 | [AI System Reconnaissance](./Section-2-Secure-AI-Systems/04-ai-system-reconnaissance/README.md) | Lab | ⬜ Not Started |
| 11 | [AI Threat Modelling Assessment](./Section-2-Secure-AI-Systems/05-ai-threat-modelling-assessment/README.md) | Assessment | ⬜ Not Started |

---

### 💉 Section 3 — Prompt Security

| # | Room | Type | Status |
|---|------|------|--------|
| 12 | [Prompt Injection](./Section-3-Prompt-Security/01-prompt-injection/README.md) | Theory + Lab | ⬜ Not Started |
| 13 | [Jailbreaking](./Section-3-Prompt-Security/02-jailbreaking/README.md) | Theory + Lab | ⬜ Not Started |
| 14 | [Prompt Defence](./Section-3-Prompt-Security/03-prompt-defence/README.md) | Theory + Lab | ⬜ Not Started |
| 15 | [LLMborghini](./Section-3-Prompt-Security/04-llmborghini/README.md) | CTF Lab | ⬜ Not Started |
| 16 | [White Rabbit](./Section-3-Prompt-Security/05-white-rabbit/README.md) | CTF Lab | ⬜ Not Started |

---

### ⛓️ Section 4 — AI Supply Chain Security

| # | Room | Type | Status |
|---|------|------|--------|
| 17 | [Understanding AI Supply Chains](./Section-4-AI-Supply-Chain-Security/01-understanding-ai-supply-chains/README.md) | Theory | ⬜ Not Started |
| 18 | [Supply Chain Attack Vectors](./Section-4-AI-Supply-Chain-Security/02-supply-chain-attack-vectors/README.md) | Theory | ⬜ Not Started |
| 19 | [Securing the AI Supply Chain](./Section-4-AI-Supply-Chain-Security/03-securing-the-ai-supply-chain/README.md) | Theory | ⬜ Not Started |
| 20 | [Payload](./Section-4-AI-Supply-Chain-Security/04-payload/README.md) | CTF Lab | ⬜ Not Started |
| 21 | [Checkpoint](./Section-4-AI-Supply-Chain-Security/05-checkpoint/README.md) | CTF Lab | ⬜ Not Started |

---

### ☠️ Section 5 — Data Poisoning

| # | Room | Type | Status |
|---|------|------|--------|
| 22 | [RAG Security Fundamentals](./Section-5-Data-Poisoning/01-rag-security-fundamentals/README.md) | Theory | ⬜ Not Started |
| 23 | [Data Poisoning in RAG Systems](./Section-5-Data-Poisoning/02-data-poisoning-in-rag-systems/README.md) | Theory + Lab | ⬜ Not Started |
| 24 | [Sensitive Information Disclosure](./Section-5-Data-Poisoning/03-sensitive-information-disclosure/README.md) | Theory + Lab | ⬜ Not Started |
| 25 | [UnIndexed](./Section-5-Data-Poisoning/04-unindexed/README.md) | CTF Lab | ⬜ Not Started |
| 26 | [Lockdown](./Section-5-Data-Poisoning/05-lockdown/README.md) | CTF Lab | ⬜ Not Started |

---

## 📁 Repo Structure

```
THM-AI-Security-Path/
│
├── README.md                          ← You are here
│
├── Section-1-AI-Fundamentals/
│   ├── 01-ai-security-path-ticketing-event/
│   │   └── README.md
│   ├── 02-ai-ml-security-threats/
│   │   └── README.md
│   ├── 03-ai-models-and-data/
│   │   └── README.md
│   ├── 04-prompt-engineering/
│   │   └── README.md
│   ├── 05-ai-forensics/
│   │   └── README.md
│   └── 06-containment/
│       └── README.md
│
├── Section-2-Secure-AI-Systems/
│   ├── 01-securing-ai-systems/
│   ├── 02-llm-security/
│   ├── 03-ai-threat-modelling/
│   ├── 04-ai-system-reconnaissance/
│   └── 05-ai-threat-modelling-assessment/
│
├── Section-3-Prompt-Security/
│   ├── 01-prompt-injection/
│   ├── 02-jailbreaking/
│   ├── 03-prompt-defence/
│   ├── 04-llmborghini/
│   └── 05-white-rabbit/
│
├── Section-4-AI-Supply-Chain-Security/
│   ├── 01-understanding-ai-supply-chains/
│   ├── 02-supply-chain-attack-vectors/
│   ├── 03-securing-the-ai-supply-chain/
│   ├── 04-payload/
│   └── 05-checkpoint/
│
└── Section-5-Data-Poisoning/
    ├── 01-rag-security-fundamentals/
    ├── 02-data-poisoning-in-rag-systems/
    ├── 03-sensitive-information-disclosure/
    ├── 04-unindexed/
    └── 05-lockdown/
```

---

## 🏆 Progress Tracker

| Section | Rooms | Completed | Progress |
|---------|-------|-----------|----------|
| AI Fundamentals | 6 | 0 | ⬜⬜⬜⬜⬜⬜ |
| Secure AI Systems | 5 | 0 | ⬜⬜⬜⬜⬜ |
| Prompt Security | 5 | 0 | ⬜⬜⬜⬜⬜ |
| AI Supply Chain | 5 | 0 | ⬜⬜⬜⬜⬜ |
| Data Poisoning | 5 | 0 | ⬜⬜⬜⬜⬜ |
| **Total** | **26** | **0** | **0%** |

---

## 🔗 Resources

- [TryHackMe AI Security Path](https://tryhackme.com/path/outline/aisecurity)
- [OWASP LLM Top 10](https://owasp.org/www-project-top-10-for-large-language-model-applications/)
- [MITRE ATLAS](https://atlas.mitre.org/) — AI/ML adversarial threat matrix
- [NIST AI Risk Management Framework](https://www.nist.gov/system/files/documents/2023/01/26/NIST.AI.100-1.pdf)

---

## 📝 Notes Format

Each room README follows the same template:
- Room info & difficulty
- Key concepts covered
- Task-by-task notes
- Flags / answers (where applicable)
- Personal takeaways

---

*Made with 🔐 by a cybersecurity student. Star ⭐ the repo if it helps you!*
