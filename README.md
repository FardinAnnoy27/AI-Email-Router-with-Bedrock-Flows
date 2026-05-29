
```markdown
# 🔀 AI Email Router with Amazon Bedrock Flows

An automated AI email routing pipeline built with **Amazon Bedrock Flows** and **Bedrock Prompt Management** that classifies customer emails by intent and generates tailored responses — all without writing a single line of code.

Built as part of the [NextWork](https://learn.nextwork.org) Generative AI Developer series.

---

## 📌 Project Overview

Every customer-facing business receives emails that need different responses depending on intent — but manually sorting and replying to each one is slow and error-prone.

This project solves that by building a **visual AI workflow** that:
1. Takes a raw customer email as input.
2. Classifies it as a **complaint**, **question**, or **refund request**.
3. Routes it through conditional logic.
4. Generates the appropriate **tailored response** automatically.

---

## 🏗️ Architecture

```text
Customer Email (Input)
        │
        ▼
┌─────────────────────┐
│  Email Classifier   │ ← Prompt: nextwork-email-classifier
│  (Prompt Node)      │
└────────┬────────────┘
         │
         ▼
┌─────────────────────┐
│  Condition Node     │ ← Routes based on classification
│  (is_complaint?)    │
└───┬─────────────┬───┘
    │             │
    ▼             ▼
┌──────────┐ ┌──────────────┐
│ Complaint│ │   General    │
│ Response │ │   Response   │
│ (Prompt) │ │   (Prompt)   │
└────┬─────┘ └──────┬───────┘
     │               │
     ▼               ▼
┌─────────────────────────┐
│       Flow Output       │ ← Tailored response returned
└─────────────────────────┘

