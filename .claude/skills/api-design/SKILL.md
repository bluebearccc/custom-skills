---
name: api-design
description: |
  Design a complete REST API for the SDD: endpoint inventory, OpenAPI 3.0 spec,
  error code registry, authentication scheme, rate limiting, and versioning.

  USE WHEN:
  - api-agent is spawned to design the overall API
  - Need to create openapi.yaml conforming to OpenAPI 3.0
  - Need to define error codes, response envelope, auth scheme

  REQUIRED OUTPUT (2 files):
  1. api/openapi.yaml         — Full OpenAPI 3.0 spec
  2. api/error-codes.md       — Error code registry
version: "3.0.0"
allowed-tools: ["Read", "Write", "Edit", "Glob", "Grep"]
mode: false
---

# API Design Skill v3.0.0

## Purpose
Guide `api-agent` to design the entire REST API for the project following the OpenAPI 3.0 standard, consistent in conventions, authentication, error handling, and versioning.

---

## Step 1 — Read the SRS and determine API scope

Before writing the spec, read:
- `requirements-summary.md` → Actors, Use Cases, NFRs (Security, Performance)
- `SRS_{Project}_v*.md` → Functional requirements, Data entities
- `db/schema.sql` (if available) → Derive response schemas from table definitions

**Mapping UC → Endpoint:**
```
Each Use Case → 1 or more API endpoints
Standard pattern:
  UC: Manage {Resource}
    GET    /api/v1/{resources}           list (paginated)
    POST   /api/v1/{resources}           create
    GET    /api/v1/{resources}/{id}      get by id
    PUT    /api/v1/{resources}/{id}      update (full)
    PATCH  /api/v1/{resources}/{id}      update (partial)
    DELETE /api/v1/{resources}/{id}      soft delete

  UC: {Parent}–{Child} relationship
    GET    /api/v1/{parents}/{id}/{children}
    POST   /api/v1/{parents}/{id}/{children}

  UC: Non-CRUD action
    POST   /api/v1/{resources}/{id}/activate
    POST   /api/v1/{resources}/{id}/cancel
    POST   /api/v1/{resources}/{id}/approve
```

---

## Step 2 — API Design Conventions (establish BEFORE writing the spec)

### URL & Versioning
```
Base URL  : /api/v{major}
Versioning: URI-based (/v1, /v2)
Resource  : plural nouns, kebab-case   /api/v1/order-items
Sub-res   : /api/v1/orders/{id}/items
Action    : POST verb-noun             /api/v1/payments/{id}/refund
```

### HTTP Methods & Status Codes
| Method | Semantics | Success | Created | No Content |
|--------|-----------|---------|---------|------------|
| GET    | Read      | 200     | —       | 204        |
| POST   | Create    | —       | 201     | —          |
| PUT    | Replace   | 200     | —       | 204        |
| PATCH  | Partial   | 200     | —       | 204        |
| DELETE | Remove    | —       | —       | 204        |

### Standard Response Envelope
```json
// Success — single resource
{
  "success": true,
  "data": { ...resource... },
  "meta": null
}

// Success — list resource (paginated)
{
  "success": true,
  "data": [ ...items... ],
  "meta": {
    "total": 150,
    "page": 1,
    "limit": 20,
    "total_pages": 8,
    "has_next": true,
    "has_prev": false
  }
}

// Error
{
  "success": false,
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Input validation failed",
    "fields": {
      "email": ["Email is required", "Must be valid email format"],
      "password": ["Must be at least 8 characters"]
    }
  }
}
```

### Pagination, Filtering, Sorting
```
Pagination:  ?page=1&limit=20
Sorting:     ?sort=created_at&order=desc
Filtering:   ?status=active&category_id=123
Search:      ?q=keyword
Date range:  ?from=2026-01-01&to=2026-12-31
```

### Authentication Header
```
Authorization: Bearer {access_token}
```

### Rate Limiting Headers (response)
```
X-RateLimit-Limit: 1000
X-RateLimit-Remaining: 987
X-RateLimit-Reset: 1716700800
```

---

## Step 3 — OpenAPI 3.0 Full Template

