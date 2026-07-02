# SDD Questions Catalogue - Báo cáo Đánh giá

**File reviewed**: `SDD_Questions_Catalogue.md`  
**Date**: 2026-03-23  
**Reviewer**: Architecture & UX Review

---

## 1. Đánh giá TỔNG QUAN

| Tiêu chí | Kết quả | Điểm số |
|----------|---------|---------|
| **1. Luồng câu hỏi (Flow)** | ⚠️ Pass with concerns | 7/10 |
| **2. Tính đầy đủ (Completeness)** | ⚠️ Pass with concerns | 7/10 |
| **3. Tính khả dụng (Usability)** | ⚠️ Pass with concerns | 6/10 |
| **4. Sự nhất quán (Consistency)** | ⚠️ Pass with concerns | 7/10 |
| **5. Gợi ý trả lời (Answer Quality)** | ⚠️ Pass with concerns | 7/10 |
| **6. Mối liên kết SRS-SDD** | ❌ Fail | 4/10 |
| **TỔNG HỢP** | **⚠️ PASS WITH CONCERNS** | **6.3/10** |

---

## 2. VẤN ĐỀ CỤ THỂ CẦN SỬA

### 2.1 LUỒNG CÂU HỎI (Flow Analysis) - Điểm: 7/10

#### Vấn đề phát hiện:

**a) Thiếu phần "Per-Use-Case Design Details"**
- Hiện tại không có section nào hỏi về thiết kế chi tiết theo từng Use Case
- SDD cần phải mô tả cách mỗi Use Case từ SRS được triển khai thành các components
- Cần bổ sung: "Use Case Implementation Mapping"

**b) Thứ tự questions nên có step nhỏ hơn**
- 1.1 → 1.2 → 1.3 → 1.4 (tốt)
- Nhưng nên thêm một overview diagram request ở đầu section 1

**c) Security nên đến sau Backend-Database**
- Theo luồng thiết kế, Security nên được hỏi SAU khi đã hiểu Data và Business logic
- Đề xuất: Deployment → Security → Integration (thay vì Security → Integration → Deployment)

---

### 2.2 TÍNH ĐẦY ĐỦ (Completeness) - Điểm: 7/10

#### Thiếu các câu hỏi quan trọng:

| STT | Câu hỏi thiếu | Nơi cần bổ sung | Lý do |
|-----|---------------|-----------------|-------|
| 1 | **Business Rules / Domain Logic** | Section 3 (Backend) | Thiếu chỗ hỏi về validation rules, workflow logic, business constraints |
| 2 | **Error Handling Design** | Section 3.2 (API Design) hoặc 9.2 | Chỉ hỏi retry/circuit breaker, thiếu error code taxonomy |
| 3 | **File Upload / Storage Design** | Section 6.5 hoặc 3.x | Cần hỏi về file storage strategy, max size, allowed types |
| 4 | **Notification System Design** | Section 6.x | Fragmented trong 3.4.4, cần section riêng |
| 5 | **API Endpoint Inventory** | Section 3.2 | Chỉ hỏi pattern, không hỏi specific endpoints/resources |
| 6 | **Data Migration Strategy** | Section 7.x | Thiếu câu hỏi về DB versioning, migration approach |
| 7 | **Feature Toggles** | Section 7.3.4 đã có, nhưng thiếu use case | Cần hỏi thêm về toggle categories |
| 8 | **Localization / i18n** | Thiếu hoàn toàn | Cần cho ứng dụng quốc tế |
| 9 | **Analytics / Tracking** | Thiếu hoàn toàn | User behavior tracking, conversion tracking |
| 10 | **Session Management** | Section 5.3 có nhưng chung chung | Cần tách riêng session timeout, concurrent sessions |

#### Câu hỏi thừa hoặc không cần thiết:

| STT | Câu hỏi | Vấn đề | Đề xuất |
|-----|---------|--------|---------|
| 1 | 3.3.5 Password policy + 5.1.4 Password reset | Overlap với 5.1.x | Gộp vào 5.1.x |
| 2 | 3.3.6 Account lockout policy | Overlap với Security section | Gộp vào 5.1.x |
| 3 | 7.2.4 Helm charts hay Kustomize | Quá chi tiết cho questionnaire | Chuyển thành Optional |
| 4 | 10.1.4 Code complexity limits | Quá kỹ thuật cho stakeholder | Loại bỏ hoặc đánh dấu [T] rõ ràng |

---

### 2.3 TÍNH KHẢ DỤNG (Usability) - Điểm: 6/10

#### Vấn đề:

**a) Câu hỏi quá kỹ thuật cho Business Stakeholders:**

| Câu hỏi | Vấn đề | Đề xuất |
|---------|--------|---------|
| 1.4.3 "Service Discovery mechanism" | Kỹ thuật microservices sâu | Thêm giải thích ngắn |
| 3.4.2 "Cache invalidation strategy" | Cần background knowledge | Thêm ví dụ đơn giản |
| 5.2.1 "RBAC/ABAC/PBAC" | Kỹ thuật access control | Thêm "hoặc tự mô tả" |
| 6.4.2 "Event schema registry" | Quá chuyên biệt | Đánh dấu [T] |
| 9.4.6 "SLO/SLI definitions" | Cần giải thích | Thêm note |
| 10.2.8 "Mutation testing" | Quá niche | Loại bỏ khỏi main list |

**b) Thiếu context/ngữ cảnh:**
- Không có phần giới thiệu tổng quan về tài liệu
- Không có hướng dẫn cách trả lời
- Không có example về 1 câu hỏi đã trả lời

**c) Mixed language:**
- Line 214: "user只能访问自己的数据" - Chinese text không nhất quán
- Nên thống nhất Tiếng Việt hoặc English

---

### 2.4 SỰ NHẤT QUÁN (Consistency) - Điểm: 7/10

#### Vấn đề:

**a) Answer format không nhất quán:**

| Câu hỏi | Answer Format | Vấn đề |
|---------|--------------|---------|
| 1.1.1 | "Monolithic / Layered / MVC / Microservices" | Đầy đủ |
| 1.2.1 | "Cloud (AWS/Azure/GCP) / On-premise" | Tốt |
| 3.3.2 | "Access token: 15 min / Refresh token: 7 days" | Tốt |
| 8.1.1 | "X users đồng thời" | Quá vague, không có example |
| 9.5.2 | "X minutes / X hours" | Cần số cụ thể |

**b) Trùng lặp [B]/[T] marking:**
- 5.1.x và 3.3.x có overlap về authentication/password
- 9.4.x và 7.5.x có overlap về monitoring

**c) Số thứ tự không liên tục trong một số section:**
- 3.2 có 7 câu hỏi (1.1 → 1.7)
- Nên renumber nếu có thêm

---

### 2.5 GỢI Ý TRẢ LỜI (Answer Quality) - Điểm: 7/10

#### Thiếu sót:

**a) Không có Tech Stack Options cụ thể cho:**

| Area | Hiện tại | Cần thêm |
|------|---------|---------|
| Monitoring | "Prometheus, Datadog" | Thêm giá tham chiếu: "Enterprise: Datadog/New Relic; Open source: Prometheus+Grafana" |
| Message Queue | "Kafka, RabbitMQ" | Thêm so sánh ngắn: "High throughput: Kafka; Simple task: RabbitMQ/SQS" |
| API Gateway | "Kong, AWS API Gateway" | Thêm use-case recommendation |
| Database | List dài | Phân loại: "OLTP vs OLAP, ACID vs BASE" |

**b) Examples thiếu:**

| Câu hỏi | Cần thêm example |
|---------|-----------------|
| 8.2.1 Response time SLA | "vd: p95 < 200ms, p99 < 500ms" |
| 8.1.1 Concurrent users | "vd: 1000 users đồng thời" |
| 9.5.2 RTO | "vd: < 4 giờ" |

**c) Tech stack recommendations theo project size:**

