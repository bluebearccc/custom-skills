# Requirements Summary — Finance Tracker

> **Status:** DRAFT — awaiting user CONFIRM
> **Date:** 2026-07-03
> **Project identifier:** `Finance Tracker`
> **Actual scope:** Internal football/soccer team management website (2 teams)

---

## 1. Project Context

**Finance Tracker** is an internal web application for managing a small football/soccer
club made up of **2 teams**. Despite the identifier "Finance Tracker", the system is a
**team management platform** whose modules cover the team **fund (quỹ)**, **members
(thành viên)**, **matches (trận đấu)**, **individual goal/assist statistics**, and
**tournaments (giải đấu)**.

It is a **brand-new** system for internal use only (not public-facing). The core problem
it solves: today the club tracks dues, match results, player stats, and standings manually
(spreadsheets/chat), which is error-prone and hard to keep consistent. Finance Tracker
centralizes all of this in one place with role-based access.

- **Platform:** Responsive **web + mobile** (mobile-responsive is a priority; members check on the go).
- **Language / Currency:** **Vietnamese** UI, **VND** for all fund amounts.
- **Scale:** Small — **< 100 users** total, low concurrency.

## 2. Stakeholders

| Stakeholder | Role |
|-------------|------|
| Club organizer / System Admin | Sponsor, product owner, approval authority, primary operator |
| Team members | End users |

## 3. Actors

| Actor | Description | Capabilities |
|-------|-------------|--------------|
| **Admin** | System manager (club organizer). | Full control: manage member accounts, fund (contributions/expenses), matches & player stats, lineups/attendance, tournaments & standings. Sends/triggers notifications. |
| **Member** | A football team member (player). | Log in; view own profile & "my stats"; view fund summary & personal dues status; view matches, lineups, standings, leaderboards; confirm match attendance; receive match reminders. |

> Account creation is **Admin-only** — members do not self-register. Members simply log in
> with credentials provisioned by the Admin.

## 4. Feature List (MoSCoW)

