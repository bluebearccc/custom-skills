# SRS Questions Catalogue
# Catalogue of Software Requirements Gathering Questions

**Purpose:** This document contains a comprehensive list of questions for gathering information from customers/stakeholders before writing the Software Requirements Specification (SRS) document.

**How to use:** Mark ✓ next to questions that have been answered, collect the answers from stakeholders, and save them into the Requirements Gathering Summary document.

---

## 1. BASIC PROJECT INFORMATION

### 1.1 Project Name & Description

| # | Question | Type | Required |
|---|---------|------|----------|
| 1.1.1 | What is the official name of the project? | Text | ✓ |
| 1.1.2 | A brief description (1-2 sentences) of the system to be built? | Text | ✓ |
| 1.1.3 | Does this new system replace or supplement an existing system? | Text | ✓ |
| 1.1.4 | What business problem or opportunity does this project address? | Text | ✓ |
| 1.1.5 | What alternative solutions have been considered (including doing nothing)? | Text | Optional |

### 1.2 Business Objectives

| # | Question | Type | Required |
|---|---------|------|----------|
| 1.2.1 | What is the main objective of the project (SMART)? | Text | ✓ |
| 1.2.2 | Which KPIs will be used to measure success? | List | ✓ |
| 1.2.3 | What is the expected ROI benefit? | Number | Optional |
| 1.2.4 | What is the timeline for achieving the expected benefits? | Date/Duration | Optional |
| 1.2.5 | What do the key stakeholders expect from the project? | Text | ✓ |

### 1.3 Scope

| # | Question | Type | Required |
|---|---------|------|----------|
| 1.3.1 | What is the official in-scope of the project? | Text | ✓ |
| 1.3.2 | What out-of-scope is accepted? | Text | ✓ |
| 1.3.3 | Are there any features in future phases that need to be reserved? | Boolean + Text | Optional |
| 1.3.4 | Dependencies on other projects/teams? | List | ✓ |
| 1.3.5 | Are there any restrictions regarding vendor lock-in? | Text | Optional |

### 1.4 Stakeholders

| # | Question | Type | Required |
|---|---------|------|----------|
| 1.4.1 | List all stakeholder groups and individuals? | Table (Name, Role, Contact) | ✓ |
| 1.4.2 | Who is the Project Sponsor (person responsible for the budget)? | Text | ✓ |
| 1.4.3 | Who is the Product Owner/Business Owner? | Text | ✓ |
| 1.4.4 | Who is the main point of contact for requirements? | Text | ✓ |
| 1.4.5 | Who has final approval authority? | Text | ✓ |
| 1.4.6 | Are there any stakeholders with conflicting interests? | Text | Optional |

### 1.5 Budget & Timeline

| # | Question | Type | Required |
|---|---------|------|----------|
| 1.5.1 | What is the total project budget? | Currency | ✓ |
| 1.5.2 | Is the budget allocated by phases? | Table | Optional |
| 1.5.3 | Key deadlines (go-live, milestones)? | Date List | ✓ |
| 1.5.4 | Is there a penalty for delayed delivery? | Currency/Text | Optional |
| 1.5.5 | What team resources (FTE) are allocated? | Number | ✓ |

---

## 2. USERS & ACTORS

### 2.1 Identifying Users/Actors

| # | Question | Type | Required |
|---|---------|------|----------|
| 2.1.1 | Who will directly use the system? | List | ✓ |
| 2.1.2 | How many distinct user/actor types are there? | Number | ✓ |
| 2.1.3 | How many expected users are there for each actor/user type? | Table | ✓ |
| 2.1.4 | Who are the primary actors (frequent use)? | List | ✓ |
| 2.1.5 | Who are the secondary actors (infrequent use)? | List | Optional |
| 2.1.6 | Are there anonymous/guest users? | Boolean | Optional |
| 2.1.7 | Are users segmented by region/department? | Text | Optional |