Nên thêm bảng reference:
- Small project (< 6 tháng): Tech X, Y, Z
- Medium (6-12 tháng): Tech A, B, C  
- Large enterprise: Tech 1, 2, 3

---

### 2.6 MỐI LIÊN KẾT SRS-SDD - Điểm: 4/10

#### Vấn đề nghiêm trọng:

**a) Không có Reference đến SRS Use Cases:**

|缺失 | Tác động |
|-----|---------|
| Không hỏi "Use Case nào từ SRS?" | SDD không link được với requirements |
| Không hỏi "Actor nào?" | Thiếu context cho architecture |
| Không hỏi "Functional requirements nào?" | Design có thể không cover đủ |

**b) Không có Cross-reference mechanism:**

Cần bổ sung:
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

**c) Appendix không liên kết với SRS:**

"Appendix: Quick Reference" section 512-546 nên include:
- Link đến Use Cases
- Traceability matrix
- Requirements ID mapping

---

## 3. ĐỀ XUẤT CẢI TIẾN CỤ THỂ

### 3.1 CẤU TRÚC LẠI LUỒNG (Flow Restructure)

**Thứ tự hiện tại:**
```
1. Architecture → 2. Frontend → 3. Backend → 4. DB → 5. Security → 6. Integration → 7. Deployment → 8. Performance → 9. Reliability → 10. Dev Workflow
```

**Thứ tự đề xuất:**
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

### 3.2 BỔ SUNG SECTION MỚI

**NEW: Section 0 - Requirements Mapping**
```markdown
## 0. REQUIREMENTS MAPPING

### 0.1 SRS Reference
| Câu hỏi | Loại | Gợi ý câu trả lời |
|---------|------|-------------------|
| 0.1.1 | SRS document đã hoàn thành? | [B] | Có (version X.X) / Chưa |
| 0.1.2 | Các Use Cases cần triển khai? | [B] | UC-01, UC-02, ... |
| 0.1.3 | Key functional requirements? | [B] | FR-1.1, FR-2.3, ... |
| 0.1.4 | Non-functional requirements từ SRS? | [B] | NFR-1 (Performance), NFR-2 (Security), ... |
```

**NEW: Section 2.5 - Use Case Implementation Mapping**
```markdown
### 2.5 Use Case Implementation

| Use Case | Component | API Endpoint | DB Entity | Error Handling |
|----------|-----------|--------------|-----------|-----------------|
| UC-01 Login | Auth Service | POST /auth/login | Users | 401, 403, 429 |
| UC-02 Browse | Product Service | GET /products | Products | 404, 500 |
```

### 3.3 SỬA ĐỔI NGÔN NGỮ

**Issue: Line 214 có Chinese text**
```markdown
# TRƯỚC:
| 5.2.3 | Có cần resource-level permissions không? | [T] | Có (user只能访问自己的数据) / Không / Cần tư vấn |

# SAU:
| 5.2.3 | Có cần resource-level permissions không? | [T] | Có (user chỉ truy cập dữ liệu của mình) / Không / Cần tư vấn |
```

### 3.4 THÊM QUICK START GUIDE

**Bổ sung sau header:**
```markdown
> **Hướng dẫn sử dụng nhanh**:
> 1. Đọc hết 1 lượt để hiểu scope
> 2. Trả lời các câu [B] trước (khoảng 30-40 câu)
> 3. Đánh dấu các câu [T] tùy theo complexity
> 4. Đối với mỗi câu trả lời, cung cấp càng chi tiết càng tốt
> 5. Tham khảo "Questions by Project Type" để biết focus areas
```

### 3.5 CẢI THIỆN ANSWER SUGGESTIONS

**Thêm Tech Stack Comparison Table vào Appendix:**
```markdown
### Tech Stack Selection Guide

| Nhu cầu | Small Project | Medium Project | Enterprise |
|---------|---------------|-----------------|------------|
| API | REST | REST + GraphQL | REST + gRPC |
| Database | PostgreSQL | PostgreSQL + Redis | Multi-DB + Sharding |
| Cache | None/Memory | Redis | Redis + CDN |
| Queue | Sync calls | Redis Pub/Sub | Kafka/RabbitMQ |
| Monitoring | Logs only | Prometheus+Grafana | Datadog/New Relic |
| CI/CD | GitHub Actions | GitLab CI | Jenkins + ArgoCD |
```

