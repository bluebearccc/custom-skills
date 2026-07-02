---
name: test-design
description: |
  Tạo Test Plan, Test Cases có cấu trúc từ Acceptance Criteria (AC) trong SRS.
  
  SỬ DỤNG KHI:
  - test-agent được spawn để tạo test artifacts
  - Cần TEST-PLAN.md theo chuẩn IEEE 829
  - Cần test cases theo Given-When-Then / Step-Expected format
  - Cần Postman collection từ openapi.yaml

  OUTPUT BẮT BUỘC:
  1. test-plan/TEST-PLAN.md
  2. test-plan/TEST-SUMMARY.md
  3. test-plan/test-cases/TC-{UC-ID}-{name}.md  (1 file per UC)
  4. test-plan/api-tests/postman-collection.json
version: "1.0.0"
allowed-tools: ["Read", "Write", "Edit", "Glob"]
mode: false
---

# Test Design Skill v1.0.0

## Mục đích
Tạo test artifacts đầy đủ cho dự án: test plan chiến lược, test cases theo AC, và API test collection — theo chuẩn IEEE 829 và best practices.

---

## Output 1 — TEST-PLAN.md

```markdown
# Test Plan
## {ProjectName} v{Version}

> IEEE 829 Compliant  
> Author: AgentCode Framework — test-agent  
> Date: {date}

---

## 1. Introduction

### 1.1 Purpose
Tài liệu này định nghĩa chiến lược, scope, approach, và criteria cho việc test {ProjectName}.

### 1.2 Scope

**In Scope:**
| Module | Test Types |
|--------|-----------|
| AuthModule | Unit, Integration, API, Security |
| ProductModule | Unit, Integration, API |
| OrderModule | Unit, Integration, API, E2E |
| {Module} | ... |

**Out of Scope:**
- Third-party services (Payment Gateway, SMS provider) — mocked
- Infrastructure testing
- Browser compatibility testing (unless specified in NFRs)

### 1.3 References

| Document | Version | Location |
|----------|---------|----------|
| SRS | v{version} | SRS_{ProjectName}_v{version}.md |
| SDD | v{version} | SDD_{ProjectName}_v{version}.md |
| RTM | v1.0 | traceability/RTM.md |
| API Spec | v1.0 | api/openapi.yaml |

---

## 2. Test Strategy

### 2.1 Test Pyramid

```
        ╔═══════╗
        ║  E2E  ║  10% — Critical user journeys
       ╔╝═══════╚╗
       ║ Integ.  ║  20% — Component interactions, DB, API
      ╔╝═════════╚╗
      ║   Unit    ║  70% — Business logic, validators, utils
      ╚═══════════╝
```

**Unit Tests:** Service layer, validators, calculators, utils
**Integration Tests:** Controller → Service → Repository → DB; External service calls
**API Tests:** All endpoints in openapi.yaml — happy path + error paths
**E2E Tests:** Top 3-5 critical user journeys (login → browse → order → pay)

### 2.2 Testing Types

| Type | Tool | Coverage Target | Environment |
|------|------|----------------|-------------|
| Unit | Jest / JUnit / pytest | ≥ 80% line coverage | Local / CI |
| Integration | Testcontainers / Docker | Key flows | CI |
| API | Postman / Newman | All endpoints | CI / Staging |
| E2E | Playwright | Critical journeys | Staging |
| Performance | k6 | NFR-P targets | Staging |
| Security | OWASP ZAP + Semgrep | OWASP Top 10 | Staging |

### 2.3 Test Environment

| Environment | Purpose | Data |
|-------------|---------|------|
| Local | Development testing | Mocked / Docker |
| CI (GitHub Actions) | Automated on every PR | Test DB |
| Staging | Pre-release validation | Anonymized prod data |
| Production | Smoke tests post-deploy | Real data (read-only) |

### 2.4 Entry & Exit Criteria

**Entry Criteria (khi nào bắt đầu test):**
- [ ] Code review approved
- [ ] Unit tests pass locally
- [ ] Deployed to test environment
- [ ] Test cases reviewed and approved

**Exit Criteria (khi nào dừng test và sign-off):**
- [ ] All P1/P2 test cases executed
- [ ] 0 open Critical/High bugs
- [ ] Unit test coverage ≥ 80%
- [ ] All API endpoints tested
- [ ] Performance NFRs met on staging

---

## 3. Test Schedule

| Phase | Activity | Duration | Owner |
|-------|----------|----------|-------|
| Sprint N | Unit test writing (parallel with dev) | Per sprint | Dev |
| Sprint N | Integration test writing | Per sprint | Dev |
| Sprint N+1 | API test execution | 3 days | QA |
| Before UAT | E2E test execution | 2 days | QA |
| UAT | User Acceptance Testing | 1 week | Business |
| Pre-prod | Performance testing | 2 days | DevOps/QA |
| Pre-prod | Security testing | 2 days | Security |

---

## 4. Risk Register

| Risk | Probability | Impact | Mitigation |
|------|-------------|--------|------------|
| Test environment unstable | Medium | High | Docker Compose for consistent env |
| Insufficient test data | Medium | Medium | Factory-pattern test data generators |
| Third-party service downtime | Low | High | Mock all external dependencies |
| Regression in critical paths | Medium | High | E2E smoke suite on every deploy |

---

## 5. Defect Management

### Severity Levels
| Level | Description | SLA to Fix |
|-------|-------------|-----------|
| P1 Critical | System crash / data loss / security breach | 4 hours |
| P2 High | Core feature broken / major data error | 1 business day |
| P3 Medium | Feature impaired, workaround exists | 1 sprint |
| P4 Low | Minor UI issue / cosmetic | Backlog |

### Defect Lifecycle
`New → Assigned → In Progress → Fixed → Verification → Closed / Reopened`

---

## 6. Test Deliverables

| Deliverable | Location | Owner |
|-------------|----------|-------|
| Test Plan | test-plan/TEST-PLAN.md | QA Lead |
| Test Cases | test-plan/test-cases/ | QA |
| API Tests | test-plan/api-tests/ | QA |
| Test Summary Report | test-plan/TEST-SUMMARY.md | QA Lead |
| Bug Reports | Issue tracker (Jira/GitHub) | QA |
```