### 2.2 Permissions & Authorization

| # | Question | Type | Required |
|---|---------|------|----------|
| 2.2.1 | What permissions does each actor have in the system? | Table (Actor, Permissions) | ✓ |
| 2.2.2 | Is authorization needed across hierarchical levels? | Boolean + Text | ✓ |
| 2.2.3 | Which roles are required for approval workflows? | List | ✓ |
| 2.2.4 | Is there a concept of "separation of duties"? | Boolean + Text | Optional |
| 2.2.5 | What special capabilities do admin users have? | List | ✓ |

### 2.3 External Actors

| # | Question | Type | Required |
|---|---------|------|----------|
| 2.3.1 | Are there any external systems that interact with the application? | List | ✓ |
| 2.3.2 | Do partners/vendors/customers access the system? | Boolean + List | ✓ |
| 2.3.3 | Do external actors need integration via API or UI? | List | Optional |
| 2.3.4 | What data do third-party systems need? | List | Optional |

---

## 3. USE CASES & FEATURES

### 3.1 Use Case Overview

| # | Question | Type | Required |
|---|---------|------|----------|
| 3.1.1 | List all the main use cases/features? | List | ✓ |
| 3.1.2 | How are use cases prioritized (MoSCoW)? | Table (UC, Priority) | ✓ |
| 3.1.3 | Is there an overall use case diagram? | File/Link | Optional |
| 3.1.4 | Dependencies between use cases? | List | ✓ |
| 3.1.5 | Which use cases are mandatory vs. nice-to-have? | Table | ✓ |

### 3.2 Use Case Details

| # | Question | Type | Required |
|---|---------|------|----------|
| 3.2.1 | Use case name and a brief description? | Text | ✓ |
| 3.2.2 | Which actor(s) are involved in this use case? | List | ✓ |
| 3.2.3 | Preconditions before the use case starts? | List | ✓ |
| 3.2.4 | Postconditions after the use case completes? | List | ✓ |
| 3.2.5 | A detailed step-by-step description of the main flow (happy path)? | Text | ✓ |
| 3.2.6 | Alternative flows? | Text | Optional |
| 3.2.7 | Exception/error flows and how they are handled? | Text | ✓ |
| 3.2.8 | What trigger event initiates the use case? | Text | ✓ |

### 3.3 Business Rules for Use Cases

| # | Question | Type | Required |
|---|---------|------|----------|
| 3.3.1 | Which business rules apply to this use case? | List | ✓ |
| 3.3.2 | Validation rules for data input? | List | ✓ |
| 3.3.3 | Specific authorization rules? | List | ✓ |
| 3.3.4 | Calculation/formula business rules? | Text | Optional |
| 3.3.5 | State transition rules (if there are workflow states)? | List | Optional |

---

## 4. FUNCTIONAL REQUIREMENTS

### 4.1 Data Input/Output

| # | Question | Type | Required |
|---|---------|------|----------|
| 4.1.1 | Which data elements are entered into the system? | List | ✓ |
| 4.1.2 | Sources of input data (users, systems, files)? | List | ✓ |
| 4.1.3 | What data output/reports are generated? | List | ✓ |
| 4.1.4 | Expected output format (PDF, Excel, HTML, etc.)? | List | ✓ |
| 4.1.5 | Data frequency (real-time, batch, on-demand)? | Text | ✓ |
| 4.1.6 | Are there required vs. optional data fields? | List | ✓ |

### 4.2 Business Logic

| # | Question | Type | Required |
|---|---------|------|----------|
| 4.2.1 | A detailed description of the main processing business logic? | Text | ✓ |
| 4.2.2 | Are there any special calculations/computations? | Text | ✓ |
| 4.2.3 | Workflow processing logic (approval, routing)? | Text | ✓ |
| 4.2.4 | When does the notification/alerting logic trigger? | List | ✓ |
| 4.2.5 | Is there any historical data processing? | Boolean + Text | Optional |

