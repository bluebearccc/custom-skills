# Sprint 2 — SRS & SDD Template Patches

> Apply these additions to `skills/srs-writing/SKILL.md` and `skills/sdd-writing/SKILL.md`.

---

## PATCH 1 — srs-writing/SKILL.md

### Add to Section 2 "User Requirements" — after 2.2 Use Cases

Insert a new sub-section **2.3 Acceptance Criteria per Use Case**:

```markdown
### 2.3 Acceptance Criteria per Use Case

> Each Use Case **MUST** have at least 3 Acceptance Criteria (AC) in Given-When-Then form.
> These ACs become test cases in `test-plan/`.

#### 2.3.X UC-XX — {Use Case Name}

**AC-XX-01 (Happy Path):**
```
Given: {precondition — initial system state}
When:  {actor performs an action}
Then:  {expected result}
And:   {additional side effects, if any}
```

**AC-XX-02 (Alternative Flow):**
```
Given: {a different precondition}
When:  {same or different action}
Then:  {different result}
```

**AC-XX-03 (Error / Boundary):**
```
Given: {invalid data or error condition}
When:  {actor performs an action}
Then:  {a specific error is shown / the system responds correctly}
```

> **Example UC-01 Login:**
>
> AC-01-01 (Happy Path):
> Given: User is not logged in, the account is active and the email is verified
> When:  User enters the correct email + password and submits
> Then:  The system returns an access_token and refresh_token
> And:   User is redirected to the Dashboard
>
> AC-01-02 (Wrong Password):
> Given: User is not logged in
> When:  User enters the correct email but the wrong password
> Then:  The system displays the error "Incorrect email or password"
> And:   It does not reveal which field is wrong
>
> AC-01-03 (Account Disabled):
> Given: The account is disabled (is_active = false)
> When:  User enters the correct email + password
> Then:  The system displays "This account has been disabled"
```

---

### Add to Section 5 "Requirement Appendix" — add sub-section 5.5

```markdown
### 5.5 Requirements Traceability Index (RTI)

> Summary linking table — see `traceability/RTM.md` for full details.

| FR ID | Feature | UC | Priority | Status |
|-------|---------|-----|----------|--------|
| FR-1.1 | {Feature name} | UC-01, UC-02 | Must | Draft |
| FR-1.2 | {Feature name} | UC-03 | Must | Draft |
| FR-2.1 | {Feature name} | UC-04 | Should | Draft |

*Status column: Draft → Reviewed → Approved → Implemented → Tested → Done*
```

---

## PATCH 2 — sdd-writing/SKILL.md

### Add Section 0 — Requirements Mapping (BEFORE Section 1 Introduction)

```markdown
## 0. Requirements Mapping

> This section links the SDD back to the SRS — ensuring every requirement has a design.

### 0.1 SRS Reference

| Item | Value |
|------|-------|
| SRS Document | [SRS_{ProjectName}_v{Version}.md](./SRS_{ProjectName}_v{Version}.md) |
| SRS Version | v{Version} |
| Requirements Summary | [requirements-summary.md](./requirements-summary.md) |
| Total Use Cases | N |
| Total Functional Requirements | N |

### 0.2 Use Case → Implementation Mapping

> Each UC in the SRS must be mapped to: a Component, API endpoint, and DB entities.

| UC ID | Use Case Name | Component | API Endpoint(s) | DB Entities |
|-------|--------------|-----------|-----------------|-------------|
| UC-01 | {UC Name} | {ComponentName} | POST /api/v1/{resource} | {table1}, {table2} |
| UC-02 | {UC Name} | {ComponentName} | GET /api/v1/{resource} | {table1} |
| UC-03 | {UC Name} | {ComponentName} | POST /api/v1/{resource}/{id}/action | {table1}, {table2} |

### 0.3 Non-Functional Requirements → Design Decisions

| NFR ID | NFR Description | Design Decision |
|--------|-----------------|-----------------|
| NFR-P1 | Response time < 200ms (P95) | Redis cache for read-heavy endpoints; DB indexes on FKs and filter fields |
| NFR-S1 | JWT authentication | Access token 15min + refresh token 7 days; HTTPS only |
| NFR-A1 | 99.9% uptime | Multi-AZ deployment; Health check endpoints; Circuit breaker pattern |
| NFR-SC1 | 1000 concurrent users | Horizontal scaling; Connection pooling (HikariCP); Async processing |
```

