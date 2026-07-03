# SRS Questions Catalogue - Review Report

**Document:** `SRS_Questions_Catalogue.md`  
**Review Date:** 2026-03-23  
**Reviewer:** Requirements Engineering Review

---

## 1. OVERALL ASSESSMENT

| Criterion | Result | Notes |
|----------|---------|---------|
| **1. QUESTION FLOW** | ✅ PASS | Logical order: Project Info → Users → Use Cases → Features → NFR → UI → Data → Business → Integration → Maintenance |
| **2. COMPLETENESS** | ⚠️ PASS WITH CONCERNS | Missing several important questions: Error Handling, Configuration Management, Audit Trail Review |
| **3. USABILITY** | ⚠️ PASS WITH CONCERNS | Some questions are too technical for business users |
| **4. CONSISTENCY** | ⚠️ PASS WITH CONCERNS | Contains typos and inconsistent formatting |
| **5. ANSWER QUALITY** | ⚠️ PASS WITH CONCERNS | Missing examples and default values |
| **6. CROSS-REFERENCES** | ⚠️ PASS WITH CONCERNS | Some duplication between categories |

### OVERALL RESULT: ⚠️ **PASS WITH CONCERNS**

---

## 2. SPECIFIC ISSUES TO FIX

### 2.1 TYPOS

| # | Location | Error | Fix |
|---|--------|-----|------------|
| 1 | 7.1 header | `Lo_type` | `Type` |
| 2 | 9.4 header | `Lo_type` | `Type` |
| 3 | 10.2.1 | `Wartung` (German) | `Maintenance` |
| 4 | 1.3.3 | Stray Chinese character in "future phases need to be designed for 预留" | "future phases need to be designed for in advance (reserved)" |
| 5 | 4.2.4 | Stray Chinese character in "when does it 触发" | "when does it trigger" |

### 2.2 QUESTIONS TOO TECHNICAL FOR A BUSINESS USER

| # | Question | Issue | Suggestion |
|---|---------|--------|---------|
| 1 | 5.2.1 `Which authentication method (SSO, MFA, password)?` | A business user may not know what SSO or MFA is | Add guidance: "E.g.: Simple login (username/password), Company login (SSO via Microsoft/Google), Two-step verification (MFA)" |
| 2 | 5.2.2 `Authorization model (RBAC, ABAC, permissions)?` | RBAC and ABAC are technical concepts | Rephrase as: "Should permissions be role-based (e.g. Admin, Manager, User) or attribute-based (e.g. can only edit data for their own department)?" |
| 3 | 5.5.5 `API version compatibility policy?` | Too technical | Rephrase as: "When the system is updated, does an old version of the app/client still need to keep working?" |
| 4 | 8.3.4 `Mandatory coding standards and frameworks?` | Coding standards are not a question for the business side | Move to the technical constraints section or remove from business requirements |
| 5 | 9.2.3 `GraphQL subscriptions/queries/mutations?` | Too technical | Replace with: "Does a third-party application need to connect via API? What kind of API?" |
| 6 | 9.3.4 `EDI standards, if any (B2B integrations)?` | EDI is too specialized | Add an explanation: "EDI (Electronic Data Interchange) — a standard for electronic data exchange with B2B partners" |

### 2.3 MISSING QUESTIONS — TO BE ADDED

| # | Category | Question to add | Rationale |
|---|----------|------------------|-------|
| 1 | **Error Handling** | `How should the system handle errors when something goes wrong? Is there a default message for the user?` | Missing a section on global error handling |
| 2 | **Error Handling** | `Are custom error codes needed?` | Important for debugging and support |
| 3 | **Configuration** | `Is there any configuration that admins need to be able to change without a redeploy?` | Important for operational flexibility |
| 4 | **Configuration** | `Are feature flags/toggles needed?` | Common in modern development |
| 5 | **Audit** | `Do audit logs need to be reviewed periodically? Who reviews them?` | A compliance requirement that is often overlooked |
| 6 | **Audit** | `Where are logs stored, and for how long?` | Complements 5.2.5 Audit logging |
| 7 | **Security** | `Is data masking/anonymization required?` | Important for PII protection |
| 8 | **Security** | `Is penetration testing required?` | Security validation |
| 9 | **UX/UI** | `Are there user testing/beta testing requirements?` | UX validation |
| 10 | **UX/UI** | `Is a prototype/MVP needed?` | Common in agile development |
| 11 | **Data** | `How are data encryption keys managed?` | Security implementation detail |
| 12 | **Business** | `Are there any SLA/contract requirements with customers?` | A legal requirement that is often overlooked |
| 13 | **Integration** | `Are webhooks/real-time notifications needed?` | Common integration pattern |
| 14 | **Maintenance** | `Is an application health dashboard needed?` | Operations visibility |

