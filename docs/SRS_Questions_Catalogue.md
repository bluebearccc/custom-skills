# SRS Questions Catalogue
# Danh mục Câu hỏi Thu thập Yêu cầu Phần mềm

**Mục đích:** Tài liệu này chứa danh sách toàn diện các câu hỏi để thu thập thông tin từ khách hàng/stakeholders trước khi viết tài liệu Software Requirements Specification (SRS).

**Cách sử dụng:** Đánh dấu ✓ các câu hỏi đã được trả lời, thu thập câu trả lời từ stakeholders, và lưu vào tài liệu Requirements Gathering Summary.

---

## 1. THÔNG TIN DỰ ÁN CƠ BẢN

### 1.1 Tên & Mô tả Dự án

| # | Câu hỏi | Loại | Bắt buộc |
|---|---------|------|----------|
| 1.1.1 | Tên chính thức của dự án là gì? | Text | ✓ |
| 1.1.2 | Mô tả ngắn gọn (1-2 câu) về hệ thống cần xây dựng? | Text | ✓ |
| 1.1.3 | Hệ thống mới này thay thế hay bổ sung cho hệ thống hiện có nào? | Text | ✓ |
| 1.1.4 | Đâu là vấn đề hoặc cơ hội kinh doanh mà dự án này giải quyết? | Text | ✓ |
| 1.1.5 | Giải pháp thay thế nào đã được xem xét (bao gồm việc không làm gì)? | Text | Tùy chọn |

### 1.2 Mục tiêu Kinh doanh

| # | Câu hỏi | Loại | Bắt buộc |
|---|---------|------|----------|
| 1.2.1 | Mục tiêu chính của dự án (SMART) là gì? | Text | ✓ |
| 1.2.2 | KPIs nào sẽ được dùng để đo lường thành công? | List | ✓ |
| 1.2.3 | Lợi ích ROI dự kiến là bao nhiêu? | Number | Tùy chọn |
| 1.2.4 | Thời hạn để đạt được lợi ích mong đợi? | Date/Duration | Tùy chọn |
| 1.2.5 | Các stakeholder chính kỳ vọng điều gì từ dự án? | Text | ✓ |

### 1.3 Phạm vi (Scope)

| # | Câu hỏi | Loại | Bắt buộc |
|---|---------|------|----------|
| 1.3.1 | Phạm vi chính thức (in-scope) của dự án là gì? | Text | ✓ |
| 1.3.2 | Đâu là phạm vi ngoài (out-of-scope) được chấp nhận? | Text | ✓ |
| 1.3.3 | Có tính năng nào trong future phases cần reserved không? | Boolean + Text | Tùy chọn |
| 1.3.4 | Dependencies với các dự án/teams khác? | List | ✓ |
| 1.3.5 | Có hạn chế nào về vendor lock-in không? | Text | Tùy chọn |

### 1.4 Các Bên Liên quan (Stakeholders)

| # | Câu hỏi | Loại | Bắt buộc |
|---|---------|------|----------|
| 1.4.1 | Liệt kê tất cả stakeholder groups và individuals? | Table (Name, Role, Contact) | ✓ |
| 1.4.2 | Ai là Project Sponsor (người chịu trách nhiệm ngân sách)? | Text | ✓ |
| 1.4.3 | Ai là Product Owner/Business Owner? | Text | ✓ |
| 1.4.4 | Ai là main point of contact cho requirements? | Text | ✓ |
| 1.4.5 | Ai có quyền phê duyệt cuối cùng? | Text | ✓ |
| 1.4.6 | Có stakeholders nào có xung đột lợi ích không? | Text | Tùy chọn |

### 1.5 Ngân sách & Timeline

| # | Câu hỏi | Loại | Bắt buộc |
|---|---------|------|----------|
| 1.5.1 | Tổng ngân sách dự án là bao nhiêu? | Currency | ✓ |
| 1.5.2 | Ngân sách có được phân bổ theo phases không? | Table | Tùy chọn |
| 1.5.3 | Deadline quan trọng (go-live, milestones)? | Date List | ✓ |
| 1.5.4 | Có penalty cho việc delayed delivery không? | Currency/Text | Tùy chọn |
| 1.5.5 | Nguồn lực team (FTE) được phân bổ? | Number | ✓ |

---

## 2. NGƯỜI DÙNG & ACTORS

### 2.1 Xác định Users/Actors

