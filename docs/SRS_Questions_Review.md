# SRS Questions Catalogue - Review Report

**Document:** `SRS_Questions_Catalogue.md`  
**Review Date:** 2026-03-23  
**Reviewer:** Requirements Engineering Review

---

## 1. ĐÁNH GIÁ TỔNG QUAN

| Tiêu chí | Kết quả | Ghi chú |
|----------|---------|---------|
| **1. LUỒNG CÂU HỎI (Flow)** | ✅ PASS | Thứ tự hợp lý: Project Info → Users → Use Cases → Features → NFR → UI → Data → Business → Integration → Maintenance |
| **2. TÍNH ĐẦY ĐỦ (Completeness)** | ⚠️ PASS WITH CONCERNS | Thiếu một số câu hỏi quan trọng: Error Handling, Configuration Management, Audit Trail Review |
| **3. TÍNH KHẢ DỤNG (Usability)** | ⚠️ PASS WITH CONCERNS | Một số câu hỏi kỹ thuật quá mức cho business user |
| **4. SỰ NHẤT QUÁN (Consistency)** | ⚠️ PASS WITH CONCERNS | Có lỗi typo và format không nhất quán |
| **5. GỢI Ý TRẢ LỜI (Answer Quality)** | ⚠️ PASS WITH CONCERNS | Thiếu examples và default values |
| **6. MỐI LIÊN KẾT (Cross-references)** | ⚠️ PASS WITH CONCERNS | Có sự trùng lặp giữa một số danh mục |

### KẾT QUẢ TỔNG THỂ: ⚠️ **PASS WITH CONCERNS**

---

## 2. VẤN ĐỀ CỤ THỂ CẦN SỬA

### 2.1 LỖI TYPO

| # | Vị trí | Lỗi | Sửa thành |
|---|--------|-----|------------|
| 1 | 7.1 header | `Lo_type` | `Loại` |
| 2 | 9.4 header | `Lo_type` | `Loại` |
| 3 | 10.2.1 | `Wartung` (German) | `Maintenance` |
| 4 | 1.3.3 | `future phases cần thiết kế预留` | `future phases cần thiết kế trước (reserved)` |
| 5 | 4.2.4 | `khi nào触发` | `khi nào trigger` |

### 2.2 CÂU HỎI KỸ THUẬT QUÁ MỨC CHO BUSINESS USER

| # | Câu hỏi | Vấn đề | Đề xuất |
|---|---------|--------|---------|
| 1 | 5.2.1 `Authentication method nào (SSO, MFA, password)?` | Business user có thể không biết SSO, MFA là gì | Thêm gợi ý: "VD: Đăng nhập đơn giản (username/password), Đăng nhập công ty (SSO qua Microsoft/Google), Xác thực 2 bước (MFA)" |
| 2 | 5.2.2 `Authorization model (RBAC, ABAC, permissions)?` | RBAC, ABAC là khái niệm kỹ thuật | Đổi thành: "Phân quyền theo vai trò (vd: Admin, Manager, User) hay theo đặc tính cụ thể (vd: chỉ được sửa dữ liệu phòng mình)?" |
| 3 | 5.5.5 `API version compatibility policy?` | Quá kỹ thuật | Đổi thành: "Khi cập nhật hệ thống, phiên bản cũ của ứng dụng/app cũ có cần hoạt động không?" |
| 4 | 8.3.4 `Coding standards và frameworks bắt buộc?` | Coding standards không phải câu hỏi cho business | Chuyển sang phần technical constraints hoặc loại bỏ khỏi business requirements |
| 5 | 9.2.3 `GraphQL subscriptions/queries/mutations?` | Quá kỹ thuật | Thay bằng: "Có cần API để ứng dụng bên thứ ba kết nối không? Loại API nào?" |
| 6 | 9.3.4 `EDI standards nếu có (B2B integrations)?` | EDI quá chuyên môn | Thêm giải thích: "EDI (Electronic Data Interchange) - chuẩn trao đổi dữ liệu điện tử với đối tác B2B" |

### 2.3 CÂU HỎI THIẾU - CẦN THÊM