### 4.3 Reports & Notifications

| # | Question | Type | Required |
|---|---------|------|----------|
| 4.3.1 | Which types of reports are needed? | List | ✓ |
| 4.3.2 | Report schedule (daily, weekly, monthly)? | Table | ✓ |
| 4.3.3 | Report recipients and distribution method? | Table | ✓ |
| 4.3.4 | Report retention period? | Duration | Optional |
| 4.3.5 | Required notification channels (email, SMS, push)? | List | ✓ |
| 4.3.6 | Alert thresholds for notifications? | List | Optional |

### 4.4 Workflow & Data Processing

| # | Question | Type | Required |
|---|---------|------|----------|
| 4.4.1 | Description of the end-to-end data workflow? | Diagram/Text | ✓ |
| 4.4.2 | What steps does the approval workflow have? | List | ✓ |
| 4.4.3 | At which points is data validated? | List | ✓ |
| 4.4.4 | Are there manual review/approval steps? | Boolean + List | ✓ |
| 4.4.5 | SLA for each workflow step? | Table | Optional |

---

## 5. NON-FUNCTIONAL REQUIREMENTS

### 5.1 Performance

| # | Question | Type | Required |
|---|---------|------|----------|
| 5.1.1 | How fast should the system respond? (e.g. simple operations under 2 seconds, complex operations under 5 seconds) | Table | ✓ |
|       | [TECHNICAL] Maximum response time for each operation type? | | |
| 5.1.2 | How many concurrent users can the system handle? (e.g. 100 concurrent users, 1000 concurrent users) | Number | ✓ |
|       | [TECHNICAL] Required throughput (transactions/second)? | | |
| 5.1.3 | Maximum expected concurrent users? | Number | ✓ |
| 5.1.4 | Peak load scenarios and expected response? | Text | ✓ |
| 5.1.5 | Are there batch processing windows? | Text | Optional |
| 5.1.6 | Industry performance benchmarks? | Text | Optional |

### 5.2 Security

| # | Question | Type | Required |
|---|---------|------|----------|
| 5.2.1 | How would you like login authentication to work? (e.g. password only, or an additional OTP code via phone) | List | ✓ |
|       | [TECHNICAL] Which authentication method (SSO, MFA, password)? | | |
| 5.2.2 | How would you like user permissions to work? (e.g. by department, by position, or should everyone be able to do everything) | Text | ✓ |
|       | [TECHNICAL] Authorization model (RBAC, ABAC, permissions)? | | |
| 5.2.3 | Does the data need special protection? (e.g. data encryption, view access restricted to authorized people only) | List | ✓ |
|       | [TECHNICAL] Data encryption requirements (at-rest, in-transit)? | | |
| 5.2.4 | Are there any special legal compliance requirements? (e.g. secure storage of customer data, credit card information security) | List | ✓ |
|       | [TECHNICAL] Are there any compliance requirements (GDPR, HIPAA, PCI-DSS)? | | |
| 5.2.5 | Does the system need to record who did what? (e.g. tracking an activity log, who created/edited/deleted data) | List | ✓ |
|       | [TECHNICAL] Detailed audit logging requirements? | | |
| 5.2.6 | After how long of inactivity should the system automatically log out? (e.g. 15 minutes, 30 minutes) | Number | ✓ |
|       | [TECHNICAL] Session timeout and security policies? | | |
| 5.2.7 | Password policy requirements? | List | ✓ |
| 5.2.8 | Is an IP allowlist/blocklist needed? | Boolean + List | Optional |

### 5.3 Availability & Reliability