| # | Câu hỏi | Loại | Bắt buộc |
|---|---------|------|----------|
| 2.1.1 | Ai sẽ trực tiếp sử dụng hệ thống? | List | ✓ |
| 2.1.2 | Có bao nhiêu loại user/actor phân biệt? | Number | ✓ |
| 2.1.3 | Mỗi actor/user type có bao nhiêu người dùng dự kiến? | Table | ✓ |
| 2.1.4 | Ai là primary actors (sử dụng thường xuyên)? | List | ✓ |
| 2.1.5 | Ai là secondary actors (sử dụng ít thường xuyên)? | List | Tùy chọn |
| 2.1.6 | Có anonymous/guest users không? | Boolean | Tùy chọn |
| 2.1.7 | Users có phân chia theo regions/departments không? | Text | Tùy chọn |

### 2.2 Quyền hạn & Phân quyền

| # | Câu hỏi | Loại | Bắt buộc |
|---|---------|------|----------|
| 2.2.1 | Mỗi actor có những quyền hạn gì trong hệ thống? | Table (Actor, Permissions) | ✓ |
| 2.2.2 | Có cần phân quyền theo hierarchical levels không? | Boolean + Text | ✓ |
| 2.2.3 | Approval workflows yêu cầu những role nào? | List | ✓ |
| 2.2.4 | Có khái niệm "separation of duties" không? | Boolean + Text | Tùy chọn |
| 2.2.5 | Admin users có những capabilities đặc biệt nào? | List | ✓ |

### 2.3 Actors bên ngoài

| # | Câu hỏi | Loại | Bắt buộc |
|---|---------|------|----------|
| 2.3.1 | Có hệ thống bên ngoài nào tương tác với ứng dụng không? | List | ✓ |
| 2.3.2 | Có partners/vendors/customers truy cập hệ thống không? | Boolean + List | ✓ |
| 2.3.3 | External actors cần integration qua API hay UI? | List | Tùy chọn |
| 2.3.4 | Third-party systems cần những data gì? | List | Tùy chọn |

---

## 3. USE CASES & TÍNH NĂNG

### 3.1 Tổng quan Use Cases

| # | Câu hỏi | Loại | Bắt buộc |
|---|---------|------|----------|
| 3.1.1 | Liệt kê tất cả use cases/chức năng chính? | List | ✓ |
| 3.1.2 | Use cases được ưu tiên như thế nào (MoSCoW)? | Table (UC, Priority) | ✓ |
| 3.1.3 | Có use case diagram tổng quan không? | File/Link | Tùy chọn |
| 3.1.4 | Dependencies giữa các use cases? | List | ✓ |
| 3.1.5 | Use cases nào là bắt buộc vs nice-to-have? | Table | ✓ |

### 3.2 Chi tiết Use Case

| # | Câu hỏi | Loại | Bắt buộc |
|---|---------|------|----------|
| 3.2.1 | Tên use case và mô tả ngắn? | Text | ✓ |
| 3.2.2 | Actor(s) tham gia use case này? | List | ✓ |
| 3.2.3 | Preconditions trước khi use case bắt đầu? | List | ✓ |
| 3.2.4 | Postconditions sau khi use case hoàn thành? | List | ✓ |
| 3.2.5 | Main flow (happy path) mô tả chi tiết từng bước? | Text | ✓ |
| 3.2.6 | Alternative flows (các luồng thay thế)? | Text | Tùy chọn |
| 3.2.7 | Exception/error flows và cách xử lý? | Text | ✓ |
| 3.2.8 | Trigger event nào khởi tạo use case? | Text | ✓ |

### 3.3 Business Rules cho Use Cases

| # | Câu hỏi | Loại | Bắt buộc |
|---|---------|------|----------|
| 3.3.1 | Những business rules nào áp dụng cho use case này? | List | ✓ |
| 3.3.2 | Validation rules cho data input? | List | ✓ |
| 3.3.3 | Authorization rules cụ thể? | List | ✓ |
| 3.3.4 | Calculation/formula business rules? | Text | Tùy chọn |
| 3.3.5 | State transition rules (nếu có workflow states)? | List | Tùy chọn |

---

## 4. YÊU CẦU CHỨC NĂNG (FUNCTIONAL)

### 4.1 Data Input/Output

| # | Câu hỏi | Loại | Bắt buộc |
|---|---------|------|----------|
| 4.1.1 | Những data elements nào được nhập vào hệ thống? | List | ✓ |
| 4.1.2 | Nguồn dữ liệu đầu vào (users, systems, files)? | List | ✓ |
| 4.1.3 | Data output/reports được tạo ra là gì? | List | ✓ |
| 4.1.4 | Output format mong đợi (PDF, Excel, HTML, etc)? | List | ✓ |
| 4.1.5 | Data frequency (real-time, batch, on-demand)? | Text | ✓ |
| 4.1.6 | Có data fields bắt buộc vs optional không? | List | ✓ |