| # | Danh mục | Câu hỏi cần thêm | Lý do |
|---|----------|------------------|-------|
| 1 | **Error Handling** | `Hệ thống cần xử lý lỗi như thế nào khi có sự cố? Có message mặc định cho user không?` | Thiếu section về error handling toàn cục |
| 2 | **Error Handling** | `Có cần custom error codes không?` | Quan trọng cho debugging và support |
| 3 | **Configuration** | `Có cấu hình nào cần thay đổi được bởi admin mà không cần deploy lại không?` | Quan trọng cho operational flexibility |
| 4 | **Configuration** | `Feature flags/toggles có cần thiết không?` | Phổ biến trong phát triển hiện đại |
| 5 | **Audit** | `Audit logs có cần reviewed định kỳ không? Ai review?` | Compliance requirement thường bị bỏ qua |
| 6 | **Audit** | `Logs được lưu ở đâu và trong bao lâu?` | Bổ sung cho 5.2.5 Audit logging |
| 7 | **Security** | `Data masking/anonymization có cần không?` | Quan trọng cho PII protection |
| 8 | **Security** | `Có yêu cầu penetration testing không?` | Security validation |
| 9 | **UX/UI** | `Có user testing/beta testing requirements không?` | UX validation |
| 10 | **UX/UI** | `Prototype/MVP có cần không?` | Phổ biến trong agile development |
| 11 | **Data** | `Data encryption keys được quản lý như thế nào?` | Security implementation detail |
| 12 | **Business** | `Có yêu cầu về SLA/contract với khách hàng không?` | Legal requirement thường bị bỏ qua |
| 13 | **Integration** | `Webhook/realtime notification có cần không?` | Integration pattern phổ biến |
| 14 | **Maintenance** | `Application health dashboard có cần không?` | Operations visibility |

---

## 3. VẤN ĐỀ TRÙNG LẶP (CROSS-REFERENCE)

| # | Câu hỏi A | Câu hỏi B | Vấn đề | Đề xuất |
|---|-----------|-----------|--------|---------|
| 1 | 2.3.1 `Hệ thống bên ngoài nào tương tác?` | 9.1.1 `Hệ thống bên ngoài nào cần tích hợp?` | Trùng lặp về external systems | Gộp thành 1 câu, reference sang section 9 |
| 2 | 4.1.2 `Nguồn dữ liệu đầu vào` | 7.1.1 `Liệt kê tất cả main data entities` | Liên quan chặt chẽ, có thể trùng lặp | Thêm reference: "Xem thêm 7.1.1" |
| 3 | 5.2.5 `Audit logging requirements` | 10.4.1 `Logging requirements chi tiết` | Audit log vs general logging | Tách rõ: Audit log (security) vs Application log (operations) |
| 4 | 4.3.5 `Notification channels` | 10.4.4 `Alerting thresholds` | Notifications vs Alerts | Có overlap, nên thêm reference |
| 5 | 7.4 `Backup & Recovery` | 5.3.3 `Disaster recovery requirements` | Liên quan nhưng khác góc độ | Cần cross-reference rõ ràng |

---

## 4. ĐỀ XUẤT CẢI TIẾN

### 4.1 CẢI TIẾN VỀ FORMAT

**Thống nhất Answer Type Notation:**
```
Hiện tại: Boolean + Text, Table, List, Number + Unit
Đề xuất: Thêm prefix rõ ràng hơn
  - [Boolean] Có/Không
  - [Text] Mô tả ngắn
  - [List] Danh sách (mỗi item một dòng)
  - [Table: Col1, Col2] Bảng với các cột cụ thể
  - [Number] Số cụ thể
  - [Currency] Số tiền (VD: 100,000,000 VND)
  - [Date] Ngày tháng (VD: 2024-12-31)
  - [Duration] Thời gian (VD: 30 phút, 2 giờ)
  - [Percentage] Phần trăm (VD: 99.9%)
  - [File/Link] Đính kèm file hoặc link
```

### 4.2 THÊM EXAMPLE ANSWERS

| Câu hỏi | Example Answer |
|---------|---------------|
| 1.1.2 | "Hệ thống quản lý bán hàng trực tuyến cho phép khách hàng đặt hàng, thanh toán và theo dõi đơn hàng" |
| 5.1.1 | "Trang chủ: < 2 giây, Tìm kiếm: < 1 giây, Thanh toán: < 5 giây" |
| 5.3.1 | "99.9% uptime (tương đương downtime tối đa 8.7 giờ/năm)" |
| 5.2.7 | "Tối thiểu 8 ký tự, có chữ hoa, chữ thường, số, ký tự đặc biệt, hết hạn sau 90 ngày" |
| 9.3.1 | "CSV, XML, JSON, PDF, Excel (.xlsx)" |

### 4.3 CẢI TIẾN VỀ STRUCTURE

**Thêm Quick-Start Section:**
```
## QUICK START GUIDE
Cho dự án nhỏ (< 3 tháng), chỉ cần trả lời các câu hỏi có ✓ (bắt buộc)
```

**Thêm Dependencies Notes:**
```
## 3.1.4 Dependencies giữa các use cases?
Note: Một số use cases có thể phụ thuộc vào use cases khác hoặc cần được thực hiện trước/sau
```

**Tách riêng Technical vs Business Questions:**
```
### SECTION X.X: Technical Requirements (For IT/Technical Stakeholders only)
```

### 4.4 CẢI TIẾN VỀ USABILITY

1. **Thêm glossary terms mở rộng** trong Appendix B:
   - MFA, SSO, RBAC, ABAC
   - API, REST, GraphQL, SOAP
   - EDI, SFTP
   - MTBF, MTTR, RTO, RPO
   - Containerization, CI/CD