---

## 3. DUPLICATION ISSUES (CROSS-REFERENCE)

| # | Question A | Question B | Issue | Suggestion |
|---|-----------|-----------|--------|---------|
| 1 | 2.3.1 `Which external systems interact with this one?` | 9.1.1 `Which external systems need to be integrated?` | Duplicates external systems | Merge into a single question, referencing section 9 |
| 2 | 4.1.2 `Input data sources` | 7.1.1 `List all main data entities` | Closely related, potentially duplicated | Add a reference: "See also 7.1.1" |
| 3 | 5.2.5 `Audit logging requirements` | 10.4.1 `Detailed logging requirements` | Audit log vs. general logging | Clearly separate: Audit log (security) vs. Application log (operations) |
| 4 | 4.3.5 `Notification channels` | 10.4.4 `Alerting thresholds` | Notifications vs. Alerts | There is overlap; should add a reference |
| 5 | 7.4 `Backup & Recovery` | 5.3.3 `Disaster recovery requirements` | Related but from a different angle | Needs a clear cross-reference |

---

## 4. IMPROVEMENT SUGGESTIONS

### 4.1 FORMAT IMPROVEMENTS

**Standardize the Answer Type notation:**
```
Current: Boolean + Text, Table, List, Number + Unit
Proposed: Add clearer prefixes
  - [Boolean] Yes/No
  - [Text] Short description
  - [List] List (one item per line)
  - [Table: Col1, Col2] Table with specific columns
  - [Number] Specific number
  - [Currency] Amount of money (e.g. 100,000,000 VND)
  - [Date] Date (e.g. 2024-12-31)
  - [Duration] Duration (e.g. 30 minutes, 2 hours)
  - [Percentage] Percentage (e.g. 99.9%)
  - [File/Link] Attached file or link
```

### 4.2 ADD EXAMPLE ANSWERS

| Question | Example Answer |
|---------|---------------|
| 1.1.2 | "An online sales management system that lets customers place orders, pay, and track shipments" |
| 5.1.1 | "Home page: < 2 seconds, Search: < 1 second, Checkout: < 5 seconds" |
| 5.3.1 | "99.9% uptime (equivalent to a maximum of 8.7 hours of downtime per year)" |
| 5.2.7 | "At least 8 characters, with uppercase, lowercase, digits, special characters, expiring after 90 days" |
| 9.3.1 | "CSV, XML, JSON, PDF, Excel (.xlsx)" |

### 4.3 STRUCTURAL IMPROVEMENTS

**Add a Quick-Start Section:**
```
## QUICK START GUIDE
For small projects (< 3 months), only the questions marked ✓ (required) need to be answered
```

**Add Dependency Notes:**
```
## 3.1.4 Dependencies between use cases?
Note: Some use cases may depend on other use cases or need to be performed before/after them
```

**Separate Technical vs. Business Questions:**
```
### SECTION X.X: Technical Requirements (For IT/Technical Stakeholders only)
```

### 4.4 USABILITY IMPROVEMENTS

1. **Add an expanded glossary** in Appendix B:
   - MFA, SSO, RBAC, ABAC
   - API, REST, GraphQL, SOAP
   - EDI, SFTP
   - MTBF, MTTR, RTO, RPO
   - Containerization, CI/CD