### 4.2 Business Logic

| # | Câu hỏi | Loại | Bắt buộc |
|---|---------|------|----------|
| 4.2.1 | Mô tả chi tiết business logic xử lý chính? | Text | ✓ |
| 4.2.2 | Có calculations/computations đặc biệt nào không? | Text | ✓ |
| 4.2.3 | Workflow processing logic (approval, routing)? | Text | ✓ |
| 4.2.4 | Notification/alerting logic khi nào触发? | List | ✓ |
| 4.2.5 | Có historical data processing không? | Boolean + Text | Tùy chọn |

### 4.3 Reports & Notifications

| # | Câu hỏi | Loại | Bắt buộc |
|---|---------|------|----------|
| 4.3.1 | Những loại reports nào cần thiết? | List | ✓ |
| 4.3.2 | Report schedule (daily, weekly, monthly)? | Table | ✓ |
| 4.3.3 | Report recipients và distribution method? | Table | ✓ |
| 4.3.4 | Report retention period? | Duration | Tùy chọn |
| 4.3.5 | Notification channels cần thiết (email, SMS, push)? | List | ✓ |
| 4.3.6 | Alert thresholds cho notifications? | List | Tùy chọn |

### 4.4 Workflow & Data Processing

| # | Câu hỏi | Loại | Bắt buộc |
|---|---------|------|----------|
| 4.4.1 | Mô tả end-to-end data workflow? | Diagram/Text | ✓ |
| 4.4.2 | Approval workflow có những bước nào? | List | ✓ |
| 4.4.3 | Data được validate ở những điểm nào? | List | ✓ |
| 4.4.4 | Có manual review/approval steps không? | Boolean + List | ✓ |
| 4.4.5 | SLA cho mỗi workflow step? | Table | Tùy chọn |

---

## 5. YÊU CẦU PHI CHỨC NĂNG (NON-FUNCTIONAL)

### 5.1 Performance

| # | Câu hỏi | Loại | Bắt buộc |
|---|---------|------|----------|
| 5.1.1 | Hệ thống phản hồi nhanh như thế nào? (VD: thao tác đơn giản dưới 2 giây, thao tác phức tạp dưới 5 giây) | Table | ✓ |
|       | [TECHNICAL] Response time tối đa cho mỗi operation type? | | |
| 5.1.2 | Hệ thống có thể xử lý bao nhiêu người dùng cùng lúc? (VD: 100 người đồng thời, 1000 người đồng thời) | Number | ✓ |
|       | [TECHNICAL] Throughput yêu cầu (transactions/giây)? | | |
| 5.1.3 | Concurrent users tối đa dự kiến? | Number | ✓ |
| 5.1.4 | Peak load scenarios và expected response? | Text | ✓ |
| 5.1.5 | Có batch processing windows không? | Text | Tùy chọn |
| 5.1.6 | Performance benchmarks từ industry? | Text | Tùy chọn |

### 5.2 Security

| # | Câu hỏi | Loại | Bắt buộc |
|---|---------|------|----------|
| 5.2.1 | Bạn muốn xác thực đăng nhập như thế nào? (VD: chỉ cần password, hoặc thêm mã OTP qua điện thoại) | List | ✓ |
|       | [TECHNICAL] Authentication method nào (SSO, MFA, password)? | | |
| 5.2.2 | Bạn muốn phân quyền user như thế nào? (VD: theo phòng ban, theo chức vụ, hay ai cũng làm được hết) | Text | ✓ |
|       | [TECHNICAL] Authorization model (RBAC, ABAC, permissions)? | | |
| 5.2.3 | Dữ liệu có cần bảo mật đặc biệt không? (VD: mã hóa dữ liệu, chỉ cho phép người có quyền xem) | List | ✓ |
|       | [TECHNICAL] Data encryption requirements (at-rest, in-transit)? | | |
| 5.2.4 | Có yêu cầu tuân thủ pháp luật nào đặc biệt không? (VD: lưu trữ dữ liệu khách hàng an toàn, bảo mật thông tin thẻ tín dụng) | List | ✓ |
|       | [TECHNICAL] Có compliance requirements nào (GDPR, HIPAA, PCI-DSS)? | | |
| 5.2.5 | Có cần ghi lại ai đã làm gì trên hệ thống không? (VD: theo dõi nhật ký hoạt động, ai đã tạo/sửa/xóa dữ liệu) | List | ✓ |
|       | [TECHNICAL] Audit logging requirements chi tiết? | | |
| 5.2.6 | Hệ thống nên tự đăng xuất sau bao lâu không sử dụng? (VD: 15 phút, 30 phút) | Number | ✓ |
|       | [TECHNICAL] Session timeout và security policies? | | |
| 5.2.7 | Password policy requirements? | List | ✓ |
| 5.2.8 | IP allowlist/blocklist cần thiết không? | Boolean + List | Tùy chọn |

