# AgentCode Framework - User Flow Documentation

## Overview

AgentCode Framework is an AI agent system that helps automatically generate SRS and SDD documentation following the IEEE standard. Users simply type a simple command, and the system spawns multiple agents in parallel to produce the documentation.

---

## User Commands

| Command | Description |
|---------|--------|
| `/create-srs [project_name]` | Generate the SRS document |
| `/create-sdd [project_name]` | Generate the SDD document |
| `/generate-docs [project_name]` | Generate both SRS and SDD |
| `/interview-stakeholder` | Interview a stakeholder |

---

## ⚠️ IMPORTANT: The Interview Is MANDATORY

```
╔══════════════════════════════════════════════════════════════════════════════╗
║                                                                              ║
║   ⚠️  THE INTERVIEW CANNOT BE SKIPPED!                                    ║
║                                                                              ║
║   Before creating the SRS, the system MUST interview you.                 ║
║   This ensures the document is ACCURATE to the real requirements.         ║
║                                                                              ║
║   You will be asked 23 questions, one at a time.                          ║
║   After each question, you'll be asked "Are you satisfied?"               ║
║   Finally, you need to type "CONFIRM" to proceed.                         ║
║                                                                              ║
╚══════════════════════════════════════════════════════════════════════════════╝
```

---

## User Flow: `/create-srs MyProject`

### Step 1: User Types Command

```
User: /create-srs "Sales Management System"
```

### Step 2: Introduction + Confirm Start

```
╔══════════════════════════════════════════════════════════════════════════════╗
║  👋 Hello! I'll help you gather the requirements for your project.       ║
║                                                                              ║
║  RULES:                                                                     ║
║  • I will ask ONE question at a time                                      ║
║  • For each question, I need a FULL and SPECIFIC answer                  ║
║  • If an answer is incomplete, I will dig deeper                          ║
║  • After each answer, you'll be asked "Are you satisfied?"                ║
║  • You can add more information at any time                               ║
║                                                                              ║
║  Ready to start? (Type 'start' or 'yes' to continue)                      ║
╚══════════════════════════════════════════════════════════════════════════════╝

User: start
```

### Step 3: THE 23-QUESTION INTERVIEW

```
╔══════════════════════════════════════════════════════════════════════════════╗
║  STEP 2: BASIC PROJECT INFORMATION                                        ║
╠══════════════════════════════════════════════════════════════════════════════╣
║                                                                              ║
║  📝 Question 1/23: What is your project name?                             ║
║                                                                              ║
║  Examples: "Sales Management System", "Food Ordering App", "CRM Software" ║
║                                                                              ║
╚══════════════════════════════════════════════════════════════════════════════╝

User: Sales Management System

📝 Question 2/23: Briefly describe (2-3 sentences) the system you want to build. ║
                                                                              ║
║  Answer hints:                                                            ║
║  • What does the system do?                                               ║
║  • Who uses it?                                                           ║
║  • What is the main purpose?                                              ║
║                                                                              ║
║  Example: "A sales management system for a store with 5 employees..."    ║
╚══════════════════════════════════════════════════════════════════════════════╝

User: sales management

⚠️ Agent Probing (because the answer is VAGUE):
"
You said 'sales management' - specifically, do you mean:
a) Creating orders?
b) Managing inventory?
c) Billing/payment?
d) Printing invoices?
e) Viewing reports?
f) Managing customers?

Do you want all of these, or just some of the features?
"

User: a, b, c, e

👆 Satisfaction Check:
"☝️ That's reasonable. System description:
- Create orders
- Manage inventory
- Billing/payment
- View reports

Are you satisfied with this description? Anything to add?"
```

### Step 4: Continue With the Next Questions

```
📝 Question 3/23: Does this system replace or supplement an existing system?

Answer one of:
A) BRAND NEW - Built from scratch
B) REPLACEMENT - There is an old system that will be fully retired
C) SUPPLEMENT - There is an old system, adding new features
D) UPGRADE - Moving to new technology

User: A

📝 Question 4/23: What business problem or opportunity does this project address?
User: (answer or skip)

☝️ STEP 3: USERS & ACTORS

📝 Question 5/23: Who will use this system?

List all user groups (actors).

Format:
| User Group | Short Description |
| Admin          | Manages the system |
| Sales staff | Creates orders |
| Customer     | Orders online |

User: Admin, Staff, Customer

⚠️ Agent Probing:
"For each user group, I need to know:
1. Admin: What do they do? → Manage users, view reports, configure the system
2. Staff: What do they do? → Create orders, manage inventory
3. Customer: What do they do? → View products, place orders

Is that correct?"
```