2. **Add a decision tree** for some complex questions:
   ```
   5.2.1 Authentication Method?
   ├── Internal company application → SSO (Single Sign-On)
   ├── Customer-facing application → MFA (Multi-Factor Authentication)
   └── Both → SSO + MFA
   ```

3. **Add visual difficulty markers:**
   ```
   [✓ Required] [○ Optional] [⚠️ Needs technical consultation]
   ```

---

## 5. DETAILED ASSESSMENT BY CATEGORY

### Score: 1-10 (10 = excellent, 5 = average, <5 = needs improvement)

| # | Category | Score | Comment |
|---|----------|------|----------|
| 1 | **1. BASIC PROJECT INFORMATION** | **8/10** | Complete, logical. Missing a question about project risks. |
| 2 | **2. USERS & ACTORS** | **8/10** | Good, distinguishes primary/secondary actors. Includes external actors. |
| 3 | **3. USE CASES & FEATURES** | **7/10** | Good UC structure (precondition, postcondition, main flow, alternative, exception). Missing acceptance criteria. |
| 4 | **4. FUNCTIONAL REQUIREMENTS** | **7/10** | Data I/O, Business Logic, Reports are all covered. Missing an Error Handling section. |
| 5 | **5. NON-FUNCTIONAL REQUIREMENTS** | **8/10** | Performance, Security, Availability, Scalability, Compatibility are all good. Missing Maintainability. |
| 6 | **6. USER INTERFACE** | **7/10** | UI type, Screens, Accessibility are covered. Missing UX research/testing requirements. |
| 7 | **7. DATA & STORAGE** | **8/10** | ER diagram, Data volume, Retention, Backup are all good. |
| 8 | **8. BUSINESS RULES & CONSTRAINTS** | **7/10** | Legal, Technical, Resource constraints are all covered. Missing Policy/Governance. |
| 9 | **9. INTEGRATION & EXTERNAL SYSTEMS** | **7/10** | External systems, API, File formats, Third-party are all good. |
| 10 | **10. MAINTENANCE & OPERATIONS** | **7/10** | Deployment, Maintenance, Training, Monitoring are all covered. Contains the "Wartung" error. |

---

## 6. PRIORITY MATRIX — FIX ORDER

### HIGH PRIORITY (Fix immediately — affects document quality)

| # | Issue | Reason |
|---|-------|-------|
| 1 | 7.1 & 9.4 typo `Lo_type` | Syntax error, affects parsing |
| 2 | 10.2.1 `Wartung` | Incorrect German word in an English/Vietnamese document |
| 3 | Missing Error Handling questions | Large gap in requirements |
| 4 | Missing Configuration Management | Important for operations |
| 5 | 5.2.2 RBAC/ABAC too technical | Business users won't understand |

### MEDIUM PRIORITY (Fix in the next version)

| # | Issue |
|---|-------|
| 1 | Add example answers |
| 2 | Standardize the Answer Type notation |
| 3 | Fix cross-references between 2.3 and 9.1 |
| 4 | Add glossary terms (MFA, SSO, RBAC, etc.) |
| 5 | 1.3.3 stray Chinese character `预留` |

### LOW PRIORITY (Improve gradually)

| # | Issue |
|---|-------|
| 1 | Add a Quick-Start guide |
| 2 | Add decision trees |
| 3 | Add visual difficulty markers |
| 4 | Separate Technical vs. Business sections |

---

## 7. CONCLUSION

**Overall score: 7.3/10**

**Strengths:**
- Clear, logical classification structure
- Good coverage of requirements aspects
- Good use of MoSCoW prioritization
- Includes a useful glossary appendix

**Needs improvement:**
- Fix 5 typo/format errors
- Add Error Handling and Configuration Management sections
- Reduce technical language for business users
- Add example answers
- Fix cross-references

**Recommendations:**
- Release version 1.1 with the HIGH priority fixes
- Consider splitting into 2 versions: a Business Requirements Questionnaire and a Technical Requirements Checklist

---

**Document Info:**
- Review Version: 1.0
- Review Date: 2026-03-23
- Status: Ready for Revision
</content>