### 5.3 Availability & Reliability

| # | Câu hỏi | Loại | Bắt buộc |
|---|---------|------|----------|
| 5.3.1 | Uptime SLA yêu cầu (% per year)? | Percentage | ✓ |
| 5.3.2 | Planned maintenance windows? | Schedule | Tùy chọn |
| 5.3.3 | Disaster recovery requirements (RTO, RPO)? | Number | ✓ |
| 5.3.4 | Backup frequency và retention? | Table | ✓ |
| 5.3.5 | MTBF/MTTR targets? | Number | Tùy chọn |
| 5.3.6 | Error rate tolerance? | Percentage | Tùy chọn |
| 5.3.7 | Có geographic redundancy requirements không? | Boolean + Text | Tùy chọn |

### 5.4 Scalability

| # | Câu hỏi | Loại | Bắt buộc |
|---|---------|------|----------|
| 5.4.1 | Concurrent users growth projection (1yr, 3yr, 5yr)? | Table | ✓ |
| 5.4.2 | Data volume growth rate dự kiến? | Percentage | ✓ |
| 5.4.3 | Hệ thống có cần mở rộng quy mô khi lượng người dùng tăng lên không? (VD: thêm máy chủ, nâng cấp phần cứng) | Text | Tùy chọn |
|       | [TECHNICAL] Horizontal vs vertical scaling approach? | | |
| 5.4.4 | Có geographic scaling requirements không? | Boolean + Text | Tùy chọn |

### 5.5 Compatibility

| # | Câu hỏi | Loại | Bắt buộc |
|---|---------|------|----------|
| 5.5.1 | Supported browsers và versions? | List | ✓ |
| 5.5.2 | Supported OS platforms (Windows, macOS, Linux)? | List | Tùy chọn |
| 5.5.3 | Mobile platform support (iOS, Android)? | List | Tùy chọn |
| 5.5.4 | Backward compatibility với legacy systems? | Text | Tùy chọn |
| 5.5.5 | API version compatibility policy? | Text | Tùy chọn |

---

## 6. GIAO DIỆN NGƯỜI DÙNG (UI/UX)

### 6.1 Loại & Nền tảng UI

| # | Câu hỏi | Loại | Bắt buộc |
|---|---------|------|----------|
| 6.1.1 | UI type: Web, Mobile (native/hybrid), Desktop? | List | ✓ |
| 6.1.2 | Nếu mobile: iOS, Android, hoặc cả hai? | List | Tùy chọn |
| 6.1.3 | Có Progressive Web App (PWA) requirements không? | Boolean | Tùy chọn |
| 6.1.4 | Offline functionality cần thiết không? | Boolean | Tùy chọn |

### 6.2 Screens & Navigation

| # | Câu hỏi | Loại | Bắt buộc |
|---|---------|------|----------|
| 6.2.1 | Liệt kê tất cả screens/pages chính? | List | ✓ |
| 6.2.2 | Sitemap hoặc navigation structure? | Diagram/Text | ✓ |
| 6.2.3 | User flows chính cần supported? | Diagram | ✓ |
| 6.2.4 | Có breadcrumb/page hierarchy không? | Boolean | Tùy chọn |
| 6.2.5 | Responsive layout requirements (breakpoints)? | List | ✓ |

### 6.3 Accessibility & Internationalization

| # | Câu hỏi | Loại | Bắt buộc |
|---|---------|------|----------|
| 6.3.1 | Accessibility standards (WCAG 2.1 Level)? | Level (A, AA, AAA) | ✓ |
| 6.3.2 | Supported languages (localization)? | List | ✓ |
| 6.3.3 | RTL (right-to-left) language support cần không? | Boolean | Tùy chọn |
| 6.3.4 | Date/time/number/currency format theo locale? | List | Tùy chọn |
| 6.3.5 | Screen reader compatibility? | Boolean | Tùy chọn |

### 6.4 UI/UX Design Standards

| # | Câu hỏi | Loại | Bắt buộc |
|---|---------|------|----------|
| 6.4.1 | Có brand style guide/corporate identity không? | File/Link | Tùy chọn |
| 6.4.2 | Design system/component library có sẵn không? | Text | Tùy chọn |
| 6.4.3 | Theme preferences (light/dark mode)? | List | Tùy chọn |
| 6.4.4 | Animation/motion guidelines? | Text | Tùy chọn |