---

## Output 2 — Test Case per UC

Tạo 1 file `test-plan/test-cases/TC-{UC-ID}-{uc-name-kebab}.md` cho mỗi UC.

```markdown
# Test Cases — UC-{ID}: {Use Case Name}

> UC Description: {mô tả ngắn}  
> Primary Actor: {Actor}  
> Priority: {Must/Should/Could}  
> Last Updated: {date}

---

## TC-{ID}-01 — Happy Path (Normal Flow)

| Field | Value |
|-------|-------|
| Test Case ID | TC-{ID}-01 |
| Test Case Name | {Use Case} — Happy Path |
| Type | Functional — Positive |
| Priority | P1 Critical |
| AC Reference | AC-{ID}-01 |

**Preconditions:**
- {Precondition 1 — trạng thái hệ thống ban đầu}
- {Precondition 2}

**Test Steps:**

| Step | Action | Expected Result |
|------|--------|-----------------|
| 1 | {Actor} {thực hiện hành động cụ thể} | {Kết quả hiển thị/xảy ra} |
| 2 | {Nhập dữ liệu hợp lệ: field=value} | {Field được accept} |
| 3 | {Submit/Click/Navigate} | {Response: 201 Created / redirect / toast} |

**Expected Outcome:**
- ✅ {Kết quả chính mong đợi}
- ✅ {Side effect 1: DB record created}
- ✅ {Side effect 2: Email sent}

**Test Data:**
```json
{
  "field1": "valid_value",
  "field2": "valid_value"
}
```

**API Request (if applicable):**
```
POST /api/v1/{endpoint}
Authorization: Bearer {token}
Content-Type: application/json