| # | Question | Type | Required |
|---|---------|------|----------|
| 5.3.1 | Required uptime SLA (% per year)? | Percentage | ✓ |
| 5.3.2 | Planned maintenance windows? | Schedule | Optional |
| 5.3.3 | Disaster recovery requirements (RTO, RPO)? | Number | ✓ |
| 5.3.4 | Backup frequency and retention? | Table | ✓ |
| 5.3.5 | MTBF/MTTR targets? | Number | Optional |
| 5.3.6 | Error rate tolerance? | Percentage | Optional |
| 5.3.7 | Are there geographic redundancy requirements? | Boolean + Text | Optional |

### 5.4 Scalability

| # | Question | Type | Required |
|---|---------|------|----------|
| 5.4.1 | Concurrent user growth projection (1yr, 3yr, 5yr)? | Table | ✓ |
| 5.4.2 | Expected data volume growth rate? | Percentage | ✓ |
| 5.4.3 | Does the system need to scale as the number of users grows? (e.g. adding servers, upgrading hardware) | Text | Optional |
|       | [TECHNICAL] Horizontal vs. vertical scaling approach? | | |
| 5.4.4 | Are there geographic scaling requirements? | Boolean + Text | Optional |

### 5.5 Compatibility

| # | Question | Type | Required |
|---|---------|------|----------|
| 5.5.1 | Supported browsers and versions? | List | ✓ |
| 5.5.2 | Supported OS platforms (Windows, macOS, Linux)? | List | Optional |
| 5.5.3 | Mobile platform support (iOS, Android)? | List | Optional |
| 5.5.4 | Backward compatibility with legacy systems? | Text | Optional |
| 5.5.5 | API version compatibility policy? | Text | Optional |

---

## 6. USER INTERFACE (UI/UX)

### 6.1 UI Type & Platform

| # | Question | Type | Required |
|---|---------|------|----------|
| 6.1.1 | UI type: Web, Mobile (native/hybrid), Desktop? | List | ✓ |
| 6.1.2 | If mobile: iOS, Android, or both? | List | Optional |
| 6.1.3 | Are there Progressive Web App (PWA) requirements? | Boolean | Optional |
| 6.1.4 | Is offline functionality needed? | Boolean | Optional |

### 6.2 Screens & Navigation

| # | Question | Type | Required |
|---|---------|------|----------|
| 6.2.1 | List all the main screens/pages? | List | ✓ |
| 6.2.2 | Sitemap or navigation structure? | Diagram/Text | ✓ |
| 6.2.3 | What main user flows need to be supported? | Diagram | ✓ |
| 6.2.4 | Is there a breadcrumb/page hierarchy? | Boolean | Optional |
| 6.2.5 | Responsive layout requirements (breakpoints)? | List | ✓ |

### 6.3 Accessibility & Internationalization

| # | Question | Type | Required |
|---|---------|------|----------|
| 6.3.1 | Accessibility standards (WCAG 2.1 Level)? | Level (A, AA, AAA) | ✓ |
| 6.3.2 | Supported languages (localization)? | List | ✓ |
| 6.3.3 | Is RTL (right-to-left) language support needed? | Boolean | Optional |
| 6.3.4 | Date/time/number/currency format by locale? | List | Optional |
| 6.3.5 | Screen reader compatibility? | Boolean | Optional |

### 6.4 UI/UX Design Standards

| # | Question | Type | Required |
|---|---------|------|----------|
| 6.4.1 | Is there a brand style guide/corporate identity? | File/Link | Optional |
| 6.4.2 | Is a design system/component library available? | Text | Optional |
| 6.4.3 | Theme preferences (light/dark mode)? | List | Optional |
| 6.4.4 | Animation/motion guidelines? | Text | Optional |

---

## 7. DATA & STORAGE

### 7.1 Data Entities & Model

| # | Question | Type | Required |
|---|---------|------|----------|
| 7.1.1 | List all the main data entities? | List | ✓ |
| 7.1.2 | ER diagram or data model diagram? | File/Diagram | Optional |
| 7.1.3 | Attributes for each entity? | Table | ✓ |
| 7.1.4 | Do the data entities have relationships with each other? (e.g. a customer has many orders, an order has many products) | List | ✓ |
|       | [TECHNICAL] Relationships between entities? | | |
| 7.1.5 | Do you need fast searching/sorting of data? (e.g. search customers by name, sort orders by date) | List | Optional |
|       | [TECHNICAL] Required primary keys and indexes? | | |

