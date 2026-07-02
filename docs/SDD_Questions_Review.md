# SDD Questions Catalogue - Review Report

**File reviewed**: `SDD_Questions_Catalogue.md`  
**Date**: 2026-03-23  
**Reviewer**: Architecture & UX Review

---

## 1. OVERALL ASSESSMENT

| Criterion | Result | Score |
|----------|---------|---------|
| **1. Question Flow** | ⚠️ Pass with concerns | 7/10 |
| **2. Completeness** | ⚠️ Pass with concerns | 7/10 |
| **3. Usability** | ⚠️ Pass with concerns | 6/10 |
| **4. Consistency** | ⚠️ Pass with concerns | 7/10 |
| **5. Answer Quality** | ⚠️ Pass with concerns | 7/10 |
| **6. SRS-SDD Linkage** | ❌ Fail | 4/10 |
| **OVERALL** | **⚠️ PASS WITH CONCERNS** | **6.3/10** |

---

## 2. SPECIFIC ISSUES TO FIX

### 2.1 QUESTION FLOW (Flow Analysis) - Score: 7/10

#### Issues found:

**a) Missing a "Per-Use-Case Design Details" section**
- Currently there is no section asking about the detailed design for each Use Case
- The SDD needs to describe how each Use Case from the SRS is implemented as components
- Should add: "Use Case Implementation Mapping"

**b) The question order should use smaller steps**
- 1.1 → 1.2 → 1.3 → 1.4 (good)
- But an overview diagram request should be added at the start of section 1

**c) Security should come after Backend-Database**
- Following the design flow, Security should be asked about AFTER Data and Business logic are understood
- Proposal: Deployment → Security → Integration (instead of Security → Integration → Deployment)

---

### 2.2 COMPLETENESS - Score: 7/10

#### Missing important questions:

| # | Missing question | Where to add it | Reason |
|-----|---------------|-----------------|-------|
| 1 | **Business Rules / Domain Logic** | Section 3 (Backend) | No place asking about validation rules, workflow logic, business constraints |
| 2 | **Error Handling Design** | Section 3.2 (API Design) or 9.2 | Only asks about retry/circuit breaker, missing an error code taxonomy |
| 3 | **File Upload / Storage Design** | Section 6.5 or 3.x | Needs a question about file storage strategy, max size, allowed types |
| 4 | **Notification System Design** | Section 6.x | Fragmented across 3.4.4, needs its own section |
| 5 | **API Endpoint Inventory** | Section 3.2 | Only asks about patterns, not specific endpoints/resources |
| 6 | **Data Migration Strategy** | Section 7.x | Missing a question about DB versioning, migration approach |
| 7 | **Feature Toggles** | Already in Section 7.3.4, but missing a use case | Needs a further question about toggle categories |
| 8 | **Localization / i18n** | Completely missing | Needed for international applications |
| 9 | **Analytics / Tracking** | Completely missing | User behavior tracking, conversion tracking |
| 10 | **Session Management** | Section 5.3 exists but is too generic | Should separate session timeout and concurrent sessions |

#### Redundant or unnecessary questions:

| # | Question | Issue | Suggestion |
|-----|---------|--------|---------|
| 1 | 3.3.5 Password policy + 5.1.4 Password reset | Overlaps with 5.1.x | Merge into 5.1.x |
| 2 | 3.3.6 Account lockout policy | Overlaps with the Security section | Merge into 5.1.x |
| 3 | 7.2.4 Helm charts or Kustomize | Too detailed for a questionnaire | Make it Optional |
| 4 | 10.1.4 Code complexity limits | Too technical for a stakeholder | Remove or clearly mark as [T] |

---

### 2.3 USABILITY - Score: 6/10

#### Issues:

**a) Questions too technical for Business Stakeholders:**