```yaml
openapi: "3.0.3"

info:
  title: "{ProjectName} REST API"
  description: |
    REST API for **{ProjectName}**.
    
    ## Authentication
    Use `Authorization: Bearer {access_token}` header for protected endpoints.
    Obtain token via `POST /api/v1/auth/login`.
    
    ## Rate Limiting
    - Authenticated: 1000 req/hour per user
    - Unauthenticated: 100 req/hour per IP
    
    ## Versioning
    Current version: v1. Breaking changes will be released as v2.
    
    ## Error Handling
    All errors follow the standard envelope format with `success: false` and an `error` object.
    See [Error Codes](./error-codes.md) for the full registry.
  version: "1.0.0"
  contact:
    name: "Development Team"
    email: "dev@{domain}.com"
  license:
    name: "Proprietary"

servers:
  - url: "http://localhost:8080/api/v1"
    description: "Local Development"
  - url: "https://staging-api.{domain}.com/api/v1"
    description: "Staging"
  - url: "https://api.{domain}.com/api/v1"
    description: "Production"

tags:
  - name: "auth"
    description: "Authentication & token management"
  - name: "users"
    description: "User profile management"
  - name: "{resource1}"
    description: "{Resource1} management"
  - name: "{resource2}"
    description: "{Resource2} management"

# ─────────────────────────────────────────────
# PATHS
# ─────────────────────────────────────────────
paths:

  # ── AUTH ──────────────────────────────────
  /auth/register:
    post:
      tags: ["auth"]
      summary: "Register a new account"
      operationId: "authRegister"
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: "#/components/schemas/RegisterRequest"
            example:
              email: "user@example.com"
              password: "P@ssword123"
              full_name: "Nguyen Van A"
      responses:
        "201":
          description: "Account created successfully"
          content:
            application/json:
              schema:
                allOf:
                  - $ref: "#/components/schemas/SuccessResponse"
                  - type: object
                    properties:
                      data:
                        $ref: "#/components/schemas/UserProfile"
        "409":
          $ref: "#/components/responses/Conflict"
        "422":
          $ref: "#/components/responses/ValidationError"

  /auth/login:
    post:
      tags: ["auth"]
      summary: "Login and obtain tokens"
      operationId: "authLogin"
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: "#/components/schemas/LoginRequest"
      responses:
        "200":
          description: "Login successful"
          content:
            application/json:
              schema:
                allOf:
                  - $ref: "#/components/schemas/SuccessResponse"
                  - type: object
                    properties:
                      data:
                        $ref: "#/components/schemas/TokenPair"
        "401":
          $ref: "#/components/responses/Unauthorized"
        "422":
          $ref: "#/components/responses/ValidationError"

  /auth/refresh:
    post:
      tags: ["auth"]
      summary: "Refresh access token"
      operationId: "authRefresh"
      requestBody:
        required: true
        content:
          application/json:
            schema:
              type: object
              required: ["refresh_token"]
              properties:
                refresh_token:
                  type: string
      responses:
        "200":
          description: "New token pair issued"
          content:
            application/json:
              schema:
                allOf:
                  - $ref: "#/components/schemas/SuccessResponse"
                  - type: object
                    properties:
                      data:
                        $ref: "#/components/schemas/TokenPair"
        "401":
          $ref: "#/components/responses/Unauthorized"

  /auth/logout:
    post:
      tags: ["auth"]
      summary: "Logout and revoke token"
      operationId: "authLogout"
      security:
        - BearerAuth: []
      responses:
        "204":
          description: "Logged out successfully"
        "401":
          $ref: "#/components/responses/Unauthorized"

  # ── USERS ─────────────────────────────────
  /users/me:
    get:
      tags: ["users"]
      summary: "Get current user profile"
      operationId: "getCurrentUser"
      security:
        - BearerAuth: []
      responses:
        "200":
          description: "Success"
          content:
            application/json:
              schema:
                allOf:
                  - $ref: "#/components/schemas/SuccessResponse"
                  - type: object
                    properties:
                      data:
                        $ref: "#/components/schemas/UserProfile"
        "401":
          $ref: "#/components/responses/Unauthorized"
    patch:
      tags: ["users"]
      summary: "Update current user profile"
      operationId: "updateCurrentUser"
      security:
        - BearerAuth: []
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: "#/components/schemas/UpdateProfileRequest"
      responses:
        "200":
          description: "Profile updated"
          content:
            application/json:
              schema:
                allOf:
                  - $ref: "#/components/schemas/SuccessResponse"
                  - type: object
                    properties:
                      data:
                        $ref: "#/components/schemas/UserProfile"
        "401":
          $ref: "#/components/responses/Unauthorized"
        "422":
          $ref: "#/components/responses/ValidationError"

  # ── {RESOURCE} CRUD TEMPLATE ──────────────
  /{resources}:
    get:
      tags: ["{resource1}"]
      summary: "List {resources} (paginated)"
      operationId: "list{Resource}"
      parameters:
        - $ref: "#/components/parameters/PageParam"
        - $ref: "#/components/parameters/LimitParam"
        - $ref: "#/components/parameters/SortParam"
        - $ref: "#/components/parameters/OrderParam"
        - name: "status"
          in: "query"
          description: "Filter by status"
          schema:
            type: "string"
            enum: ["active", "inactive", "pending"]
      responses:
        "200":
          description: "Paginated list"
          content:
            application/json:
              schema:
                allOf:
                  - $ref: "#/components/schemas/SuccessResponse"
                  - type: object
                    properties:
                      data:
                        type: array
                        items:
                          $ref: "#/components/schemas/{Resource}"
                      meta:
                        $ref: "#/components/schemas/PaginationMeta"
    post:
      tags: ["{resource1}"]
      summary: "Create new {resource}"
      operationId: "create{Resource}"
      security:
        - BearerAuth: []
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: "#/components/schemas/Create{Resource}Request"
      responses:
        "201":
          description: "Created"
          content:
            application/json:
              schema:
                allOf:
                  - $ref: "#/components/schemas/SuccessResponse"
                  - type: object
                    properties:
                      data:
                        $ref: "#/components/schemas/{Resource}"
        "401":
          $ref: "#/components/responses/Unauthorized"
        "403":
          $ref: "#/components/responses/Forbidden"
        "422":
          $ref: "#/components/responses/ValidationError"

  /{resources}/{id}:
    parameters:
      - $ref: "#/components/parameters/IdParam"
    get:
      tags: ["{resource1}"]
      summary: "Get {resource} by ID"
      operationId: "get{Resource}ById"
      responses:
        "200":
          description: "Success"
          content:
            application/json:
              schema:
                allOf:
                  - $ref: "#/components/schemas/SuccessResponse"
                  - type: object
                    properties:
                      data:
                        $ref: "#/components/schemas/{Resource}"
        "404":
          $ref: "#/components/responses/NotFound"
    put:
      tags: ["{resource1}"]
      summary: "Update {resource}"
      operationId: "update{Resource}"
      security:
        - BearerAuth: []
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: "#/components/schemas/Update{Resource}Request"
      responses:
        "200":
          description: "Updated"
          content:
            application/json:
              schema:
                allOf:
                  - $ref: "#/components/schemas/SuccessResponse"
                  - type: object
                    properties:
                      data:
                        $ref: "#/components/schemas/{Resource}"
        "401":
          $ref: "#/components/responses/Unauthorized"
        "403":
          $ref: "#/components/responses/Forbidden"
        "404":
          $ref: "#/components/responses/NotFound"
        "422":
          $ref: "#/components/responses/ValidationError"
    delete:
      tags: ["{resource1}"]
      summary: "Delete {resource} (soft delete)"
      operationId: "delete{Resource}"
      security:
        - BearerAuth: []
      responses:
        "204":
          description: "Deleted"
        "401":
          $ref: "#/components/responses/Unauthorized"
        "403":
          $ref: "#/components/responses/Forbidden"
        "404":
          $ref: "#/components/responses/NotFound"

# ─────────────────────────────────────────────
# COMPONENTS
# ─────────────────────────────────────────────
components:

  securitySchemes:
    BearerAuth:
      type: http
      scheme: bearer
      bearerFormat: JWT
      description: |
        JWT Bearer token. Obtain via POST /auth/login.
        Access token expires in 15 minutes.
        Use POST /auth/refresh with refresh_token to renew.

  parameters:
    IdParam:
      name: id
      in: path
      required: true
      description: "Resource ID"
      schema:
        type: integer
        format: int64
        minimum: 1
    PageParam:
      name: page
      in: query
      schema:
        type: integer
        default: 1
        minimum: 1
    LimitParam:
      name: limit
      in: query
      schema:
        type: integer
        default: 20
        minimum: 1
        maximum: 100
    SortParam:
      name: sort
      in: query
      description: "Field to sort by"
      schema:
        type: string
        default: "created_at"
    OrderParam:
      name: order
      in: query
      schema:
        type: string
        enum: ["asc", "desc"]
        default: "desc"

  schemas:
    # ── Envelope ──────────────────────────────
    SuccessResponse:
      type: object
      required: [success]
      properties:
        success:
          type: boolean
          example: true

    ErrorResponse:
      type: object
      required: [success, error]
      properties:
        success:
          type: boolean
          example: false
        error:
          type: object
          required: [code, message]
          properties:
            code:
              type: string
              example: "VALIDATION_ERROR"
            message:
              type: string
              example: "Input validation failed"
            fields:
              type: object
              additionalProperties:
                type: array
                items:
                  type: string
              description: "Field-level validation errors (422 only)"

    PaginationMeta:
      type: object
      properties:
        total:
          type: integer
          example: 150
        page:
          type: integer
          example: 1
        limit:
          type: integer
          example: 20
        total_pages:
          type: integer
          example: 8
        has_next:
          type: boolean
        has_prev:
          type: boolean

    # ── Auth Schemas ──────────────────────────
    RegisterRequest:
      type: object
      required: [email, password, full_name]
      properties:
        email:
          type: string
          format: email
          maxLength: 255
        password:
          type: string
          minLength: 8
          maxLength: 72
          description: "Min 8 chars, 1 uppercase, 1 number, 1 special char"
        full_name:
          type: string
          minLength: 2
          maxLength: 100

    LoginRequest:
      type: object
      required: [email, password]
      properties:
        email:
          type: string
          format: email
        password:
          type: string

    TokenPair:
      type: object
      properties:
        access_token:
          type: string
          description: "JWT. Expires in 15 minutes."
        refresh_token:
          type: string
          description: "Opaque token. Expires in 7 days."
        expires_in:
          type: integer
          description: "Seconds until access_token expires"
          example: 900
        token_type:
          type: string
          example: "Bearer"

    # ── User Schemas ──────────────────────────
    UserProfile:
      type: object
      properties:
        id:
          type: integer
          format: int64
        email:
          type: string
          format: email
        full_name:
          type: string
        roles:
          type: array
          items:
            type: string
          example: ["member"]
        is_active:
          type: boolean
        created_at:
          type: string
          format: date-time
        updated_at:
          type: string
          format: date-time

    UpdateProfileRequest:
      type: object
      properties:
        full_name:
          type: string
          minLength: 2
          maxLength: 100
        phone:
          type: string
          pattern: '^\+?[0-9]{9,15}$'

    # ── Resource Schema Template ──────────────
    "{Resource}":
      type: object
      description: "Replace with actual resource fields from DB schema"
      properties:
        id:
          type: integer
          format: int64
        # ... domain fields ...
        created_at:
          type: string
          format: date-time
        updated_at:
          type: string
          format: date-time

    "Create{Resource}Request":
      type: object
      description: "Replace with actual required + optional fields"
      required: ["{required_field}"]
      properties:
        "{required_field}":
          type: string

    "Update{Resource}Request":
      type: object
      description: "Replace with updatable fields (all optional for PATCH-like PUT)"
      properties:
        "{updatable_field}":
          type: string

  responses:
    Unauthorized:
      description: "Authentication required or token invalid/expired"
      content:
        application/json:
          schema:
            $ref: "#/components/schemas/ErrorResponse"
          example:
            success: false
            error:
              code: "UNAUTHENTICATED"
              message: "Authentication required"
    Forbidden:
      description: "Authenticated but lacks permission"
      content:
        application/json:
          schema:
            $ref: "#/components/schemas/ErrorResponse"
          example:
            success: false
            error:
              code: "FORBIDDEN"
              message: "You do not have permission to perform this action"
    NotFound:
      description: "Resource not found"
      content:
        application/json:
          schema:
            $ref: "#/components/schemas/ErrorResponse"
          example:
            success: false
            error:
              code: "NOT_FOUND"
              message: "The requested resource was not found"
    Conflict:
      description: "Resource already exists"
      content:
        application/json:
          schema:
            $ref: "#/components/schemas/ErrorResponse"
          example:
            success: false
            error:
              code: "CONFLICT"
              message: "A resource with this identifier already exists"
    ValidationError:
      description: "Input validation failed"
      content:
        application/json:
          schema:
            $ref: "#/components/schemas/ErrorResponse"
          example:
            success: false
            error:
              code: "VALIDATION_ERROR"
              message: "Input validation failed"
              fields:
                email: ["Must be a valid email address"]
                password: ["Must be at least 8 characters"]
    TooManyRequests:
      description: "Rate limit exceeded"
      headers:
        Retry-After:
          schema:
            type: integer
          description: "Seconds until rate limit resets"
      content:
        application/json:
          schema:
            $ref: "#/components/schemas/ErrorResponse"
          example:
            success: false
            error:
              code: "RATE_LIMIT_EXCEEDED"
              message: "Too many requests. Please retry after 60 seconds."
```