---

## 4. ĐÁNH GIÁ CHI TIẾT TỪNG DANH MỤC

### 4.1 SYSTEM ARCHITECTURE (1.x) - 8/10

| Tiêu chí | Đánh giá |
|----------|----------|
| Architectural Pattern (1.1) | Tốt, cover đủ các pattern phổ biến |
| Deployment Architecture (1.2) | Tốt, có cả cloud/on-premise |
| High-Level Components (1.3) | Cần bổ sung component responsibility matrix |
| Communication Patterns (1.4) | Tốt, cover sync/async |

**Điểm mạnh**: Phân loại [B]/[T] rõ ràng  
**Cần cải thiện**: Thêm visual architecture request (diagram)

---

### 4.2 FRONTEND ARCHITECTURE (2.x) - 7/10

| Tiêu chí | Đánh giá |
|----------|----------|
| Technology Stack (2.1) | Đầy đủ |
| Component Architecture (2.2) | Thiếu atomic design detail |
| State Management (2.3) | Tốt |
| Routing & Navigation (2.4) | Tốt |
| Responsive & PWA (2.5) | Thiếu accessibility (a11y) |

**Thiếu**: 
- Accessibility requirements (WCAG compliance)
- Internationalization (i18n)
- Performance budgets (JS bundle size limit)

---

### 4.3 BACKEND ARCHITECTURE (3.x) - 7/10

| Tiêu chí | Đánh giá |
|----------|----------|
| Technology Stack (3.1) | Tốt |
| API Design (3.2) | Tốt, thiếu endpoint inventory |
| Authentication (3.3) | Overlap với Security 5.x |
| Caching & Queue (3.4) | Tốt |

**Overlaps cần resolve**:
- 3.3.x với 5.1.x (authentication)
- 3.4.x với 8.3.x (caching)

---

### 4.4 DATABASE DESIGN (4.x) - 8/10

| Tiêu chí | Đánh giá |
|----------|----------|
| Database Type (4.1) | Tốt |
| Schema Design (4.2) | Tốt |
| Indexing & Performance (4.3) | Tốt |
| Data Partitioning (4.4) | Tốt |

**Điểm mạnh**: Cover đầy đủ SQL/NoSQL, ORM options  
**Cần thêm**: Data lifecycle management, archival strategy chi tiết hơn

---

### 4.5 SECURITY ARCHITECTURE (5.x) - 8/10

| Tiêu chí | Đánh giá |
|----------|----------|
| Authentication & Identity (5.1) | Tốt |
| Authorization Model (5.2) | Tốt |
| Data Protection (5.3) | Tốt |
| API & Application Security (5.4) | Tốt, cover OWASP top 10 |
| Compliance & Audit (5.5) | Tốt |

**Điểm mạnh**: Cover đầy đủ compliance requirements  
**Cần cải thiện**: Thêm security architecture diagram request

---

### 4.6 INTEGRATION ARCHITECTURE (6.x) - 7/10

| Tiêu chí | Đánh giá |
|----------|----------|
| Third-Party Services (6.1) | Tốt |
| Payment Integration (6.2) | Chi tiết và đầy đủ |
| Authentication Providers (6.3) | Tốt |
| Event-Driven & Messaging (6.4) | Tốt |
| External System Integration (6.5) | Tốt |

**Thiếu**: 
- Integration with internal legacy systems
- Data format transformation/ETL considerations

---

### 4.7 DEPLOYMENT & INFRASTRUCTURE (7.x) - 8/10

| Tiêu chí | Đánh giá |
|----------|----------|
| CI/CD Pipeline (7.1) | Tốt |
| Containerization (7.2) | Tốt |
| Environment Configuration (7.3) | Tốt |
| Infrastructure as Code (7.4) | Tốt |
| Monitoring & Logging (7.5) | Tốt |