### 7.2 Data Volume & Growth

| # | Question | Type | Required |
|---|---------|------|----------|
| 7.2.1 | How much data does the system need to store initially? (e.g. about 10,000 customers, 100,000 orders) | Number + Unit | ✓ |
|       | [TECHNICAL] Estimated initial data volume? | | |
| 7.2.2 | How much does the data grow each month/year? (e.g. grows 10% per year) | Percentage | ✓ |
|       | [TECHNICAL] Data growth rate per month/year? | | |
| 7.2.3 | Are there any periods when the data volume spikes? (e.g. year-end, peak season) | Number | Optional |
| 7.2.4 | Does old data need to be stored separately? (e.g. moving data older than 5 years to separate storage) | Text | Optional |
|       | [TECHNICAL] Archiving strategy for old data? | | |

### 7.3 Data Retention & Migration

| # | Question | Type | Required |
|---|---------|------|----------|
| 7.3.1 | How long does data need to be retained? (e.g. per legal regulations such as 5 years, 10 years) | Text | ✓ |
|       | [TECHNICAL] Data retention policy per regulations? | | |
| 7.3.2 | Is there any data that must be kept permanently or cannot be deleted? (e.g. financial records, contracts) | Boolean + Text | Optional |
|       | [TECHNICAL] Legal hold requirements for certain data? | | |
| 7.3.3 | Does data need to be migrated from an old system to the new one? | Boolean + Text | ✓ |
| 7.3.4 | Migration strategy and tools? | Text | Optional |
| 7.3.5 | Data cleansing requirements before migration? | List | Optional |

### 7.4 Backup & Recovery

| # | Question | Type | Required |
|---|---------|------|----------|
| 7.4.1 | How often should the system back up data? (e.g. daily, weekly, or in real time) | Schedule | ✓ |
|       | [TECHNICAL] Backup frequency (daily, weekly, real-time)? | | |
| 7.4.2 | Where should backups be stored? (e.g. on company servers, or in cloud storage) | Text | ✓ |
| 7.4.3 | How long should backups be retained? (e.g. 30 days, 90 days, 1 year) | Duration | ✓ |
| 7.4.4 | Is periodic recovery testing needed? | Schedule | Optional |
|       | [TECHNICAL] Recovery testing frequency? | | |
| 7.4.5 | Is restoring data to a specific point in time needed? (e.g. restore data to how it was at 9am yesterday) | Boolean | Optional |
|       | [TECHNICAL] Is point-in-time recovery required? | | |

---

## 8. BUSINESS RULES & CONSTRAINTS

### 8.1 Business Rules

| # | Question | Type | Required |
|---|---------|------|----------|
| 8.1.1 | List all the applicable business rules? | List | ✓ |
| 8.1.2 | Are the business rules documented? | File/Link | Optional |
| 8.1.3 | Exceptions to the business rules? | List | Optional |
| 8.1.4 | Do the business rules change over time? | Boolean + Frequency | Optional |

### 8.2 Legal & Regulatory

| # | Question | Type | Required |
|---|---------|------|----------|
| 8.2.1 | Which industry regulations apply? | List | ✓ |
| 8.2.2 | Data protection/privacy laws (GDPR, CCPA, etc.)? | List | ✓ |
| 8.2.3 | Audit requirements from regulatory bodies? | List | ✓ |
| 8.2.4 | Data sovereignty requirements (where data must be stored)? | Text | ✓ |
| 8.2.5 | Certification/compliance standards to achieve? | List | Optional |

### 8.3 Technical Constraints

