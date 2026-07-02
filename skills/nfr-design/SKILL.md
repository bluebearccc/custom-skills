---
name: nfr-design
description: |
  Template và hướng dẫn thiết kế Non-Functional Requirements đầy đủ cho SDD:
  capacity planning, disaster recovery, i18n/l10n, accessibility (WCAG).

  SỬ DỤNG KHI:
  - sdd-agent cần điền Section 8 (Performance), Section 9 (Reliability/DR) trong SDD
  - Cần thiết kế chi tiết về capacity, DR procedure, i18n, accessibility
  - Cần concrete targets từ NFRs trong SRS

  OUTPUT: Các sections markdown để chèn vào SDD document
version: "1.0.0"
allowed-tools: ["Read", "Write", "Edit"]
mode: false
---

# NFR Design Skill v1.0.0

## Mục đích
Cung cấp templates đầy đủ cho tất cả Non-Functional Requirements trong SDD — đảm bảo mỗi NFR đều có concrete target, measurement method, và design decision.

---

## Section 8: Performance & Scalability

### 8.1 Capacity Planning

```markdown
#### 8.1 Capacity Planning

**8.1.1 Traffic Projections**

| Metric | Current (Baseline) | 6 Months | 12 Months | 24 Months |
|--------|--------------------|----------|-----------|-----------|
| Daily Active Users (DAU) | 0 (new system) | {N} | {N} | {N} |
| Concurrent Users (peak) | — | {N} | {N} | {N} |
| API Requests / second | — | {N} rps | {N} rps | {N} rps |
| Page Views / day | — | {N}K | {N}K | {N}M |

> **Methodology:** {Estimate based on: market research / existing system data / business forecast}
> **Peak Factor:** 3x average (typically: lunch hour + year-end sale events)

**8.1.2 Storage Growth Projections**

| Data Type | Avg Size / Record | Growth Rate | 1 Year Storage | 3 Year Storage |
|-----------|------------------|-------------|----------------|----------------|
| User accounts | ~2 KB | {N} new/month | {N} MB | {N} GB |
| Transaction records | ~5 KB | {N} new/day | {N} GB | {N} GB |
| Product catalog | ~10 KB | {N} new/month | {N} GB | {N} GB |
| Media files (images) | avg 500 KB | {N} uploads/day | {N} TB | {N} TB |
| Audit logs | ~1 KB | ~100% of transactions | {N} GB | {N} GB |
| **Total Estimated** | | | **{N} GB** | **{N} TB** |

> **Storage Strategy:**
> - Hot storage (DB): last 3 months active data
> - Warm storage (cheaper SSD): 3–12 months
> - Cold storage (object storage/glacier): 12+ months

**8.1.3 Infrastructure Sizing**

| Component | Development | Staging | Production (initial) | Production (scaled) |
|-----------|-------------|---------|---------------------|---------------------|
| API Servers | 1× shared | 1× t3.small | 2× t3.medium | 4–10× auto-scale |
| Database | Docker | db.t3.micro | db.t3.large | db.r6g.xlarge Multi-AZ |
| Cache (Redis) | Docker | cache.t3.micro | cache.r6g.large | cache.r6g.large cluster |
| Message Queue | Docker | shared | mq.m5.large | mq.m5.xlarge |
| Load Balancer | N/A | N/A | ALB | ALB + WAF |
| CDN | N/A | N/A | CloudFront | CloudFront |

**8.1.4 Scaling Triggers**

| Resource | Scale Out When | Scale In When | Max Instances |
|----------|---------------|---------------|---------------|
| API Service | CPU > 70% for 2 min | CPU < 30% for 10 min | 10 |
| Worker Service | Queue depth > 100 msg | Queue depth < 10 msg | 5 |
| Read Replica DB | CPU > 60% | Manual | 3 |
```

### 8.2 Performance SLAs

