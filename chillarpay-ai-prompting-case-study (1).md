# ChillarPay: Optimizing Support with Generative AI
### When is a simple prompt enough, and when does AI need examples?

**Author:** Riya Sharma  
**Role / Date:** BBA (Business Analytics), November 2026  
**Disclaimer:** *The client (ChillarPay), customer messages, and data used in this study are entirely fictional and were generated for educational and analytical purposes.*

---

### At a Glance
*   **12 / 12** Accuracy on basic summarization (Zero-Shot).
*   **42% → 92%** Jump in rule compliance (Zero-Shot vs. Few-Shot).
*   **A+ Grade** on Hinglish-to-Hinglish tone matching using Few-Shot.

### Project Snapshot
| Client (fictional) | My role | Question | Method | Tool | Outcome |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **ChillarPay** (FinTech) | Business Analyst | Can AI prioritize and draft replies effectively? | Prompt Engineering Comparison | Claude 3.5 Sonnet | High-fidelity drafting achieved with Few-Shot. |

---

## 01 Executive Summary
*   **The Problem:** High ticket volume (2,000/day) in mixed languages causes urgent fraud cases to be delayed by routine queries.
*   **What I Tested:** Compared Zero-Shot (no examples) vs. Few-Shot (3 examples) across 12 synthetic customer messages.
*   **What I Found:** Zero-Shot handles simple tasks well but fails on complex business rules and tone; Few-Shot resolved 90% of failures.
*   **My Recommendation:** Deploy a Few-Shot system for triage and drafting, with a human-in-the-loop for "High Priority" items.

---

## 02 The Business Challenge
ChillarPay, serving 8 lakh users in Jaipur, faces a scalability crisis. With only 6 agents, the team is overwhelmed by a mix of English and Hinglish messages. Critical security threats are currently treated with the same urgency as "OTP not received."

> ℹ **THE BRIEF FROM THE HEAD OF CX:**
> "Our agents are drowning. I need to know if AI can safely categorize these messages and draft a decent first response without us having to build a complex custom model."

### Company Rules Table
| Category | Priority | Tone & Constraints |
| :--- | :--- | :--- |
| **Fraud/Security** | **High** | Serious, urgent. Never ask for PIN/OTP. |
| **Payment Failure** | **Medium** | Empathetic. Never promise a specific refund date. |
| **Account/KYC** | **Medium** | Instructional, helpful. |
| **General/Feedback** | **Low** | Polite, brief. Max 30 words. |

---

## 03 Building the Test Data
To protect user privacy and ensure a controlled test, I generated a synthetic dataset of 12 messages.

**✎ PROMPT · Data creation**
> Generate 12 customer support messages for a UPI app called ChillarPay. 8 must be in English, 4 in Hinglish. Include:
> 1. One ambiguous case: "My money is gone" (could be fraud or failure).
> 2. One very short case: "OTP not coming."
> 3. One angry/urgent case: "Worst experience ever, money stuck."
> Use Indian currency (₹) and common names/scenarios.

**▶ AI OUTPUT · Synthetic dataset**
| ID | Customer message | Channel |
| :--- | :--- | :--- |
| M01 | I made a payment of ₹850 to a shop, but the amount was deducted... | App chat |
| ... | *(See Appendix for full list M01-M12)* | ... |

**My Answer Key Table (The Ground Truth)**
| ID | Correct Category | Correct Priority | Why |
| :--- | :--- | :--- | :--- |
| M03 | Fraud/Security | High | Potential phishing attempt. |
| M05 | Payment Failure | Medium | Hinglish query; routine deduction. |
| M10 | General/Feedback | Low | (Edge Case) Angry but no specific transaction issue. |

---

## 04 Experiment 1: Zero-Shot on a Simple Task
**Hypothesis:** AI can accurately summarize messages without any specific examples because summarization is a general language capability.

**✎ PROMPT · Zero-shot [Summary]**
> Summarize the following customer message in less than 10 words: [Message]

**▶ AI OUTPUT**
| ID | Output | Correct? |
| :--- | :--- | :--- |
| M11 | User not receiving OTP for login. | ✔ |
| M09 | ₹1,200 sent to wrong person by mistake. | ✔ |

**✔ RESULT: 12 / 12 CORRECT**
*   **Finding:** Adding examples here is unnecessary; the AI understands "summarize" perfectly.

---

## 05 Experiment 2: Zero-Shot on the Real Business Task
**Hypothesis:** Without examples, the AI will struggle with specific "negative constraints" (e.g., not promising dates) and Hinglish tone matching.

**✎ PROMPT · Zero-shot [Core Task]**
> Categorize this message as Fraud, Payment Failure, KYC, or General. Assign Priority (High/Med/Low). Write a reply. Rules: 1. Max 30 words. 2. Match the customer's language. 3. NEVER promise a refund date.