{request body}
```

**API Expected Response:**
```json
HTTP 201 Created
{
  "success": true,
  "data": { ... }
}
```

---

## TC-{ID}-02 — Alternative Flow

| Field | Value |
|-------|-------|
| Test Case ID | TC-{ID}-02 |
| Test Case Name | {Use Case} — {Alternative Scenario} |
| Type | Functional — Alternative |
| Priority | P2 High |
| AC Reference | AC-{ID}-02 |

**Preconditions:**
- {Khác với happy path}

**Test Steps:**

| Step | Action | Expected Result |
|------|--------|-----------------|
| 1 | {Hành động dẫn đến alternative flow} | {Trigger condition} |
| 2 | {Hành động tiếp theo} | {Alternative result} |

**Expected Outcome:**
- ✅ {Kết quả alternative}

---

## TC-{ID}-03 — Negative / Error Path

| Field | Value |
|-------|-------|
| Test Case ID | TC-{ID}-03 |
| Test Case Name | {Use Case} — {Error Scenario} |
| Type | Functional — Negative |
| Priority | P2 High |
| AC Reference | AC-{ID}-03 |

**Preconditions:**
- {Dữ liệu không hợp lệ hoặc điều kiện lỗi}

**Test Steps:**

| Step | Action | Expected Result |
|------|--------|-----------------|
| 1 | {Nhập dữ liệu không hợp lệ} | {Validation error shown} |

**Expected Outcome:**
- ✅ Error message: "{exact error message}"
- ✅ HTTP status: {4xx}
- ✅ No data persisted

**API Expected Response:**
```json
HTTP 422 Unprocessable Entity
{
  "success": false,
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "...",
    "fields": { "field_name": ["error message"] }
  }
}
```

---

## TC-{ID}-04 — Boundary / Edge Case

| Field | Value |
|-------|-------|
| Test Case ID | TC-{ID}-04 |
| Test Case Name | {Use Case} — Boundary Condition |
| Type | Boundary |
| Priority | P3 Medium |
| AC Reference | AC-{ID}-01 |

**Test Data Boundaries:**
| Field | Min | Max | Empty | Special Chars |
|-------|-----|-----|-------|---------------|
| email | — | 255 chars | ❌ Error | @ required |
| password | 8 chars | 72 chars | ❌ Error | Special OK |
| name | 2 chars | 100 chars | ❌ Error | Unicode OK |

---

## TC-{ID}-05 — Security Test (if applicable)

| Field | Value |
|-------|-------|
| Test Case ID | TC-{ID}-05 |
| Test Case Name | {Use Case} — Unauthorized Access |
| Type | Security |
| Priority | P1 Critical |

**Test Steps:**

| Step | Action | Expected Result |
|------|--------|-----------------|
| 1 | Gọi API endpoint không có Authorization header | HTTP 401 Unauthorized |
| 2 | Gọi API endpoint với token hết hạn | HTTP 401 Token Expired |
| 3 | Gọi API endpoint với role thấp hơn required | HTTP 403 Forbidden |

---

## Test Coverage Summary for UC-{ID}

| AC ID | AC Description | Test Case | Status |
|-------|---------------|-----------|--------|
| AC-{ID}-01 | Happy path | TC-{ID}-01 | ⬜ Not Run |
| AC-{ID}-02 | Alternative | TC-{ID}-02 | ⬜ Not Run |
| AC-{ID}-03 | Error path | TC-{ID}-03 | ⬜ Not Run |
| — | Boundary | TC-{ID}-04 | ⬜ Not Run |
| — | Security | TC-{ID}-05 | ⬜ Not Run |

**Status values:** ⬜ Not Run | 🔵 In Progress | ✅ Pass | ❌ Fail | ⏭️ Skipped
```

---

## Output 3 — Postman Collection Template