```markdown
#### 8.2 Performance Requirements & SLAs

**8.2.1 Response Time Targets**

| API Endpoint Type | P50 | P95 | P99 | Max Acceptable |
|-------------------|-----|-----|-----|----------------|
| Authentication (login) | 80ms | 200ms | 500ms | 1000ms |
| Simple reads (GET by ID) | 30ms | 100ms | 200ms | 500ms |
| List queries (paginated) | 50ms | 150ms | 300ms | 800ms |
| Search queries | 100ms | 300ms | 600ms | 2000ms |
| Write operations (POST/PUT) | 100ms | 300ms | 800ms | 2000ms |
| File upload | 500ms | 2000ms | 5000ms | 10000ms |
| Report generation | 500ms | 2000ms | 5000ms | 30000ms |

> **Measurement:** Measured at API Gateway, excluding network latency to client
> **Monitoring:** AWS CloudWatch + custom Prometheus metrics
> **Alert threshold:** P95 > target × 1.5 for 5 consecutive minutes

**8.2.2 Database Query Budget**

| Query Type | Target Execution Time | Action if Exceeded |
|-----------|----------------------|--------------------|
| Primary key lookup | < 1ms | Check indexes |
| Indexed column query | < 10ms | Review query plan |
| Join query (2-3 tables) | < 50ms | Add composite index |
| Complex report query | < 500ms | Consider materialized view |
| > 500ms query | WARN log | Optimize or cache |
| > 2000ms query | ERROR log | Must fix before release |

**8.2.3 Frontend Performance (Web Vitals)**

| Metric | Target | Measurement Tool |
|--------|--------|-----------------|
| LCP (Largest Contentful Paint) | < 2.5s | Lighthouse, CrUX |
| FID (First Input Delay) | < 100ms | CrUX |
| CLS (Cumulative Layout Shift) | < 0.1 | Lighthouse |
| TTFB (Time to First Byte) | < 600ms | WebPageTest |
| Bundle size (initial JS) | < 200KB gzipped | webpack-bundle-analyzer |
| API calls on page load | ≤ 3 | Network tab audit |
```

### 8.3 Caching Strategy (expanded)

```markdown
#### 8.3 Caching Strategy

(Đã có từ Sprint 2 SDD patch — tham khảo Section 4.1.3)

**Bổ sung: Cache Invalidation Patterns**

| Pattern | Khi nào dùng | Implementation |
|---------|-------------|----------------|
| TTL-based | Read-heavy, acceptable staleness | Redis EXPIRE |
| Write-through | Data phải consistent ngay | Update cache on write |
| Cache-aside (Lazy) | Read-heavy, infrequent writes | Miss → DB → cache |
| Event-driven | Microservices, eventual consistency | Pub/Sub invalidation |

**Cache Monitoring:**
- Hit rate target: > 80%
- Miss spike alert: > 50% misses in 5 min
- Memory usage alert: > 80% of allocated
```

---

## Section 9: Reliability, Disaster Recovery & Observability

### 9.5 Disaster Recovery (expanded)

```markdown
#### 9.5 Disaster Recovery Procedure

**9.5.1 DR Objectives**

| Metric | Target | Measurement |
|--------|--------|-------------|
| RTO (Recovery Time Objective) | < 4 hours | Time from incident declaration to system operational |
| RPO (Recovery Point Objective) | < 15 minutes | Maximum data loss acceptable |
| MTTR (Mean Time to Recovery) | < 2 hours | Average recovery time across incidents |
| Availability SLA | 99.9% | = max 8.7 hours downtime/year |

**9.5.2 Backup Strategy**

| Data | Backup Frequency | Retention | Storage | Encryption |
|------|-----------------|-----------|---------|------------|
| RDS Full backup | Daily (02:00 UTC) | 30 days | S3 | AES-256 |
| RDS Incremental | Every 15 min (binlog) | 7 days | S3 | AES-256 |
| S3 Media files | Versioning enabled | 90 days versions | S3 cross-region | SSE-S3 |
| Application config | Git repository | Indefinite | GitHub | N/A |
| Secrets/keys | AWS Secrets Manager | Rotation 90 days | AWS | KMS |

**9.5.3 DR Runbook — Database Failure**

```
SCENARIO: RDS Primary instance fails

STEP 1 — Detection (0–5 min):
  [ ] CloudWatch alarm fires: RDS DatabaseConnections = 0
  [ ] Auto-failover to Read Replica begins (Multi-AZ)
  [ ] Estimated automatic recovery: 1–2 min (Multi-AZ)

STEP 2 — Verification (5–15 min):
  [ ] Confirm new primary endpoint is active
  [ ] Run health check: GET /api/health → 200 OK
  [ ] Check application logs for DB connection errors
  [ ] Verify data consistency on last {N} records

STEP 3 — Communication:
  [ ] Notify on-call engineer via PagerDuty
  [ ] Update status page: "Investigating database issue"
  [ ] Notify stakeholders if downtime > 5 min

STEP 4 — Resolution:
  [ ] Monitor for 30 min post-recovery
  [ ] Create incident report
  [ ] Update status page: "Resolved"

ESCALATION: If auto-failover fails after 10 min → Manual DR
```

**Manual DR Runbook — Full Region Failure:**

```
PREREQUISITE: Cross-region backup in ap-northeast-1 (Tokyo)

STEP 1 — Declare DR (0–15 min):
  [ ] CTO/Tech Lead declares DR event
  [ ] Notify all engineers
  [ ] Switch status page to maintenance

