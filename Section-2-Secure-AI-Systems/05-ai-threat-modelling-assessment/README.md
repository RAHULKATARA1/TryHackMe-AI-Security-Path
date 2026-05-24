# 🏠 AI Threat Modelling Assessment — TryHackMe AI Security Path

> **Section:** Section 2 — Secure AI Systems | **Difficulty:** Medium | **Type:** Assessment  
> **Room Link:** [https://tryhackme.com/room/ai-threat-modelling-assessment](https://tryhackme.com/room/ai-threat-modelling-assessment)  
> **Completed:** 24/05/2026

---

## 📋 Room Overview

This capstone assessment room puts all the theory from Section 2 to the test. You act as a Lead AI Security Architect hired to review the design of "Med-AI", a conceptual healthcare application that utilizes LLMs to analyze patient data. Your goal is to conduct a full threat modelling exercise, identify critical architectural flaws, assign risk scores, and propose concrete mitigations.

**What you'll learn:**
- Conducting a comprehensive, real-world AI Threat Modelling exercise.
- Applying CVSS and DREAD risk scoring methodologies to AI vulnerabilities.
- Identifying systemic flaws in data privacy, model access, and output handling.
- Writing actionable security recommendations for AI engineering teams.

---

## 🧠 Key Concepts

### Practical Threat Analysis
Threat analysis in practice requires moving beyond theoretical vulnerabilities to assessing realistic impact. In a healthcare context, a prompt injection attack isn't just a gimmick; if it forces the AI to output incorrect medical advice or leak Patient Health Information (PHI), the impact is catastrophic.

### Risk Scoring for AI (CVSS/DREAD)
Evaluating the severity of AI vulnerabilities requires nuance. A data poisoning attack might be incredibly difficult to execute (low exploitability) but could compromise the entire system (high impact). Utilizing frameworks like DREAD (Damage, Reproducibility, Exploitability, Affected Users, Discoverability) helps prioritize remediation efforts.

### The "Human-in-the-Loop" (HITL) Imperative
A recurring theme in secure AI architecture, particularly in high-stakes environments like healthcare or finance, is the absolute necessity of HITL. LLMs must not possess unilateral authority to execute critical state-changing actions or deliver final diagnoses without human verification.

---

## 📝 Task Walkthrough

### Task 1 — Architecture Review

**Summary:** Reviewing the provided architectural diagram and Data Flow Diagram (DFD) of the Med-AI platform.

**Questions & Answers:**

| Question | Answer |
|----------|--------|
| Looking at the DFD, which component directly receives raw, unsanitized user input before passing it to the LLM? | `API Gateway` |
| Does the architecture currently enforce a Human-in-the-Loop before sending medical summaries to the patient portal? (Yes/No) | `No` |

**Notes:**
> The architectural diagram immediately highlights a massive flaw: the LLM has direct read/write access to the patient database without an intermediary sanitization or authorization layer.

---

### Task 2 — Threat Identification

**Summary:** Using STRIDE and MITRE ATLAS to identify specific threats based on the architecture.

**Questions & Answers:**

| Question | Answer |
|----------|--------|
| What attack vector is present due to the LLM executing SQL queries directly against the patient database based on user prompts? | `LLM-Assisted SQL Injection` |
| The lack of rate limiting on the inference API makes the system highly susceptible to what type of attack? | `Denial of Service (Sponge Attack)` |

**Notes:**
> Insecure Output Handling is the critical vulnerability here. The system blindly trusts the LLM's generated SQL queries. An attacker could use indirect prompt injection via a malicious patient file to drop tables or exfiltrate other patients' data.

---

### Task 3 — Risk Assessment & Mitigation

**Summary:** Prioritizing the identified vulnerabilities and proposing architectural changes.

**Questions & Answers:**

| Question | Answer |
|----------|--------|
| What is the recommended mitigation to prevent the LLM from executing malicious database queries? | `Implement strict Role-Based Access Control (RBAC) and parameterize LLM outputs.` |
| To prevent training data extraction, what technique should be applied to the data before it is used to fine-tune the model? | `Data Anonymization and PII Scrubbing` |

**Notes:**
> The primary recommendation is decoupling the LLM from the database execution environment. The LLM should only generate *intents* or structured JSON, which a traditional, strictly typed backend validates and executes using parameterized queries.

---

## 🚩 Flags / Final Answers

| Flag # | Value |
|--------|-------|
| Assessment Completion Hash | `THM{arch_r3v13w_c0mpl3t3_99x}` |

---

## 💡 Personal Takeaways

- This assessment beautifully highlighted the intersection of traditional AppSec and AI Security. An LLM doesn't replace the need for input validation and parameterized queries; it makes them *more* necessary because the attack surface is semantically complex.
- Implementing a zero-trust architecture is paramount. The LLM should be treated as an untrusted user, not a trusted administrator.
- The exercise underscored that securing AI is as much an architectural and engineering challenge as it is an algorithmic one. A perfectly robust model can still cause a massive breach if deployed in a flawed architecture.

---

## 🔗 Additional Resources

- [OWASP AI Security and Privacy Guide](https://owasp.org/www-project-ai-security-and-privacy-guide/)
- [Threat Modeling with PASTA](https://versprite.com/blog/what-is-pasta-threat-modeling/)

---

> ⬅️ [Previous Room](../04-ai-system-reconnaissance/README.md) | [Back to Main](../../README.md) | [Next Room](../../Section-3-Prompt-Security/01-prompt-injection/README.md) ➡️