### Step 5: STEP 11 - FINAL CONFIRMATION

```
╔══════════════════════════════════════════════════════════════════════════════╗
║  📋 FINAL REVIEW - CONFIRM REQUIREMENTS                                    ║
╠══════════════════════════════════════════════════════════════════════════════╣
║                                                                              ║
║  1. PROJECT INFO                                                             ║
║     Name: Sales Management System                                          ║
║     Description: Create orders, manage inventory, billing, reports        ║
║     Legacy system: Brand new                                              ║
║                                                                              ║
║  2. USERS & ACTORS (3 actors)                                              ║
║     Admin: Manages the system, full access                                ║
║     Staff: Creates orders, manages inventory                              ║
║     Customer: Views products, places orders                               ║
║                                                                              ║
║  3. FEATURES (8 features)                                                  ║
║     Must: 5 | Should: 2 | Could: 1                                         ║
║                                                                              ║
║  4. DATA ENTITIES (6 entities)                                             ║
║     User, Product, Order, Payment, Inventory, Report                        ║
║                                                                              ║
║  5. TECHNICAL                                                               ║
║     Platform: Web + Mobile Responsive                                       ║
║     Performance: < 2s response                                              ║
║     Security: Password + OTP                                                ║
║                                                                              ║
║  6. Open Questions: 2 optional questions unanswered                        ║
║                                                                              ║
╠══════════════════════════════════════════════════════════════════════════════╣
║                                                                              ║
║  ✅ If everything is CORRECT, type "CONFIRM" to generate the SRS          ║
║  📝 If something needs FIXING, tell me which question number to fix       ║
║  ➕ If you want to ADD something, tell me what                             ║
║                                                                              ║
╚══════════════════════════════════════════════════════════════════════════════╝

User: CONFIRM
```

### Step 6: Generate the Requirements Summary

```
╔══════════════════════════════════════════════════════════════════════════════╗
║  ✅ INTERVIEW COMPLETE!                                                    ║
║                                                                              ║
║  📄 Created: docs/HeThongQuanLyBanHang/requirements-summary.md              ║
║                                                                              ║
║  Continuing to generate the SRS...                                        ║
╚══════════════════════════════════════════════════════════════════════════════╝
```

### Step 7: Generate the SRS With Parallel uc-diagram-agents

```
╔══════════════════════════════════════════════════════════════════════════════╗
║  🔄 GENERATING SRS                                                        ║
║                                                                              ║
║  SRS Agent reads requirements-summary.md                                   ║
║                                                                              ║
║  Spawns MULTIPLE uc-diagram-agents in parallel:                           ║
║  @uc-diagram-agent (UC01: Login) &                                        ║
║  @uc-diagram-agent (UC02: Register) &                                     ║
║  @uc-diagram-agent (UC03: ViewProduct) &                                 ║
║  @uc-diagram-agent (UC04: PlaceOrder) &                                   ║
║  @uc-diagram-agent (UC05: Payment) &                                      ║
║  ...                                                                       ║
║  wait                                                                       ║
║                                                                              ║
║  [████████████████████░░░░░░░░░░░░░░] 60%                               ║
╚══════════════════════════════════════════════════════════════════════════════╝
```

### Step 8: Verification + Completion

