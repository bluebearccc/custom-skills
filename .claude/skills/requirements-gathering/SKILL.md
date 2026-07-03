---
name: requirements-gathering
description: |
  Gather software requirements by interviewing the user in DETAIL, question by question.
  The user MUST answer fully BEFORE moving to the next question.
  Skipping is NOT ALLOWED - if an answer is incomplete, DIG DEEPER until the user CONFIRMS they are satisfied.

  USE WHEN:
  - The user wants to gather requirements for a new project
  - Stakeholders need to be interviewed with specific questions

  IMPORTANT: You MUST use the AskUserQuestion tool for ALL interview questions.
version: "4.0.0"
allowed-tools: ["Read", "Write", "Edit", "Glob", "AskUserQuestion"]
mode: false
---

# Requirements Gathering Skill v4.0.0

## MANDATORY Principles

### 0. HOW TO ASK — AskUserQuestion FOR CHOICES, PROSE FOR FREE-FORM INPUT

> ⚠️ **IMPORTANT — read carefully before using:** The `AskUserQuestion` tool **requires every question to have 2–4 `options`**.
> The tool **automatically adds an "Other" option** so the user can type freely. **There is no text-only mode.**
> Therefore:
> - **CHOICE questions** (selecting from a list: MoSCoW, platform, sync mode, yes/no…) → use `AskUserQuestion` with `options`.
> - **OPEN-ENDED questions** (project name, description, listing features/entities, filling in a table…) → **ask in prose** (plain text) and let the user type their answer. Do NOT force these into an `AskUserQuestion` with empty options.

```
✅ CHOICE question → AskUserQuestion (with options)
✅ OPEN question   → prose: "📝 Question X/38: Briefly describe the system you want to build?"
```

### AskUserQuestion — CORRECT signature

> The code blocks below in this skill are **illustrative**. When actually running, call the real tool
> with the exact field names below (especially `multiSelect`, **not** `multiSelect`):

```javascript
AskUserQuestion({
  questions: [
    {
      question: "Question text (line breaks with \n)",
      header: "Short label (≤ 12 characters)",
      multiSelect: false,          // false = single choice · true = multiple choice
      options: [                    // REQUIRED 2–4 options
        { label: "Option A", description: "Description A" },
        { label: "Option B", description: "Description B" }
      ]
    }
  ]
})
```

### USAGE RULES:
- Every AskUserQuestion question **must have 2–4 `options`** — if there's no clear set of choices, ask in prose instead.
- `multiSelect: false` for single choice; `multiSelect: true` for multiple choice.
- The user can always choose "Other" to type a free-form answer — no need to manually add an "Other" option.
- ALWAYS use `\n` for line breaks in the question text.

### 1. ASK ONE QUESTION AT A TIME - LOOP UNTIL COMPLETE
```
❌ WRONG: Ask → User gives a short answer → Move to the next question
✅ RIGHT: Ask → User answers → Evaluate → If NOT COMPLETE → DIG DEEPER → Repeat → Until COMPLETE → Ask "Are you satisfied?" → Confirm → Next question
```

### 2. THE "SATISFIED?" MECHANISM - CONFIRMATION IS MANDATORY
```
After EVERY main answer, you MUST ask:
"☝️ That's good for now. Would you like to add anything else? Or shall we move to the next question?"
- User agrees → Move to the next question
- User wants to add more → Continue with the current question
```

### 3. DIG DEEPER WHEN ANSWERS ARE VAGUE/SHORT
```
User: "We need user management"
→ "What exactly does 'user management' mean? Is it:"
     a) Creating a new user?
     b) Editing user info?
     c) Deleting a user?
     d) Managing user permissions?
     e) Resetting passwords?
   Do you want all of these, or just some of these features?"

User: "around 1000"
→ "1000 users? 1000 products? 1000 orders? What kind of data does this number refer to?"
```

### 4. TRACK THE STATUS OF EACH QUESTION
```
Every question must have a status:
[ ] Pending    - Not yet asked
[ ] Asked      - Asked, awaiting an answer
[ ] Probing    - Currently digging deeper
[ ] Satisfied  - User confirmed it's complete
[ ] Skipped    - User declined to answer (record the reason)
```

### 5. USE CASES MUST BE FULLY STRUCTURED BEFORE MOVING ON
```
Every Must-Have feature (and any Should-Have the user flags as needing detailed design) MUST have
a complete Use Case entry before you leave Question 10:

  UC ID · Actor(s) · Trigger · Preconditions · Main Flow (numbered steps)
  · Alternative Flows · Exception/Error Flows · Postconditions

This is NOT optional detail. The diagram-generation agents downstream (sequence diagrams, state
diagrams, screen flows) read directly from these fields. A feature with only a name and a
one-line description is NOT enough to generate a correct diagram from — it will be fabricated
by a later agent instead of reflecting what the stakeholder actually said.
```

---

## Interview Flow (12 Steps)

### STEP 1: Introduction + Confirm Start

**USE the AskUserQuestion tool:**
```javascript
tool.Call("AskUserQuestion", {
  questions: [{
    question: "👋 Hello! I'll help you gather requirements for your project.\n\nRULES:\n• I will ask ONE question at a time\n• For each question, I need you to answer FULLY and SPECIFICALLY\n• If an answer is incomplete, I will dig deeper\n• After each answer, I'll ask you 'Are you satisfied?'\n• You can add more information at any time\n\nShall we start?",
    header: "Start",
    options: [
      { label: "start", description: "I'm ready to begin the interview" },
      { label: "Need more info first", description: "I'd like to learn more about the process" }
    ],
    multiSelect: false
  }]
})
```

---

### STEP 2: Basic Project Information

**Question 2.1 (Required) - PROJECT NAME:**

**USE the AskUserQuestion tool:**
```javascript
tool.Call("AskUserQuestion", {
  questions: [{
    question: "📝 Question 1/38: What is the name of your project?\n\nExamples: 'Sales Management System', 'Food Ordering App', 'CRM Software'",
    header: "Project Name"
  }]
})
```

**Probing 2.1a (if too short/generic) - USE AskUserQuestion:**
```javascript
tool.Call("AskUserQuestion", {
  questions: [{
    question: "Is this the official name of the project?\nOr is this just a working name to distinguish it from other projects?",
    header: "Confirm Name"
  }]
})
```

**SATISFACTION CHECK 2.1 - USE AskUserQuestion:**
```javascript
tool.Call("AskUserQuestion", {
  questions: [{
    question: "☝️ Project name: [name entered]\n\nAre you satisfied with this name? Would you like to change or add anything?",
    header: "Confirm OK?"
  }]
})
```

---

**Question 2.2 (Required) - SYSTEM DESCRIPTION:**

**USE the AskUserQuestion tool:**
```javascript
tool.Call("AskUserQuestion", {
  questions: [{
    question: "📝 Question 2/38: Briefly describe (2-3 sentences) the system you want to build.\n\nSuggested points to cover:\n• What does the system do?\n• Who uses it?\n• What is the main purpose?\n\nExample: 'A sales management system for a store with 5 employees. Employees can create orders and track inventory. The store owner views revenue reports.'",
    header: "System Desc."
  }]
})
```

**Probing 2.2a (if the description is too generic) - USE AskUserQuestion:**
```javascript
tool.Call("AskUserQuestion", {
  questions: [{
    question: "You mentioned 'sales management' - specifically, does that mean:\na) Creating orders?\nb) Managing inventory?\nc) Calculating payments/checkout?\nd) Printing invoices?\ne) Viewing reports?\nf) Managing customers?\n\nDo you want all of these, or only some of these features?",
    header: "Clarify Features",
    options: [
      { label: "All (a-f)", description: "Need all features" },
      { label: "Only a, b, c", description: "Orders, inventory, payments" },
      { label: "Only a, b, e", description: "Orders, inventory, reports" },
      { label: "Other", description: "I'll list them specifically" }
    ],
    multiSelect: false
  }]
})
```

**SATISFACTION CHECK 2.2 - USE AskUserQuestion:**
```javascript
tool.Call("AskUserQuestion", {
  questions: [{
    question: "☝️ System description:\n[entered]\n\nAre you satisfied with this description? Does it need anything added about:\n• The system's main functions?\n• The target users?\n• The system's goals?",
    header: "Confirm OK?"
  }]
})
```

---

**Question 2.3 (Required) - EXISTING SYSTEM:**

**USE the AskUserQuestion tool:**
```javascript
tool.Call("AskUserQuestion", {
  questions: [{
    question: "📝 Question 3/38: Does this system replace or supplement any existing system?\n\nAnswer with one of the following cases:\nA) BRAND NEW - Built from scratch, no existing system\nB) REPLACEMENT - There is an old system, it will be fully retired\nC) SUPPLEMENT - There is an old system, adding new features to it\nD) UPGRADE - There is an old system, migrating to new technology",
    header: "Existing System",
    options: [
      { label: "A) Brand new", description: "Built from scratch, no existing system" },
      { label: "B) Replacement", description: "There is an old system, will be fully retired" },
      { label: "C) Supplement", description: "There is an old system, adding new features" },
      { label: "D) Upgrade", description: "There is an old system, migrating to new technology" }
    ],
    multiSelect: false
  }]
})
```

**Probing 2.3a (if B/C/D is selected) - USE AskUserQuestion:**
```javascript
tool.Call("AskUserQuestion", {
  questions: [{
    question: "• What is the current system called?\n• What technology is it currently using? (Excel, legacy software, database...)\n• Is there any data that needs to be migrated?\n• Which features from the old system MUST be kept?\n• Which features from the old system CAN be dropped?",
    header: "Old System Details"
  }]
})
```

**SATISFACTION CHECK 2.3 - USE AskUserQuestion:**
```javascript
tool.Call("AskUserQuestion", {
  questions: [{
    question: "☝️ Current system: [described]\n\nIs the information complete? Would you like to add anything about:\n• Data that needs to be migrated?\n• Features that must be kept?\n• Transition timeline?",
    header: "Confirm OK?"
  }]
})
```

---

**Question 2.4 (Optional) - BUSINESS PROBLEM:**

**USE the AskUserQuestion tool:**
```javascript
tool.Call("AskUserQuestion", {
  questions: [{
    question: "📝 Question 4/38: What business problem or opportunity does this project address?\n\nSuggested points to cover:\n• What problems exist with the current process?\n• What benefits will this project bring?\n• How will success be measured? (time saved, revenue increase, fewer errors...)",
    header: "Business Problem"
  }]
})
```