STEP 2 — Restore Infrastructure (15–90 min):
  [ ] Provision RDS from latest snapshot in DR region
  [ ] Update DNS: api.domain.com → DR region ALB
  [ ] Deploy latest container image from ECR (cross-region)
  [ ] Update environment variables for DR region endpoints

STEP 3 — Data Validation (90–120 min):
  [ ] Verify record count matches last backup timestamp
  [ ] Run automated smoke tests: newman run postman-collection.json
  [ ] Manual verification of last {N} critical transactions

STEP 4 — Go-live (120–240 min):
  [ ] Update CDN origin to DR region
  [ ] Enable traffic: monitoring for error spikes
  [ ] Remove maintenance mode
  [ ] Monitor for 1 hour

STEP 5 — Post-recovery:
  [ ] Sync any data created during DR period back to primary
  [ ] Switch back to primary region when ready
  [ ] Document RPO achieved (actual data loss)
  [ ] Post-mortem within 48 hours
```

**9.5.4 DR Testing Schedule**

| Test Type | Frequency | Duration | Success Criteria |
|-----------|-----------|----------|-----------------|
| Backup restore test | Monthly | 2 hours | DB restored, data valid |
| Failover simulation | Quarterly | 4 hours | RTO < 4h, RPO < 15min |
| Full DR simulation | Annually | 1 day | All systems operational in DR region |
| Chaos engineering | Per sprint | 2 hours | System degrades gracefully |
```

---

## Section 10: Internationalization & Localization

```markdown
#### 10.X Internationalization & Localization (i18n/l10n)

**10.X.1 i18n Requirements**

| Requirement | Target | Implementation |
|-------------|--------|---------------|
| Languages supported | {vi, en} minimum | i18next / react-intl |
| Default language | Vietnamese (vi-VN) | Accept-Language header |
| Language switching | Runtime (no reload) | Context provider |
| RTL support | No (future: Arabic) | CSS logical properties |

**10.X.2 Locale Coverage**

| Category | Standard | Implementation |
|----------|---------|---------------|
| Date format | DD/MM/YYYY (vi), MM/DD/YYYY (en) | date-fns with locale |
| Time format | 24h (vi), 12h AM/PM (en) | Intl.DateTimeFormat |
| Number format | 1.000,00 (vi), 1,000.00 (en) | Intl.NumberFormat |
| Currency | VND (₫), USD ($) | Intl.NumberFormat currency |
| Phone format | +84 xxx xxx xxx (vi) | libphonenumber-js |
| Address format | Vietnamese postal format | Custom formatter |
| First day of week | Monday (vi/ISO), Sunday (en-US) | Calendar config |
| Timezone | Asia/Ho_Chi_Minh default | date-fns-tz |

**10.X.3 Translation File Structure**

```
src/locales/
├── vi/
│   ├── common.json      ← Buttons, labels, errors dùng chung
│   ├── auth.json        ← Auth module strings
│   ├── product.json     ← Product module strings
│   ├── order.json       ← Order module strings
│   └── validation.json  ← Form validation messages
└── en/
    ├── common.json
    ├── auth.json
    ├── product.json
    ├── order.json
    └── validation.json
```

**Translation Key Convention:**
```json
{
  "auth": {
    "login": {
      "title": "Đăng nhập",
      "email_label": "Email",
      "password_label": "Mật khẩu",
      "submit_button": "Đăng nhập",
      "error_invalid": "Email hoặc mật khẩu không đúng",
      "forgot_password": "Quên mật khẩu?"
    }
  }
}
```

**Backend i18n:**
```
Error messages: trả về error.code (không translate ở BE)
Client translate: dựa vào error.code → localized message
Email templates: tạo template riêng cho từng ngôn ngữ
PDF/Reports: generate với locale của user
```

**10.X.4 SEO Considerations (nếu có public pages)**
```
<html lang="vi">
<link rel="alternate" hreflang="vi" href="https://domain.com/vi/page" />
<link rel="alternate" hreflang="en" href="https://domain.com/en/page" />
<link rel="alternate" hreflang="x-default" href="https://domain.com/page" />
```
```

---

## Section 11: Accessibility (WCAG 2.1)