```
╔══════════════════════════════════════════════════════════════════════════════╗
║  ✅ SRS COMPLETE!                                                          ║
║                                                                              ║
║  Project: Sales Management System                                          ║
║  Version: v1.0.0                                                           ║
║                                                                              ║
║  📄 SRS_HTTT_BanHang_v1.0.0.md                                             ║
║  📁 diagrams/uc-01/ (6 files)                                             ║
║  📁 diagrams/uc-02/ (6 files)                                             ║
║  📁 diagrams/uc-03/ (6 files)                                             ║
║  📁 diagrams/uc-04/ (6 files)                                             ║
║  📁 diagrams/uc-05/ (6 files)                                             ║
║  📁 diagrams/context-diagram.puml                                          ║
║  📁 diagrams/entity-relationship.puml                                     ║
║                                                                              ║
║  Next: /create-sdd "Sales Management System"                              ║
╚══════════════════════════════════════════════════════════════════════════════╝
```

---

## User Flow: `/create-sdd MyProject`

### The SDD Requires the SRS to Be Complete

```
User: /create-sdd "Sales Management System"

╔══════════════════════════════════════════════════════════════════════════════╗
║  ✅ VERIFYING SRS                                                          ║
║                                                                              ║
║  ✓ SRS_HTTT_BanHang_v1.0.0.md exists                                     ║
║  ✓ requirements-summary.md exists                                         ║
║  ✓ User has fully confirmed                                               ║
║                                                                              ║
║  Continuing to the SDD...                                                 ║
╚══════════════════════════════════════════════════════════════════════════════╝
```

### The SDD Agent at Work

```
╔══════════════════════════════════════════════════════════════════════════════╗
║  🔄 GENERATING SDD                                                        ║
║                                                                              ║
║  SDD Agent reads the SRS and identifies components from the features      ║
║                                                                              ║
║  Spawns MULTIPLE component-agents in parallel:                            ║
║  @component-agent (Auth) &                                                 ║
║  @component-agent (Product) &                                              ║
║  @component-agent (Order) &                                                ║
║  @component-agent (Payment) &                                              ║
║  @component-agent (Inventory) &                                            ║
║  @component-agent (Report) &                                               ║
║  wait                                                                       ║
║                                                                              ║
║  [████████████████████░░░░░░░░░░░░░░] 60%                               ║
╚══════════════════════════════════════════════════════════════════════════════╝
```

---

## User Flow: `/generate-docs MyProject` (Both)

```
User: /generate-docs "Sales Management System"

┌─────────────────────────────────────────────────────────────┐
│  1. MANDATORY interview (as above)                       │
│  2. User types "CONFIRM"                                  │
│  3. requirements-summary.md is created                    │
└─────────────────────────────────────────────────────────────┘
                          │
                          ▼
┌─────────────────────────────────────────────────────────────┐
│  4. SRS + SDD run IN PARALLEL:                            │
│                                                             │
│  SRS Agent path:              SDD Agent path:              │
│  - uc-diagram-agents         - component-agents           │
│  - srs-writer               - db-agent + api-agent       │
│                              - sdd-writer                  │
└─────────────────────────────────────────────────────────────┘
                          │
                          ▼
╔══════════════════════════════════════════════════════════════════════╗
║  ✅ ALL DOCUMENTS COMPLETE!                                       ║
║                                                                      ║
║  📄 SRS_HTTT_BanHang_v1.0.0.md                                   ║
║  📄 SDD_HTTT_BanHang_v1.0.0.md                                   ║
║  📄 requirements-summary.md                                        ║
║  📁 diagrams/ (50+ files)                                         ║
║  📁 db/schema.sql                                                 ║
║  📁 api/openapi.yaml                                              ║
╚══════════════════════════════════════════════════════════════════════╝
```

---

## Detailed Interview Flow (23 Questions)

