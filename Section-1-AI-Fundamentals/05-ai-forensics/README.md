# 🔍 Room 05 — AI Forensics

> **Section:** AI Fundamentals (Section 1 of 5)
> **Difficulty:** Medium
> **Type:** Theory + Practical Lab (DFIR Investigation)
> **Room Link:** [https://tryhackme.com/room/aiforensics](https://tryhackme.com/room/aiforensics)
> **Completed:** 19/05/2026

---

## 📋 Room Overview

Explores how AI and Machine Learning are being applied to Digital Forensics and Incident Response (DFIR). Covers the benefits, challenges, and ethical/legal implications of AI-assisted forensics, followed by a hands-on investigation of a real breach scenario at a fictional company (RobbCo) using ML-enhanced scripts built on **Scikit-learn**.

**Prerequisites:**
- AI/ML Security Threats room (Room 02 in this path)
- Basic DFIR knowledge (DFIR: An Introduction, Linux Forensics)

**What you'll learn:**
- How AI augments DFIR capabilities through anomaly detection and pattern recognition
- What types of neural networks are used in image/video forensics
- How AI correlates event data to reconstruct incident timelines
- Practical use of ML scripts to triage logs and flag suspicious files
- Why AI is a tool to augment — not replace — human forensic intuition

---

## 🧠 Key Concepts

### Why AI in DFIR?
Modern incidents generate massive volumes of logs and artefacts. AI's core value in forensics is speed and scale — it processes thousands of log entries in seconds to surface the "needle in the haystack," freeing the analyst to apply human intuition to validation and interpretation.

### AI Capabilities in Forensics
| Capability | Description |
|-----------|-------------|
| **Anomaly Detection** | Recognises patterns in logs/behaviour that deviate from baseline — the primary ability that augments DFIR investigators |
| **Image & Video Forensics** | CNNs (Convolutional Neural Networks) learn spatial patterns in visual data — used for deepfake detection, metadata analysis |
| **Sentiment Analysis** | Performed on social media and chat logs to assess emotional tone and intent |
| **Timeline Reconstruction** | AI correlates **event data** across multiple log sources to automatically reconstruct an incident timeline |
| **Behavioural Analysis** | Observes how a program behaves (API call sequences) to determine if it is malicious — this is **dynamic analysis** |

### AI Characteristics to Know
| Term | Meaning |
|------|---------|
| **Nondeterminism** | The same input may yield different outputs across different runs — critical to understand when using AI in forensic workflows |

### Challenges & Limitations
- AI is **not a replacement** for human expertise — it augments, not automates, forensic judgement
- Nondeterminism means AI outputs can vary between runs — always validate with human review
- ML models trained on "normal" behaviour may miss novel attack patterns they've never seen
- Ethical/legal implications of AI in forensics are still evolving (admissibility, bias, explainability)

### The RobbCo Case (Practical Scenario)
**Target:** RobbCo, a software company famous for its RETROS BIOS and Unified Operating System (UOS)  
**Incident:** Off-the-clock login flagged by SOC. Ransom note found on desktop. Source code potentially exfiltrated.

**ML Tools used in the investigation:**
- `classify_logs.py` — Scikit-learn model trained on labelled auth logs; identifies anomalous login behaviour
- `file_anomalies.py` — Scikit-learn model trained on high-priority directory contents; flags suspicious files by analysing name, path, size, extension, entropy, permissions, and creation time

**Attack Chain Reconstructed:**
1. Attacker gained initial access via **phishing** (sent to `j.morgan`)
2. Successful login at **03:01:02** as `j.morgan` (after initial failed attempts)
3. Lateral movement: `sudo nano /home/r.house/.ssh/authorized_keys` — planted SSH key in `r.house`'s account
4. Persistence: two reverse shells planted (`/tmp/.x` as unprivileged `j.morgan`, second as elevated `r.house`)
5. Exfiltration: source code archived and encrypted at `/dev/shm/.core_dump_2025.tgz.enc`
6. Attacker's email: `akeane@poseidonenergy.net`

---

## 📝 Task Walkthrough

### Task 1 — Introduction

| Question | Answer |
|----------|--------|
| *(No answer required — intro/objectives acknowledgement)* | `No answer needed` |

---

### Task 2 — AI in DFIR (Theory)

| Question | Answer |
|----------|--------|
| What ability of AI helps a DFIR investigator by recognising patterns they might not have been able to comprehend? | `Anomaly Detection` |
| What term describes the AI characteristic where the same input may yield different outputs across different runs? | `Nondeterminism` |
| What type of neural network is commonly used in image and video forensics due to its ability to learn spatial patterns in visual data? | `Convolutional Neural Network` |
| What kind of analysis can be performed on social media or chat logs to assess the emotional tone of messages? | `Sentiment Analysis` |
| What type of data do AI systems correlate to reconstruct the timeline of an incident automatically? | `Event Data` |
| What type of analysis observes how a program behaves to determine whether it is malicious, e.g., using its API call sequence? | `Dynamic Analysis` |

---

### Task 3 — The Digital Trail (Practical Investigation)

**Setup:** SSH into the compromised machine as `o.deer` with password `TryHackMe!`

**Step 1 — Run `classify_logs.py` against `auth.log`**
```bash
python3 classify_logs.py auth.log
```
The ML model flags anomalous login behaviour. Initial failed login followed by success implies credential access.

**Step 2 — Run `file_anomalies.py` to detect suspicious files**
```bash
python3 file_anomalies.py
```
Key flagged artefacts:
- `/tmp/invoice_dump.txt` — recon data dump (SSH usage, usernames, active sessions)
- `/tmp/.x` — reverse shell (unprivileged, `j.morgan`)
- Persistence files in disguised locations (rev shell disguised as sysmon tool)
- `/dev/shm/.core_dump_2025.tgz.enc` — encrypted exfiltration archive

**Step 3 — Human validation** — Navigate to each flagged artefact and piece together the attack chain.

| Question | Answer |
|----------|--------|
| At what time does the attacker successfully log in as j.morgan? | `03:01:02` |
| What attack method was used to gain initial access? | `Phishing` |
| Can you find the attacker's email address? | `akeane@poseidonenergy.net` |
| What command did the attacker run as j.morgan to gain access to the r.house account? | `sudo nano /home/r.house/.ssh/authorized_keys` |
| What is the full path of the archive used to steal RobbCo's source code? | `/dev/shm/.core_dump_2025.tgz.enc` |

---

## 🚩 Flags

No traditional `THM{...}` flags in this room — answers are investigative findings from the forensic analysis above.

---

## 💡 Personal Takeaways

- AI in DFIR is a force multiplier, not a replacement — it narrows the search space, but human intuition connects the dots
- The ML scripts process thousands of log lines in seconds; without them, finding the anomalous login in a massive `auth.log` would take hours
- "AI is an augmentation tool, not a replacement for human experts" — the key lesson of this room
- Dynamic analysis (API call sequences) is how AI-powered AV detects novel malware — understand this for the defensive side
- CNNs in forensics = deepfake detection, image metadata — keep this in mind for future investigation work
- *(Add your own thoughts after completing the room)*

---

## 🔗 Additional Resources

- [Scikit-learn Documentation](https://scikit-learn.org/stable/)
- [MITRE ATLAS — AI in Incident Response](https://atlas.mitre.org/)
- [TryHackMe — DFIR: An Introduction](https://tryhackme.com/room/introductoryroomdfirmodule)
- [TryHackMe — Linux Forensics](https://tryhackme.com/room/linuxforensics)
- [NIST — AI in Cybersecurity](https://www.nist.gov/artificial-intelligence)

---

> ⬅️ [Previous Room](../04-prompt-engineering/README.md) | [Back to Main](../../README.md) | [Next Room → ContAInment](../06-containment/README.md) ➡️