| Question | Issue | Suggestion |
|---------|--------|---------|
| 1.4.3 "Service Discovery mechanism" | Deep microservices technicality | Add a short explanation |
| 3.4.2 "Cache invalidation strategy" | Requires background knowledge | Add a simple example |
| 5.2.1 "RBAC/ABAC/PBAC" | Access-control technicality | Add "or describe in your own words" |
| 6.4.2 "Event schema registry" | Too specialized | Mark as [T] |
| 9.4.6 "SLO/SLI definitions" | Needs explanation | Add a note |
| 10.2.8 "Mutation testing" | Too niche | Remove from the main list |

**b) Missing context:**
- No overview introduction to the document
- No guidance on how to answer
- No example of an already-answered question

**c) Mixed language:**
- Line 214: "user只能访问自己的数据" — inconsistent Chinese text
- Should be standardized to a single language

---

### 2.4 CONSISTENCY - Score: 7/10

#### Issues:

**a) Inconsistent answer format:**

| Question | Answer Format | Issue |
|---------|--------------|---------|
| 1.1.1 | "Monolithic / Layered / MVC / Microservices" | Complete |
| 1.2.1 | "Cloud (AWS/Azure/GCP) / On-premise" | Good |
| 3.3.2 | "Access token: 15 min / Refresh token: 7 days" | Good |
| 8.1.1 | "X concurrent users" | Too vague, no example |
| 9.5.2 | "X minutes / X hours" | Needs a concrete number |

**b) Duplicate [B]/[T] marking:**
- 5.1.x and 3.3.x overlap on authentication/password
- 9.4.x and 7.5.x overlap on monitoring

**c) Non-continuous numbering in some sections:**
- 3.2 has 7 questions (1.1 → 1.7)
- Should be renumbered if more are added

---

### 2.5 ANSWER QUALITY - Score: 7/10

#### Shortcomings:

**a) No concrete Tech Stack Options for:**

| Area | Currently | Needs to add |
|------|---------|---------|
| Monitoring | "Prometheus, Datadog" | Add reference guidance: "Enterprise: Datadog/New Relic; Open source: Prometheus+Grafana" |
| Message Queue | "Kafka, RabbitMQ" | Add a short comparison: "High throughput: Kafka; Simple task: RabbitMQ/SQS" |
| API Gateway | "Kong, AWS API Gateway" | Add use-case recommendations |
| Database | Long list | Categorize: "OLTP vs OLAP, ACID vs BASE" |

**b) Missing examples:**

| Question | Example needed |
|---------|-----------------|
| 8.2.1 Response time SLA | "e.g. p95 < 200ms, p99 < 500ms" |
| 8.1.1 Concurrent users | "e.g. 1000 concurrent users" |
| 9.5.2 RTO | "e.g. < 4 hours" |

**c) Tech stack recommendations by project size:**

Should add a reference table:
- Small project (< 6 months): Tech X, Y, Z
- Medium (6-12 months): Tech A, B, C  
- Large enterprise: Tech 1, 2, 3

---

### 2.6 SRS-SDD LINKAGE - Score: 4/10

#### Serious issue:

**a) No reference to SRS Use Cases:**

| Missing | Impact |
|-----|---------|
| Doesn't ask "Which Use Case from the SRS?" | The SDD cannot be linked to requirements |
| Doesn't ask "Which Actor?" | Missing context for the architecture |
| Doesn't ask "Which functional requirements?" | The design may not cover everything |

**b) No cross-reference mechanism:**

Should add:
```
## 0. REQUIREMENTS MAPPING (NEW SECTION)

### 0.1 SRS Reference
- SRS Document: [link or version]
- Use Cases covered: UC-01, UC-02, ...
- Key Functional Requirements: FR-1.1, FR-2.3, ...

### 0.2 Requirements → Architecture Mapping
| Use Case | Architectural Component | API Endpoint |
|----------|------------------------|--------------|
| UC-01 Login | Auth Service | POST /auth/login |
| UC-02 Browse Products | Product Service | GET /products |
```

**c) The Appendix isn't linked to the SRS:**

The "Appendix: Quick Reference" section (lines 512-546) should include:
- Links to Use Cases
- A traceability matrix
- Requirements ID mapping

---

