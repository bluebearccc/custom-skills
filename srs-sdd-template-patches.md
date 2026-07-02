# Sprint 2 — SRS & SDD Template Patches

> Áp dụng các bổ sung này vào `skills/srs-writing/SKILL.md` và `skills/sdd-writing/SKILL.md`.

---

## PATCH 1 — srs-writing/SKILL.md

### Thêm vào Section 2 "User Requirements" — sau 2.2 Use Cases

Chèn sub-section mới **2.3 Acceptance Criteria per Use Case**:

```markdown
### 2.3 Acceptance Criteria per Use Case

> Mỗi Use Case **PHẢI** có ít nhất 3 Acceptance Criteria (AC) ở dạng Given-When-Then.
> ACs này sẽ trở thành test cases trong `test-plan/`.

#### 2.3.X UC-XX — {Use Case Name}

**AC-XX-01 (Happy Path):**
```
Given: {precondition — trạng thái hệ thống ban đầu}
When:  {actor thực hiện hành động}
Then:  {kết quả mong đợi}
And:   {side effects bổ sung nếu có}
```

**AC-XX-02 (Alternative Flow):**
```
Given: {precondition khác}
When:  {cùng hoặc khác hành động}
Then:  {kết quả khác nhau}
```

**AC-XX-03 (Error / Boundary):**
```
Given: {dữ liệu không hợp lệ hoặc điều kiện lỗi}
When:  {actor thực hiện hành động}
Then:  {lỗi cụ thể được hiển thị / hệ thống phản hồi đúng}
```

> **Ví dụ UC-01 Login:**
>
> AC-01-01 (Happy Path):
> Given: User chưa đăng nhập, tài khoản active và email đã verify
> When:  User nhập email + password đúng và submit
> Then:  Hệ thống trả về access_token và refresh_token
> And:   User được chuyển đến Dashboard
>
> AC-01-02 (Wrong Password):
> Given: User chưa đăng nhập
> When:  User nhập đúng email nhưng sai password
> Then:  Hệ thống hiển thị lỗi "Email hoặc mật khẩu không đúng"
> And:   Không tiết lộ field nào sai
>
> AC-01-03 (Account Disabled):
> Given: Tài khoản bị disable (is_active = false)
> When:  User nhập đúng email + password
> Then:  Hệ thống hiển thị "Tài khoản đã bị vô hiệu hóa"
```

---

### Thêm vào Section 5 "Requirement Appendix" — thêm sub-section 5.5

```markdown
### 5.5 Requirements Traceability Index (RTI)

> Bảng tóm tắt linking — chi tiết đầy đủ xem `traceability/RTM.md`.

| FR ID | Feature | UC | Priority | Status |
|-------|---------|-----|----------|--------|
| FR-1.1 | {Feature name} | UC-01, UC-02 | Must | Draft |
| FR-1.2 | {Feature name} | UC-03 | Must | Draft |
| FR-2.1 | {Feature name} | UC-04 | Should | Draft |

*Cột Status: Draft → Reviewed → Approved → Implemented → Tested → Done*
```

---

## PATCH 2 — sdd-writing/SKILL.md

### Thêm Section 0 — Requirements Mapping (TRƯỚC Section 1 Introduction)

```markdown
## 0. Requirements Mapping

> Section này link SDD ngược lại SRS — đảm bảo mọi requirement đều có design.

### 0.1 SRS Reference

| Item | Value |
|------|-------|
| SRS Document | [SRS_{ProjectName}_v{Version}.md](./SRS_{ProjectName}_v{Version}.md) |
| SRS Version | v{Version} |
| Requirements Summary | [requirements-summary.md](./requirements-summary.md) |
| Total Use Cases | N |
| Total Functional Requirements | N |

### 0.2 Use Case → Implementation Mapping

> Mỗi UC trong SRS phải được map đến: Component, API endpoint, DB entities.

| UC ID | Use Case Name | Component | API Endpoint(s) | DB Entities |
|-------|--------------|-----------|-----------------|-------------|
| UC-01 | {UC Name} | {ComponentName} | POST /api/v1/{resource} | {table1}, {table2} |
| UC-02 | {UC Name} | {ComponentName} | GET /api/v1/{resource} | {table1} |
| UC-03 | {UC Name} | {ComponentName} | POST /api/v1/{resource}/{id}/action | {table1}, {table2} |

### 0.3 Non-Functional Requirements → Design Decisions

| NFR ID | NFR Description | Design Decision |
|--------|-----------------|-----------------|
| NFR-P1 | Response time < 200ms (P95) | Redis cache cho read-heavy endpoints; DB indexes trên FKs và filter fields |
| NFR-S1 | JWT authentication | Access token 15min + refresh token 7 days; HTTPS only |
| NFR-A1 | 99.9% uptime | Multi-AZ deployment; Health check endpoints; Circuit breaker pattern |
| NFR-SC1 | 1000 concurrent users | Horizontal scaling; Connection pooling (HikariCP); Async processing |
```

---

### Thêm Section 4.1.X — Error Handling Strategy (vào đầu Section 4 Detailed Component Design)

```markdown
### 4.1 Common Design

#### 4.1.1 Global Error Handling Strategy

Tất cả components đều follow error handling strategy thống nhất:

**Error Hierarchy:**
```
BaseException
  ├── BusinessException (4xx — lỗi do user/business logic)
  │     ├── ValidationException    (422)
  │     ├── NotFoundException      (404)
  │     ├── ConflictException      (409)
  │     └── ForbiddenException     (403)
  └── TechnicalException (5xx — lỗi hệ thống)
        ├── DatabaseException
        ├── ExternalServiceException
        └── InternalServerException
```

**Error Response Format** (nhất quán với `api/error-codes.md`):
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

**Global Exception Handler** xử lý tập trung, log stack trace, trả về response chuẩn.

#### 4.1.2 Logging Strategy

| Level | Khi nào |
|-------|---------|
| ERROR | Unhandled exceptions, external service failures, DB errors |
| WARN  | Handled business exceptions, retry attempts, slow queries (>500ms) |
| INFO  | Request/response summary, significant business events |
| DEBUG | SQL queries, detailed flow (chỉ dev/staging) |

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
| Application | Redis | Per use case (xem bảng dưới) | LRU |
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
vd: myapp:v1:product:123
    myapp:v1:products:list:page=1&limit=20&status=active
```
```

---

### Thêm vào cuối Section 6 API Design — 6.X Rate Limiting Design

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
**Exceeded response:** HTTP 429 với `Retry-After` header.
```