---

**Question 2.5 (Required) - STAKEHOLDERS & APPROVAL:**

**USE the AskUserQuestion tool:**
```javascript
tool.Call("AskUserQuestion", {
  questions: [{
    question: "📝 Question 5/38: Who are the key stakeholders for this project?\n\nI need to know:\n• Project Sponsor - who owns the budget and is ultimately accountable?\n• Product Owner / Business Owner - who decides what goes in scope?\n• Main point of contact - who do I ask when I have requirement questions?\n• Final approval authority - who signs off on the SRS before development starts?\n\nExample: 'Sponsor: CFO Nguyen Van A. Product Owner: Sales Director Tran Thi B. Point of contact: Tran Thi B. Approval: both must sign off.'",
    header: "Stakeholders"
  }]
})
```

**Probing 2.5a (if roles are unclear or overlapping) - USE AskUserQuestion:**
```javascript
tool.Call("AskUserQuestion", {
  questions: [{
    question: "A few follow-ups:\n• Is the Sponsor the same person as the Product Owner, or someone different?\n• If the Sponsor and Product Owner disagree on a requirement, who has the final say?\n• Are there other stakeholders whose sign-off is needed (e.g., Legal, Security, IT)?\n• Is there anyone with a conflicting interest we should be aware of (e.g., two departments wanting different priorities)?",
    header: "Clarify Roles"
  }]
})
```

**SATISFACTION CHECK 2.5 - USE AskUserQuestion:**
```javascript
tool.Call("AskUserQuestion", {
  questions: [{
    question: "☝️ Stakeholders:\n• Sponsor: [name]\n• Product Owner: [name]\n• Point of contact: [name]\n• Approval authority: [name(s)]\n\nAre you satisfied with this? Is anyone missing who needs to sign off on the SRS?",
    header: "Confirm OK?"
  }]
})
```

---

### STEP 3: Users & Actors

**Question 3.1 (Required) - LIST OF USERS:**

**USE the AskUserQuestion tool:**
```javascript
tool.Call("AskUserQuestion", {
  questions: [{
    question: "📝 Question 6/38: Who will use this system?\n\nList all user groups (actors).\n\nAnswer in this format:\n| User Group | Short Description |\n| Admin | Manages the system |\n| Sales Staff | Creates orders |\n| Customer | Orders online |\n\nExample: 'Admin, Warehouse Staff, Accountant, Customer'",
    header: "User List"
  }]
})
```

**Probing 3.1a (if the list is too generic) - USE AskUserQuestion:**
```javascript
tool.Call("AskUserQuestion", {
  questions: [{
    question: "For each user group, I need to know:\n1. [Group 1]: What do they do in the system? (create? read? update? delete?)\n2. [Group 2]: What do they do in the system?\n...\n\nExample:\n• Admin: Full control over the system\n• Accountant: Can only view and export financial reports\n• Customer: Can only place orders and view order history",
    header: "User Details"
  }]
})
```

**Probing 3.1b (if obvious actors are missing) - USE AskUserQuestion:**
```javascript
tool.Call("AskUserQuestion", {
  questions: [{
    question: "It looks like your list may be incomplete. Consider:\n• A manager (views reports, approves requests)?\n• A delivery person (updates delivery status)?\n• A prospective customer (views products before signing up)?\n• A partner/supplier (views purchase orders)?",
    header: "Actor Suggestions"
  }]
})
```

**SATISFACTION CHECK 3.1 - USE AskUserQuestion:**
```javascript
tool.Call("AskUserQuestion", {
  questions: [{
    question: "☝️ User list:\n[listed]\n\nAre you satisfied with this list?\n• Are there other groups that use the system?\n• Has each group's permissions been described sufficiently?\n• Does any group need to be SPLIT into sub-groups?",
    header: "Confirm OK?"
  }]
})
```

---

**Question 3.2 (Required) - PERMISSIONS PER ACTOR:**

**USE the AskUserQuestion tool:**
```javascript
tool.Call("AskUserQuestion", {
  questions: [{
    question: "📝 Question 7/38: What permissions does each user group have, and how do they differ?\n\nFor each actor, I need to know:\n• What are they ALLOWED to do?\n• What are they NOT allowed to do?\n• Do they need approval from anyone?\n\nSuggested format:\n| Actor | Allowed | Not Allowed | Needs Approval |\n|-------|---------|----------------|---------------|\n| Admin | Everything | Nothing | No one |\n| Staff | CRUD orders | Delete approved orders | Manager |",
    header: "Permissions"
  }]
})
```

**Probing 3.2a (if permissions are vague) - USE AskUserQuestion:**
```javascript
tool.Call("AskUserQuestion", {
  questions: [{
    question: "'Admin can do everything' - specifically, does that mean:\na) Create/edit/delete all data?\nb) Change system configuration?\nc) View sensitive reports?\nd) Manage other users?\n\nFor Staff:\na) CRUD orders - what does CRUD mean here? Create, view, edit, delete?\nb) Can they delete orders?\nc) Can they issue refunds?\nd) Can they view reports?",
    header: "Clarify Permissions"
  }]
})
```

**SATISFACTION CHECK 3.2 - USE AskUserQuestion:**
```javascript
tool.Call("AskUserQuestion", {
  questions: [{
    question: "☝️ Actor permissions:\n[described]\n\nAre you satisfied with these permissions?\n• Are there any permission conflicts?\n• Does any group need higher/lower permissions?",
    header: "Confirm OK?"
  }]
})
```

---

**Question 3.3 (Optional) - ANONYMOUS USERS:**

**USE the AskUserQuestion tool:**
```javascript
tool.Call("AskUserQuestion", {
  questions: [{
    question: "📝 Question 8/38: Are there anonymous users (guests/not logged in)?\n\nIf YES, can they:\n• View products/services?\n• Compare products?\n• Add items to a cart without ordering?\n• Register a new account?\n\nIf NO, why not? (must log in before viewing content?)",
    header: "Guest Users"
  }]
})
```

---

## SECTION C: FUNCTIONAL REQUIREMENTS (Questions 9-14)

### Question 9 (Required) - FEATURES LIST & MOSCOW:

**USE the AskUserQuestion tool:**
```javascript
tool.Call("AskUserQuestion", {
  questions: [{
    question: "📝 Question 9/38: What functions does the system need? List all features in priority order.\n\nFormat:\n| No. | Feature Name | Short Description | Main Actor | MoSCoW |\n|-----|--------------|-------------|-------------|--------|\n| 1 | Login | Email + password | User | Must |\n| 2 | Create Order | Create a new order | Staff | Must |\n...\n\nSuggested feature groups:\n• Account management (register, login, forgot password)\n• Basic CRUD (create, read, update, delete data)\n• Search & Filter & Sort\n• Reports & Statistics\n• Payment & Transactions\n• Notifications (email/SMS/push)\n\nNote: for every feature marked Must (and any Should you want designed in detail), we will assign it a Use Case ID (UC01, UC02, ...) and specify its full flow in the next question — so make sure the feature list is complete before moving on.",
    header: "Features"
  }]
})
```

**Probing 9a (if obvious features are missing) - USE AskUserQuestion:**
```javascript
tool.Call("AskUserQuestion", {
  questions: [{
    question: "It looks like you haven't listed some common features:\n• [ ] Login/Logout\n• [ ] Register/Create account\n• [ ] Forgot password/Reset password\n• [ ] View list/List data\n• [ ] Search\n• [ ] Filter\n• [ ] Sort\n• [ ] Pagination\n• [ ] Export report (Excel, PDF)\n• [ ] Import data\n• [ ] Backup data\n• [ ] Notifications (email/SMS/push)\n• [ ] Activity history/Audit log",
    header: "Feature Suggestions",
    options: [
      { label: "Add all", description: "Add the common features" },
      { label: "Only some", description: "Select the relevant features" },
      { label: "That's enough", description: "No need to add more" }
    ],
    multiSelect: false
  }]
})
```

**SATISFACTION CHECK 9 - USE AskUserQuestion:**
```javascript
tool.Call("AskUserQuestion", {
  questions: [{
    question: "☝️ Feature list:\n[listed]\n\nAre you satisfied with this list?\n• Are there any other features?\n• Which features aren't described in enough detail yet?\n• Is the priority order correct?",
    header: "Confirm OK?"
  }]
})
```

---

### Question 10 (Required, REPEAT FOR EACH Must-Have FEATURE) - USE CASE DETAILED SPECIFICATION:

⚠️ **This question MUST be repeated for every Must-Have feature from Question 9 (and any Should-Have feature the user wants designed in detail) before moving to Question 11.** Track completion explicitly, e.g. "UC01 ✅, UC02 ✅, UC03 ⏳..." Do not proceed with partial coverage — see Principle 5.

**USE the AskUserQuestion tool (once per feature) — OPEN-ENDED, so ask in prose:**
```javascript
tool.Call("AskUserQuestion", {
  questions: [{
    question: "📝 Question 10/38 — Use Case [UC0X] '[Feature Name]':\n\nI need the full specification for this use case:\n\n1. UC ID: UC0X\n2. Actor(s): who performs it?\n3. Trigger: what starts it? (user action, schedule, event, another system)\n4. Preconditions: what must be true BEFORE this can start? (e.g., user is logged in, cart is not empty)\n5. Main Flow: the happy-path steps, numbered (Step 1 → Step 2 → Step 3 → ...)\n6. Alternative Flows: valid variations of the happy path (e.g., user applies a discount code)\n7. Exception/Error Flows: what happens when something goes wrong at each step? (validation fails, payment declined, network error)\n8. Postconditions: what must be true AFTER this completes successfully? (e.g., order status = 'Paid', inventory decremented)\n\nExample (UC04 - Place Order):\n• Preconditions: User is logged in; cart has ≥1 item\n• Main Flow: 1) User reviews cart → 2) User enters shipping address → 3) User selects payment method → 4) User confirms order → 5) System creates order record → 6) System sends confirmation email\n• Alternative Flow: User applies a discount code before confirming\n• Exception Flow: Payment is declined → order stays 'Pending Payment', user is notified, cart is preserved\n• Postconditions: Order status = 'Paid'; inventory decremented; confirmation email sent",
    header: "UC0X Spec"
  }]
})
```

