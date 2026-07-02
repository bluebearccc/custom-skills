---
name: sdd-writing
description: |
  Writing Software Design Document (SDD) following IEEE 1016-2009 standard.
  LINK to external PlantUML files instead of embedding directly.

  USE WHEN:
  - Creating SDD from scratch
  - Designing system architecture
  - Describing detailed component designs
version: "4.0.0"
allowed-tools: ["Read", "Write", "Edit", "Glob", "Grep"]
mode: false
---

# SDD Writing Skill

## Purpose
Create complete Software Design Document (SDD) following the IEEE 1016-2009 standard template. **LINK to external PlantUML files** instead of embedding directly.

## Nguyên tắc quan trọng

### KHÔNG embed PlantUML trực tiếp
- ❌ KHÔNG viết `@startuml...@enduml` trong body của tài liệu SDD
- ✅ SỬ DỤNG link/reference đến external PlantUML files
- ✅ Diagrams được tạo bởi các specialized agents spawn song song

---

## SDD Template Structure with External Links

### 0. Requirements Mapping

> Section này link SDD ngược lại SRS — đảm bảo mọi requirement đều có design.

#### 0.1 SRS Reference

| Item | Value |
|------|-------|
| SRS Document | [SRS_{ProjectName}_v{Version}.md](./SRS_{ProjectName}_v{Version}.md) |
| SRS Version | v{Version} |
| Requirements Summary | [requirements-summary.md](./requirements-summary.md) |
| Total Use Cases | N |
| Total Functional Requirements | N |

#### 0.2 Use Case → Implementation Mapping

> Mỗi UC trong SRS phải được map đến: Component, API endpoint, DB entities.

| UC ID | Use Case Name | Component | API Endpoint(s) | DB Entities |
|-------|--------------|-----------|-----------------|-------------|
| UC-01 | {UC Name} | {ComponentName} | POST /api/v1/{resource} | {table1}, {table2} |
| UC-02 | {UC Name} | {ComponentName} | GET /api/v1/{resource} | {table1} |
| UC-03 | {UC Name} | {ComponentName} | POST /api/v1/{resource}/{id}/action | {table1}, {table2} |

#### 0.3 Non-Functional Requirements → Design Decisions

| NFR ID | NFR Description | Design Decision |
|--------|-----------------|-----------------|
| NFR-P1 | Response time < 200ms (P95) | Redis cache cho read-heavy endpoints; DB indexes trên FKs và filter fields |
| NFR-S1 | JWT authentication | Access token 15min + refresh token 7 days; HTTPS only |
| NFR-A1 | 99.9% uptime | Multi-AZ deployment; Health check endpoints; Circuit breaker pattern |
| NFR-SC1 | 1000 concurrent users | Horizontal scaling; Connection pooling; Async processing |

---

### 1. Introduction

#### 1.1 Purpose
- Document purpose
- Design scope
- Relationship with SRS

#### 1.2 Definitions, Acronyms and Abbreviations

| Term | Definition |
|------|-----------|
| SRS | Software Requirement Specification |
| SDD | Software Design Document |

#### 1.3 References

| Reference | Description |
|-----------|-------------|
| SRS_{Project}_v{Version}.md | Software Requirement Specification |

---

### 2. System Architecture

#### 2.1 System Overview

**System Overview Diagram:** [system-overview.puml](./diagrams/system-overview.puml)

**Layered Architecture:** [layered-architecture.puml](./diagrams/layered-architecture.puml)

**Deployment Diagram:** [deployment.puml](./diagrams/deployment.puml)

**Integration Diagram:** [integration.puml](./diagrams/integration.puml)

**Component Interaction:** [component-interaction.puml](./diagrams/components/component-interaction.puml)

**Data Flow Diagram:** [dfd-level1.puml](./diagrams/dfd-level1.puml)

#### 2.2 Architectural Patterns

| Pattern | Description |
|---------|-------------|
| Layered Architecture | Separation into Presentation/Business/Data layers |
| REST API | Communication between frontend and backend |
| Repository Pattern | Data access abstraction |

---

### 3. Software Architecture Design

#### 3.1 Frontend Architecture

**Component Hierarchy:** [frontend-class-diagram.puml](./diagrams/frontend-class-diagram.puml)

#### 3.2 Backend Architecture

**Backend Classes:** [backend-class-diagram.puml](./diagrams/backend-class-diagram.puml)

#### 3.3 Data Layer Architecture

**Database Schema:** [schema.sql](./db/schema.sql)

**ER Diagram:** [entity-relationship.puml](./diagrams/entity-relationship.puml)

---

### 4. Detailed Component Design

#### 4.1 Common Design

##### 4.1.1 Global Error Handling Strategy

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

Global Exception Handler xử lý tập trung, log stack trace, trả về response chuẩn.

##### 4.1.2 Logging Strategy

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

##### 4.1.3 Caching Strategy

| Layer | Technology | TTL | Eviction |
|-------|-----------|-----|---------|
| Application | Redis | Per use case (xem bảng dưới) | LRU |
| HTTP | CDN / Nginx | Static assets: 1 year; API: no-store | — |

**Cache TTL per resource type:**

| Resource | TTL | Strategy | Invalidation |
|----------|-----|----------|--------------|
| User session | 15 min (access) / 7 days (refresh) | Cache-aside | On logout |
| Product catalog | 5 min | Cache-aside | On update |
| System config | 1 hour | Read-through | On update |
| Search results | 2 min | Cache-aside | TTL only |

**Cache Key Convention:**
```
{project}:{version}:{resource}:{id_or_query_hash}
vd: myapp:v1:product:123
    myapp:v1:products:list:page=1&limit=20&status=active
```