| # | Question | Type | Required |
|---|---------|------|----------|
| 8.3.1 | Mandatory or restricted technology stack? | List | ✓ |
| 8.3.2 | Infrastructure constraints (cloud provider, on-prem)? | Text | ✓ |
| 8.3.3 | Integration constraints with existing systems? | List | Optional |
| 8.3.4 | Mandatory coding standards and frameworks? | List | Optional |

### 8.4 Resource Constraints

| # | Question | Type | Required |
|---|---------|------|----------|
| 8.4.1 | What is the hard cap on the budget? | Currency | ✓ |
| 8.4.2 | Timeline constraints that cannot change? | Date | ✓ |
| 8.4.3 | Team size/skills constraints? | Text | ✓ |
| 8.4.4 | Vendor/partner dependencies? | List | Optional |

---

## 9. INTEGRATION & EXTERNAL SYSTEMS

### 9.1 External Systems Integration

| # | Question | Type | Required |
|---|---------|------|----------|
| 9.1.1 | Which external systems need to be integrated? | List | ✓ |
| 9.1.2 | Purpose of each integration? | Table | ✓ |
| 9.1.3 | Data flow direction (inbound/outbound/both)? | Table | ✓ |
| 9.1.4 | Integration criticality/priority? | Table | ✓ |
| 9.1.5 | Can existing integration infrastructure be reused? | Text | Optional |

### 9.2 API Requirements

| # | Question | Type | Required |
|---|---------|------|----------|
| 9.2.1 | Does the system need to connect to other systems? (e.g. connecting to an accounting system, CRM, or existing systems) | List | ✓ |
|       | [TECHNICAL] Which REST API endpoints are needed? | | |
| 9.2.2 | Are SOAP/Web Services needed? | Boolean + List | Optional |
| 9.2.3 | GraphQL subscriptions/queries/mutations? | List | Optional |
| 9.2.4 | API rate limiting requirements? | Number | Optional |
| 9.2.5 | API versioning policy? | Text | Optional |
| 9.2.6 | API documentation format (OpenAPI/Swagger)? | Format | Optional |

### 9.3 File Formats & Protocols

| # | Question | Type | Required |
|---|---------|------|----------|
| 9.3.1 | In which formats does the system need to export/import data? (e.g. Excel, PDF, Word) | List | ✓ |
|       | [TECHNICAL] Which file formats need to be supported (CSV, XML, JSON, PDF)? | | |
| 9.3.2 | File size limits? | Number | ✓ |
| 9.3.3 | Protocol requirements (SFTP, HTTPS, etc.)? | List | ✓ |
| 9.3.4 | EDI standards, if any (B2B integrations)? | Text | Optional |

### 9.4 Third-party Services

| # | Question | Type | Required |
|---|---------|------|----------|
| 9.4.1 | Which third-party services need to be used (payment, SMS, email)? | List | ✓ |
| 9.4.2 | SLA commitments from third-party vendors? | Table | Optional |
| 9.4.3 | Cost structure of third-party services? | Table | Optional |
| 9.4.4 | Fallback mechanisms when a third-party fails? | Text | Optional |

---

## 10. MAINTENANCE & OPERATIONS

### 10.1 Deployment Environment

| # | Question | Type | Required |
|---|---------|------|----------|
| 10.1.1 | On which environment does the system run? (e.g. company servers, AWS/Azure/Google cloud) | List | ✓ |
|       | [TECHNICAL] Deployment environment (Dev, Staging, Production)? | | |
| 10.1.2 | Cloud provider preference (AWS, Azure, GCP)? | Text | ✓ |
| 10.1.3 | Is an on-premises requirement needed? | Boolean | ✓ |
| 10.1.4 | Is Docker/Kubernetes needed? (e.g. for easier deployment, scaling the system) | Boolean | Optional |
|       | [TECHNICAL] Is containerization (Docker, Kubernetes) required? | | |
| 10.1.5 | CI/CD pipeline requirements? | Text | Optional |