## 3. SPECIFIC IMPROVEMENT SUGGESTIONS

### 3.1 FLOW RESTRUCTURE

**Current order:**
```
1. Architecture → 2. Frontend → 3. Backend → 4. DB → 5. Security → 6. Integration → 7. Deployment → 8. Performance → 9. Reliability → 10. Dev Workflow
```

**Proposed order:**
```
0. REQUIREMENTS MAPPING (NEW - link to SRS)
1. Architecture Overview (high-level)
2. USE CASE MAPPING (NEW - how each UC is implemented)
3. Frontend Architecture
4. Backend Architecture + Business Rules
5. Database Design
6. Integration Architecture  
7. Security Architecture
8. Deployment & Infrastructure
9. Performance & Scalability
10. Reliability & Monitoring
11. Development Workflow
```

### 3.2 ADD A NEW SECTION

**NEW: Section 0 - Requirements Mapping**
```markdown
## 0. REQUIREMENTS MAPPING

### 0.1 SRS Reference
| Question | Type | Suggested answer |
|---------|------|-------------------|
| 0.1.1 | Has the SRS document been completed? | [B] | Yes (version X.X) / Not yet |
| 0.1.2 | Which Use Cases need to be implemented? | [B] | UC-01, UC-02, ... |
| 0.1.3 | Key functional requirements? | [B] | FR-1.1, FR-2.3, ... |
| 0.1.4 | Non-functional requirements from the SRS? | [B] | NFR-1 (Performance), NFR-2 (Security), ... |
```

**NEW: Section 2.5 - Use Case Implementation Mapping**
```markdown
### 2.5 Use Case Implementation

| Use Case | Component | API Endpoint | DB Entity | Error Handling |
|----------|-----------|--------------|-----------|-----------------|
| UC-01 Login | Auth Service | POST /auth/login | Users | 401, 403, 429 |
| UC-02 Browse | Product Service | GET /products | Products | 404, 500 |
```

### 3.3 LANGUAGE FIX

**Issue: Line 214 contains Chinese text**
```markdown
# BEFORE:
| 5.2.3 | Are resource-level permissions needed? | [T] | Yes (user只能访问自己的数据) / No / Needs consultation |

# AFTER:
| 5.2.3 | Are resource-level permissions needed? | [T] | Yes (user can only access their own data) / No / Needs consultation |
```

### 3.4 ADD A QUICK START GUIDE

**Add after the header:**
```markdown
> **Quick usage guide**:
> 1. Read through once to understand the scope
> 2. Answer the [B] questions first (about 30-40 questions)
> 3. Mark the [T] questions depending on complexity
> 4. For each answer, provide as much detail as possible
> 5. See "Questions by Project Type" for focus areas
```

### 3.5 IMPROVE ANSWER SUGGESTIONS

**Add a Tech Stack Comparison Table to the Appendix:**
```markdown
### Tech Stack Selection Guide

| Need | Small Project | Medium Project | Enterprise |
|---------|---------------|-----------------|------------|
| API | REST | REST + GraphQL | REST + gRPC |
| Database | PostgreSQL | PostgreSQL + Redis | Multi-DB + Sharding |
| Cache | None/Memory | Redis | Redis + CDN |
| Queue | Sync calls | Redis Pub/Sub | Kafka/RabbitMQ |
| Monitoring | Logs only | Prometheus+Grafana | Datadog/New Relic |
| CI/CD | GitHub Actions | GitLab CI | Jenkins + ArgoCD |
```

---

## 4. DETAILED ASSESSMENT BY CATEGORY

### 4.1 SYSTEM ARCHITECTURE (1.x) - 8/10

| Criterion | Assessment |
|----------|----------|
| Architectural Pattern (1.1) | Good, covers all common patterns |
| Deployment Architecture (1.2) | Good, covers both cloud/on-premise |
| High-Level Components (1.3) | Should add a component responsibility matrix |
| Communication Patterns (1.4) | Good, covers sync/async |

**Strength**: Clear [B]/[T] classification  
**Needs improvement**: Add a visual architecture diagram request