#### 4.2 Component: {ComponentName}

| Item | Description |
|------|-------------|
| Component ID | COMP-XXX |
| Component Name | |
| Purpose | |

**Backend Class Diagram:** [{component}-class-backend.puml](./diagrams/components/{component}/{component}-class-backend.puml)

**Frontend Class Diagram:** [{component}-class-frontend.puml](./diagrams/components/{component}/{component}-class-frontend.puml)

**Sequence Diagram:** [{component}-sequence.puml](./diagrams/components/{component}/{component}-sequence.puml)

**State Diagram:** [{component}-state.puml](./diagrams/components/{component}/{component}-state.puml)

---

### 5. Database Design

#### 5.1 Database Design

**ER Diagram:** [entity-relationship.puml](./diagrams/entity-relationship.puml)

**Database Schema:** [schema.sql](./db/schema.sql)

#### 5.2 Table Definitions

Per-component SQL files: `db/tables/{component}_tables.sql`

---

### 6. API Design

#### 6.1 REST API Endpoints

**OpenAPI Specification:** [openapi.yaml](./api/openapi.yaml)

**Error Codes Registry:** [error-codes.md](./api/error-codes.md)

| Endpoint | Method | Description | Auth |
|----------|--------|-------------|------|
| /api/v1/auth/login | POST | User login | No |
| /api/v1/auth/register | POST | User registration | No |

#### 6.2 Request/Response Formats

```json
{
  "success": true,
  "data": {},
  "meta": null
}
```

#### 6.3 Authentication & Authorization

- Bearer JWT token
- Access token: 15 minutes
- Refresh token: 7 days

#### 6.X Rate Limiting Design

| Tier | Limit | Window | Scope |
|------|-------|--------|-------|
| Unauthenticated | 100 req | 1 hour | Per IP |
| Authenticated (user) | 1000 req | 1 hour | Per user_id |
| Authenticated (admin) | 5000 req | 1 hour | Per user_id |
| Sensitive endpoints (/auth/login, /auth/register) | 10 req | 15 min | Per IP |

**Implementation:** Redis sliding window counter.
**Headers returned:** `X-RateLimit-Limit`, `X-RateLimit-Remaining`, `X-RateLimit-Reset`.
**Exceeded response:** HTTP 429 với `Retry-After` header.

---

### 7. Security Design

#### 7.1 Authentication

**Auth Flow:** [components/auth/auth-sequence.puml](./diagrams/components/auth/auth-sequence.puml)

- JWT Bearer tokens
- Refresh token rotation

#### 7.2 Authorization

**Permission Matrix:** [permissions.puml](./diagrams/permissions.puml)

#### 7.3 Data Protection

- HTTPS only
- Sensitive fields encrypted at rest
- PII handling per GDPR/local regulations

---

### 8. Deployment Architecture

**Deployment Diagram:** [deployment.puml](./diagrams/deployment.puml)

**Production Topology:** [deployment-prod.puml](./diagrams/deployment-prod.puml)

**Development Setup:** [deployment-dev.puml](./diagrams/deployment-dev.puml)

| Environment | Description |
|-------------|-------------|
| Development | Local Docker Compose |
| Staging | Pre-production testing |
| Production | Multi-AZ cloud deployment |

---

## Output Directory Structure

```
docs/{ProjectName}/
├── SDD_{ProjectName}_v{Version}.md    # Main SDD document
├── SRS_{ProjectName}_v{Version}.md     # Reference to SRS
├── index.md                            # Documentation index
├── diagrams/
│   ├── system-overview.puml
│   ├── layered-architecture.puml
│   ├── deployment.puml
│   ├── deployment-dev.puml
│   ├── deployment-prod.puml
│   ├── integration.puml
│   ├── entity-relationship.puml
│   ├── dfd-level1.puml
│   └── components/
│       ├── component-interaction.puml
│       ├── auth/
│       │   ├── auth-class-backend.puml
│       │   ├── auth-class-frontend.puml
│       │   ├── auth-sequence.puml
│       │   └── auth-state.puml
│       └── {component}/
│           └── ...
├── db/
│   ├── schema.sql
│   └── tables/
│       ├── auth_tables.sql
│       └── ...
└── api/
    ├── openapi.yaml
    └── error-codes.md
```

## Quality Checklist

- [ ] Section 0.2 UC → Implementation Mapping đã điền đủ cho mọi UC trong SRS
- [ ] Section 0.3 NFR → Design Decision đã address mọi NFR Must
- [ ] System Overview: [system-overview.puml](./diagrams/system-overview.puml) ✓
- [ ] Architecture: [layered-architecture.puml](./diagrams/layered-architecture.puml) ✓
- [ ] Component Interaction: [component-interaction.puml](./diagrams/components/component-interaction.puml) ✓
- [ ] Deployment: [deployment-prod.puml](./diagrams/deployment-prod.puml) ✓
- [ ] DFD Level 1: [dfd-level1.puml](./diagrams/dfd-level1.puml) ✓
- [ ] ER Diagram: [entity-relationship.puml](./diagrams/entity-relationship.puml) ✓
- [ ] API Spec: [openapi.yaml](./api/openapi.yaml) ✓
- [ ] Error Codes: [error-codes.md](./api/error-codes.md) ✓
- [ ] Each component has: class-backend, class-frontend, sequence, state diagrams
- [ ] Database schema: [schema.sql](./db/schema.sql) ✓
- [ ] Global error handling strategy defined (Section 4.1.1)
- [ ] Caching strategy defined (Section 4.1.3)
- [ ] Rate limiting defined (Section 6.X)

## Commands

Sử dụng command `/create-sdd` để trigger skill này.