---

### Add Section 4.1.X — Error Handling Strategy (at the start of Section 4 Detailed Component Design)

```markdown
### 4.1 Common Design

#### 4.1.1 Global Error Handling Strategy

All components follow a unified error handling strategy:

**Error Hierarchy:**
```
BaseException
  ├── BusinessException (4xx — user/business logic errors)
  │     ├── ValidationException    (422)
  │     ├── NotFoundException      (404)
  │     ├── ConflictException      (409)
  │     └── ForbiddenException     (403)
  └── TechnicalException (5xx — system errors)
        ├── DatabaseException
        ├── ExternalServiceException
        └── InternalServerException
```

**Error Response Format** (consistent with `api/error-codes.md`):
```json
{
  "success": false,
  "error": {
    "code": "DOMAIN_SPECIFIC_CODE",
    "message": "Human-readable message",
    "fields": { "field_name": ["validation error"] }
  }
}
```

The **Global Exception Handler** handles errors centrally, logs the stack trace, and returns a standard response.

#### 4.1.2 Logging Strategy

| Level | When |
|-------|---------|
| ERROR | Unhandled exceptions, external service failures, DB errors |
| WARN  | Handled business exceptions, retry attempts, slow queries (>500ms) |
| INFO  | Request/response summary, significant business events |
| DEBUG | SQL queries, detailed flow (dev/staging only) |

**Log Format (JSON):**
```json
{
  "timestamp": "2026-05-26T10:00:00.000Z",
  "level": "ERROR",
  "service": "{ProjectName}",
  "component": "{ComponentName}",
  "traceId": "abc123",
  "userId": 42,
  "message": "Payment processing failed",
  "exception": "PaymentGatewayException",
  "duration_ms": 1250
}
```

#### 4.1.3 Caching Strategy

| Layer | Technology | TTL | Eviction |
|-------|-----------|-----|---------|
| Application | Redis | Per use case (see table below) | LRU |
| HTTP | CDN / Nginx | Static assets: 1 year; API: no-store | — |
| DB | Connection Pool | N/A | N/A |

**Cache TTL per resource type:**

| Resource | TTL | Strategy | Invalidation |
|----------|-----|----------|--------------|
| User session | 15 min (access) / 7 days (refresh) | Cache-aside | On logout |
| Product catalog | 5 min | Cache-aside | On update |
| System config | 1 hour | Read-through | On update |
| Search results | 2 min | Cache-aside | TTL only |
| User profile | 10 min | Cache-aside | On update |

**Cache Key Convention:**
```
{project}:{version}:{resource}:{id_or_query_hash}
e.g.: myapp:v1:product:123
      myapp:v1:products:list:page=1&limit=20&status=active
```
```

---

### Add to the end of Section 6 API Design — 6.X Rate Limiting Design

```markdown
### 6.X Rate Limiting Design

| Tier | Limit | Window | Scope |
|------|-------|--------|-------|
| Unauthenticated | 100 req | 1 hour | Per IP |
| Authenticated (user) | 1000 req | 1 hour | Per user_id |
| Authenticated (admin) | 5000 req | 1 hour | Per user_id |
| Sensitive endpoints (/auth/login, /auth/register) | 10 req | 15 min | Per IP |

**Implementation:** Redis sliding window counter.
**Headers returned:** `X-RateLimit-Limit`, `X-RateLimit-Remaining`, `X-RateLimit-Reset`.
**Exceeded response:** HTTP 429 with a `Retry-After` header.
```