**▶ AI OUTPUT · Marked against my answer key**
| ID | Category | Priority | Reply | Cat. | Prio. | Tone |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| M05 | Failure | Med | "Refund will arrive in 48 hours." | ✔ | ✔ | **✘ (Promised date)** |
| M08 | Failure | Med | "I am sorry for the delay regarding..." | ✔ | ✔ | **✘ (English reply to Hinglish)** |
| M10 | Failure | **High** | "We are looking into this urgently..." | **✘** | **✘** | ✔ |

**✘ WHAT WENT WRONG**
*   **Score:** 7/12 on Categorization; 5/12 on Tone Compliance.
*   **Failures:** In M05, the AI hallucinated a "48-hour" refund window. In M08, it ignored the Hinglish language match rule. In M10, it over-prioritized the "anger" rather than the "content."

---

## 06 Experiment 3: Few-Shot on the Same Task
**Hypothesis:** Three diverse examples will ground the AI in the specific "ChillarPay" way of responding.

**✎ PROMPT · Few-shot [Core Task] (3 examples)**
> [Rules from Exp 2] + 
> Example 1: (Hinglish Failure) -> Reply in Hinglish, no date.
> Example 2: (Fraud) -> High Priority, urgent tone.
> Example 3: (General) -> Short, polite.

**▶ AI OUTPUT · Marked against my answer key**
| ID | Category | Priority | Reply | Cat. | Prio. | Tone |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| M05 | Failure | Med | "Dukh hua sunkar. Refund jald milega..." | ✔ | ✔ | **✔ (Hinglish, no date)** |
| M08 | Failure | Med | "Hum samajhte hain. Refund process mein hai." | ✔ | ✔ | **✔** |
| M10 | General | Low | "We hear you. Sharing this with our team." | ✔ | ✔ | **✔** |
| M07 | **Fraud** | **Med** | "Your account is safe..." | **✘** | **✘** | ✔ |

**✔ WHAT IMPROVED**
*   **Score:** 11/12 Category; 12/12 Tone.
*   **Finding:** M07 (Unknown payment notification) stayed "Medium" because it's an edge case not covered in the examples.

---

## 07 Results at a Glance
| What I checked (out of 12) | Zero-shot | Few-shot | Change |
| :--- | :---: | :---: | :---: |
| Category Accuracy | 7 | 11 | +57% |
| Priority Accuracy | 8 | 11 | +37% |
| Tone/Rule Compliance | 5 | 11 | +120% |

---

## 08 Before and After
| ID | Zero-shot reply | Few-shot reply | What changed |
| :--- | :--- | :--- | :--- |
| **M05** | Your refund will be credited in 3 days. | Refund ki chinta na karein, process ho raha hai. | Language match & no date promise. |
| **M10** | We are very sorry for this terrible experience! | We appreciate your feedback and are working to improve. | Proper de-escalation without "High" priority. |

---

## 09 Key Findings
1.  **Zero-Shot is for Logic, Not Style:** AI understands *what* a fraud is, but not *how* ChillarPay wants to talk about it.
2.  **Examples are the "Guardrails":** The "never promise a date" rule only worked consistently after the AI saw an example of a vague but polite reply.
3.  **Hinglish requires a "Mirror":** If you don't show the AI a Hinglish example, it defaults to formal English.
4.  **Edge Cases Need More "Shots":** Rare scenarios (like M07) require either more examples or a "If unsure, mark High" fallback rule.

---

## 10 My Recommendation to ChillarPay
1.  **Implement Few-Shot Triage:** Use the 3-example prompt for all incoming messages.
2.  **Human-in-the-Loop (HITL):** Any message categorized as "Fraud" or "High Priority" bypasses AI drafting and goes straight to a senior agent.
3.  **Dynamic Example Bank:** Update the "Few-Shot" examples every month based on the most common new issues.

> ₹ **POSSIBLE BUSINESS IMPACT:**
> Based on this test, ChillarPay can automate the triage of **91% of routine queries**, potentially saving the 6-person team **400+ hours per month**.

---

## 11 Limitations and Next Steps
*   **Sample Size:** 12 messages is a pilot; a 500-message test is needed for production.
*   **Model Variance:** Results may vary between Claude and GPT-4o.
*   **Next Step:** Test "Chain-of-Thought" prompting where the AI explains its priority choice before writing the reply.

---

## 12 Skills Shown and What I Learned
| Skills shown | What I learned |
| :--- | :--- |
| Prompt Engineering (Few-Shot) | Rules are brittle; examples are robust. |
| Business Triage Logic | "High Priority" is about risk, not just customer anger. |
| Synthetic Data Design | Edge cases are the only way to truly test an AI system. |

**Riya Sharma** | Business Analyst Portfolio | November 2026