---

### 4.2 FRONTEND ARCHITECTURE (2.x) - 7/10

| Criterion | Assessment |
|----------|----------|
| Technology Stack (2.1) | Complete |
| Component Architecture (2.2) | Missing atomic-design detail |
| State Management (2.3) | Good |
| Routing & Navigation (2.4) | Good |
| Responsive & PWA (2.5) | Missing accessibility (a11y) |

**Missing**: 
- Accessibility requirements (WCAG compliance)
- Internationalization (i18n)
- Performance budgets (JS bundle size limit)

---

### 4.3 BACKEND ARCHITECTURE (3.x) - 7/10

| Criterion | Assessment |
|----------|----------|
| Technology Stack (3.1) | Good |
| API Design (3.2) | Good, missing an endpoint inventory |
| Authentication (3.3) | Overlaps with Security 5.x |
| Caching & Queue (3.4) | Good |

**Overlaps to resolve**:
- 3.3.x with 5.1.x (authentication)
- 3.4.x with 8.3.x (caching)

---

### 4.4 DATABASE DESIGN (4.x) - 8/10

| Criterion | Assessment |
|----------|----------|
| Database Type (4.1) | Good |
| Schema Design (4.2) | Good |
| Indexing & Performance (4.3) | Good |
| Data Partitioning (4.4) | Good |

**Strength**: Fully covers SQL/NoSQL, ORM options  
**Needs adding**: Data lifecycle management, a more detailed archival strategy

---

### 4.5 SECURITY ARCHITECTURE (5.x) - 8/10

| Criterion | Assessment |
|----------|----------|
| Authentication & Identity (5.1) | Good |
| Authorization Model (5.2) | Good |
| Data Protection (5.3) | Good |
| API & Application Security (5.4) | Good, covers OWASP top 10 |
| Compliance & Audit (5.5) | Good |

**Strength**: Fully covers compliance requirements  
**Needs improvement**: Add a security architecture diagram request

---

### 4.6 INTEGRATION ARCHITECTURE (6.x) - 7/10

| Criterion | Assessment |
|----------|----------|
| Third-Party Services (6.1) | Good |
| Payment Integration (6.2) | Detailed and complete |
| Authentication Providers (6.3) | Good |
| Event-Driven & Messaging (6.4) | Good |
| External System Integration (6.5) | Good |

**Missing**: 
- Integration with internal legacy systems
- Data format transformation/ETL considerations

---

### 4.7 DEPLOYMENT & INFRASTRUCTURE (7.x) - 8/10

| Criterion | Assessment |
|----------|----------|
| CI/CD Pipeline (7.1) | Good |
| Containerization (7.2) | Good |
| Environment Configuration (7.3) | Good |
| Infrastructure as Code (7.4) | Good |
| Monitoring & Logging (7.5) | Good |

**Strength**: Fully covers the DevOps lifecycle  
**Needs adding**: Cost estimation/budget considerations

---

### 4.8 PERFORMANCE & SCALABILITY (8.x) - 7/10

| Criterion | Assessment |
|----------|----------|
| Capacity Planning (8.1) | Good, needs concrete numbers |
| Performance Requirements (8.2) | Missing concrete SLAs |
| Caching Strategy (8.3) | Good |
| Scalability (8.4) | Good |

**Needs improvement**: 
- Add reference values (industry benchmarks)
- Database query performance budgets

---

### 4.9 RELIABILITY & MONITORING (9.x) - 8/10

| Criterion | Assessment |
|----------|----------|
| Availability & SLA (9.1) | Good |
| Error Handling (9.2) | Good |
| Logging Strategy (9.3) | Good |
| Monitoring & Alerting (9.4) | Good |
| Disaster Recovery (9.5) | Very detailed |

**Strength**: The DR section is very comprehensive  
**Needs adding**: Chaos engineering considerations

---

### 4.10 DEVELOPMENT WORKFLOW (10.x) - 7/10