---

## 7. DỮ LIỆU & STORAGE

### 7.1 Data Entities & Model

| # | Câu hỏi | Loại | Bắt buộc |
|---|---------|------|----------|
| 7.1.1 | Liệt kê tất cả main data entities? | List | ✓ |
| 7.1.2 | ER diagram hoặc data model diagram? | File/Diagram | Tùy chọn |
| 7.1.3 | Attributes cho mỗi entity? | Table | ✓ |
| 7.1.4 | Dữ liệu có mối liên hệ gì với nhau không? (VD: khách hàng có nhiều đơn hàng, một đơn hàng có nhiều sản phẩm) | List | ✓ |
|       | [TECHNICAL] Relationships giữa các entities? | | |
| 7.1.5 | Có cần tìm kiếm, sắp xếp dữ liệu nhanh không? (VD: tìm khách hàng theo tên, sắp xếp đơn hàng theo ngày) | List | Tùy chọn |
|       | [TECHNICAL] Primary keys và indexes cần thiết? | | |

### 7.2 Data Volume & Growth

| # | Câu hỏi | Loại | Bắt buộc |
|---|---------|------|----------|
| 7.2.1 | Ban đầu hệ thống cần lưu trữ bao nhiêu dữ liệu? (VD: khoảng 10,000 khách hàng, 100,000 đơn hàng) | Number + Unit | ✓ |
|       | [TECHNICAL] Initial data volume ước tính? | | |
| 7.2.2 | Dữ liệu tăng thêm bao nhiêu mỗi tháng/năm? (VD: tăng 10% mỗi năm) | Percentage | ✓ |
|       | [TECHNICAL] Data growth rate per month/year? | | |
| 7.2.3 | Có thời điểm nào lượng dữ liệu tăng đột biến không? (VD: cuối năm, mùa cao điểm) | Number | Tùy chọn |
| 7.2.4 | Dữ liệu cũ có cần lưu trữ riêng không? (VD: chuyển dữ liệu 5 năm trở lên sang lưu trữ khác) | Text | Tùy chọn |
|       | [TECHNICAL] Archiving strategy cho old data? | | |

### 7.3 Data Retention & Migration

| # | Câu hỏi | Loại | Bắt buộc |
|---|---------|------|----------|
| 7.3.1 | Dữ liệu cần được lưu trữ bao lâu? (VD: theo quy định pháp luật như 5 năm, 10 năm) | Text | ✓ |
|       | [TECHNICAL] Data retention policy theo regulations? | | |
| 7.3.2 | Có dữ liệu nào cần giữ vĩnh viễn hoặc không được xóa không? (VD: hồ sơ tài chính, hợp đồng) | Boolean + Text | Tùy chọn |
|       | [TECHNICAL] Legal hold requirements cho certain data? | | |
| 7.3.3 | Có cần chuyển dữ liệu từ hệ thống cũ sang hệ thống mới không? | Boolean + Text | ✓ |
| 7.3.4 | Migration strategy và tools? | Text | Tùy chọn |
| 7.3.5 | Data cleansing requirements trước migration? | List | Tùy chọn |

### 7.4 Backup & Recovery

| # | Câu hỏi | Loại | Bắt buộc |
|---|---------|------|----------|
| 7.4.1 | Hệ thống nên sao lưu dữ liệu với tần suất nào? (VD: mỗi ngày, mỗi tuần, hay theo thời gian thực) | Schedule | ✓ |
|       | [TECHNICAL] Backup frequency (daily, weekly, real-time)? | | |
| 7.4.2 | Sao lưu dữ liệu ở đâu? (VD: trên máy chủ công ty, hay lưu trữ đám mây) | Text | ✓ |
| 7.4.3 | Giữ bản sao lưu trong bao lâu? (VD: 30 ngày, 90 ngày, 1 năm) | Duration | ✓ |
| 7.4.4 | Có cần kiểm tra khả năng phục hồi dữ liệu định kỳ không? | Schedule | Tùy chọn |
|       | [TECHNICAL] Recovery testing frequency? | | |
| 7.4.5 | Có cần khôi phục dữ liệu về một thời điểm cụ thể không? (VD: phục hồi dữ liệu như lúc 9h sáng hôm qua) | Boolean | Tùy chọn |
|       | [TECHNICAL] Point-in-time recovery yêu cầu không? | | |

---

## 8. BUSINESS RULES & CONSTRAINTS

