# Software Requirements Specification (SRS)
## Finance Tracker — Internal Football Team Management System

| | |
|---|---|
| **Document version** | 1.0.0 |
| **Date** | 2026-07-03 |
| **Status** | Baselined (post requirements confirmation) |
| **Standard** | IEEE 830 / ISO/IEC/IEEE 29148 |
| **Prepared by** | SRS Agent |
| **Approval authority** | Club Organizer (System Admin) |

---

## Table of Contents
1. [Introduction](#1-introduction)
2. [Overall Description](#2-overall-description)
3. [Specific Requirements — Functional](#3-specific-requirements--functional)
4. [Use Case Specifications](#4-use-case-specifications)
5. [Data Requirements](#5-data-requirements)
6. [Non-Functional Requirements](#6-non-functional-requirements)
7. [External Interface Requirements](#7-external-interface-requirements)
8. [Constraints, Assumptions & Dependencies](#8-constraints-assumptions--dependencies)
9. [Acceptance Criteria & Governance](#9-acceptance-criteria--governance)
10. [Appendices](#10-appendices)

---

## 1. Introduction

### 1.1 Purpose
This document specifies the software requirements for **Finance Tracker**, an internal web
application for managing a small football/soccer club composed of **two teams**. It is
intended for the project sponsor (club organizer), developers, testers, and any future
maintainers. It defines what the system must do (functional requirements), the qualities it
must exhibit (non-functional requirements), and the constraints under which it operates.

### 1.2 Scope
Finance Tracker centralizes the club's operational data that is currently tracked manually.
Its modules are:

- **Fund / Treasury (quỹ):** member contributions, expenses, running balance and history, per-member payment status.
- **Members (thành viên):** profiles, team assignment (Team A / Team B), playing position, career stats.
- **Matches (trận đấu):** schedule and results, goals/assists per player, lineups and attendance.
- **Tournaments (giải đấu):** tournament setup, auto-computed standings (league table), per-tournament player leaderboards.
- **Reporting:** leaderboards (top scorers/assists), fund summary dashboard, personal "my stats".
- **Notifications:** upcoming-match email reminders.

The system is **internal only** (no public/guest access). Accounts are provisioned by the
Admin; members do not self-register. The user interface is in **Vietnamese** and all monetary
values are in **Vietnamese Dong (VND)**.

**Out of scope for v1.0.0:** public access/self-registration, multi-currency/i18n beyond
Vietnamese+VND, bank/payment-gateway integration, SMS notifications, and strict regulatory
compliance regimes (e.g., GDPR/PCI-DSS).

### 1.3 Definitions, Acronyms & Abbreviations
| Term | Meaning |
|------|---------|
| Admin | System manager (club organizer) with full privileges |
| Member | A team player; end user with read-mostly access |
| Quỹ | Team fund / treasury |
| Dues / Contribution | Money a member pays into the fund |
| Standings | League table ranking teams by points (W/D/L) |
| Leaderboard | Ranked list of players by goals or assists |
| VND | Vietnamese Dong (currency) |
| NFR | Non-Functional Requirement |
| RBAC | Role-Based Access Control |

### 1.4 References
- ISO/IEC/IEEE 29148:2018 — Requirements engineering
- IEEE 830-1998 — Recommended Practice for SRS
- Requirements Summary — [`requirements-summary.md`](requirements-summary.md)

### 1.5 Overview
Section 2 gives the overall product context. Section 3 lists functional requirements (FRs).
Section 4 details each use case with flows and links to PlantUML diagrams. Sections 5–8 cover
data, non-functional requirements, interfaces, and constraints. Section 9 defines acceptance.

---

## 2. Overall Description

### 2.1 Product Perspective
Finance Tracker is a **new, self-contained** web system with a browser-based responsive
frontend, an application/API backend, and a relational database. It integrates with an
external **email service** for match reminders. High-level system context and actor/use-case
relationships are shown in the system use-case diagram:

📊 **Diagram:** [`diagrams/usecase-overview.puml`](diagrams/usecase-overview.puml)

### 2.2 Product Functions (summary)
- Authenticate users and enforce Admin/Member roles.
- Manage member accounts and profiles (2-team assignment).
- Manage the team fund: contributions, expenses, balance/history, payment status.
- Manage matches: scheduling, results, per-player goals/assists, lineups/attendance.
- Manage tournaments and auto-compute standings and per-tournament leaderboards.
- Present dashboards, leaderboards, and personal statistics.
- Send upcoming-match email reminders.

### 2.3 User Classes and Characteristics
| User class | Description | Technical skill | Frequency |
|------------|-------------|-----------------|-----------|
| **Admin** | Club organizer; manages all data. Single or few. | Moderate | Regular (data entry after each match / dues cycle) |
| **Member** | Player; views own stats, dues, standings; confirms attendance. Up to ~100. | Basic; often on mobile | Occasional |

### 2.4 Operating Environment
- **Client:** Modern desktop and mobile web browsers (responsive UI).
- **Server:** Self-hosted on a **VPS**.
- **Frontend:** Next.js. **Backend:** Express.js. **Database:** PostgreSQL.

### 2.5 Design and Implementation Constraints
See [Section 8](#8-constraints-assumptions--dependencies). Key: fixed stack (Next.js /
Express.js / PostgreSQL / VPS), Vietnamese UI, VND currency, Admin-only account creation.

### 2.6 Assumptions and Dependencies
See [Section 8.2](#82-assumptions). Notably: fund entries are manual; email provider TBD.

---

## 3. Specific Requirements — Functional

Priorities use **MoSCoW**: **M**ust / **S**hould / **C**ould / **W**on't.

### 3.1 Authentication & Access Control
| ID | Requirement | Priority |
|----|-------------|----------|
| FR-01 | The system shall authenticate users via username/email + password. | Must |
| FR-02 | The system shall enforce RBAC with two roles: **Admin** and **Member**. | Must |
| FR-03 | The system shall restrict all create/update/delete of fund, matches, tournaments, and member accounts to **Admin**. | Must |
| FR-04 | The system shall lock an account temporarily after a configurable number of consecutive failed logins. | Should |
| FR-05 | The system shall **not** allow public self-registration; only Admin creates accounts. | Must |

### 3.2 Member Management
| ID | Requirement | Priority |
|----|-------------|----------|
| FR-06 | Admin shall create member accounts with name, photo, contact (phone/email), join date, and initial credentials. | Must |
| FR-07 | Admin shall assign each member to exactly one team (Team A or Team B). | Must |
| FR-08 | Admin shall record playing position (GK/DEF/MID/FWD) and jersey number. | Must |
| FR-09 | The system shall validate uniqueness of email and jersey number (per team). | Must |
| FR-10 | Admin shall edit, reassign team, or deactivate a member. | Must |
| FR-11 | The system shall display a member's career stats summary (goals, assists, matches played, attendance rate). | Must |

### 3.3 Fund / Treasury
| ID | Requirement | Priority |
|----|-------------|----------|
| FR-12 | Admin shall record member contributions (amount in VND, date, period, note). | Must |
| FR-13 | Admin shall record expenses (amount in VND, date, category, description). | Must |
| FR-14 | The system shall maintain a running fund balance and full transaction history. | Must |
| FR-15 | The system shall track per-member payment status (paid/unpaid) per period. | Must |
| FR-16 | The system shall warn (but allow) when an expense exceeds the current balance. | Should |
| FR-17 | Members shall view the fund summary and their own dues status. | Must |
| FR-18 | The system shall highlight/notify members with outstanding dues. | Should |

### 3.4 Match Management & Player Stats
| ID | Requirement | Priority |
|----|-------------|----------|
| FR-19 | Admin shall schedule matches (date/time, opponent, venue, optional tournament). | Must |
| FR-20 | Admin shall edit or cancel a scheduled match. | Must |
| FR-21 | Admin shall record match results (score, W/L/D). | Must |
| FR-22 | Admin shall attribute goals and assists to individual players per match. | Must |
| FR-23 | The system shall validate that attributed goals do not exceed the team's score. | Should |
| FR-24 | Admin shall record lineups/attendance (starter/sub, who played). | Must |
| FR-25 | Recording a result shall update player career stats automatically. | Must |

### 3.5 Tournaments & Standings
| ID | Requirement | Priority |
|----|-------------|----------|
| FR-26 | Admin shall create/manage tournaments (name, dates, participating teams). | Must |
| FR-27 | The system shall auto-compute a league table (points from W/D/L) per tournament. | Must |
| FR-28 | The system shall compute per-tournament top scorers and top assists. | Must |
| FR-29 | The system may support knockout/bracket stages. | Could |

### 3.6 Reporting & Dashboards
| ID | Requirement | Priority |
|----|-------------|----------|
| FR-30 | The system shall display overall and per-tournament leaderboards (top scorers/assists). | Must |
| FR-31 | The system shall display a fund summary dashboard (balance, contributions vs expenses, outstanding dues). | Must |
| FR-32 | Each member shall have a personal "My Stats" view (goals, assists, matches played, attendance, dues). | Must |
| FR-33 | The system may support exporting fund reports and stats (Excel/PDF) and print-friendly views. | Could |

### 3.7 Notifications
| ID | Requirement | Priority |
|----|-------------|----------|
| FR-34 | The system shall send email reminders for upcoming matches to relevant members. | Must |
| FR-35 | Match reminders shall request attendance confirmation. | Should |
| FR-36 | The system shall log notification delivery and retry/flag failures. | Should |
| FR-37 | The system shall allow the Admin to trigger a match reminder manually. | Should |

---

## 4. Use Case Specifications

Each use case links to its PlantUML activity diagram. The system-wide use case diagram is
[`diagrams/usecase-overview.puml`](diagrams/usecase-overview.puml).

### UC-01 — Log in
- **Actors:** Admin, Member · **Related FRs:** FR-01, FR-02, FR-04
- **Trigger:** User submits credentials.
- **Preconditions:** Account exists (created by Admin).
- **Main flow:** (1) Enter credentials → (2) system validates → (3) session created, role dashboard loaded.
- **Alternative:** 3a. "Remember me" extends the session.
- **Exceptions:** 2a. Invalid credentials → error; repeated failures → temporary lockout.
- **Postcondition:** User authenticated with correct role.
- 📊 [`diagrams/uc01-login.puml`](diagrams/uc01-login.puml)

### UC-02 — Manage Member Accounts
- **Actor:** Admin · **Related FRs:** FR-06…FR-11
- **Trigger:** Admin opens Member Management.
- **Preconditions:** Admin authenticated.
- **Main flow:** (1) Enter member info + team + position + jersey + credentials → (2) validate uniqueness → (3) save; edit/reassign/deactivate as needed.
- **Exceptions:** 2a. Duplicate email/jersey → validation error.
- **Postcondition:** Member record and login exist.
- 📊 [`diagrams/uc02-manage-members.puml`](diagrams/uc02-manage-members.puml)

### UC-03 — Record Fund Contribution
- **Actor:** Admin · **Related FRs:** FR-12, FR-14, FR-15
- **Trigger:** A member pays dues.
- **Preconditions:** Member exists.
- **Main flow:** (1) Select member → (2) enter amount/date/period/note → (3) save; balance increases; member marked paid.
- **Exceptions:** 2a. Invalid amount/date → validation error.
- **Postcondition:** Balance increased; payment status updated.
- 📊 [`diagrams/uc03-record-contribution.puml`](diagrams/uc03-record-contribution.puml)

### UC-04 — Record Fund Expense
- **Actor:** Admin · **Related FRs:** FR-13, FR-14, FR-16
- **Trigger:** Club spends money.
- **Main flow:** (1) Enter amount/date/category/description → (2) validate → (3) save; balance decreases.
- **Exceptions:** 2a. Expense > balance → warning (allowed, flagged).
- **Postcondition:** Balance decreased; expense in history.
- 📊 [`diagrams/uc04-record-expense.puml`](diagrams/uc04-record-expense.puml)

### UC-05 — View Fund Balance, History & Payment Status
- **Actors:** Admin (full), Member (own) · **Related FRs:** FR-14, FR-15, FR-17
- **Main flow:** (1) Open Fund → (2) system loads balance; Admin sees full history + paid/unpaid list, Member sees own dues.
- **Postcondition:** Fund state visible per role.
- 📊 [`diagrams/uc05-view-fund.puml`](diagrams/uc05-view-fund.puml)

### UC-06 — Schedule & Manage Match
- **Actor:** Admin · **Related FRs:** FR-19, FR-20
- **Main flow:** (1) Create match (date/time, opponent, venue, optional tournament) → (2) save (scheduled); edit/cancel before played.
- **Postcondition:** Match on schedule, eligible for reminders.
- 📊 [`diagrams/uc06-schedule-match.puml`](diagrams/uc06-schedule-match.puml)

### UC-07 — Record Match Result & Player Stats
- **Actor:** Admin · **Related FRs:** FR-21…FR-25, FR-27, FR-28
- **Trigger:** A match has been played.
- **Preconditions:** Match exists.
- **Main flow:** (1) Enter score + result → (2) record lineup/attendance → (3) attribute goals/assists → (4) system updates career stats and, if in a tournament, standings + leaderboards.
- **Exceptions:** 3a. Attributed goals exceed score → validation warning.
- **Postcondition:** Result, stats, attendance, standings, leaderboards updated.
- 📊 [`diagrams/uc07-record-match-stats.puml`](diagrams/uc07-record-match-stats.puml)

### UC-08 — Manage Tournament & Standings
- **Actor:** Admin · **Related FRs:** FR-26, FR-27, FR-28
- **Main flow:** (1) Create tournament (name, dates, teams) → (2) as results are recorded, auto-compute league table and per-tournament leaderboards.
- **Postcondition:** Standings/leaderboards reflect recorded results.
- 📊 [`diagrams/uc08-manage-tournament.puml`](diagrams/uc08-manage-tournament.puml)

### UC-09 — View Leaderboards & Standings
- **Actors:** Admin, Member · **Related FRs:** FR-30
- **Main flow:** (1) Open Leaderboards → (2) select scope (overall/tournament) → (3) system shows top scorers, top assists, and standings.
- **Postcondition:** Rankings displayed.
- 📊 [`diagrams/uc09-view-leaderboards.puml`](diagrams/uc09-view-leaderboards.puml)

### UC-10 — View Dashboards & My Stats
- **Actors:** Admin (fund/performance), Member (my stats) · **Related FRs:** FR-31, FR-32
- **Main flow:** (1) On login, role dashboard loads → (2) Member sees own goals/assists/matches/attendance + dues; Admin sees fund summary + performance.
- **Postcondition:** Personalized overview displayed.
- 📊 [`diagrams/uc10-dashboards.puml`](diagrams/uc10-dashboards.puml)

### UC-11 — Send Match Reminder
- **Actors:** System (scheduled) / Admin (manual) → Members · **Related FRs:** FR-34…FR-37
- **Trigger:** Upcoming-match window reached or Admin triggers.
- **Preconditions:** Match scheduled; members have emails.
- **Main flow:** (1) Compose reminder → (2) send email with attendance request.
- **Exceptions:** 2a. Delivery failure → log, retry/flag.
- **Postcondition:** Members notified; (Should) attendance collected.
- 📊 [`diagrams/uc11-match-reminder.puml`](diagrams/uc11-match-reminder.puml)

---

## 5. Data Requirements

The logical data model is defined in the entity-relationship diagram:

📊 **Diagram:** [`diagrams/er-diagram.puml`](diagrams/er-diagram.puml)

**Core entities:** `User`, `Team`, `MemberProfile`, `FundTransaction`, `PaymentStatus`,
`Match`, `Lineup`, `PlayerMatchStat`, `Tournament`, `TournamentTeam`, `Standing`,
`Notification`.

**Key rules:**
- A `User` with role Member has exactly one `MemberProfile` linked to one `Team`.
- `FundTransaction.type` ∈ {contribution, expense}; `member_id` is null for expenses.
- `PaymentStatus` is keyed by (member, period).
- `PlayerMatchStat` and `Lineup` are keyed by (match, member).
- `Standing` is derived from recorded `Match` results within a `Tournament`.
- All monetary fields are stored in **VND** (integer, no fractional currency).

---

## 6. Non-Functional Requirements

| ID | Category | Requirement |
|----|----------|-------------|
| NFR-01 | Performance | Typical page/API responses within **1–2 s** under expected load (< 100 users, low concurrency). |
| NFR-02 | Scalability | Support up to ~100 registered users and multiple seasons of match/fund history without redesign. |
| NFR-03 | Usability | UI in **Vietnamese**; intuitive for non-technical members. |
| NFR-04 | Mobile | **Mobile-responsive** layout is a priority; all member-facing views usable on phones. |
| NFR-05 | Localization | Currency in **VND**; local date formatting. |
| NFR-06 | Security | Passwords hashed (bcrypt/argon2); RBAC (Admin/Member); secure session management; no public registration. |
| NFR-07 | Availability | Best-effort for internal use on a single VPS; scheduled PostgreSQL backups. |
| NFR-08 | Reliability | Fund balance and stats derivations must be consistent with recorded transactions/results. |
| NFR-09 | Maintainability | Conventional layered architecture; clear separation of frontend/backend/data. |
| NFR-10 | Auditability | Fund transactions record `created_by` and timestamp for traceability. |

---

## 7. External Interface Requirements

### 7.1 User Interfaces
- Responsive web UI (Next.js) in Vietnamese; role-based navigation (Admin vs Member).
- Key screens: Login, Dashboard, Members, Fund, Matches, Tournaments, Leaderboards, My Stats.

### 7.2 Software Interfaces
- **Database:** PostgreSQL (server-side, via backend only).
- **Email service / SMTP:** outbound email for match reminders (provider TBD).

### 7.3 Communication Interfaces
- HTTPS between browser and backend API.
- SMTP/API to the email provider for notifications.

---

## 8. Constraints, Assumptions & Dependencies

### 8.1 Constraints
- **Technology stack (mandated):** Next.js (frontend), Express.js (backend), PostgreSQL (database).
- **Hosting:** self-managed **VPS**.
- **Language/Currency:** Vietnamese UI, VND only.
- **Accounts:** Admin-provisioned only; no self-registration or guest access.
- **Teams:** exactly two teams in v1.0.0.

### 8.2 Assumptions
- Fund contributions/expenses are entered **manually** by the Admin (no bank/payment integration).
- Members have valid email addresses for reminders.
- An email provider/SMTP will be selected during design.
- Team structure is fixed at two teams; member transfers handled via profile edit.

### 8.3 Dependencies
- Availability of a VPS and an email/SMTP service.

---

## 9. Acceptance Criteria & Governance

### 9.1 Success Criteria
- Admin can manage members, fund, matches, player stats, and tournaments end-to-end.
- Members can view their stats, standings, leaderboards, and dues status.
- Match reminder emails are delivered to relevant members.

### 9.2 Acceptance
- Club Organizer (Admin) sign-off following UAT on all **Must-Have** use cases (UC-01…UC-11).

### 9.3 Prioritization Roadmap
- **Release 1 (MVP):** all Must-Have FRs.
- **Release 1.x:** Should-have (dues reminders, attendance confirmation flow).
- **Backlog:** Could-have (export, knockout brackets, cards/MVP events).

---

## 10. Appendices

### 10.1 Requirements Traceability (FR → UC)
| FR range | Use Case(s) |
|----------|-------------|
| FR-01, FR-02, FR-04 | UC-01 |
| FR-05 | UC-01, UC-02 |
| FR-06…FR-11 | UC-02 |
| FR-12, FR-14, FR-15 | UC-03 |
| FR-13, FR-16 | UC-04 |
| FR-17, FR-18 | UC-05, UC-10 |
| FR-19, FR-20 | UC-06 |
| FR-21…FR-25 | UC-07 |
| FR-26…FR-28 | UC-07, UC-08 |
| FR-29 | UC-08 (Could) |
| FR-30 | UC-09 |
| FR-31, FR-32 | UC-10 |
| FR-33 | UC-05, UC-09 (Could) |
| FR-34…FR-37 | UC-11 |

### 10.2 Diagram Index
| Diagram | File |
|---------|------|
| System use case | [`diagrams/usecase-overview.puml`](diagrams/usecase-overview.puml) |
| Entity relationship | [`diagrams/er-diagram.puml`](diagrams/er-diagram.puml) |
| UC-01 Login | [`diagrams/uc01-login.puml`](diagrams/uc01-login.puml) |
| UC-02 Manage members | [`diagrams/uc02-manage-members.puml`](diagrams/uc02-manage-members.puml) |
| UC-03 Record contribution | [`diagrams/uc03-record-contribution.puml`](diagrams/uc03-record-contribution.puml) |
| UC-04 Record expense | [`diagrams/uc04-record-expense.puml`](diagrams/uc04-record-expense.puml) |
| UC-05 View fund | [`diagrams/uc05-view-fund.puml`](diagrams/uc05-view-fund.puml) |
| UC-06 Schedule match | [`diagrams/uc06-schedule-match.puml`](diagrams/uc06-schedule-match.puml) |
| UC-07 Record match stats | [`diagrams/uc07-record-match-stats.puml`](diagrams/uc07-record-match-stats.puml) |
| UC-08 Manage tournament | [`diagrams/uc08-manage-tournament.puml`](diagrams/uc08-manage-tournament.puml) |
| UC-09 View leaderboards | [`diagrams/uc09-view-leaderboards.puml`](diagrams/uc09-view-leaderboards.puml) |
| UC-10 Dashboards | [`diagrams/uc10-dashboards.puml`](diagrams/uc10-dashboards.puml) |
| UC-11 Match reminder | [`diagrams/uc11-match-reminder.puml`](diagrams/uc11-match-reminder.puml) |

### 10.3 Revision History
| Version | Date | Author | Description |
|---------|------|--------|-------------|
| 1.0.0 | 2026-07-03 | SRS Agent | Initial baseline from confirmed requirements. |