**Điểm mạnh**: Cover đầy đủ DevOps lifecycle  
**Cần thêm**: Cost estimation/budget considerations

---

### 4.8 PERFORMANCE & SCALABILITY (8.x) - 7/10

| Tiêu chí | Đánh giá |
|----------|----------|
| Capacity Planning (8.1) | Tốt, cần concrete numbers |
| Performance Requirements (8.2) | Thiếu concrete SLAs |
| Caching Strategy (8.3) | Tốt |
| Scalability (8.4) | Tốt |

**Cần cải thiện**: 
- Thêm reference values (industry benchmarks)
- Database query performance budgets

---

### 4.9 RELIABILITY & MONITORING (9.x) - 8/10

| Tiêu chí | Đánh giá |
|----------|----------|
| Availability & SLA (9.1) | Tốt |
| Error Handling (9.2) | Tốt |
| Logging Strategy (9.3) | Tốt |
| Monitoring & Alerting (9.4) | Tốt |
| Disaster Recovery (9.5) | Rất chi tiết |

**Điểm mạnh**: DR section rất comprehensive  
**Cần thêm**: Chaos engineering considerations

---

### 4.10 DEVELOPMENT WORKFLOW (10.x) - 7/10

| Tiêu chí | Đánh giá |
|----------|----------|
| Code Quality (10.1) | Tốt |
| Testing Requirements (10.2) | Tốt, có test pyramid |
| Code Review (10.3) | Tốt |
| Version Control (10.4) | Tốt |
| Documentation (10.5) | Tốt |

**Thiếu**:
- Definition of Done (DoD) criteria
- Definition of Ready (DoR) criteria
- Technical debt management

---

## 5. TỔNG KẾT VÀ KHUYẾN NGHỊ

### 5.1 ƯU TIÊN SỬA ĐỔI

| Priority | Issue | Effort | Impact |
|----------|-------|--------|--------|
| **P0** | Thêm Section 0: Requirements Mapping | Medium | High |
| **P0** | Thêm Use Case → Component mapping | Medium | High |
| **P1** | Fix mixed language (line 214) | Low | Medium |
| **P1** | Thêm concrete examples trong answer suggestions | Low | High |
| **P2** | Resolve overlap 3.3.x và 5.1.x | Medium | Medium |
| **P2** | Thêm i18n, accessibility sections | Low | Medium |
| **P2** | Restructure flow: Security after DB | Medium | Medium |

### 5.2 RECOMMENDED ACTIONS

1. **Ngay lập tức**: Thêm Section 0 để link SRS-SDD
2. **Tuần này**: Fix language inconsistency và thêm examples
3. **Sprint sau**: Restructure flow và resolve overlaps

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
| 1 | 5.2.3 | 214 | Chinese text "user只能访问自己的数据" | Change to Vietnamese |
| 2 | ALL | - | No SRS Use Case reference | Add Section 0 |
| 3 | - | - | No use case → component mapping | Add section 2.5 |

### 6.2 SHOULD FIX (Recommended)

| # | Section | Issue | Fix |
|---|---------|-------|-----|
| 1 | 3.3.x, 5.1.x | Overlap authentication | Merge or cross-reference |
| 2 | 2.5 | Missing i18n | Add 2.5.6 Internationalization |
| 3 | 2.x | Missing accessibility | Add 2.6 Accessibility |
| 4 | 8.x | Vague SLAs | Add industry reference values |
| 5 | 10.x | Missing DoD/DoR | Add definition sections |

### 6.3 NICE TO HAVE (Enhancement)

| # | Section | Issue | Suggestion |
|---|---------|-------|------------|
| 1 | Header | No usage guide | Add quick start guide |
| 2 | Appendix | No tech stack guide | Add comparison table |
| 3 | 7.x | No cost estimation | Add budget considerations |
| 4 | 9.x | No chaos engineering | Add resilience testing |

---

*Báo cáo này được tạo tự động bởi Architecture & UX Review System*