### 10.2 Maintenance & Support

| # | Question | Type | Required |
|---|---------|------|----------|
| 10.2.1 | Maintenance window schedule? | Schedule | ✓ |
| 10.2.2 | Support tier levels (L1, L2, L3)? | Table | ✓ |
| 10.2.3 | Bug fix SLA commitments? | Table | ✓ |
| 10.2.4 | Is service desk/helpdesk integration needed? | Boolean + List | Optional |
| 10.2.5 | Monitoring and alerting infrastructure? | List | ✓ |

### 10.3 Training & Documentation

| # | Question | Type | Required |
|---|---------|------|----------|
| 10.3.1 | User training requirements? | Text | ✓ |
| 10.3.2 | Training delivery format (online, in-person, documentation)? | List | Optional |
| 10.3.3 | Admin/technical documentation requirements? | List | ✓ |
| 10.3.4 | End-user help documentation/User manual? | Boolean | Optional |
| 10.3.5 | API documentation for developers? | Boolean | Optional |

### 10.4 Operations & Monitoring

| # | Question | Type | Required |
|---|---------|------|----------|
| 10.4.1 | Detailed logging requirements? | List | ✓ |
| 10.4.2 | Application performance monitoring (APM) tools? | List | Optional |
| 10.4.3 | Are health check endpoints needed? | List | Optional |
| 10.4.4 | Alerting thresholds and notification channels? | Table | ✓ |
| 10.4.5 | Are operational dashboards needed? | List | Optional |

---

## 11. ERROR HANDLING & EXCEPTIONS

This section gathers requirements on how the system handles errors, exceptions, and recovery mechanisms when incidents occur.

### 11.1 Error Handling Requirements

| # | Question | Type | Required |
|---|---------|------|----------|
| 11.1.1 | Which types of errors does the system need to handle (validation, system, network, data)? | List | ✓ |
| 11.1.2 | A consistent error code and messaging convention? | List | ✓ |
| 11.1.3 | Is an error code reference document needed? | Boolean | Optional |
| 11.1.4 | Standard error response format (JSON structure)? | Text | ✓ |
| 11.1.5 | Is error tracking/integration with a bug tracking system needed? | Boolean + Text | Optional |

### 11.2 Exception Scenarios

| # | Question | Type | Required |
|---|---------|------|----------|
| 11.2.1 | Timeout thresholds for each operation type? | Table | ✓ |
| 11.2.2 | Retry policy for transient failures (attempts, backoff)? | Text | ✓ |
| 11.2.3 | Is a circuit breaker pattern needed? | Boolean | Optional |
| 11.2.4 | Dead letter queue/failed message handling? | Text | Optional |
| 11.2.5 | Exception scenarios that need specific handling (payment failure, auth failure)? | List | ✓ |

### 11.3 User Feedback for Errors

| # | Question | Type | Required |
|---|---------|------|----------|
| 11.3.1 | User-friendly error messages to display to end users? | List | ✓ |
| 11.3.2 | Are technical error details displayed to users? | Boolean | ✓ |
| 11.3.3 | Error message localization requirements? | List | Optional |
| 11.3.4 | Is there guidance/suggestions when users encounter an error? | Boolean + Text | Optional |
| 11.3.5 | What notifications are sent when errors occur (email, SMS, dashboard)? | List | Optional |

### 11.4 Logging Requirements

| # | Question | Type | Required |
|---|---------|------|----------|
| 11.4.1 | Which log levels need to be used (DEBUG, INFO, WARN, ERROR, FATAL)? | List | ✓ |
| 11.4.2 | Log retention period per compliance? | Duration | ✓ |
| 11.4.3 | Is sensitive data masked in logs? | Boolean + List | ✓ |
| 11.4.4 | Log storage location (cloud, on-prem, SIEM)? | Text | Optional |
| 11.4.5 | Is a centralized logging system needed? | Boolean + Tool | Optional |
| 11.4.6 | Log aggregation and analysis requirements? | Text | Optional |

