---
name: interview-stakeholder
description: Interview a stakeholder to gather requirements
agent: doc-coordinator
context: fork
syntax: /interview-stakeholder [stakeholder_name] [--type user|manager|developer|customer]
examples:
  - "/interview-stakeholder John --type user"
  - "/interview-stakeholder"
parameters:
  - name: stakeholder_name
    type: string
    required: false
    description: Name of the stakeholder to interview
  - name: type
    type: string
    required: false
    default: user
    description: Stakeholder type
---

# Command: /interview-stakeholder

## Description
Assists with interviewing a stakeholder to gather software requirements.

## Process

### Step 1: Identify the Stakeholder
- Determine the stakeholder type (User, Manager, Developer, Customer)
- Prepare the appropriate questions

### Step 2: Interview
Use the sample question set for the type:

#### User Stakeholder Questions
1. What do you use the system for?
2. What are the main tasks you perform daily?
3. What frustrates you about the current system?
4. What features do you need?
5. What problems do you encounter?

#### Manager Stakeholder Questions
1. What are the main business objectives?
2. What KPIs need to be achieved?
3. Budget and timeline?
4. What business constraints exist?
5. Who is the decision maker?

#### Developer Stakeholder Questions
1. Technical constraints?
2. Integration requirements?
3. Performance expectations?
4. Security requirements?
5. Deployment environment?

### Step 3: Record and Summarize
- Save the answers
- Extract requirements
- Generate requirement artifacts

## Output

```
Created: interview_{stakeholder_name}_{date}.md
Location: ./docs/interviews/
```