### 8.1 Quy tắc Nghiệp vụ

| # | Câu hỏi | Loại | Bắt buộc |
|---|---------|------|----------|
| 8.1.1 | Liệt kê tất cả business rules áp dụng? | List | ✓ |
| 8.1.2 | Business rules có được documented không? | File/Link | Tùy chọn |
| 8.1.3 | Exceptions to business rules? | List | Tùy chọn |
| 8.1.4 | Business rules có thay đổi theo thời gian không? | Boolean + Frequency | Tùy chọn |

### 8.2 Legal & Regulatory

| # | Câu hỏi | Loại | Bắt buộc |
|---|---------|------|----------|
| 8.2.1 | Industry regulations nào áp dụng? | List | ✓ |
| 8.2.2 | Data protection/privacy laws (GDPR, CCPA, etc)? | List | ✓ |
| 8.2.3 | Audit requirements từ regulatory bodies? | List | ✓ |
| 8.2.4 | Data sovereignty requirements (data phải lưu ở đâu)? | Text | ✓ |
| 8.2.5 | Certification/compliance standards cần đạt? | List | Tùy chọn |

### 8.3 Technical Constraints

| # | Câu hỏi | Loại | Bắt buộc |
|---|---------|------|----------|
| 8.3.1 | Technology stack bắt buộc hoặc restricted? | List | ✓ |
| 8.3.2 | Infrastructure constraints (cloud provider, on-prem)? | Text | ✓ |
| 8.3.3 | Integration constraints với existing systems? | List | Tùy chọn |
| 8.3.4 | Coding standards và frameworks bắt buộc? | List | Tùy chọn |

### 8.4 Resource Constraints

| # | Câu hỏi | Loại | Bắt buộc |
|---|---------|------|----------|
| 8.4.1 | Ngân sách hard cap là bao nhiêu? | Currency | ✓ |
| 8.4.2 | Timeline constraints không thể thay đổi? | Date | ✓ |
| 8.4.3 | Team size/skills constraints? | Text | ✓ |
| 8.4.4 | Vendor/partner dependencies? | List | Tùy chọn |

---

## 9. TÍCH HỢP & NGOẠI VI

### 9.1 External Systems Integration

| # | Câu hỏi | Loại | Bắt buộc |
|---|---------|------|----------|
| 9.1.1 | Hệ thống bên ngoài nào cần tích hợp? | List | ✓ |
| 9.1.2 | Mục đích của mỗi integration? | Table | ✓ |
| 9.1.3 | Data flow direction (inbound/outbound/both)? | Table | ✓ |
| 9.1.4 | Integration criticality/priority? | Table | ✓ |
| 9.1.5 | Existing integration infrastructure có thể tái sử dụng? | Text | Tùy chọn |

### 9.2 API Requirements

| # | Câu hỏi | Loại | Bắt buộc |
|---|---------|------|----------|
| 9.2.1 | Hệ thống có cần kết nối với hệ thống khác không? (VD: kết nối với hệ thống kế toán, CRM, hoặc các hệ thống hiện có) | List | ✓ |
|       | [TECHNICAL] REST API endpoints cần thiết? | | |
| 9.2.2 | SOAP/Web Services cần thiết? | Boolean + List | Tùy chọn |
| 9.2.3 | GraphQL subscriptions/queries/mutations? | List | Tùy chọn |
| 9.2.4 | API rate limiting requirements? | Number | Tùy chọn |
| 9.2.5 | API versioning policy? | Text | Tùy chọn |
| 9.2.6 | API documentation format (OpenAPI/Swagger)? | Format | Tùy chọn |

### 9.3 File Formats & Protocols

| # | Câu hỏi | Loại | Bắt buộc |
|---|---------|------|----------|
| 9.3.1 | Hệ thống cần xuất/nhập dữ liệu ở định dạng nào? (VD: Excel, PDF, Word) | List | ✓ |
|       | [TECHNICAL] File formats cần hỗ trợ (CSV, XML, JSON, PDF)? | | |
| 9.3.2 | File size limits? | Number | ✓ |
| 9.3.3 | Protocol requirements (SFTP, HTTPS, etc)? | List | ✓ |
| 9.3.4 | EDI standards nếu có (B2B integrations)? | Text | Tùy chọn |

### 9.4 Third-party Services

| # | Câu hỏi | Loại | Bắt buộc |
|---|---------|------|----------|
| 9.4.1 | Third-party services cần sử dụng (payment, SMS, email)? | List | ✓ |
| 9.4.2 | SLA commitments từ third-party vendors? | Table | Tùy chọn |
| 9.4.3 | Cost structure của third-party services? | Table | Tùy chọn |
| 9.4.4 | Fallback mechanisms khi third-party fails? | Text | Tùy chọn |