### Must Have (MVP)
- **M1 — Authentication & session** (Admin-provisioned accounts, role-based access: Admin vs Member).
- **M2 — Member management** (CRUD member profiles: basic info, team assignment (Team A / Team B), playing position + jersey number).
- **M3 — Fund management** (record member contributions; record expenses; running balance + full transaction history; per-member payment status — who has/hasn't paid).
- **M4 — Match management** (schedule matches: date, opponent, venue; record results: score, W/L/D).
- **M5 — Player match stats** (attribute goals & assists to individual players per match).
- **M6 — Lineups & attendance** (record who played / starters vs subs / attendance per match).
- **M7 — Tournament management** (Admin creates/manages tournaments with participating teams).
- **M8 — Standings / league table** (auto-calculated points table W/D/L per tournament).
- **M9 — Leaderboards** (top scorers & top assists — overall and per tournament).
- **M10 — Dashboards** (fund summary dashboard; personal "my stats" view; career stats summary per member — goals, assists, matches played, attendance rate).
- **M11 — Match reminder notifications via email** (upcoming match alerts + attendance confirmation request).

### Should Have
- **S1 — Per-member dues reminder** (highlight/notify outstanding contributions — visible in-app even if email reminders focus on matches).
- **S2 — Attendance confirmation flow** (members confirm availability for upcoming matches).

### Could Have
- **C1 — Data export** (fund reports / stats to Excel or PDF; print-friendly views) — no strong requirement.
- **C2 — Knockout/bracket tournament format** (in addition to league format).
- **C3 — Cards & extra match events** (yellow/red cards, MVP, injuries).

### Won't Have (this release)
- Public/guest access or self-registration.
- Multi-currency / internationalization beyond Vietnamese + VND.
- Bank/payment-gateway integration (contributions recorded manually by Admin).
- SMS notifications.
- Strict regulatory compliance regime (GDPR/PCI-DSS) — standard security practices only.

## 5. Use Cases (Must-Have detail)

### UC-01 — Log in
- **Actor:** Admin, Member
- **Trigger:** User opens the app and submits credentials.
- **Preconditions:** Account exists (created by Admin).
- **Main flow:**
  1. User enters email/username + password.
  2. System validates credentials.
  3. System establishes an authenticated session and loads the role-appropriate dashboard.
- **Alternative:** 3a. "Remember me" keeps session longer.
- **Exception:** 2a. Invalid credentials → error message; after N failures, temporary lockout.
- **Postcondition:** User is authenticated with Admin or Member privileges.

### UC-02 — Manage member accounts (Admin)
- **Actor:** Admin
- **Trigger:** Admin opens Member Management.
- **Preconditions:** Admin authenticated.
- **Main flow:**
  1. Admin creates a member (name, photo, phone/email, join date, team A/B, position, jersey number) and initial credentials.
  2. System validates uniqueness (e.g., email, jersey per team) and saves.
  3. Admin can edit or deactivate a member.
- **Alternative:** 3a. Reassign member to the other team.
- **Exception:** 2a. Duplicate email/jersey → validation error.
- **Postcondition:** Member record and login exist; member can log in.

### UC-03 — Record fund contribution (Admin)
- **Actor:** Admin
- **Trigger:** A member pays dues; Admin records it.
- **Preconditions:** Member exists.
- **Main flow:**
  1. Admin selects member, enters amount (VND), date, period, note.
  2. System records the contribution and updates fund balance + member payment status.
- **Exception:** 2a. Invalid amount/date → validation error.
- **Postcondition:** Fund balance increases; member marked paid for that period.

### UC-04 — Record fund expense (Admin)
- **Actor:** Admin
- **Trigger:** Club spends money (field rental, equipment, referee…).
- **Main flow:**
  1. Admin enters expense (amount VND, date, category, description).
  2. System records it and decreases fund balance.
- **Exception:** 2a. Expense exceeds balance → warning (allowed but flagged).
- **Postcondition:** Fund balance decreases; expense in history.

### UC-05 — View fund balance, history & payment status
- **Actor:** Admin (full), Member (own status + summary)
- **Main flow:**
  1. User opens Fund. System shows current balance, contributions vs expenses.
  2. Admin sees full transaction history and per-member paid/unpaid list; Member sees own dues status.
- **Postcondition:** Fund state is visible per role.

### UC-06 — Schedule & manage match (Admin)
- **Actor:** Admin
- **Main flow:**
  1. Admin creates a match: date/time, opponent, venue, (optional) tournament.
  2. System saves it and lists it as upcoming.
  3. Admin can edit/cancel before it is played.
- **Postcondition:** Match appears on schedule; eligible for reminders.

### UC-07 — Record match result & player stats (Admin)
- **Actor:** Admin
- **Trigger:** A match has been played.
- **Preconditions:** Match exists.
- **Main flow:**
  1. Admin enters final score and result (W/L/D).
  2. Admin records lineup/attendance (starters, subs, who played).
  3. Admin attributes each goal and assist to individual players.
  4. System updates player career stats and, if the match belongs to a tournament, updates standings and per-tournament leaderboards.
- **Exception:** 3a. Goals attributed exceed team score → validation warning.
- **Postcondition:** Result, per-player stats, attendance, standings, and leaderboards updated.

### UC-08 — Manage tournament & standings (Admin)
- **Actor:** Admin
- **Main flow:**
  1. Admin creates a tournament (name, dates, participating teams).
  2. As tournament matches are recorded, system auto-computes the league table (points W/D/L) and per-tournament top scorers/assists.
- **Postcondition:** Standings and tournament leaderboards reflect recorded results.

### UC-09 — View leaderboards & standings
- **Actor:** Admin, Member
- **Main flow:**
  1. User opens Leaderboards. System shows top scorers and top assists (overall + per tournament) and current standings.
- **Postcondition:** Rankings displayed.

### UC-10 — View dashboards & "My Stats"
- **Actor:** Admin (fund + performance dashboards), Member (my stats + fund summary)
- **Main flow:**
  1. On login, user sees role-appropriate dashboard.
  2. Member views own goals, assists, matches played, attendance rate, and dues status.
  3. Admin views fund summary (balance, contributions vs expenses, outstanding dues).
- **Postcondition:** Personalized overview displayed.

### UC-11 — Send match reminder (System / Admin)
- **Actor:** System (scheduled) / Admin (manual trigger); recipients: Members
- **Trigger:** A match is upcoming (e.g., within X hours) or Admin triggers manually.
- **Preconditions:** Match scheduled; members have email addresses.
- **Main flow:**
  1. System composes a reminder for the upcoming match.
  2. System sends email to relevant members with an attendance-confirmation request.
- **Exception:** 2a. Email delivery failure → logged; retried/flagged to Admin.
- **Postcondition:** Members notified; attendance responses collected (Should-have).

## 6. Data Model (entities & key fields)

- **User** — id, name, photo, email, phone, passwordHash, role (Admin/Member), status, joinDate.
- **Team** — id, name (Team A / Team B).
- **MemberProfile** — userId, teamId, position (GK/DEF/MID/FWD), jerseyNumber, careerStats (derived).
- **FundTransaction** — id, type (contribution/expense), memberId (nullable for expense), amountVND, date, category, period, note, createdBy.
- **PaymentStatus** — memberId, period, status (paid/unpaid), amount, linked FundTransaction.
- **Match** — id, date/time, opponent, venue, tournamentId (nullable), homeTeamId, scoreFor, scoreAgainst, result (W/L/D), status (scheduled/played/cancelled).
- **MatchAttendance / Lineup** — matchId, memberId, role (starter/sub), played (bool).
- **PlayerMatchStat** — matchId, memberId, goals, assists.
- **Tournament** — id, name, startDate, endDate, participatingTeams.
- **Standing** — tournamentId, teamId, played, win, draw, loss, points (derived).
- **Notification** — id, type (match reminder), recipientId, matchId, channel (email), status, sentAt.

## 7. Non-Functional Requirements

- **Performance:** Snappy for < 100 users, low concurrency; typical page/API response < 1–2s.
- **Availability:** Best-effort for internal use; single VPS; routine backups of PostgreSQL.
- **Usability:** **Vietnamese** UI; **mobile-responsive** first (works well on phones).
- **Localization:** Amounts in **VND**; dates in local format.
- **Security:** Password hashing (bcrypt/argon2), role-based access control (Admin vs Member), session management; no public registration. Standard practices — no strict compliance regime.
- **Maintainability:** Conventional layered architecture.

## 8. Technical Constraints (from stakeholder)

- **Frontend:** **Next.js**
- **Backend:** **Express.js**
- **Database:** **PostgreSQL**
- **Hosting:** Self-hosted on a **VPS**
- **Email:** An email service/SMTP for match reminders (provider TBD — assumption).

## 9. Assumptions & Open Items

- Contributions/expenses are entered **manually** by Admin (no bank/payment integration).
- Exactly **2 teams** in this release; structure is fixed (transfers possible via edit).
- Email provider/SMTP details to be decided during design.
- Data export (Excel/PDF) treated as **Could-have**; not required for MVP.
- No fixed delivery deadline; scope prioritized as MVP (Must-Have) first.
- Attendance-confirmation responses from members are a **Should-have** enhancement.

---

## 10. Governance

- **Success criteria:** Admin can manage members, fund, matches, stats, and tournaments end-to-end; members can view their stats, standings, and dues; match reminders are delivered.
- **Acceptance:** Club organizer (Admin) sign-off after UAT on the Must-Have use cases.

---

> **Please review.** Reply **CONFIRM** to lock this summary and proceed to
> **Phase 2 (PlantUML use-case diagrams)** and **Phase 3 (IEEE SRS writing)**.
> Or tell me what to change (add/remove features, adjust priorities, fix flows).