2. **Thêm decision tree** cho một số câu hỏi phức tạp:
   ```
   5.2.1 Authentication Method?
   ├── Ứng dụng nội bộ công ty → SSO (Single Sign-On)
   ├── Ứng dụng khách hàng → MFA (Multi-Factor Authentication)
   └── Cả hai → SSO + MFA
   ```

3. **Thêm visual markers** cho độ khó:
   ```
   [✓ Bắt buộc] [○ Tùy chọn] [⚠️ Cần tư vấn kỹ thuật]
   ```

---

## 5. ĐÁNH GIÁ CHI TIẾT THEO DANH MỤC

### Điểm số: 1-10 (10 = xuất sắc, 5 = trung bình, <5 = cần cải thiện)

| # | Danh mục | Điểm | Nhận xét |
|---|----------|------|----------|
| 1 | **1. THÔNG TIN DỰ ÁN CƠ BẢN** | **8/10** | Đầy đủ, logic. Thiếu câu hỏi về project risks. |
| 2 | **2. NGƯỜI DÙNG & ACTORS** | **8/10** | Tốt, có phân biệt primary/secondary actors. Có external actors. |
| 3 | **3. USE CASES & TÍNH NĂNG** | **7/10** | Cấu trúc UC tốt (precondition, postcondition, main flow, alternative, exception). Thiếu acceptance criteria. |
| 4 | **4. YÊU CẦU CHỨC NĂNG** | **7/10** | Data I/O, Business Logic, Reports đều có. Thiếu Error Handling section. |
| 5 | **5. YÊU CẦU PHI CHỨC NĂNG** | **8/10** | Performance, Security, Availability, Scalability, Compatibility đều tốt. Thiếu Maintainability. |
| 6 | **6. GIAO DIỆN NGƯỜI DÙNG** | **7/10** | UI type, Screens, Accessibility có. Thiếu UX research/testing requirements. |
| 7 | **7. DỮ LIỆU & STORAGE** | **8/10** | ER diagram, Data volume, Retention, Backup đều tốt. |
| 8 | **8. BUSINESS RULES & CONSTRAINTS** | **7/10** | Legal, Technical, Resource constraints đều có. Thiếu Policy/Governance. |
| 9 | **9. TÍCH HỢP & NGOẠI VI** | **7/10** | External systems, API, File formats, Third-party đều tốt. |
| 10 | **10. BẢO TRÌ & VẬN HÀNH** | **7/10** | Deployment, Maintenance, Training, Monitoring đều có. Có lỗi "Wartung". |

---

## 6. PRIORITY MATRIX - SỬA THEO THỨ TỰ

### HIGH PRIORITY (Sửa ngay - ảnh hưởng chất lượng tài liệu)

| # | Issue | Lý do |
|---|-------|-------|
| 1 | 7.1 & 9.4 typo `Lo_type` | Lỗi syntax, ảnh hưởng parsing |
| 2 | 10.2.1 `Wartung` | Tiếng Đức sai trong tài liệu Tiếng Việt |
| 3 | Thiếu Error Handling questions | Gap lớn trong requirements |
| 4 | Thiếu Configuration Management | Quan trọng cho operations |
| 5 | 5.2.2 RBAC/ABAC quá kỹ thuật | Business user không hiểu |

### MEDIUM PRIORITY (Sửa trong phiên bản tiếp theo)

| # | Issue |
|---|-------|
| 1 | Thêm example answers |
| 2 | Thống nhất Answer Type notation |
| 3 | Fix cross-references giữa 2.3 và 9.1 |
| 4 | Thêm glossary terms (MFA, SSO, RBAC, etc.) |
| 5 | 1.3.3 Chinese character `预留` |

### LOW PRIORITY (Cải tiến từ từ)

| # | Issue |
|---|-------|
| 1 | Thêm Quick-Start guide |
| 2 | Thêm decision trees |
| 3 | Thêm visual markers cho độ khó |
| 4 | Tách riêng Technical vs Business sections |

---

## 7. KẾT LUẬN

**Điểm tổng thể: 7.3/10**

**Điểm mạnh:**
- Cấu trúc phân loại rõ ràng, logic
- Độ phủ sóng tốt về các khía cạnh requirements
- Sử dụng MoSCoW prioritization tốt
- Có Appendix glossary hữu ích

**Cần cải thiện:**
- Sửa 5 lỗi typo/format
- Thêm Error Handling và Configuration Management sections
- Giảm ngôn ngữ kỹ thuật cho business user
- Thêm example answers
- Fix cross-references

**Khuyến nghị:**
- Phát hành phiên bản 1.1 với các HIGH priority fixes
- Xem xét tách thành 2 phiên bản: Business Requirements Questionnaire và Technical Requirements Checklist

---

**Document Info:**
- Review Version: 1.0
- Review Date: 2026-03-23
- Status: Ready for Revision