---

## 10. BẢO TRÌ & VẬN HÀNH

### 10.1 Deployment Environment

| # | Câu hỏi | Loại | Bắt buộc |
|---|---------|------|----------|
| 10.1.1 | Hệ thống chạy trên môi trường nào? (VD: máy chủ công ty, đám mây AWS/Azure/Google) | List | ✓ |
|       | [TECHNICAL] Deployment environment (Dev, Staging, Production)? | | |
| 10.1.2 | Cloud provider preference (AWS, Azure, GCP)? | Text | ✓ |
| 10.1.3 | On-premises requirement không? | Boolean | ✓ |
| 10.1.4 | Có cần dùng Docker/Kubernetes không? (VD: để deploy dễ dàng hơn, mở rộng hệ thống) | Boolean | Tùy chọn |
|       | [TECHNICAL] Containerization (Docker, Kubernetes) required? | | |
| 10.1.5 | CI/CD pipeline requirements? | Text | Tùy chọn |

### 10.2 Maintenance & Support

| # | Câu hỏi | Loại | Bắt buộc |
|---|---------|------|----------|
| 10.2.1 | Maintenance window schedule? | Schedule | ✓ |
| 10.2.2 | Support tier levels (L1, L2, L3)? | Table | ✓ |
| 10.2.3 | Bug fix SLA commitments? | Table | ✓ |
| 10.2.4 | Service desk/helpdesk integration cần thiết? | Boolean + List | Tùy chọn |
| 10.2.5 | Monitoring và alerting infrastructure? | List | ✓ |

### 10.3 Training & Documentation

| # | Câu hỏi | Loại | Bắt buộc |
|---|---------|------|----------|
| 10.3.1 | User training requirements? | Text | ✓ |
| 10.3.2 | Training delivery format (online, in-person, documentation)? | List | Tùy chọn |
| 10.3.3 | Admin/technical documentation requirements? | List | ✓ |
| 10.3.4 | End-user help documentation/User manual? | Boolean | Tùy chọn |
| 10.3.5 | API documentation cho developers? | Boolean | Tùy chọn |

### 10.4 Operations & Monitoring

| # | Câu hỏi | Loại | Bắt buộc |
|---|---------|------|----------|
| 10.4.1 | Logging requirements chi tiết? | List | ✓ |
| 10.4.2 | Application performance monitoring (APM) tools? | List | Tùy chọn |
| 10.4.3 | Health check endpoints cần thiết? | List | Tùy chọn |
| 10.4.4 | Alerting thresholds và notification channels? | Table | ✓ |
| 10.4.5 | Operational dashboards cần thiết? | List | Tùy chọn |

---

## 11. XỬ LÝ LỖI & NGOẠI LỆ (ERROR HANDLING & EXCEPTIONS)

Phần này thu thập các yêu cầu về cách hệ thống xử lý lỗi, ngoại lệ và cơ chế phục hồi khi có sự cố xảy ra.

### 11.1 Error Handling Requirements

| # | Câu hỏi | Loại | Bắt buộc |
|---|---------|------|----------|
| 11.1.1 | Hệ thống cần xử lý những loại lỗi nào (validation, system, network, data)? | List | ✓ |
| 11.1.2 | Error codes và messaging convention thống nhất? | List | ✓ |
| 11.1.3 | Có cần error code reference documentation không? | Boolean | Tùy chọn |
| 11.1.4 | Error response format chuẩn (JSON structure)? | Text | ✓ |
| 11.1.5 | Có error tracking/integration với bug tracking system không? | Boolean + Text | Tùy chọn |

### 11.2 Exception Scenarios

| # | Câu hỏi | Loại | Bắt buộc |
|---|---------|------|----------|
| 11.2.1 | Timeout thresholds cho mỗi operation type? | Table | ✓ |
| 11.2.2 | Retry policy khi gặp transient failures (attempts, backoff)? | Text | ✓ |
| 11.2.3 | Circuit breaker pattern có cần thiết không? | Boolean | Tùy chọn |
| 11.2.4 | Dead letter queue/message failed handling? | Text | Tùy chọn |
| 11.2.5 | Exception scenarios cần specific handling (payment failure, auth failure)? | List | ✓ |

### 11.3 User Feedback for Errors