```markdown
#### 11.X Accessibility Design (WCAG 2.1 Level AA)

**11.X.1 Compliance Target**

| Standard | Level | Scope |
|----------|-------|-------|
| WCAG 2.1 | AA | All public-facing pages |
| WCAG 2.1 | A minimum | Admin interface |
| ARIA 1.1 | — | All interactive components |
| Section 508 | — | If US government clients |

**11.X.2 Perceivable Requirements**

| Criterion | Requirement | Implementation |
|-----------|-------------|---------------|
| 1.1.1 Alt text | All images have alt text | `<img alt="...">`, decorative: `alt=""` |
| 1.3.1 Info & Relationships | Semantic HTML | `<header>`, `<nav>`, `<main>`, `<article>` |
| 1.3.3 Sensory characteristics | No color-only instructions | Pair color with text/icon |
| 1.4.1 Use of Color | Info not conveyed by color alone | Icons + text labels |
| 1.4.3 Contrast (minimum) | Text contrast ≥ 4.5:1 | Check: WebAIM Contrast Checker |
| 1.4.4 Resize Text | Page functional at 200% zoom | CSS relative units (rem, em) |
| 1.4.11 Non-text contrast | UI components 3:1 contrast | Borders, icons, focus indicators |
| 1.4.12 Text Spacing | No content loss on spacing changes | CSS not overriding user styles |

**11.X.3 Operable Requirements**

| Criterion | Requirement | Implementation |
|-----------|-------------|---------------|
| 2.1.1 Keyboard | All functionality via keyboard | Tab order, no keyboard traps |
| 2.1.2 No Keyboard Trap | Focus never trapped | Escape closes modals |
| 2.4.1 Bypass Blocks | Skip navigation link | `<a href="#main">Skip to content</a>` |
| 2.4.3 Focus Order | Logical focus sequence | DOM order = visual order |
| 2.4.4 Link Purpose | Links describe destination | "Learn more about {topic}" not "click here" |
| 2.4.7 Focus Visible | Visible focus indicator | `outline: 2px solid #005FCC` min |
| 2.5.3 Label in Name | Visible label in accessible name | `aria-label` matches visible text |

**11.X.4 Understandable Requirements**

| Criterion | Requirement | Implementation |
|-----------|-------------|---------------|
| 3.1.1 Language of Page | HTML lang attribute | `<html lang="vi">` |
| 3.2.2 On Input | No unexpected context change on input | No auto-submit on selection |
| 3.3.1 Error Identification | Errors identified in text | Error messages below fields |
| 3.3.2 Labels or Instructions | Form fields have labels | `<label for="...">` |
| 3.3.3 Error Suggestion | Describe how to fix errors | "Vui lòng nhập email hợp lệ (vd: user@gmail.com)" |

**11.X.5 Robust Requirements**

| Criterion | Requirement | Implementation |
|-----------|-------------|---------------|
| 4.1.1 Parsing | Valid HTML | HTML validator in CI |
| 4.1.2 Name, Role, Value | ARIA for custom widgets | `role`, `aria-label`, `aria-expanded` |
| 4.1.3 Status Messages | Programmatic status | `role="status"` for toasts, alerts |

**11.X.6 Testing Tools**

| Tool | Type | When |
|------|------|------|
| axe DevTools | Automated browser extension | Development |
| WAVE | Automated web tool | Pre-release |
| Lighthouse | Automated (CI) | Every PR |
| NVDA + Chrome | Manual screen reader | Quarterly audit |
| Keyboard-only navigation | Manual | Pre-release |

**11.X.7 ARIA Component Patterns**

Common components cần ARIA:

```html
<!-- Modal Dialog -->
<div role="dialog" aria-modal="true" aria-labelledby="dialog-title">
  <h2 id="dialog-title">Xác nhận xóa</h2>
  ...
</div>

<!-- Live Notifications (Toast) -->
<div role="status" aria-live="polite" aria-atomic="true">
  Đã lưu thành công
</div>

<!-- Error Alert -->
<div role="alert" aria-live="assertive">
  Đã xảy ra lỗi: Email không hợp lệ
</div>

<!-- Loading State -->
<button aria-busy="true" aria-disabled="true">
  <span aria-hidden="true">⏳</span> Đang xử lý...
</button>

<!-- Expandable Nav -->
<button aria-expanded="false" aria-controls="nav-menu">Menu</button>
<nav id="nav-menu" hidden>...</nav>
```
```

---

## Cách sử dụng

`sdd-agent` gọi skill này để điền các sections còn thiếu trong SDD:

```
skill({ name: "nfr-design", section: "capacity" })
  → Trả về template 8.1 để điền vào SDD

skill({ name: "nfr-design", section: "dr" })
  → Trả về template 9.5 để điền vào SDD

skill({ name: "nfr-design", section: "i18n" })
  → Trả về template 10.X để điền vào SDD

skill({ name: "nfr-design", section: "accessibility" })
  → Trả về template 11.X để điền vào SDD

skill({ name: "nfr-design", section: "all" })
  → Trả về tất cả templates
```

Sau khi nhận template, agent điền giá trị cụ thể từ `requirements-summary.md`:
- `{N}` → giá trị từ NFR section trong SRS
- `{vi, en}` → ngôn ngữ từ yêu cầu i18n trong SRS
- RTO/RPO → từ NFR availability trong SRS