```json
{
  "info": {
    "name": "{ProjectName} API Tests",
    "description": "Generated by AgentCode Framework — test-agent",
    "schema": "https://schema.getpostman.com/json/collection/v2.1.0/collection.json"
  },
  "variable": [
    { "key": "base_url", "value": "http://localhost:8080/api/v1" },
    { "key": "access_token", "value": "" },
    { "key": "refresh_token", "value": "" }
  ],
  "item": [
    {
      "name": "Auth",
      "item": [
        {
          "name": "POST /auth/register — Happy Path",
          "event": [
            {
              "listen": "test",
              "script": {
                "exec": [
                  "pm.test('Status 201', () => pm.response.to.have.status(201));",
                  "pm.test('success = true', () => {",
                  "  const body = pm.response.json();",
                  "  pm.expect(body.success).to.be.true;",
                  "  pm.expect(body.data).to.have.property('id');",
                  "  pm.expect(body.data).to.have.property('email');",
                  "});"
                ]
              }
            }
          ],
          "request": {
            "method": "POST",
            "url": "{{base_url}}/auth/register",
            "header": [{ "key": "Content-Type", "value": "application/json" }],
            "body": {
              "mode": "raw",
              "raw": "{ \"email\": \"test+{{$timestamp}}@example.com\", \"password\": \"P@ssword123\", \"full_name\": \"Test User\" }"
            }
          }
        },
        {
          "name": "POST /auth/login — Happy Path",
          "event": [
            {
              "listen": "test",
              "script": {
                "exec": [
                  "pm.test('Status 200', () => pm.response.to.have.status(200));",
                  "pm.test('Returns tokens', () => {",
                  "  const body = pm.response.json();",
                  "  pm.expect(body.success).to.be.true;",
                  "  pm.expect(body.data.access_token).to.be.a('string');",
                  "  pm.collectionVariables.set('access_token', body.data.access_token);",
                  "  pm.collectionVariables.set('refresh_token', body.data.refresh_token);",
                  "});"
                ]
              }
            }
          ],
          "request": {
            "method": "POST",
            "url": "{{base_url}}/auth/login",
            "header": [{ "key": "Content-Type", "value": "application/json" }],
            "body": {
              "mode": "raw",
              "raw": "{ \"email\": \"user@example.com\", \"password\": \"P@ssword123\" }"
            }
          }
        },
        {
          "name": "POST /auth/login — Wrong Password (Negative)",
          "event": [
            {
              "listen": "test",
              "script": {
                "exec": [
                  "pm.test('Status 401', () => pm.response.to.have.status(401));",
                  "pm.test('Error response', () => {",
                  "  const body = pm.response.json();",
                  "  pm.expect(body.success).to.be.false;",
                  "  pm.expect(body.error.code).to.equal('INVALID_CREDENTIALS');",
                  "});"
                ]
              }
            }
          ],
          "request": {
            "method": "POST",
            "url": "{{base_url}}/auth/login",
            "header": [{ "key": "Content-Type", "value": "application/json" }],
            "body": {
              "mode": "raw",
              "raw": "{ \"email\": \"user@example.com\", \"password\": \"WrongPassword\" }"
            }
          }
        }
      ]
    },
    {
      "name": "Auth — Security",
      "item": [
        {
          "name": "GET /protected — No Token (401)",
          "event": [
            {
              "listen": "test",
              "script": {
                "exec": [
                  "pm.test('Status 401 without token', () => pm.response.to.have.status(401));"
                ]
              }
            }
          ],
          "request": {
            "method": "GET",
            "url": "{{base_url}}/users/me"
          }
        }
      ]
    },
    {
      "name": "{Resource}",
      "item": [
        {
          "name": "GET /{resources} — List (Paginated)",
          "event": [
            {
              "listen": "test",
              "script": {
                "exec": [
                  "pm.test('Status 200', () => pm.response.to.have.status(200));",
                  "pm.test('Paginated response', () => {",
                  "  const body = pm.response.json();",
                  "  pm.expect(body.success).to.be.true;",
                  "  pm.expect(body.data).to.be.an('array');",
                  "  pm.expect(body.meta).to.have.property('total');",
                  "  pm.expect(body.meta).to.have.property('total_pages');",
                  "});"
                ]
              }
            }
          ],
          "request": {
            "method": "GET",
            "url": {
              "raw": "{{base_url}}/{resources}?page=1&limit=20",
              "query": [
                { "key": "page", "value": "1" },
                { "key": "limit", "value": "20" }
              ]
            }
          }
        }
      ]
    }
  ]
}
```

---

## Output 4 — TEST-SUMMARY.md Template

```markdown
# Test Summary — {ProjectName}

> Generated: {date}

## Statistics

| Type | Total | Pass | Fail | Skip |
|------|-------|------|------|------|
| Functional (Happy Path) | N | — | — | — |
| Functional (Alternative) | N | — | — | — |
| Functional (Negative) | N | — | — | — |
| Boundary | N | — | — | — |
| Security | N | — | — | — |
| API Tests | N | — | — | — |
| **Total** | **N** | — | — | — |

## UC Coverage

| UC | Name | TCs | AC Coverage |
|----|------|-----|-------------|
| UC-01 | {Name} | 5 | 3/3 ACs ✅ |
| UC-02 | {Name} | 4 | 3/3 ACs ✅ |
| UC-N | {Name} | 0 | 0/3 ACs ❌ |

## Gaps to Fill Before UAT
- UC-N: No test cases created
```

---

## Hướng dẫn viết AC → TC

| AC Pattern | Loại TC | TC Pattern |
|-----------|---------|-----------|
| Happy path với valid input | Positive | Test với đúng data → assert 2xx + response body |
| Alternative flow | Alternative | Test trigger condition → assert khác kết quả |
| Invalid input | Negative | Test với bad data → assert 4xx + error.code |
| Missing required field | Boundary | Test field = empty → assert 422 + fields.{field} |
| Duplicate resource | Conflict | Test create duplicate → assert 409 + CONFLICT |
| Unauthorized access | Security | Test without token → assert 401 |
| Insufficient permission | Security | Test với wrong role → assert 403 |