| # | Câu hỏi | Loại | Bắt buộc |
|---|---------|------|----------|
| 11.3.1 | User-friendly error messages cần hiển thị cho end users? | List | ✓ |
| 11.3.2 | Technical error details có hiển thị cho users không? | Boolean | ✓ |
| 11.3.3 | Error message localization requirements? | List | Tùy chọn |
| 11.3.4 | Có guidance/suggestions khi users gặp lỗi không? | Boolean + Text | Tùy chọn |
| 11.3.5 | Notification nào được gửi khi errors xảy ra (email, SMS, dashboard)? | List | Tùy chọn |

### 11.4 Logging Requirements

| # | Câu hỏi | Loại | Bắt buộc |
|---|---------|------|----------|
| 11.4.1 | Log levels cần sử dụng (DEBUG, INFO, WARN, ERROR, FATAL)? | List | ✓ |
| 11.4.2 | Log retention period theo compliance? | Duration | ✓ |
| 11.4.3 | Sensitive data có được mask trong logs không? | Boolean + List | ✓ |
| 11.4.4 | Log storage location (cloud, on-prem, SIEM)? | Text | Tùy chọn |
| 11.4.5 | Centralized logging system có cần thiết không? | Boolean + Tool | Tùy chọn |
| 11.4.6 | Log aggregation và analysis requirements? | Text | Tùy chọn |

### 11.5 Fallback Mechanisms

| # | Câu hỏi | Loại | Bắt buộc |
|---|---------|------|----------|
| 11.5.1 | Graceful degradation scenarios khi services fail? | List | ✓ |
| 11.5.2 | Fallback data sources khi primary unavailable? | List | Tùy chọn |
| 11.5.3 | Cached data có được sử dụng khi database unavailable? | Boolean | Tùy chọn |
| 11.5.4 | Manual override/override mode có cần không? | Boolean + Text | Tùy chọn |
| 11.5.5 | System-wide failover triggers và procedures? | Text | ✓ |

---

## 12. QUẢN LÝ CẤU HÌNH (CONFIGURATION MANAGEMENT)

Phần này thu thập các yêu cầu về cách cấu hình, tùy chỉnh và quản lý các tham số hệ thống.

### 12.1 Configuration Parameters

| # | Câu hỏi | Loại | Bắt buộc |
|---|---------|------|----------|
| 12.1.1 | Các configuration parameters cần thiết cho hệ thống? | List | ✓ |
| 12.1.2 | Configuration stored ở đâu (DB, file, env vars, config server)? | List | ✓ |
| 12.1.3 | Có cần dynamic configuration changes không? | Boolean | ✓ |
| 12.1.4 | Configuration validation rules khi thay đổi? | List | Tùy chọn |
| 12.1.5 | Configuration versioning/audit trail? | Boolean | Tùy chọn |

### 12.2 Environment Settings

| # | Câu hỏi | Loại | Bắt buộc |
|---|---------|------|----------|
| 12.2.1 | Environment-specific settings (dev, staging, prod)? | List | ✓ |
| 12.2.2 | Environment promotion process (dev → staging → prod)? | Text | ✓ |
| 12.2.3 | Secrets management approach (vault, KMS, env vars)? | Text | ✓ |
| 12.2.4 | Environment variables required? | List | ✓ |
| 12.2.5 | Configuration drift detection cần thiết không? | Boolean | Tùy chọn |

### 12.3 Feature Flags

| # | Câu hỏi | Loại | Bắt buộc |
|---|---------|------|----------|
| 12.3.1 | Feature flags/toggles có cần thiết không? | Boolean | ✓ |
| 12.3.2 | Feature flag management system có sẵn không? | Text | Tùy chọn |
| 12.3.3 | Feature targeting (user segment, percentage rollout)? | List | Tùy chọn |
| 12.3.4 | Feature flag lifecycle management (enable/disable/expiry)? | Text | Tùy chọn |
| 12.3.5 | A/B testing requirements với feature flags? | Boolean + List | Tùy chọn |

### 12.4 System Customization Options

| # | Câu hỏi | Loại | Bắt buộc |
|---|---------|------|----------|
| 12.4.1 | Tenant-specific configuration (multi-tenant)? | Boolean + List | Tùy chọn |
| 12.4.2 | UI layout/theme customization per tenant/organization? | Boolean + List | Tùy chọn |
| 12.4.3 | Business rules customizable bởi admins không? | Boolean + Text | ✓ |
| 12.4.4 | Workflow customization options? | List | Tùy chọn |
| 12.4.5 | Third-party integrations configurable không? | Boolean + List | Tùy chọn |

---

## APPENDIX A: Requirements Prioritization Template

Sử dụng MoSCoW methodology:

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
- Prepared by: Requirements Engineering Team