**Probing 10a (if any of the 8 fields is missing or vague):**
```
This use case is still missing some detail. Specifically:
[list the missing field(s): Preconditions / Main Flow steps / Alternative Flows / Exception Flows / Postconditions]

For exception flows in particular, walk through EACH step of the main flow and ask:
"what could go wrong here, and what does the system do about it?"
```

**Probing 10b (if the feature has no meaningful alternative/exception flow) - USE AskUserQuestion:**
```javascript
tool.Call("AskUserQuestion", {
  questions: [{
    question: "Is it really true that this use case has no alternative paths and nothing can go wrong? Even simple use cases usually have at least one exception (e.g., 'required field missing', 'permission denied', 'record not found'). Confirm there really is none, or add it now.",
    header: "Confirm No Exceptions",
    options: [
      { label: "Confirmed - none", description: "This use case genuinely has no alternative/exception flow" },
      { label: "Add one now", description: "I'll describe the missing flow" }
    ],
    multiSelect: false
  }]
})
```

**SATISFACTION CHECK 10 (per UC) - USE AskUserQuestion:**
```javascript
tool.Call("AskUserQuestion", {
  questions: [{
    question: "☝️ UC0X '[Feature Name]':\n• Preconditions: [listed]\n• Main Flow: [N steps]\n• Alternative Flows: [listed / none]\n• Exception Flows: [listed]\n• Postconditions: [listed]\n\nIs this use case complete? Ready for the next one, or need to add anything here?",
    header: "Confirm OK?"
  }]
})
```

**LOOP CHECK (after each UC, until all Must-Have features are covered) - USE AskUserQuestion:**
```javascript
tool.Call("AskUserQuestion", {
  questions: [{
    question: "✅ UC0X done. [Y] of [N] Must-Have use cases specified so far.\n\nReady for UC0(X+1) '[next feature name]'?",
    header: "Next UC?",
    options: [
      { label: "Yes, continue", description: "Move to the next use case" },
      { label: "Pause here", description: "I need a break, we'll continue later" }
    ],
    multiSelect: false
  }]
})
```

---

### Question 11 (Required) - MOSCOW SANITY CHECK:

**USE the AskUserQuestion tool:**
```javascript
tool.Call("AskUserQuestion", {
  questions: [{
    question: "📝 Question 11/38: Now that every Must-Have feature has a full use case spec, let's sanity-check priorities. Which features are truly MANDATORY, and which are 'nice to have'?\n\nUse MoSCoW:\n| Symbol | Meaning | Definition |\n|---------|---------|-------------|\n| M | Must Have | Without it = CANNOT be released |\n| S | Should Have | Should have = Important but not mandatory |\n| C | Could Have | Could have = Nice to have |\n| W | Won't Have | Not included in this version |",
    header: "MoSCoW"
  }]
})
```

**Probing 11a (if everything is marked Must) - USE AskUserQuestion:**
```javascript
tool.Call("AskUserQuestion", {
  questions: [{
    question: "⚠️ You marked everything as MUST. In reality, very few systems need every feature right away.\n\nConsider:\n• If feature X were MISSING, could the system still function?\n• If feature Y were missing, would users give up?\n\nOften you can shift:\n• Detailed reports → export to Excel first instead\n• Complex dashboard → use a simple table first\n• Mobile app → use responsive web first\n• Real-time notifications → use regular email first",
    header: "Adjust Must-Haves"
  }]
})
```

**SATISFACTION CHECK 11 - USE AskUserQuestion:**
```javascript
tool.Call("AskUserQuestion", {
  questions: [{
    question: "☝️ MoSCoW classification:\nMust Have: [list]\nShould Have: [list]\nCould Have: [list]\n\nAre you satisfied with this classification?\n• Is any feature mis-prioritized?\n• Does anything need adjusting?",
    header: "Confirm OK?"
  }]
})
```

---

### Question 12 (Required) - BUSINESS RULES & CALCULATIONS:

**USE the AskUserQuestion tool:**
```javascript
tool.Call("AskUserQuestion", {
  questions: [{
    question: "📝 Question 12/38: Are there any special business rules the system should automate?\n\nFormat:\n| Rule | Description | Trigger | Action |\n|------|-------|---------|--------|\n| Auto_cancel | Auto-cancel order after 24h | Order unpaid > 24h | Cancel + notify |\n| Tier_pricing | Discount by quantity | Qty > 10 | 10% off |\n| Auto_refund | Auto-refund on cancellation | Order cancelled | Refund to original payment |\n\nExamples:\n• Automatically send an email when a new order arrives\n• Automatically calculate commission for agents (%)\n• Automatically renew a subscription\n• Automatic inventory alert when stock runs out\n• Validation rules (conditions for validating input)",
    header: "Business Rules"
  }]
})
```

**Probing 12a - USE AskUserQuestion:**
```javascript
tool.Call("AskUserQuestion", {
  questions: [{
    question: "For each rule:\n1. When does this rule apply? (trigger condition)\n2. What does the system do when triggered? (action)\n3. Are there any exceptions? (when does it not apply)\n4. Who can override it manually?\n5. Is there a log for auditing?",
    header: "Dig Into Rules"
  }]
})
```

**SATISFACTION CHECK 12 - USE AskUserQuestion:**
```javascript
tool.Call("AskUserQuestion", {
  questions: [{
    question: "☝️ Business Rules list:\n[listed]\n\nAre you satisfied with this list?\n• Are there any other rules?\n• Does the logic of any rule need adjusting?",
    header: "Confirm OK?"
  }]
})
```

---

### Question 13 (Required) - VALIDATION & ERROR HANDLING:

**USE the AskUserQuestion tool:**
```javascript
tool.Call("AskUserQuestion", {
  questions: [{
    question: "📝 Question 13/38: How does the system validate data? How is error handling done?\n\nFor each input type, I need to know:\n• Required fields?\n• Format validation? (email format, phone format, date format)\n• Range validation? (min/max quantity, date range)\n• Custom validation rules?\n\nError handling:\n• What is shown when validation fails?\n• What happens on a network error?\n• What happens on a timeout?\n• What happens when permission is denied?\n• Is there a retry mechanism?",
    header: "Validation"
  }]
})
```

**Probing 13a - USE AskUserQuestion:**
```javascript
tool.Call("AskUserQuestion", {
  questions: [{
    question: "For each validation rule:\n1. What is the specific rule? (e.g., email must be in a valid format)\n2. What message is shown on failure?\n3. Does it prevent form submission?\n4. Is the invalid field highlighted?\n\nFor error handling:\n1. Network error → show a retry button?\n2. Timeout → auto-retry how many times?\n3. Permission denied → redirect to login?",
    header: "Dig Into Validation"
  }]
})
```

**SATISFACTION CHECK 13 - USE AskUserQuestion:**
```javascript
tool.Call("AskUserQuestion", {
  questions: [{
    question: "☝️ Validation & Error Handling:\n[described]\n\nAre you satisfied with this handling?\n• Any other validation to add?\n• Any error case not yet handled?",
    header: "Confirm OK?"
  }]
})
```

---

### Question 14 (Optional) - REPORTING & ANALYTICS:

**USE the AskUserQuestion tool:**
```javascript
tool.Call("AskUserQuestion", {
  questions: [{
    question: "📝 Question 14/38: What reports and analytics are needed?\n\nFormat:\n| Report | Description | Viewed By | Frequency | Format |\n|--------|--------|--------|----------|--------|\n| Revenue | Revenue summary by day | Admin, Manager | Daily | PDF, Excel |\n| Inventory | Inventory report | Admin, Warehouse | Weekly | Excel |\n\nNeed to know:\n• Real-time or delayed reports?\n• Who is allowed to view which reports?\n• Is drill-down needed? (click to see details)\n• Which export formats are supported?\n• Are there scheduled reports sent automatically by email?",
    header: "Reports"
  }]
})
```

**SATISFACTION CHECK 14 - USE AskUserQuestion:**
```javascript
tool.Call("AskUserQuestion", {
  questions: [{
    question: "☝️ Reports/Analytics list:\n[listed]\n\nAre you satisfied with this list?\n• Are there other reports needed?\n• Any special dashboard/visualization needed?",
    header: "Confirm OK?"
  }]
})
```

---

## SECTION D: DATA MODEL (Questions 15-17)

### Question 15 (Required) - ENTITIES & FIELDS:

**USE the AskUserQuestion tool:**
```javascript
tool.Call("AskUserQuestion", {
  questions: [{
    question: "📝 Question 15/38: What kinds of data does the system need to store? (Entities)\n\nFormat:\n| Entity | Description | Primary Fields | Managed By |\n|--------|--------|-----------------|-------------|\n| User | User account | id, name, email, phone, password, role | Admin |\n| Product | Product/service | id, name, price, stock, category_id | Admin |\n| Order | Order | id, user_id, total, status, created_at | Staff |\n\nHint - Think about the NOUNS in the system:\n• Who? → User, Customer, Employee, Admin",
    header: "Entities"
  }]
})
```

**Probing 15a (dig deeper into each entity):**
```
For entity "[name]", I need to know:
1. What fields/information does it need?
   Example: User has: id, name, email, phone, password, role, created_at, updated_at
2. Data type of each field? (string, int, date, boolean, enum...)
3. Required or optional?
4. Default value?
5. Any special validation?
```

**Probing 15b (if common entities are missing):**
```
It looks like you haven't listed some common entities:
• [ ] User/Account (login information)
• [ ] Role/Permission (access control)
• [ ] Category/Type (classification)
• [ ] Log/Audit (change history)
• [ ] Setting/Config (system configuration)
• [ ] Notification
• [ ] Attachment/File
```

**SATISFACTION CHECK 15 - USE AskUserQuestion:**
```javascript
tool.Call("AskUserQuestion", {
  questions: [{
    question: "☝️ Entity list:\n[listed]\n\nAre you satisfied with this list?\n• Are there other entities?\n• Does any entity need more fields?",
    header: "Confirm OK?"
  }]
})
```

---

### Question 16 (Required) - RELATIONSHIPS & CARDINALITY:

**USE the AskUserQuestion tool:**
```javascript
tool.Call("AskUserQuestion", {
  questions: [{
    question: "📝 Question 16/38: How are the entities related to each other?\n\nFormat:\n| Entity A | Relationship | Entity B | Cardinality |\n|----------|--------------|----------|--------------|\n| User | has many | Order | 1:N |\n| Order | contains many | OrderItem | 1:N |\n| OrderItem | belongs to | Product | N:1 |\n| Product | belongs to | Category | N:1 |\n\nNeed to know:\n• 1:N or N:N?\n• Is there cascade delete? (deleting parent → deletes children?)\n• Is there soft delete? (is_deleted flag)\n• Is there timestamp tracking? (created_at, updated_at)",
    header: "Relationships"
  }]
})
```