### 11.5 Fallback Mechanisms

| # | Question | Type | Required |
|---|---------|------|----------|
| 11.5.1 | Graceful degradation scenarios when services fail? | List | ✓ |
| 11.5.2 | Fallback data sources when the primary is unavailable? | List | Optional |
| 11.5.3 | Is cached data used when the database is unavailable? | Boolean | Optional |
| 11.5.4 | Is a manual override/override mode needed? | Boolean + Text | Optional |
| 11.5.5 | System-wide failover triggers and procedures? | Text | ✓ |

---

## 12. CONFIGURATION MANAGEMENT

This section gathers requirements on how system parameters are configured, customized, and managed.

### 12.1 Configuration Parameters

| # | Question | Type | Required |
|---|---------|------|----------|
| 12.1.1 | Which configuration parameters are needed for the system? | List | ✓ |
| 12.1.2 | Where is configuration stored (DB, file, env vars, config server)? | List | ✓ |
| 12.1.3 | Are dynamic configuration changes needed? | Boolean | ✓ |
| 12.1.4 | Configuration validation rules on change? | List | Optional |
| 12.1.5 | Configuration versioning/audit trail? | Boolean | Optional |

### 12.2 Environment Settings

| # | Question | Type | Required |
|---|---------|------|----------|
| 12.2.1 | Environment-specific settings (dev, staging, prod)? | List | ✓ |
| 12.2.2 | Environment promotion process (dev → staging → prod)? | Text | ✓ |
| 12.2.3 | Secrets management approach (vault, KMS, env vars)? | Text | ✓ |
| 12.2.4 | Required environment variables? | List | ✓ |
| 12.2.5 | Is configuration drift detection needed? | Boolean | Optional |

### 12.3 Feature Flags

| # | Question | Type | Required |
|---|---------|------|----------|
| 12.3.1 | Are feature flags/toggles needed? | Boolean | ✓ |
| 12.3.2 | Is a feature flag management system available? | Text | Optional |
| 12.3.3 | Feature targeting (user segment, percentage rollout)? | List | Optional |
| 12.3.4 | Feature flag lifecycle management (enable/disable/expiry)? | Text | Optional |
| 12.3.5 | A/B testing requirements with feature flags? | Boolean + List | Optional |

### 12.4 System Customization Options

| # | Question | Type | Required |
|---|---------|------|----------|
| 12.4.1 | Tenant-specific configuration (multi-tenant)? | Boolean + List | Optional |
| 12.4.2 | UI layout/theme customization per tenant/organization? | Boolean + List | Optional |
| 12.4.3 | Are business rules customizable by admins? | Boolean + Text | ✓ |
| 12.4.4 | Workflow customization options? | List | Optional |
| 12.4.5 | Are third-party integrations configurable? | Boolean + List | Optional |

---

## APPENDIX A: Requirements Prioritization Template

Using the MoSCoW methodology:

| Priority | Description | Target % |
|----------|-------------|----------|
| **Must Have** | Critical functionality, deal-breakers | 60% |
| **Should Have** | Important but not critical | 20% |
| **Could Have** | Desirable but not necessary | 15% |
| **Won't Have (this time)** | Explicitly excluded from scope | 5% |

---

## APPENDIX B: Glossary

| Term | Definition |
|------|------------|
| SRS | Software Requirements Specification |
| Stakeholder | Individual or group with interest in the project |
| Actor | External entity that interacts with the system |
| Use Case | Description of system behavior from user perspective |
| Business Rule | Policy or constraint that governs business logic |
| Non-Functional Requirement | Quality attributes (performance, security, etc.) |
| RTO | Recovery Time Objective |
| RPO | Recovery Point Objective |

---

**Document Info:**
- Version: 1.0
- Last Updated: 2026-03-23
</content>
