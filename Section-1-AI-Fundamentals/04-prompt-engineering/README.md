# ✍️ Room 04 — Prompt Engineering

> **Section:** AI Fundamentals (Section 1 of 5)
> **Difficulty:** Easy
> **Type:** Theory + Interactive Lab (PromptSec Agent)
> **Room Link:** [https://tryhackme.com/room/promptengineeringaisec](https://tryhackme.com/room/promptengineeringaisec)
> **Completed:** 19/05/2026

---

## 📋 Room Overview

A practical guide to understanding how LLMs process text and how to craft effective prompts — both for legitimate security tasks and for adversarial testing. Covers LLM fundamentals, the four pillars of prompt engineering, the instruction hierarchy, and advanced techniques including Chain-of-Thought and Few-shot prompting. Concludes with a live graded challenge using the **PromptSec** AI agent.

**What you'll learn:**
- How LLMs tokenise and process text
- Why LLMs are nondeterministic and what parameters control their behaviour
- The four pillars of a well-crafted prompt
- Advanced prompting techniques: Zero-shot, One-shot, Few-shot, CoT, Templates
- How to apply prompt engineering to real security tasks (log analysis, SQL injection detection)

---

## 🧠 Key Concepts

### How LLMs Process Text
LLMs don't read words — they read **tokens** (roughly 3–4 characters each). Every word is split into token fragments, converted to numerical IDs, and processed as numbers. The model never sees natural language directly.

LLMs are **nondeterministic**: the same input can produce different outputs across runs. Responses are probabilistic. This matters enormously in security contexts where consistency and reproducibility are critical.

### Key Parameters
| Parameter | What It Controls |
|-----------|-----------------|
| Temperature | Randomness of output. `0.0` = near-deterministic (always picks highest-probability token). Higher = more creative/unpredictable |
| Top-P | Limits token selection to a cumulative probability mass — controls diversity |

### The Four Pillars of Prompt Engineering
| Pillar | Purpose |
|--------|---------|
| **Instruction** | The core task or command — what you want the AI to do |
| **Context** | Background information or scenario so the model understands the situation |
| **Output Format** | How the response should be structured (bullet points, JSON, table, etc.) |
| **Constraints** | Rules, limits, or restrictions imposed on the response (tone, length, forbidden topics) |

> 💡 **Sweet spot:** Provide enough detail to remove ambiguity, but don't overload — too verbose causes hallucination and unfocused answers.

### The Instruction Hierarchy
LLMs process all input as a single text stream, but there is an intended order of priority: **system prompt > user prompt**. This hierarchy is central to security — prompt injection attacks attempt to override it.

The term for this intended priority order: **instruction hierarchy**.

### Advanced Prompting Techniques

| Technique | Description | Best For |
|-----------|-------------|----------|
| **Zero-shot** | No examples — relies entirely on pre-trained knowledge | Simple, well-defined tasks |
| **One-shot** | One input/output example before the task | Basic pattern demonstration |
| **Few-shot** | 2–5 varied examples before the task | Nuanced classification tasks |
| **Chain-of-Thought (CoT)** | Asks model to reason step-by-step before answering | Complex multi-step analysis |
| **Zero-shot CoT** | Just add "Let's think step by step" | Quick reasoning improvement, no examples needed |
| **Prompt Templates** | Standardised reusable prompt structures with placeholders | Recurring security tasks |

> ⚠️ **CoT caveat:** Only works reliably with models above ~100B parameters. Smaller models can generate reasoning chains that look coherent but lead to wrong answers.

### Prompt Template Structure
```
Task: [Define the task]
Context: [Provide relevant background]
Output: [Specify format — JSON, bullet points, table, etc.]
Constraints: [Set rules or restrictions]
```

### Security Application — CoT for Log Analysis
```
You are a SOC analyst. Analyze the following HTTP logs for SQL injection attempts.
Think through each step:
Step 1: Filter the logs — isolate only entries with a 200 OK status code.
Step 2: For each 200 OK entry, scan request parameters for suspicious patterns
        such as ' OR 1=1, UNION SELECT, --, or encoded variants like %27.
Step 3: For each suspicious entry, explain why the pattern indicates SQL injection.
Step 4: Summarise findings in a table with columns: Timestamp | URI | Pattern Found | Risk Level
```

---

## 📝 Task Walkthrough

### Task 1 — Introduction

| Question | Answer |
|----------|--------|
| I understand the learning objectives and am ready to learn about prompt engineering! | `No answer needed` |

---

### Task 2 — LLM Fundamentals

| Question | Answer |
|----------|--------|
| What is the term for the smallest units that an LLM breaks text into in order to process it? | `tokens` |
| What parameter would you set to 0.0 to make an LLM behave as close to deterministic as possible? | `temperature` |
| What parameter restricts which tokens the model considers by limiting selection to a cumulative probability mass? | `top-p` |

---

### Task 3 — Prompt Structure

| Question | Answer |
|----------|--------|
| Which pillar instructs the model on how the answer should be structured, such as bullet points or a JSON object? | `output format` |
| Which pillar specifies rules or limits imposed on the model's response, such as enforcing a tone or forbidding certain topics? | `constraints` |
| Which pillar provides the AI with relevant background information or scenario so it understands the situation? | `context` |
| Which pillar of prompt engineering defines the core command or action you want the AI to perform? | `instruction` |
| What is the term for the intended order of priority between system and user instructions in an LLM application? | `instruction hierarchy` |

---

### Task 4 — Advanced Techniques

| Question | Answer |
|----------|--------|
| What is the term for the prompting technique introduced by Google researchers in 2022 that asks models to break tasks into intermediate reasoning steps? | `chain-of-thought` |
| What prompting technique involves providing no examples and relying entirely on the model's pre-trained knowledge? | `zero-shot` |
| What prompting technique involves saving and reusing a standardised prompt structure for recurring tasks? | `prompt templates` |

---

### Task 5 — Practical (PromptSec Challenge)

**How it works:** PromptSec gives you a technique + security task. You write a prompt, it grades you out of 10. Reach 40 points total to get the flag. You can ask for hints but it won't write the prompt for you.

**Tips for maximum scores:**
- For **CoT** tasks: explicitly list each reasoning step using numbered steps; don't just say "think step by step"
- For **Few-shot** tasks: provide 2+ examples covering different cases (e.g. critical, high, medium, low priority)
- For **Template** tasks: use clear placeholders like `[LOG_ENTRY]`, `[STATUS_CODE]`, `[OUTPUT_FORMAT]`
- Always specify the output format explicitly (JSON, table, bullet points)
- Always assign a role ("You are a SOC analyst...")

> 💡 You can exceed 40/40 — perfect prompts can score higher than the target.

| Question | Answer |
|----------|--------|
| Complete the PromptSec challenge and get the flag | `THM{Pr0mpt_3ng1n33r}` |

**Notes:**
> The flag value may vary — retrieve it from the PromptSec agent after reaching 40+ points.

---

## 🚩 Flags

| Flag | Value |
|------|-------|
| Task 5 — PromptSec Challenge | `THM{Pr0mpt_3ng1n33r}` |

---

## 💡 Personal Takeaways

- Prompts are not just natural language requests — they're structured instructions with a hierarchy that can be exploited
- The instruction hierarchy (system > user) is exactly what prompt injection attacks target — understanding this from the engineering side makes the attack side much clearer
- CoT prompting is the single biggest lever for improving output quality on complex security tasks
- Nondeterminism means a prompt that works today may not work tomorrow — always test with temperature=0 when you need consistency
- *(Add your own thoughts after completing the room)*

---

## 🔗 Additional Resources

- [Google Brain — Chain-of-Thought Prompting Paper (2022)](https://arxiv.org/abs/2201.11903)
- [Anthropic Prompt Engineering Guide](https://docs.anthropic.com/en/docs/build-with-claude/prompt-engineering/overview)
- [OpenAI Prompt Engineering Guide](https://platform.openai.com/docs/guides/prompt-engineering)
- [OWASP LLM01 — Prompt Injection](https://owasp.org/www-project-top-10-for-large-language-model-applications/)

---

> ⬅️ [Previous Room](../03-ai-models-and-data/README.md) | [Back to Main](../../README.md) | [Next Room → AI Forensics](../05-ai-forensics/README.md) ➡️