**Probing 16a - USE AskUserQuestion:**
```javascript
tool.Call("AskUserQuestion", {
  questions: [{
    question: "For each relationship:\n1. How are Entity A and Entity B related?\n2. How many Bs does one A have? (1:N, N:1, N:N)\n3. When A is deleted, is B deleted too? (Cascade delete)\n4. Is there soft delete?",
    header: "Dig Into Relationships"
  }]
})
```

**SATISFACTION CHECK 16 - USE AskUserQuestion:**
```javascript
tool.Call("AskUserQuestion", {
  questions: [{
    question: "☝️ Relationships:\n[listed]\n\nAre you satisfied with these relationships?\n• Is any relationship still unclear?\n• Do you need a junction table for any N:N relationship?",
    header: "Confirm OK?"
  }]
})
```

---

### Question 17 (Optional) - DATA VOLUME & GROWTH:

**USE the AskUserQuestion tool:**
```javascript
tool.Call("AskUserQuestion", {
  questions: [{
    question: "📝 Question 17/38: Roughly how much initial data is there? What growth is expected?\n\nEstimate for each entity:\n• Users: ~ ? (e.g., 100, 1000, 10000)\n• Products: ~ ?\n• Orders/Transactions: ~ ?\n• Storage estimate: ~ ? GB\n\nExpected growth:\n• User growth: % / month\n• Data growth: % / month\n• Peak load: (e.g., 10,000 concurrent users)\n\nThis affects:\n• Database design (indexing, partitioning)\n• Caching strategy\n• Scalability requirements",
    header: "Data Volume"
  }]
})
```

**SATISFACTION CHECK 17 - USE AskUserQuestion:**
```javascript
tool.Call("AskUserQuestion", {
  questions: [{
    question: "☝️ Data Volume & Growth:\n[estimated]\n\nAre you satisfied with these numbers?\n• Does anything need adjusting?",
    header: "Confirm OK?"
  }]
})
```

---

## SECTION E: INTEGRATION & INTERFACES (Questions 18-21)

**Question 18 (Required) - EXTERNAL SYSTEMS:**

**USE the AskUserQuestion tool:**
```javascript
tool.Call("AskUserQuestion", {
  questions: [{
    question: "📝 Question 18/38: Does the system need to connect with any other system?\n\nFormat:\n| System | Purpose of Connection | Method | Data Flow |\n|----------|-----------------|--------|-----------|\n| Salesforce CRM | Sync customers | API | Bidirectional |\n| MISA Accounting | Export invoices | File export | One-way |\n| GHN Shipping | Calculate shipping fees | API | Bidirectional |\n| VNPay Payment | Payment processing | API | Bidirectional |\n\nCommon examples:\n• Payment gateway (Stripe, VNPay, MoMo, ZaloPay)\n• Shipping (GHN, GHTK, J&T, Ninja Van)\n• SMS/Email service (Twilio, SendGrid, AWS SES)\n• Storage (AWS S3, Google Drive)",
    header: "External Systems"
  }]
})
```

**Probing 18a (for each connected system):**
```
For each system:
1. Does the external system have an API spec yet?
2. Authentication method? (API key, OAuth, JWT)
3. Rate limits? (e.g., 1000 requests/minute)
4. SLA of the external system? (uptime guarantee)
5. What happens when the external system is down?
```

**SATISFACTION CHECK 18 - USE AskUserQuestion:**
```javascript
tool.Call("AskUserQuestion", {
  questions: [{
    question: "☝️ External Systems identified:\n[listed]\n\nAre you satisfied with this list?\n• Any other systems that need connecting?\n• Do you have a clear understanding of the authentication and SLA for each system?",
    header: "Confirm OK?"
  }]
})
```

---

**Question 19 (Required) - API SPECS & DATA FORMAT:**

**USE the AskUserQuestion tool:**
```javascript
tool.Call("AskUserQuestion", {
  questions: [{
    question: "📝 Question 19/38: What data format do the API integrations use?\n\nFor each integration:\n• REST API (JSON) or GraphQL or SOAP (XML)?\n• Current API version?\n• Authentication method?\n• Standard error codes?",
    header: "API Specs"
  }]
})
```

**SATISFACTION CHECK 19 - USE AskUserQuestion:**
```javascript
tool.Call("AskUserQuestion", {
  questions: [{
    question: "☝️ API Specs identified:\n[listed]\n\nAre you satisfied with this information?\n• Do you have a clear versioning strategy?\n• Do you need to add retry logic or timeout settings?",
    header: "Confirm OK?"
  }]
})
```

---

**Question 20 (Required) - DATA SYNC STRATEGY:**

**USE the AskUserQuestion tool:**
```javascript
tool.Call("AskUserQuestion", {
  questions: [{
    question: "📝 Question 20/38: How is data synced between systems?\n\nChoose a sync mode:\nA) Real-time (Webhook, Socket) - Immediately when a change occurs\nB) Scheduled (Cron job) - Periodic (e.g., every 15 minutes)\nC) Batch (Manual trigger) - On demand\nD) Event-driven (Message queue) - When an event occurs\n\nFor each sync:\n• Sync direction? (Push, Pull, Bidirectional)\n• Conflict resolution when data mismatches?",
    header: "Data Sync"
  }]
})
```

**SATISFACTION CHECK 20 - USE AskUserQuestion:**
```javascript
tool.Call("AskUserQuestion", {
  questions: [{
    question: "☝️ Data Sync Strategy:\n[described]\n\nAre you satisfied with this sync strategy?\n• Is the sync frequency appropriate for business needs?",
    header: "Confirm OK?"
  }]
})
```

---

**Question 21 (Optional) - FILE IMPORTS/EXPORTS:**

**USE the AskUserQuestion tool:**
```javascript
tool.Call("AskUserQuestion", {
  questions: [{
    question: "📝 Question 21/38: Do you need to import/export data from files?\n\nCommon formats:\n• Excel (.xlsx, .csv) - for reports, data export\n• PDF - for invoices, contracts\n• JSON/XML - for API, data exchange\n• Image/PDF - for printing\n\nFor each type:\n• Who is allowed to export/import?\n• File size limit? (e.g., max 10MB)",
    header: "File Import/Export"
  }]
})
```

**SATISFACTION CHECK 21 - USE AskUserQuestion:**
```javascript
tool.Call("AskUserQuestion", {
  questions: [{
    question: "☝️ File Import/Export:\n[described]\n\nAre you satisfied with this information?\n• Are there other file types that need to be supported?",
    header: "Confirm OK?"
  }]
})
```

---

## SECTION F: PLATFORM & UI (Questions 22-24)

**Question 22 (Required) - PLATFORMS:**

**USE the AskUserQuestion tool:**
```javascript
tool.Call("AskUserQuestion", {
  questions: [{
    question: "📝 Question 22/38: How will users access the system?\n\nSelect all that apply:\nA) Web Browser (Chrome, Safari, Firefox, Edge...)\nB) Mobile Web (browser on a phone)\nC) Native iOS App\nD) Native Android App\nE) Desktop App (Windows/Mac/Linux)\nF) API for other systems to call\n\nIf multiple are selected, provide the usage split:\ne.g., Web 70%, Mobile Web 20%, API 10%",
    header: "Platforms",
    options: [
      { label: "A) Web Browser", description: "Desktop browser" },
      { label: "B) Mobile Web", description: "Browser on a phone" },
      { label: "C) Native iOS", description: "iPhone/iPad app" },
      { label: "D) Native Android", description: "Android app" },
      { label: "E) Desktop App", description: "Windows/Mac/Linux" },
      { label: "F) API", description: "For other systems to call" }
    ],
    multiSelect: true
  }]
})
```

**SATISFACTION CHECK 22 - USE AskUserQuestion:**
```javascript
tool.Call("AskUserQuestion", {
  questions: [{
    question: "☝️ Platforms: [selected]\nUsage split: [determined]\n\nAre you satisfied with these platforms?\n• Do you need to add any other platform?",
    header: "Confirm OK?"
  }]
})
```

---

**Question 23 (Required) - RESPONSIVE & ACCESSIBILITY:**

**USE the AskUserQuestion tool:**
```javascript
tool.Call("AskUserQuestion", {
  questions: [{
    question: "📝 Question 23/38: Are there any special requirements for the UI and accessibility?\n\nResponsive Design:\n• Mobile-first or desktop-first?\n• Breakpoints? (480px, 768px, 1024px, 1440px)\n• Touch-friendly targets? (min 44x44px)\n\nAccessibility (WCAG compliance):\n• Screen reader support? (alt text, ARIA labels)\n• Keyboard navigation?\n• Color contrast? (4.5:1 ratio minimum)\n• Dark mode support?\n\nUI/UX Standards:\n• Is there an existing design system? (Material, Bootstrap, Tailwind, custom)",
    header: "UI/UX"
  }]
})
```

**SATISFACTION CHECK 23 - USE AskUserQuestion:**
```javascript
tool.Call("AskUserQuestion", {
  questions: [{
    question: "☝️ Responsive & Accessibility:\n[described]\n\nAre you satisfied with these UI/UX requirements?\n• Which WCAG level? (A, AA, AAA)\n• Is there an existing design system, or does one need to be created from scratch?",
    header: "Confirm OK?"
  }]
})
```

---

**Question 24 (Optional) - MULTI-LANGUAGE & INTERNATIONALIZATION:**

**USE the AskUserQuestion tool:**
```javascript
tool.Call("AskUserQuestion", {
  questions: [{
    question: "📝 Question 24/38: Do you need support for multiple languages or internationalization?",
    header: "i18n"
  }]
})
```

> If i18n is NEEDED, follow up (in prose) with these details:

```
Languages:
• Primary language? (e.g., Vietnamese)
• Additional languages? (e.g., English, Chinese)
• RTL support? (Right-to-left for Arabic, Hebrew)

Localization considerations:
• Date/Time format? (DD/MM/YYYY vs MM/DD/YYYY)
• Number format? (1,000.00 vs 1.000,00)
• Currency? (VND, USD, EUR)
• Phone number format?
• Address format?

i18n implementation:
• Translation management system?
• Content management approach?
• Dynamic language switching?
```