```

---

## 🛠️ AWS Services Used

| Service | Purpose |
| --- | --- |
| **Amazon Bedrock** | Foundation model access (Amazon Nova 2 Lite) |
| **Bedrock Prompt Management** | Create, version, and manage reusable prompt templates |
| **Bedrock Flows** | Visual workflow builder to chain prompts with conditional routing |
| **Bedrock Guardrails** *(Secret Mission)* | Content safety filtering for harmful/off-topic emails |
| **IAM** | Service roles for flow permissions |

---

## 📝 Prompt Templates Created

| Prompt Name | Purpose | Input Variable | Model | Expected Output |
| --- | --- | --- | --- | --- |
| `nextwork-email-classifier` | Classifies customer emails into specific intent categories | `{{email}}` | Amazon Nova 2 Lite | One lowercase word (`complaint`, `question`, or `refund`) |
| `nextwork-complaint-response` | Generates empathetic and professional responses to customer complaints | `{{email}}` | Amazon Nova 2 Lite | Dynamic empathetic email response |
| `nextwork-general-response` | Generates professional responses for questions, refunds, and other non-complaint emails | `{{email}}` | Amazon Nova 2 Lite | Dynamic general professional response |

---

## 🔀 Flow Design: `nextwork-email-router`

The routing pipeline consists of the following components configured inside Amazon Bedrock's visual Flow Builder:

| Node Number | Node Name | Node Type | Description / Routing Rule |
| --- | --- | --- | --- |
| **1** | Flow Input Node | Input | Receives the raw customer email payload as JSON |
| **2** | EmailClassifier | Prompt Node | Evaluates the email text using `nextwork-email-classifier` |
| **3** | `is_complaint` | Condition Node | **If output = `complaint**` → Routes to Complaint branch<br>

<br>**Default (any other output)** → Routes to General branch |
| **4** | ComplaintResponsePrompt | Prompt Node | Generates an empathetic resolution message using the complaint template |
| **5** | GeneralResponsePrompt | Prompt Node | Generates a crisp, informational message using the general template |
| **6** | Flow Output Node | Output | Delivers the final tailored response back to the user/system |

---

## 🧪 Testing & Validation

| Test Case Scenario | Sample JSON Input Payload | Expected Routing Path | Status |
| --- | --- | --- | --- |
| **Complaint Email** | `{"email": "I am extremely frustrated with your service. My order arrived damaged and nobody has responded to my previous emails. This is unacceptable and I want a resolution immediately."}` | Classifier → **`complaint`** → Complaint Response branch | ✅ Passed |
| **Question Email** | `{"email": "Hi, I was wondering if your product is compatible with macOS. Also, do you offer a student discount?"}` | Classifier → **`question`** → General Response branch (Default) | ✅ Passed |

> **Trace Viewer Verification:** Used the native Bedrock Flows **trace viewer** to track the exact execution flow paths, verifying input/output transitions at every node boundary.

---

## 💎 Secret Mission: Guardrails

Added **Amazon Bedrock Guardrails** (`nextwork-email-router-guardrail`) to the classifier prompt node to filter out harmful or off-topic content before classification begins.

---

## 💰 Cost Analysis

| Resource Component | Pricing Model / Unit | Project Cost Structure |
| --- | --- | --- |
| **Prompt Management Storage** | Free Tier | $0.00 |
| **Bedrock Flows Storage** | Free Tier | $0.00 |
| **Flow Invocations** | ~$0.035 per 1,000 node transitions | < $0.001 |
| **Model Inference** | Pay-per-use (Token-based pricing) | Fraction of a cent per test execution |
| **Total Project Cost** |  | **Less than $0.01** |

---

## 📸 Screenshots

| Execution Step / Component | Target Screenshot File Path |
| --- | --- |
| Bedrock Model Catalog Configuration | `screenshots/model-catalog.png` |
| Classifier Prompt Template Definition | `screenshots/classifier-prompt.png` |
| Centralized Prompt Management Library | `screenshots/prompt-library.png` |
| Main Flow Builder Canvas Architecture | `screenshots/flow-builder.png` |
| Condition Node Branching Logic Setup | `screenshots/condition-node.png` |
| Complaint Flow Test Execution Trace | `screenshots/complaint-trace.png` |
| Question Flow Test Execution Trace | `screenshots/question-trace.png` |

---

## 🧹 Cleanup Strategy

To prevent any unexpected or active resource utilization charges, ensure you drop the following assets:

| Service Workspace | Resource Type | Resource Identifier to Delete |
| --- | --- | --- |
| **Amazon Bedrock → Flows** | AI Workflow Flow | `nextwork-email-router` |
| **Amazon Bedrock → Prompts** | Prompt Templates | `nextwork-email-classifier`, `nextwork-complaint-response`, `nextwork-general-response` |
| **Amazon Bedrock → Guardrails** | Security Guardrail | `nextwork-email-router-guardrail` *(Secret Mission Only)* |

> **Note:** Idle components do not attract recurring baseline management costs.

---

## 🧠 Key Concepts Learned

* ✅ Create and version prompt templates in Bedrock Prompt Management.
* ✅ Use prompt variables (`{{email}}`) to make prompts reusable and dynamic.
* ✅ Build a visual AI workflow with input, prompt, condition, and output nodes.
* ✅ Set up conditional routing based on AI classification results.
* ✅ Test and debug flows with the trace viewer.
* ✅ Add Bedrock Guardrails for content safety.

---

## 🔗 Resources

* [NextWork Learning Platform](https://learn.nextwork.org)
* [Amazon Bedrock Documentation](https://docs.aws.amazon.com/bedrock/)
* [Bedrock Flows User Guide](https://docs.aws.amazon.com/bedrock/latest/userguide/flows.html)

---

## 👤 Author

**Fardin**

* Project: AI Email Router with Bedrock Flows
* **GitHub:** [@FardinAnnoy27]((https://github.com/FardinAnnoy27))
* **LinkedIn:** [Fardin Evne Ayub Annoy](www.linkedin.com/in/fardin-evne-ayub-annoy-750479345)

```

```