---

## Step 4 — Error Codes Registry Template

Save to `api/error-codes.md`:

```markdown
# API Error Codes — {ProjectName} v1.0.0

## Standard HTTP Errors

| HTTP | Code | Message | When |
|------|------|---------|---------|
| 400 | INVALID_REQUEST | Malformed request body | JSON parse error, missing Content-Type |
| 401 | UNAUTHENTICATED | Authentication required | No/invalid/expired token |
| 401 | TOKEN_EXPIRED | Access token has expired | JWT exp claim passed |
| 401 | INVALID_TOKEN | Token is invalid or malformed | JWT verify fail |
| 403 | FORBIDDEN | Insufficient permissions | Role/permission check fail |
| 404 | NOT_FOUND | Resource not found | ID does not exist or is soft-deleted |
| 405 | METHOD_NOT_ALLOWED | HTTP method not supported | Wrong verb |
| 409 | CONFLICT | Resource already exists | Duplicate unique field |
| 410 | GONE | Resource permanently deleted | Hard-deleted resource |
| 422 | VALIDATION_ERROR | Input validation failed | Schema/business rule fail |
| 429 | RATE_LIMIT_EXCEEDED | Too many requests | Rate limiter hit |
| 500 | INTERNAL_ERROR | Internal server error | Unhandled exception |
| 503 | SERVICE_UNAVAILABLE | Service temporarily unavailable | Maintenance/circuit breaker |

## Auth Module Errors

| Code | Message | HTTP | When |
|------|---------|------|---------|
| INVALID_CREDENTIALS | Email or password is incorrect | 401 | Login fail |
| ACCOUNT_DISABLED | Account has been disabled | 403 | is_active = false |
| EMAIL_NOT_VERIFIED | Please verify your email first | 403 | is_email_verified = false |
| OTP_EXPIRED | OTP code has expired | 422 | OTP TTL exceeded |
| OTP_INVALID | OTP code is incorrect | 422 | Incorrect OTP |
| PASSWORD_TOO_WEAK | Password does not meet requirements | 422 | Insufficient complexity |
| EMAIL_ALREADY_EXISTS | Email is already registered | 409 | Duplicate email registration |
| REFRESH_TOKEN_EXPIRED | Refresh token has expired | 401 | Must log in again |
| REFRESH_TOKEN_REVOKED | Refresh token has been revoked | 401 | Logout has occurred |

## {Domain Module} Errors

| Code | Message | HTTP | When |
|------|---------|------|---------|
| {DOMAIN}_{ENTITY}_NOT_FOUND | {Entity} not found | 404 | ID does not exist |
| {DOMAIN}_{ENTITY}_ALREADY_EXISTS | {Entity} already exists | 409 | Duplicate key |
| {DOMAIN}_INVALID_STATUS | Invalid status transition | 422 | State machine violation |
| {DOMAIN}_INSUFFICIENT_STOCK | Insufficient stock | 422 | Insufficient stock |
| {DOMAIN}_PAYMENT_FAILED | Payment processing failed | 422 | Gateway rejected |

## Error Response Format

```json
{
  "success": false,
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Human-readable description",
    "fields": {
      "field_name": ["Error message 1", "Error message 2"]
    }
  }
}
```

`fields` is only present when `code = VALIDATION_ERROR` (HTTP 422).
```

---

## Completion Checklist

- [ ] All UCs from the SRS have at least 1 corresponding endpoint
- [ ] Auth endpoints: register, login, refresh, logout
- [ ] All list endpoints have pagination parameters
- [ ] All protected endpoints have `security: [BearerAuth: []]`
- [ ] Response schemas share the common `SuccessResponse` envelope
- [ ] All error responses use `$ref` to common responses
- [ ] `operationId` is unique and camelCase for every endpoint
- [ ] Examples are provided for important requests/responses
- [ ] `api/error-codes.md` includes: HTTP errors + domain errors
- [ ] YAML syntax valid (no tab characters, consistent indentation)