**Probing 24a:**
```
For each language:
1. Is content already available? (how much needs translating?)
2. Have UI strings been externalized?
3. How are plural forms handled?
4. Are there gender-specific translations?
5. Date/time timezone handling?

Technical considerations:
1. Is font support sufficient? (e.g., CJK characters)
2. Text expansion (e.g., German text can be up to 30% longer)
3. Is RTL layout mirroring needed?
```

**SATISFACTION CHECK 24 - USE AskUserQuestion:**
```javascript
tool.Call("AskUserQuestion", {
  questions: [{
    question: "☝️ Multi-language & i18n:\n[described]\n\nAre you satisfied with this information?\n• Is the number of languages and their priority correct?",
    header: "Confirm OK?"
  }]
})
```

---

## SECTION G: NON-FUNCTIONAL REQUIREMENTS (Questions 25-29)

### Question 25 (Required) - PERFORMANCE:

**USE the AskUserQuestion tool:**
```javascript
tool.Call("AskUserQuestion", {
  questions: [{
    question: "📝 Question 25/38: How fast does the system need to respond?\n\nAnswer specifically for each operation:\n| Operation | Acceptable | Ideal | Measurement |\n|-----------|------------|-------|------------|\n| Page load | < 3s | < 1s | P95 |\n| API call | < 1s | < 200ms | P95 |\n| Search query | < 2s | < 500ms | P95 |\n\nExamples:\n• 'The API must respond within < 500ms at P95'\n• 'Page load must be < 2 seconds with 1000 concurrent users'",
    header: "Performance"
  }]
})
```

**SATISFACTION CHECK 25 - USE AskUserQuestion:**
```javascript
tool.Call("AskUserQuestion", {
  questions: [{
    question: "☝️ Performance Requirements:\n• Page load: [target]\n• API response: [target]\n• Search: [target]\n\nAre you satisfied with these numbers?",
    header: "Confirm OK?"
  }]
})
```

---

### Question 26 (Required) - SCALABILITY & CONCURRENCY:

**USE the AskUserQuestion tool:**
```javascript
tool.Call("AskUserQuestion", {
  questions: [{
    question: "📝 Question 26/38: How does the system need to scale? How many concurrent users?\n\nConcurrent Users:\n| Metric | Value |\n|--------|-------|\n| Peak concurrent users | ~? |\n| Average concurrent users | ~? |\n| Peak hours | e.g., 9-11am, 2-4pm |\n\nData Growth:\n• User growth rate: % / month\n• Storage needed: ~? GB/TB",
    header: "Scalability"
  }]
})
```

**Probing 26a:**
```
Scalability models:
• Vertical (scale up): Add CPU/RAM to the server
• Horizontal (scale out): Add more servers

Auto-scaling triggers:
• CPU > 70% for 5 minutes → Add 1 instance
• CPU < 30% for 15 minutes → Remove 1 instance
• Request count > 1000/min → Add instances

Database scaling:
• Read replicas for read-heavy workloads
• Sharding for write-heavy workloads
• Partitioning for large tables
```

**SATISFACTION CHECK 26 - USE AskUserQuestion:**
```javascript
tool.Call("AskUserQuestion", {
  questions: [{
    question: "☝️ Scalability:\n• Concurrent users: [peak / average]\n• Auto-scaling: [yes/no]\n• Data growth: [% / month]\n\nAre you satisfied with this scalability plan?",
    header: "Confirm OK?"
  }]
})
```

---

### Question 27 (Required) - AVAILABILITY & SLA:

**USE the AskUserQuestion tool:**
```javascript
tool.Call("AskUserQuestion", {
  questions: [{
    question: "📝 Question 27/38: What uptime does the system need?\n\nSLA Levels:\n| SLA | Downtime/year | Downtime/month | Use Case |\n|-----|---------------|----------------|----------|\n| 99% | 3.65 days | 7.3 hours | Internal tools |\n| 99.9% | 8.76 hours | 43.8 min | Standard apps |\n| 99.95% | 4.38 hours | 21.9 min | E-commerce |\n| 99.99% | 52.6 min | 4.4 min | FinTech/Healthcare |\n\nMaintenance windows:\n• Planned maintenance: What day/time?\n• Emergency maintenance: When can it be done?",
    header: "Availability"
  }]
})
```

**SATISFACTION CHECK 27 - USE AskUserQuestion:**
```javascript
tool.Call("AskUserQuestion", {
  questions: [{
    question: "☝️ Availability:\n• SLA target: [X%]\n• Max downtime allowed: [X hours/year]\n\nAre you satisfied with this availability plan?",
    header: "Confirm OK?"
  }]
})
```

---

### Question 28 (Required) - BACKUP & RECOVERY:

**USE the AskUserQuestion tool:**
```javascript
tool.Call("AskUserQuestion", {
  questions: [{
    question: "📝 Question 28/38: How should backup and recovery work?\n\nBackup Strategy:\n| Type | Frequency | Retention |\n|------|-----------|-----------|\n| Full backup | Weekly | 4 weeks |\n| Incremental | Daily | 7 days |\n\nRPO (Recovery Point Objective):\n• How much data loss is acceptable?\n• e.g., RPO = 1 hour → Max acceptable data loss = 1 hour\n\nRTO (Recovery Time Objective):\n• If the system goes down, how quickly must it be back up?",
    header: "Backup"
  }]
})
```

**SATISFACTION CHECK 28 - USE AskUserQuestion:**
```javascript
tool.Call("AskUserQuestion", {
  questions: [{
    question: "☝️ Backup & Recovery:\n• RPO: [X hours]\n• RTO: [X hours]\n• Backup frequency: [daily/weekly/etc]\n\nAre you satisfied with this backup plan?",
    header: "Confirm OK?"
  }]
})
```

---

### Question 29 (Required) - SECURITY & AUTHENTICATION:

**USE the AskUserQuestion tool:**
```javascript
tool.Call("AskUserQuestion", {
  questions: [{
    question: "📝 Question 29/38: What are the security and authentication requirements?\n\nAuthentication Methods:\n| Method | Use Case | Security Level |\n|--------|----------|----------------|\n| Email + Password | Standard users | Basic |\n| Password + 2FA/TOTP | Sensitive apps | Strong |\n| OAuth/Social Login | Consumer apps | Strong |\n| SSO (SAML/OIDC) | Enterprise | Very Strong |\n\nPassword Requirements:\n• Minimum length: 8 / 12 / 16 characters?\n• Complexity requirements?\n• Is 2FA required?",
    header: "Security"
  }]
})
```

**SATISFACTION CHECK 29 - USE AskUserQuestion:**
```javascript
tool.Call("AskUserQuestion", {
  questions: [{
    question: "☝️ Security:\n• Auth method: [method]\n• Password policy: [requirements]\n• 2FA required: [yes/no]\n\nAre you satisfied with this security plan?",
    header: "Confirm OK?"
  }]
})
```

---

## SECTION H: COMPLIANCE & RISK (Questions 30-32)

### Question 30 (Required) - REGULATORY COMPLIANCE:
```
📝 Question 30/38: Are there any regulatory compliance requirements?

Common Compliance Frameworks:
| Framework | Region | Industry | Key Requirements |
|-----------|--------|----------|------------------|
| GDPR | EU | All | Data privacy, consent, right to deletion |
| PDPA | Thailand/VN | All | Data protection, consent |
| PCI-DSS | Global | Payment | Credit card data security |
| HIPAA | US | Healthcare | PHI protection |
| SOC 2 | US | SaaS | Security, availability, confidentiality |
| ISO 27001 | Global | All | Information security management |
| NIST | US | Government | Cybersecurity framework |

Data subject rights:
• Right to access (users can see their data)
• Right to rectification (correct errors)
• Right to erasure (delete account/data)
• Right to data portability (export data)
• Right to object (opt-out of processing)

Compliance evidence needed:
• Audit logs
• Data processing agreements
• Privacy policies
• Consent records
• Security certifications
```

**Probing 30a:**
```
Compliance implications:
• GDPR: €20M fine or 4% of global revenue
• PCI-DSS: $500K-$5M fine per breach
• PDPA: Up to 5% of annual revenue

Compliance roadmap:
1. Identify applicable regulations
2. Data inventory and mapping
3. Gap analysis
4. Implement controls
5. Documentation and evidence
6. Audit and certification

If unsure about compliance:
• Consult legal counsel
• Hire a compliance consultant
• Start with GDPR/PDPA as a baseline
```

**SATISFACTION CHECK 30 - USE AskUserQuestion:**
```javascript
tool.Call("AskUserQuestion", {
  questions: [{
    question: "☝️ Compliance:\n• Frameworks: [list]\n• Audit requirements: [yes/no]\n\nAre you satisfied with this compliance plan?",
    header: "Confirm OK?"
  }]
})
```

---

### Question 31 (Required) - DATA CLASSIFICATION & PRIVACY:

**USE the AskUserQuestion tool:**
```javascript
tool.Call("AskUserQuestion", {
  questions: [{
    question: "📝 Question 31/38: How is data classified and protected?\n\nData Classification:\n| Level | Examples | Handling |\n|-------|----------|----------|\n| Public | Marketing materials | Open |\n| Internal | Company policies | Internal only |\n| Confidential | Customer data | Restricted |\n| Restricted | Financial records, PII | Highly restricted |\n\nPII (Personally Identifiable Information):\n• What PII is collected? (name, email, phone, ID, IP)\n• How is PII stored?\n• PII retention period?",
    header: "Data Privacy"
  }]
})
```

**SATISFACTION CHECK 31 - USE AskUserQuestion:**
```javascript
tool.Call("AskUserQuestion", {
  questions: [{
    question: "☝️ Data Classification:\n• Classification levels: [list]\n• PII collected: [list]\n\nAre you satisfied with this data classification?",
    header: "Confirm OK?"
  }]
})
```

---