```
START
   │
   ▼
┌──────────────────────────────────────────────────────────────┐
│  STEP 1: INTRODUCTION (No question)                        │
│  - Agent introduces the rules                                │
│  - User types "start"                                        │
└──────────────────────────────────────────────────────────────┘
   │
   ▼
┌──────────────────────────────────────────────────────────────┐
│  STEP 2: PROJECT INFORMATION (4 questions)                 │
│  - Q1: Project name [REQUIRED]                             │
│  - Q2: System description [REQUIRED]                       │
│  - Q3: Existing system? [REQUIRED]                          │
│  - Q4: Business problem [OPTIONAL]                          │
└──────────────────────────────────────────────────────────────┘
   │
   ▼
┌──────────────────────────────────────────────────────────────┐
│  STEP 3: USERS & ACTORS (3 questions)                       │
│  - Q5: Who uses it? [REQUIRED]                              │
│  - Q6: Permissions? [REQUIRED]                               │
│  - Q7: Guest users? [OPTIONAL]                               │
└──────────────────────────────────────────────────────────────┘
   │
   ▼
┌──────────────────────────────────────────────────────────────┐
│  STEP 4: FEATURES & MOSCOW (2 questions)                    │
│  - Q8: Feature list [REQUIRED]                              │
│  - Q9: MoSCoW classification [REQUIRED]                     │
└──────────────────────────────────────────────────────────────┘
   │
   ▼
┌──────────────────────────────────────────────────────────────┐
│  STEP 5: DATA (2 questions)                                 │
│  - Q10: Entities [REQUIRED]                                  │
│  - Q11: Data volume [OPTIONAL]                                │
└──────────────────────────────────────────────────────────────┘
   │
   ▼
┌──────────────────────────────────────────────────────────────┐
│  STEP 6: PLATFORM (2 questions)                              │
│  - Q12: Access platform [REQUIRED]                          │
│  - Q13: UI requirements [OPTIONAL]                            │
└──────────────────────────────────────────────────────────────┘
   │
   ▼
┌──────────────────────────────────────────────────────────────┐
│  STEP 7: INTEGRATION (2 questions)                           │
│  - Q14: Other systems? [OPTIONAL]                            │
│  - Q15: Data import/export? [OPTIONAL]                       │
└──────────────────────────────────────────────────────────────┘
   │
   ▼
┌──────────────────────────────────────────────────────────────┐
│  STEP 8: NON-FUNCTIONAL (4 questions)                        │
│  - Q16: Performance [REQUIRED]                               │
│  - Q17: Number of concurrent users [REQUIRED]                │
│  - Q18: Data security [REQUIRED]                             │
│  - Q19: Uptime? [OPTIONAL]                                    │
└──────────────────────────────────────────────────────────────┘
   │
   ▼
┌──────────────────────────────────────────────────────────────┐
│  STEP 9: SECURITY (2 questions)                              │
│  - Q20: Login? [REQUIRED]                                    │
│  - Q21: Regulatory compliance? [OPTIONAL]                    │
└──────────────────────────────────────────────────────────────┘
   │
   ▼
┌──────────────────────────────────────────────────────────────┐
│  STEP 10: BUSINESS RULES (2 questions)                       │
│  - Q22: Automated rules? [OPTIONAL]                           │
│  - Q23: Budget/timeline? [OPTIONAL]                            │
└──────────────────────────────────────────────────────────────┘
   │
   ▼
┌──────────────────────────────────────────────────────────────┐
│  STEP 11: FINAL CONFIRMATION                                │
│  - Display the SUMMARY table                                │
│  - User types "CONFIRM"                                     │
└──────────────────────────────────────────────────────────────┘
   │
   ▼
┌──────────────────────────────────────────────────────────────┐
│  GENERATE THE REQUIREMENTS SUMMARY                          │
│  Continue to SRS/SDD                                        │
└──────────────────────────────────────────────────────────────┘
```

---

## Time Comparison

| Task | Manual Time | With AgentCode (with interview) |
|------|-------------|-------------------------------|
| Requirements-gathering interview | 0 (skipped) | ~25-45 min |
| SRS (5 UCs) | ~2 hours | ~2-3 min |
| SDD (6 components) | ~4 hours | ~2-3 min |
| Diagrams | ~3 hours | ~1-2 min |
| **Total** | **~9 hours** (usually needs rework) | **~30-50 min** |

**ROI:** Invest 30-45 minutes in the interview → Save 2-4 hours of document rework!

---

## Summary: What User Does vs What System Does

| User Action | System Action |
|-------------|---------------|
| Type `/create-srs MyProject` | Greets and introduces the rules |
| Types "start" | Begins the interview |
| Answers 23 questions | Asks each question + digs deeper when vague |
| Answers the satisfaction check | Ensures the information is complete |
| Types "CONFIRM" | Generates requirements-summary.md |
| Waits | Spawns 18 agents in parallel |
| Receives the result | Generates 50+ files with quality gates |