| Criterion | Assessment |
|----------|----------|
| Code Quality (10.1) | Good |
| Testing Requirements (10.2) | Good, includes a test pyramid |
| Code Review (10.3) | Good |
| Version Control (10.4) | Good |
| Documentation (10.5) | Good |

**Missing**:
- Definition of Done (DoD) criteria
- Definition of Ready (DoR) criteria
- Technical debt management

---

## 5. SUMMARY AND RECOMMENDATIONS

### 5.1 REVISION PRIORITIES

| Priority | Issue | Effort | Impact |
|----------|-------|--------|--------|
| **P0** | Add Section 0: Requirements Mapping | Medium | High |
| **P0** | Add Use Case → Component mapping | Medium | High |
| **P1** | Fix mixed language (line 214) | Low | Medium |
| **P1** | Add concrete examples to answer suggestions | Low | High |
| **P2** | Resolve the overlap between 3.3.x and 5.1.x | Medium | Medium |
| **P2** | Add i18n and accessibility sections | Low | Medium |
| **P2** | Restructure flow: Security after DB | Medium | Medium |

### 5.2 RECOMMENDED ACTIONS

1. **Immediately**: Add Section 0 to link SRS-SDD
2. **This week**: Fix the language inconsistency and add examples
3. **Next sprint**: Restructure the flow and resolve overlaps

### 5.3 FINAL SCORECARD

```
╔═══════════════════════════════════════════════════════════════╗
║            SDD QUESTIONS CATALOGUE REVIEW                      ║
╠═══════════════════════════════════════════════════════════════╣
║  Overall Rating: ⚠️ PASS WITH CONCERNS (6.3/10)               ║
║                                                               ║
║  Strengths:                                                   ║
║  ✓ Comprehensive coverage of technical areas                 ║
║  ✓ Clear [B]/[T] marking system                              ║
║  ✓ Good categorization by architectural domain                ║
║  ✓ Detailed answer suggestions                               ║
║                                                               ║
║  Weaknesses:                                                  ║
║  ✗ No SRS linkage (critical gap)                             ║
║  ✗ Mixed language (Chinese text)                             ║
║  ✗ Some questions too technical for stakeholders             ║
║  ✗ Missing use case → architecture mapping                   ║
║  ✗ Overlaps between sections                                 ║
║                                                               ║
║  Recommendation: APPROVED WITH CONDITIONS                    ║
║  - Must add Section 0 (Requirements Mapping) before use       ║
║  - Must fix language consistency issues                      ║
║  - Should restructure flow for better logical progression    ║
╚═══════════════════════════════════════════════════════════════╝
```

---

## 6. APPENDIX: DETAILED ISSUE LIST

### 6.1 MUST FIX (Before Use)

| # | Section | Line | Issue | Fix Required |
|---|---------|------|-------|---------------|
| 1 | 5.2.3 | 214 | Chinese text "user只能访问自己的数据" | Change to English |
| 2 | ALL | - | No SRS Use Case reference | Add Section 0 |
| 3 | - | - | No use case → component mapping | Add section 2.5 |

### 6.2 SHOULD FIX (Recommended)

| # | Section | Issue | Fix |
|---|---------|-------|-----|
| 1 | 3.3.x, 5.1.x | Overlapping authentication | Merge or cross-reference |
| 2 | 2.5 | Missing i18n | Add 2.5.6 Internationalization |
| 3 | 2.x | Missing accessibility | Add 2.6 Accessibility |
| 4 | 8.x | Vague SLAs | Add industry reference values |
| 5 | 10.x | Missing DoD/DoR | Add definition sections |

### 6.3 NICE TO HAVE (Enhancement)

| # | Section | Issue | Suggestion |
|---|---------|-------|------------|
| 1 | Header | No usage guide | Add a quick start guide |
| 2 | Appendix | No tech stack guide | Add a comparison table |
| 3 | 7.x | No cost estimation | Add budget considerations |
| 4 | 9.x | No chaos engineering | Add resilience testing |

---

*This report was generated automatically by the Architecture & UX Review System*
</content>