### Question 32 (Required) - AUDIT TRAIL & LOGGING:
```
📝 Question 32/38: What logging and audit trail is needed?

Audit Trail Requirements:
| Event Type | Log Details | Retention |
|------------|-------------|-----------|
| Authentication | User, IP, time, success/fail | 1 year |
| Authorization | User, action, resource, result | 1 year |
| Data access | User, data, operation, timestamp | 1 year |
| Data modification | Before/after values, user | 3 years |
| System events | Server, service, error, severity | 6 months |
| Security events | Failed logins, permission denied | 1 year |

Log Management:
• Centralized logging? (ELK, CloudWatch, Datadog)
• Log aggregation method?
• Real-time alerting on patterns?
• Log correlation for incident investigation?
• Log retention and archival?

Compliance logging:
• Who did what, when, where, from where
• Immutable logs (tamper-evident)
• Regular log reviews
• Automated anomaly detection

Privacy logging:
• Consent changes
• Data access requests
• Data deletion requests
• Data exports
```

**Probing 32a:**
```
Logging best practices:
• Use structured logging (JSON)
• Include correlation IDs for tracing
• Don't log sensitive data (PII, passwords, tokens)
• Log enough context for investigation
• Real-time alerting for critical events

Log analysis tools:
• ELK Stack (Elasticsearch, Logstash, Kibana)
• AWS CloudWatch Logs
• Google Cloud Logging
• Datadog Logs
• Splunk

Log retention:
• Hot storage: 30-90 days (fast query)
• Warm storage: 3-6 months (medium query)
• Cold storage: 1-7 years (slow query, compliance)
```

**SATISFACTION CHECK 32 - USE AskUserQuestion:**
```javascript
tool.Call("AskUserQuestion", {
  questions: [{
    question: "☝️ Audit Trail:\n• Events logged: [list]\n• Retention: [duration]\n• Alerting: [yes/no]\n\nAre you satisfied with this audit plan?",
    header: "Confirm OK?"
  }]
})
```

---

### STEP 13: Operations & Support (Questions 33-36)

---

**Question 33 (Required) - DEPLOYMENT & INFRASTRUCTURE:**
```
📝 Question 33/38: What is the deployment environment and infrastructure?

Infrastructure:
• Cloud provider? (AWS, Azure, GCP, On-premise, Hybrid)
• Region? (e.g., Singapore, Vietnam, US)
• Compute type? (VMs, Containers, Serverless)
• Database? (RDS, self-managed, managed service)

Environment Tiers:
| Environment | Purpose | Config |
|-------------|---------|--------|
| Development | Coding and testing | Dev configs |
| Staging | Pre-production testing | Production-like |
| Production | Live users | Full production |

CI/CD:
• Is a CI/CD pipeline already in place? (Jenkins, GitHub Actions, GitLab CI)
• Deployment strategy? (blue-green, canary, rolling)
• Automated testing in the pipeline?
• Rollback procedure?

Environment-specific configs:
• Feature flags?
• Environment variable management?
• Secrets management?
```

**Probing 33a (if there's no infrastructure plan yet):**
```
Do you already have a cloud account? If not, I can recommend:
• AWS: Most popular, wide range of services
• Azure: Integrates well with Microsoft
• GCP: Good for containers/Kubernetes
• On-premise: If data must stay local

For region, choose the one closest to your users:
• Vietnam users → Singapore or Vietnam region
• US users → US region
• Global → Multi-region
```

**Probing 33b (if there's no CI/CD yet):**
```
A CI/CD pipeline helps automate deployment and reduce human error.
If you don't have one, we can set up:
• GitHub Actions: Free for public repos, cheap for private
• GitLab CI: Built into GitLab
• Jenkins: Self-hosted, free but requires a server

Deployment strategy:
• Rolling: Replace instances one at a time (simple)
• Blue-green: 2 environments, switch instantly (safer)
• Canary: Deploy to a small % of users first (lower risk)
```

**SATISFACTION CHECK 33:**
```
☝️ Infrastructure:
• Cloud: [provider]
• Region: [region]
• Environments: [count]
• CI/CD: [yes/no]

Are you satisfied with this infrastructure plan?
• Do you need to add any other environments?
• Are there any special region/data residency requirements?
```

---

**Question 34 (Optional) - MONITORING & ALERTING:**
```
📝 Question 34/38: What are the monitoring and alerting requirements?

Monitoring:
• Infrastructure monitoring? (CPU, RAM, Disk, Network)
• Application monitoring? (APM - Application Performance Monitoring)
• Database monitoring? (queries, connections, slow logs)
• Uptime monitoring?

Metrics to track:
• Response time (latency)
• Error rate
• Throughput (requests/second)
• Resource utilization
• Business metrics (orders, revenue)

Alerting:
• Alert on what conditions?
  - Error rate > X%
  - Response time > Y seconds
  - CPU > Z%
  - Disk space < threshold
• Alert channels? (Email, SMS, Slack, PagerDuty)
• Alert severity levels? (Critical, High, Medium, Low)
• On-call rotation?
• Escalation procedure?

Dashboards:
• Real-time dashboard?
• Historical data retention?
• Custom dashboards for stakeholders?
```

**Probing 34a (if there's no monitoring yet):**
```
Monitoring helps detect problems BEFORE users complain.

Tool recommendations:
• Infrastructure: CloudWatch (AWS), Azure Monitor, Stackdriver (GCP)
• APM: New Relic, Datadog, Sentry (for errors)
• Uptime: Pingdom, UptimeRobot, StatusPage
• Log: ELK Stack, CloudWatch Logs, Datadog

Cost consideration:
• Basic monitoring: Free (built-in cloud tools)
• APM: $50-500/month depending on traffic
• Uptime monitoring: Free - $50/month
```

**Probing 34b (if detailed alerting is needed):**
```
Alerting needs to balance "enough information" against "not spamming".

Common alerting model:
• Critical: P1 - 24/7 on-call, immediate response (e.g., site down)
• High: P2 - Business hours response, 4h resolution (e.g., error rate > 5%)
• Medium: P3 - Next business day (e.g., disk > 70%)
• Low: P4 - Backlog, investigate when able (e.g., slow queries)

Alert channels:
• Slack: Fast, free, everyone sees it
• SMS: For Critical alerts, requires a phone number
• Email: For non-urgent items, good audit trail
• PagerDuty: For a professional on-call rotation
```

**SATISFACTION CHECK 34:**
```
☝️ Monitoring & Alerting:
• Monitoring tools: [list]
• Metrics tracked: [list]
• Alert channels: [channels]
• Severity levels: [levels]

Are you satisfied with this monitoring plan?
• Do you need to track any additional metrics?
• Do any stakeholders need dashboard access?
```

---

**Question 35 (Optional) - SUPPORT MODEL & TRAINING:**
```
📝 Question 35/38: What is the support model and training plan?

Support Model:
• Tier 1: First line support (helpdesk)
• Tier 2: Technical support (engineers)
• Tier 3: Development team (bugs, complex issues)

Support Channels:
• Email support?
• Phone support?
• Chat/WhatsApp?
• Ticketing system? (Jira, Zendesk)
• Knowledge base?

Service Hours:
• Business hours support? (9-5, Mon-Fri)
• Extended hours?
• 24/7 support?
• SLA for response time?

Training:
• Is user training required?
• Training materials? (docs, videos, hands-on)
• Admin training for system administrators?
• Training schedule?

Documentation:
• User documentation?
• Admin documentation?
• API documentation?
• Deployment runbook?
```

**Probing 35a (if there's no support model yet):**
```
The support model depends on the criticality of the system.

Common models:
• Startup/SMB: Direct developer support → Fast response but not scalable
• Growth: Helpdesk → Jira/Zendesk → Dev team
• Enterprise: Formal tiers → SLA contracts → Professional services

Tool choices:
• Jira Service Management: Best for IT teams
• Zendesk: Best for customer-facing support
• Freshdesk: Affordable, easy to set up
• Simple: Email + WhatsApp for small teams
```

**Probing 35b (if training is needed):**
```
Training is necessary if users aren't familiar with the new system.

Training components:
1. User Documentation
   • How-to guides with screenshots
   • Video tutorials (5-10 minutes/topic)
   • FAQ

2. Admin Documentation
   • System administration guide
   • Troubleshooting guide
   • Configuration reference

3. API Documentation (if there's an API)
   • OpenAPI/Swagger spec
   • Integration examples
   • Postman collection

Training delivery:
• Live training: Good for Q&A, but requires scheduling
• Self-paced: Users learn at their own speed
• Documentation: Always available, but can go out of date
```

**SATISFACTION CHECK 35:**
```
☝️ Support Model:
• Tiers: [T1/T2/T3 or single level]
• Channels: [list]
• Hours: [business hours/24-7]
• Ticketing: [tool]

Training & Documentation:
• Training required: [yes/no]
• Doc types: [user/admin/API]
• Knowledge base: [yes/no]

Are you satisfied with this support model?
• Are the SLA requirements clear?
• Has the training schedule been determined?
```

---

**Question 36 (Required) - CONFIGURATION MANAGEMENT:**

**USE the AskUserQuestion tool:**
```javascript
tool.Call("AskUserQuestion", {
  questions: [{
    question: "📝 Question 36/38: Which parts of the system should be configurable WITHOUT a code deploy?\n\nThink about:\n• Business parameters that change over time (prices, thresholds, tax rates, discount rules, approval limits)\n• Notification templates/content (email/SMS wording)\n• Feature flags — do you need to turn a feature on/off, or roll it out to a % of users, without redeploying?\n• Environment-specific settings that differ between Dev/Staging/Production (API keys, endpoints, limits)\n• If multiple clients/organizations use the same system, do they each need their own settings (multi-tenant configuration)?\n\nExample: 'Discount percentage and free-shipping threshold must be editable by Admin from a settings screen. Payment gateway API keys differ by environment. We want to be able to disable the new checkout flow instantly if something breaks.'",
    header: "Configuration"
  }]
})
```

**Probing 36a (if the user says 'nothing needs to be configurable') - USE AskUserQuestion:**
```javascript
tool.Call("AskUserQuestion", {
  questions: [{
    question: "Even simple systems usually have at least one of these. Double-check:\n• Will pricing/discount/tax rules ever change after launch?\n• Will you want to disable a feature quickly if it has a bug, without waiting for a new deployment?\n• Do Dev/Staging/Production need different API keys or limits?\n\nIf the honest answer is still 'no' to all three, that's a valid answer — just confirm it.",
    header: "Double-Check",
    options: [
      { label: "Still nothing needed", description: "Everything can be hardcoded/set once at deploy time" },
      { label: "Actually, yes to some", description: "I'll specify which ones" }
    ],
    multiSelect: false
  }]
})
```

**SATISFACTION CHECK 36 - USE AskUserQuestion:**
```javascript
tool.Call("AskUserQuestion", {
  questions: [{
    question: "☝️ Configuration Management:\n• Admin-configurable settings: [list]\n• Feature flags needed: [yes/no - which features]\n• Environment-specific settings: [list]\n• Multi-tenant config: [yes/no]\n\nAre you satisfied with this? Anything else that should be configurable without a redeploy?",
    header: "Confirm OK?"
  }]
})
```

---

### STEP 14: Project Governance (Questions 37-38)

---

**Question 37 (Required) - SUCCESS METRICS & ACCEPTANCE:**
```
📝 Question 37/38: How will the success of the project be measured?

Success Metrics (KPIs):
| KPI | Current | Target | Measurement |
|-----|---------|--------|-------------|
| Page load time | 5s | < 2s | P95 |
| Order completion rate | 60% | > 80% | % orders completed |
| Support tickets | 100/week | < 50/week | Weekly count |
| System uptime | 99% | 99.9% | % availability |

Baseline:
• Are there current baseline metrics to compare against?
• How long will it be measured before launch?

Acceptance Criteria:
• When is the project considered successful?
• Are user acceptance criteria (UAT) defined?
• Sign-off process?
• Definition of Done?

Post-implementation:
• When will the post-implementation review happen?
• Lessons learned session?
• Continuous improvement plan?
```

**Probing 37a (if there are no KPIs yet):**
```
KPIs help measure the project's ROI and determine success.

How to define good KPIs (SMART):
• Specific: What exactly
• Measurable: Can be measured
• Achievable: Attainable
• Relevant: Related to business goals
• Time-bound: Has a deadline

Examples:
• Performance: "Page load < 2s (P95)" instead of "fast"
• Conversion: "Order completion rate > 80%" instead of "more orders"
• Adoption: "50% of users active weekly within 1 month" instead of "users use it a lot"
```

**Probing 37b (if a UAT process is needed):**
```
User Acceptance Testing (UAT) is the last step before go-live.

UAT Process:
1. Preparation (week before UAT)
   • UAT test cases written and approved
   • Test environment ready
   • Users notified and scheduled

2. Execution (1-2 weeks)
   • Users execute test cases
   • Bugs logged and triaged
   • Daily standups to track progress

3. Sign-off
   • All P1/P2 bugs fixed
   • User sign-off document signed
   • Go/No-go meeting

Definition of Done:
• Code complete
• Unit tests pass
• Integration tests pass
• UAT pass
• Documentation complete
• Deployment approved
```

**SATISFACTION CHECK 37:**
```
☝️ Success Metrics:
• KPIs: [count] defined
• Baseline: [yes/no - value]
• Targets: [clear/vague]

Acceptance:
• UAT defined: [yes/no]
• Sign-off process: [description]
• Definition of Done: [description]

Are you satisfied with these success metrics and acceptance criteria?
• Are the KPIs aligned with business goals?
• Is the UAT process clear?
```

---

**Question 38 (Required) - TIMELINE & PHASES:**
```
📝 Question 38/38: What is the project timeline and what are its phases?

Timeline:
• Start date:
• Target end date:
• Total duration: ? months / ? weeks

Phases/Milestones:
| Phase | Deliverables | Target Date |
|-------|--------------|-------------|
| Discovery | SRS, Architecture | Week 1-2 |
| Design | UI/UX, SDD | Week 3-4 |
| Development | MVP | Week 5-10 |
| Testing | UAT, Bug fixes | Week 11-12 |
| Go-Live | Deployment | Week 13 |
| Stabilization | Post-launch support | Week 14-16 |

Constraints:
• Hard deadline? (e.g., must finish before Tet, before fiscal year end)
• Dependencies on external parties?
• Holiday periods when work stops?

Budget:
• Approved budget:
• Contingency budget:
• Cost breakdown by phase?
```

**Probing 38a (if the timeline isn't clear yet):**
```
The timeline needs to be realistic and have buffer for unexpected delays.

Rule of thumb:
• 1 week of development → 0.5-1 week buffer
• Complex project → 20-30% time buffer
• Dependencies → Add 1-2 weeks buffer

Phase duration estimates:
• Discovery (SRS): 1-2 weeks
• Design (UI/UX, SDD): 2-4 weeks
• Development (MVP): 4-12 weeks (depending on scope)
• Testing (UAT): 2-4 weeks
• Go-Live: 1 week
• Stabilization: 2-4 weeks

Minimum viable timeline:
• Small project (1-2 features): 6-8 weeks
• Medium project (5-10 features): 12-16 weeks
• Large project (10+ features): 20+ weeks
```

**Probing 38b (if there are special constraints):**
```
Constraints affect the timeline and scope.

Common hard deadlines:
• "Must ship before Tet" → Count backwards from Tet
• "Must launch before the competitor" → Compress the timeline
• "Fiscal year end" → Q4 deadline pressure
• "Trade show demo" → MVP only is acceptable

Dependencies to track:
• External APIs → Need the API spec before development
• Third-party vendors → Lead time for contracts
• Data migration → Need the old data export
• User training → Need materials before UAT

Budget considerations:
• Hourly rate vs fixed price
• Contingency (10-20% recommended)
• Phase-based payments
```

**SATISFACTION CHECK 38:**
```
☝️ Timeline:
• Start: [date]
• End: [date]
• Duration: [X weeks/months]

Phases:
• Discovery: [date range]
• Design: [date range]
• Development: [date range]
• Testing: [date range]
• Go-Live: [date]
• Stabilization: [date range]

Constraints:
• Hard deadline: [yes/no - description]
• Dependencies: [list]
• Budget: [approved amount]

Are you satisfied with this timeline?
• Is the deadline realistic?
• Does any phase need adjusting?
```

---

### STEP 15: FINAL CONFIRMATION (After the interview is complete)

---

**FINAL CONFIRMATION - CONFIRMING THE FINAL REQUIREMENTS:**
```
╔══════════════════════════════════════════════════════════════════════════════════════╗
║  📋 FINAL CHECK - CONFIRM REQUIREMENTS                                      ║
╠══════════════════════════════════════════════════════════════════════════════════════╣
║                                                                                      ║
║  1. PROJECT CONTEXT (5 questions)                                                       ║
║     ✅ Project name: [name]                                                            ║
║     ✅ System description: [description]                                                     ║
║     ✅ Existing system: [yes/no]                                                     ║
║     ✅ Business problem: [description]                                                  ║
║     ✅ Stakeholders: [sponsor / product owner / approval authority]                      ║
║                                                                                      ║
║  2. USERS & ACTORS (3 questions)                                                          ║
║     ✅ Actors: [list with permissions]                                         ║
║     ✅ Permissions: [detailed description]                                                 ║
║     ✅ Guest users: [yes/no]                                                      ║
║                                                                                      ║
║  3. FUNCTIONAL REQUIREMENTS (6 questions)                                                ║
║     ✅ Features: [count] features listed                                   ║
║     ✅ Use Cases: [count]/[count] Must-Have UC specs completed (preconditions/postconditions/flows) ║
║     ✅ MoSCoW: Must: [X], Should: [Y], Could: [Z]                                   ║
║     ✅ Business rules: [count] rules                                           ║
║     ✅ Error handling: [description]                                                     ║
║                                                                                      ║
║  4. DATA MODEL (3 questions)                                                             ║
║     ✅ Entities: [count] entities                                                ║
║     ✅ Fields: [description]                                                              ║
║     ✅ Relationships: [description]                                                     ║
║                                                                                      ║
║  5. INTEGRATION & INTERFACES (4 questions)                                                ║
║     ✅ External systems: [list]                                               ║
║     ✅ API specs: [description]                                                          ║
║     ✅ Data exchange: [format, frequency]                                          ║
║     ✅ Import/Export: [formats]                                                     ║
║                                                                                      ║
║  6. PLATFORM & UI (3 questions)                                                           ║
║     ✅ Platforms: [Web/Mobile/Desktop/API]                                          ║
║     ✅ Responsive: [yes/no]                                                      ║
║     ✅ Accessibility/i18n: [description]                                                  ║
║                                                                                      ║
║  7. NON-FUNCTIONAL (5 questions)                                                          ║
║     ✅ Performance: [response time targets]                                         ║
║     ✅ Scalability: [concurrent users]                                              ║
║     ✅ Availability: [SLA %]                                                        ║
║     ✅ Backup & Recovery: [RPO/RTO]                                                 ║
║     ✅ Security: [auth methods, encryption]                                          ║
║                                                                                      ║
║  8. COMPLIANCE & RISK (3 questions)                                                      ║
║     ✅ Regulatory compliance: [frameworks]                                          ║
║     ✅ Data classification: [levels]                                                ║
║     ✅ Audit trail: [description]                                                         ║
║                                                                                      ║
║  9. OPERATIONS & SUPPORT (4 questions)                                                    ║
║     ✅ Infrastructure: [cloud/provider/region/environments]                         ║
║     ✅ CI/CD: [yes/no, tool]                                                      ║
║     ✅ Monitoring: [tools/alerts]                                                   ║
║     ✅ Support model: [tiers/hours/channels]                                       ║
║     ✅ Training: [yes/no, materials]                                              ║
║     ✅ Configuration Management: [configurable settings, feature flags]              ║
║                                                                                      ║
║  10. PROJECT GOVERNANCE (2 questions)                                                    ║
║     ✅ Success metrics: [KPIs with targets]                                          ║
║     ✅ Acceptance: [UAT/sign-off/DOD]                                                ║
║     ✅ Timeline: [phases/dates]                                                     ║
║     ✅ Budget: [approved amount]                                                    ║
║                                                                                      ║
╠══════════════════════════════════════════════════════════════════════════════════════╣
║                                                                                      ║
║  📊 SUMMARY:                                                                        ║
║  ────────────────────────────────────────────────────────────────────────────────    ║
║  Total questions: 38                                                                ║
║  Required ✅ answered: [X]/29                                                    ║
║  Optional ⚠️ answered: [Y]/9                                                     ║
║  Optional ❌ skipped: [Z]/9                                                      ║
║                                                                                      ║
║  ⚠️ Warning: [If any required question is unanswered]                                   ║
║                                                                                      ║
╠══════════════════════════════════════════════════════════════════════════════════════╣
║                                                                                      ║
║  ✅ If everything is CORRECT, type "CONFIRM" to generate the SRS                            ║
║  📝 If something needs FIXING, tell me which question number needs to change                               ║
║  ➕ If you want to ADD something, tell me what                                          ║
║  ❌ If you want to SKIP a question, tell me why                                        ║
║                                                                                      ║
╚══════════════════════════════════════════════════════════════════════════════════════╝
```

**Probing FCa (if any required question is unanswered):**
```
⚠️ Warning: There are [X] REQUIRED questions that have not been fully answered:

[List of incomplete questions]

These questions may affect:
• The accuracy of the SRS
• Timeline/budget estimates
• Technical decisions

Would you like to:
A) Continue answering these questions now
B) Temporarily mark them as [TBD] and proceed
C) Accept the risk and continue with assumptions
```

**Probing FCb (if the user wants to edit a previous answer):**
```
No problem! Tell me:
• Which question number needs to change?
• What was the old information?
• What is the new information?

I'll update it and have you confirm again.
```

---

### STEP 16: GENERATE REQUIREMENTS SUMMARY (After the user confirms with "CONFIRM")

**ONLY AFTER THE USER CONFIRMS WITH "CONFIRM"** → Generate the SRS document:

```markdown
# Software Requirements Specification (SRS) - [Project Name]
**Version:** 1.0
**Date:** [Timestamp]
**Author:** Requirements Gathering Agent v4.0.0

---

## 1. Introduction

### 1.1 Purpose
[Describe the purpose of this document]

### 1.2 Scope
[Describe the scope of the system]

### 1.3 Definitions, Acronyms, and Abbreviations
| Term | Definition |
|------|------------|
| SRS | Software Requirements Specification |
| MVP | Minimum Viable Product |
| UAT | User Acceptance Testing |
| UC | Use Case |

### 1.4 References
[Reference documents]

---

## 2. Overall Description

### 2.1 Product Perspective
[Describe the new system in context]

### 2.2 System Overview
| Component | Description |
|-----------|-------------|
| [Component 1] | [Description] |
| [Component 2] | [Description] |

### 2.3 Users and Characteristics
| User Type | Description | Count (approx) |
|-----------|-------------|----------------|
| [Type 1] | [Description] | [X] |
| [Type 2] | [Description] | [Y] |

### 2.4 Operational Environment
- **Platform:** [Web/Mobile/Desktop/API]
- **Cloud Provider:** [AWS/Azure/GCP/On-premise]
- **Region:** [Region]
- **Environments:** Dev, Staging, Production

### 2.5 Dependencies
| External System | Purpose | Interface |
|-----------------|---------|-----------|
| [System 1] | [Purpose] | [API/File/Other] |
| [System 2] | [Purpose] | [API/File/Other] |

### 2.6 Stakeholders
| Role | Name | Responsibility |
|------|------|----------------|
| Project Sponsor | [Name] | Owns budget, ultimate accountability |
| Product Owner | [Name] | Decides scope, priorities |
| Point of Contact | [Name] | Answers requirement questions |
| Approval Authority | [Name(s)] | Signs off on this SRS |

---

## 3. Functional Requirements

### 3.1 Feature List (MoSCoW)

#### Must Have (Mandatory - blockers if missing)
| ID | Feature | Description | Priority | Use Case |
|----|---------|-------------|----------|----------|
| F01 | [Feature Name] | [Description] | Must | UC01 |
| F02 | [Feature Name] | [Description] | Must | UC02 |

#### Should Have (Important but not blocking)
| ID | Feature | Description | Priority | Use Case |
|----|---------|-------------|----------|----------|
| F10 | [Feature Name] | [Description] | Should | UC10 (if detailed) |

#### Could Have (Nice to have)
| ID | Feature | Description | Priority |
|----|---------|-------------|----------|
| F20 | [Feature Name] | [Description] | Could |

### 3.2 Use Case Specifications

> One entry per Use Case gathered in Question 10. Every Must-Have feature MUST have a
> corresponding entry here — this is what downstream diagram generation (sequence, state,
> screen-flow diagrams) reads from.

#### UC01 - [Use Case Name]
| Field | Value |
|-------|-------|
| Actor(s) | [Actor] |
| Trigger | [What starts it] |
| Preconditions | [What must be true before] |
| Main Flow | 1. [Step] → 2. [Step] → 3. [Step] → ... |
| Alternative Flows | [Valid variations, or "None"] |
| Exception/Error Flows | [What goes wrong, and what happens] |
| Postconditions | [What must be true after success] |

#### UC02 - [Use Case Name]
| Field | Value |
|-------|-------|
| Actor(s) | [Actor] |
| Trigger | [What starts it] |
| Preconditions | [What must be true before] |
| Main Flow | 1. [Step] → 2. [Step] → 3. [Step] → ... |
| Alternative Flows | [Valid variations, or "None"] |
| Exception/Error Flows | [What goes wrong, and what happens] |
| Postconditions | [What must be true after success] |

[... one entry per UC ...]

### 3.3 Data Handling

#### Data Entities
| Entity | Fields | Relationships |
|--------|--------|---------------|
| [Entity 1] | [field1, field2, ...] | [Relationships] |
| [Entity 2] | [field1, field2, ...] | [Relationships] |

#### Data Storage
- **Database Type:** [RDS/Self-managed/Managed service]
- **Estimated Size:** [Size]
- **Retention Policy:** [Policy]

### 3.4 Business Rules
| Rule ID | Rule | Condition | Action |
|---------|------|-----------|--------|
| BR01 | [Rule name] | [When] | [Action] |

### 3.5 Error Handling
| Error Case | Handling |
|------------|----------|
| [Case 1] | [Handling approach] |

---

## 4. Non-Functional Requirements

### 4.1 Performance
| Metric | Target | Measurement |
|--------|--------|-------------|
| Page load | < [X]s | P95 |
| API response | < [X]ms | P95 |
| Concurrent users | [X] users | Peak |

### 4.2 Scalability
- **Current capacity:** [X] users
- **Expected growth:** [%/month]
- **Scaling strategy:** [Horizontal/Vertical/Auto-scaling]

### 4.3 Availability
- **SLA:** [X]% uptime
- **Downtime allowed:** [X] hours/year
- **Maintenance window:** [Time slot]

### 4.4 Security
- **Authentication:** [Methods]
- **Authorization:** [Model]
- **Data encryption:** [At-rest/In-transit]
- **Audit logging:** [Yes/No]

### 4.5 Backup & Recovery
- **RPO (Recovery Point Objective):** [X] hours
- **RTO (Recovery Time Objective):** [X] minutes
- **Backup frequency:** [Daily/Weekly]

### 4.6 Compliance
| Regulation | Requirement | Implementation |
|------------|-------------|----------------|
| [GDPR/PDPA/PCI-DSS] | [Requirement] | [How to implement] |

---

## 5. Interface Requirements

### 5.1 User Interfaces
| Screen | Purpose | Key Features |
|--------|---------|--------------|
| [Screen 1] | [Purpose] | [Features] |

### 5.2 API Interfaces
| Endpoint | Method | Purpose |
|----------|--------|---------|
| /api/v1/[resource] | GET/POST/PUT/DELETE | [Purpose] |

### 5.3 Integration Interfaces
| External System | Interface Type | Data Flow |
|-----------------|----------------|-----------|
| [System] | [API/File] | [Direction] |

---

## 6. Supporting Processes

### 6.1 Deployment
- **Cloud Provider:** [AWS/Azure/GCP]
- **Region:** [Region]
- **Environments:** Dev, Staging, Production
- **CI/CD:** [Tool]
- **Deployment Strategy:** [Blue-green/Canary/Rolling]

### 6.2 Monitoring & Alerting
- **Monitoring Tools:** [Tools]
- **Alert Channels:** [Channels]
- **Severity Levels:** [P1/P2/P3/P4]

### 6.3 Support Model
| Tier | Description | Response Time |
|------|-------------|---------------|
| Tier 1 | [Description] | [SLA] |
| Tier 2 | [Description] | [SLA] |
| Tier 3 | [Description] | [SLA] |

### 6.4 Training
- **User Training:** [Yes/No - materials]
- **Admin Training:** [Yes/No - materials]
- **Documentation:** [User/Admin/API docs]

### 6.5 Configuration Management
- **Admin-configurable settings:** [List - e.g. discount rules, thresholds, templates]
- **Feature flags:** [Yes/No - which features, rollout strategy]
- **Environment-specific settings:** [List - e.g. API keys, limits per Dev/Staging/Prod]
- **Multi-tenant configuration:** [Yes/No - description]

---

## 7. Success Metrics & Acceptance

### 7.1 Success Metrics (KPIs)
| KPI | Baseline | Target | Measurement |
|-----|----------|--------|-------------|
| [KPI 1] | [Value] | [Value] | [Method] |

### 7.2 Acceptance Criteria
- **UAT:** [Defined/Not defined]
- **Sign-off Process:** [Process description]
- **Definition of Done:** [Criteria]

### 7.3 Timeline
| Phase | Start | End | Deliverables |
|-------|-------|-----|--------------|
| Discovery | [Date] | [Date] | SRS, Architecture |
| Design | [Date] | [Date] | UI/UX, SDD |
| Development | [Date] | [Date] | MVP |
| Testing | [Date] | [Date] | UAT, Bug fixes |
| Go-Live | [Date] | [Date] | Deployment |
| Stabilization | [Date] | [Date] | Post-launch support |

### 7.4 Budget
- **Approved Budget:** [Amount]
- **Contingency:** [Amount]

---

## 8. Open Questions
| # | Question | Status | Notes |
|---|----------|--------|-------|
| 1 | [Question] | [Open/Resolved/TBD] | [Notes] |

---

## 9. Assumptions & Dependencies
| Category | Item | Impact |
|----------|------|--------|
| [Category] | [Assumption] | [Impact] |

---

## 10. Appendix

### A. Interview Summary
- **Interview Date:** [Date]
- **Duration:** [Duration]
- **Participants:** [Names/Roles]
- **Questions Answered:** [X]/38

### B. Change History
| Version | Date | Author | Changes |
|---------|------|--------|---------|
| 1.0 | [Date] | [Agent] | Initial SRS |

---

**Document Status:** Draft → Review → Approved → Final
**Next Review Date:** [Date]
```

---

## Reference

Use SRS_Questions_Catalogue.md to:
- Find more detailed questions for each category
- Look up technical questions (Technical probing)
- Add more questions if needed
