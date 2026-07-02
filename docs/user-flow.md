# AgentCode Framework - User Flow Documentation

## Overview

AgentCode Framework là một AI agent system giúp tự động tạo tài liệu SRS và SDD theo chuẩn IEEE. Người dùng chỉ cần gõ command đơn giản, hệ thống sẽ spawn nhiều agents song song để tạo documentation.

---

## User Commands

| Command | Mô tả |
|---------|--------|
| `/create-srs [project_name]` | Tạo SRS document |
| `/create-sdd [project_name]` | Tạo SDD document |
| `/generate-docs [project_name]` | Tạo cả SRS và SDD |
| `/interview-stakeholder` | Phỏng vấn stakeholder |

---

## ⚠️ QUAN TRỌNG: Phỏng vấn BẮT BUỘC

```
╔══════════════════════════════════════════════════════════════════════════════╗
║                                                                              ║
║   ⚠️  KHÔNG THỂ BỎ QUA PHỎNG VẤN!                                         ║
║                                                                              ║
║   Trước khi tạo SRS, hệ thống BẮT BUỘC phải phỏng vấn bạn.              ║
║   Điều này đảm bảo tài liệu CHÍNH XÁC với yêu cầu thực tế.            ║
║                                                                              ║
║   Bạn sẽ được hỏi 23 câu hỏi, từng câu một.                              ║
║   Sau mỗi câu, bạn được hỏi "Có hài lòng không?"                        ║
║   Cuối cùng, bạn cần gõ "XÁC NHẬN" để tiếp tục.                        ║
║                                                                              ║
╚══════════════════════════════════════════════════════════════════════════════╝
```

---

## User Flow: `/create-srs MyProject`

### Step 1: User Types Command

```
User: /create-srs "Hệ thống Quản lý Bán hàng"
```

### Step 2: Giới thiệu + Xác nhận bắt đầu

```
╔══════════════════════════════════════════════════════════════════════════════╗
║  👋 Xin chào! Tôi sẽ giúp bạn thu thập yêu cầu cho dự án.              ║
║                                                                              ║
║  QUY TẮC:                                                                   ║
║  • Tôi sẽ hỏi TỪNG câu một                                                ║
║  • Với mỗi câu, tôi cần bạn trả lời ĐẦY ĐỦ và CỤ THỂ                 ║
║  • Nếu câu trả lời còn thiếu, tôi sẽ đào sâu thêm                       ║
║  • Sau mỗi câu trả lời, bạn sẽ được hỏi "Có hài lòng không?"          ║
║  • Bạn có thể bổ sung thêm bất cứ lúc nào                                ║
║                                                                              ║
║  Bắt đầu nhé? (Gõ 'bắt đầu' hoặc 'có' để tiếp tục)                     ║
╚══════════════════════════════════════════════════════════════════════════════╝

User: bắt đầu
```

### Step 3: PHỎNG VẤN 23 CÂU HỎI

```
╔══════════════════════════════════════════════════════════════════════════════╗
║  STEP 2: THÔNG TIN DỰ ÁN CƠ BẢN                                        ║
╠══════════════════════════════════════════════════════════════════════════════╣
║                                                                              ║
║  📝 Câu hỏi 1/23: Tên dự án của bạn là gì?                             ║
║                                                                              ║
║  Ví dụ: "Hệ thống Quản lý Bán hàng", "App Đặt món ăn", "Phần mềm CRM" ║
║                                                                              ║
╚══════════════════════════════════════════════════════════════════════════════╝

User: Hệ thống Quản lý Bán hàng

📝 Câu hỏi 2/23: Mô tả ngắn gọn (2-3 câu) hệ thống bạn muốn xây dựng.  ║
                                                                              ║
║  Gợi ý trả lời:                                                           ║
║  • Hệ thống làm gì?                                                        ║
║  • Ai sử dụng?                                                             ║
║  • Mục đích chính?                                                         ║
║                                                                              ║
║  Ví dụ: "Hệ thống quản lý bán hàng cho cửa hàng với 5 nhân viên..."    ║
╚══════════════════════════════════════════════════════════════════════════════╝

User: quản lý bán hàng

⚠️ Agent Probing (vì câu trả lời VAGUE):
"
Bạn nói 'quản lý bán hàng' - cụ thể là:
a) Tạo đơn hàng?
b) Quản lý tồn kho?
c) Tính tiền/thanh toán?
d) In hóa đơn?
e) Xem báo cáo?
f) Quản lý khách hàng?

Bạn muốn tất cả hay chỉ một số tính năng?
"

User: a, b, c, e

👆 Satisfaction Check:
"☝️ Tạm ổn rồi. Mô tả hệ thống:
- Tạo đơn hàng
- Quản lý tồn kho
- Tính tiền/thanh toán
- Xem báo cáo

Bạn có hài lòng với mô tả này không? Có cần bổ sung gì không?"
```

### Step 4: Tiếp tục các câu hỏi tiếp theo

```
📝 Câu hỏi 3/23: Hệ thống này thay thế hay bổ sung cho hệ thống hiện có?

Trả lời một trong:
A) HOÀN TOÀN MỚI - Xây từ đầu
B) THAY THẾ - Có hệ thống cũ, sẽ bỏ hoàn toàn
C) BỔ SUNG - Có hệ thống cũ, thêm tính năng mới
D) NÂNG CẤP - Chuyển đổi công nghệ mới

User: A

📝 Câu hỏi 4/23: Vấn đề hoặc cơ hội kinh doanh nào dự án này giải quyết?
User: (trả lời hoặc bỏ qua)

☝️ STEP 3: NGƯỜI DÙNG & ACTORS

📝 Câu hỏi 5/23: Ai sẽ sử dụng hệ thống này?

Liệt kê tất cả nhóm người dùng (actors).

Format:
| Nhóm người dùng | Mô tả ngắn |
| Admin          | Quản lý hệ thống |
| Nhân viên bán hàng | Tạo đơn hàng |
| Khách hàng     | Đặt hàng online |

User: Admin, Staff, Customer

⚠️ Agent Probing:
"Với mỗi nhóm người dùng, tôi cần biết:
1. Admin: Làm gì? → Quản lý users, xem báo cáo, cấu hình hệ thống
2. Staff: Làm gì? → Tạo đơn hàng, quản lý kho
3. Customer: Làm gì? → Xem sản phẩm, đặt hàng

Đúng không?"
```

### Step 5: STEP 11 - XÁC NHẬN CUỐI CÙNG

```
╔══════════════════════════════════════════════════════════════════════════════╗
║  📋 KIỂM TRA CUỐI CÙNG - XÁC NHẬN YÊU CẦU                                 ║
╠══════════════════════════════════════════════════════════════════════════════╣
║                                                                              ║
║  1. PROJECT INFO                                                             ║
║     Tên: Hệ thống Quản lý Bán hàng                                        ║
║     Mô tả: Tạo đơn hàng, quản lý tồn kho, tính tiền, báo cáo           ║
║     Hệ thống cũ: Hoàn toàn mới                                            ║
║                                                                              ║
║  2. USERS & ACTORS (3 actors)                                              ║
║     Admin: Quản lý hệ thống, full access                                  ║
║     Staff: Tạo đơn hàng, quản lý kho                                       ║
║     Customer: Xem sản phẩm, đặt hàng                                       ║
║                                                                              ║
║  3. FEATURES (8 tính năng)                                                 ║
║     Must: 5 | Should: 2 | Could: 1                                         ║
║                                                                              ║
║  4. DATA ENTITIES (6 entities)                                             ║
║     User, Product, Order, Payment, Inventory, Report                        ║
║                                                                              ║
║  5. TECHNICAL                                                               ║
║     Platform: Web + Mobile Responsive                                       ║
║     Performance: < 2s response                                              ║
║     Security: Password + OTP                                                ║
║                                                                              ║
║  6. Open Questions: 2 câu tùy chọn chưa trả lời                          ║
║                                                                              ║
╠══════════════════════════════════════════════════════════════════════════════╣
║                                                                              ║
║  ✅ Nếu mọi thứ đã CHÍNH XÁC, gõ "XÁC NHẬN" để tạo SRS                  ║
║  📝 Nếu cần SỬA, cho tôi biết câu hỏi số mấy cần sửa                    ║
║  ➕ Nếu muốn BỔ SUNG, cho tôi biết thêm gì                                ║
║                                                                              ║
╚══════════════════════════════════════════════════════════════════════════════╝

User: XÁC NHẬN
```

### Step 6: Tạo Requirements Summary

```
╔══════════════════════════════════════════════════════════════════════════════╗
║  ✅ PHỎNG VẤN HOÀN THÀNH!                                                 ║
║                                                                              ║
║  📄 Đã tạo: docs/HeThongQuanLyBanHang/requirements-summary.md              ║
║                                                                              ║
║  Tiếp tục tạo SRS...                                                       ║
╚══════════════════════════════════════════════════════════════════════════════╝
```

### Step 7: Tạo SRS với uc-diagram-agents Song Song

```
╔══════════════════════════════════════════════════════════════════════════════╗
║  🔄 ĐANG TẠO SRS                                                          ║
║                                                                              ║
║  SRS Agent đọc requirements-summary.md                                     ║
║                                                                              ║
║  Spawn NHIỀU uc-diagram-agents song song:                                 ║
║  @uc-diagram-agent (UC01: Login) &                                        ║
║  @uc-diagram-agent (UC02: Register) &                                     ║
║  @uc-diagram-agent (UC03: ViewProduct) &                                 ║
║  @uc-diagram-agent (UC04: PlaceOrder) &                                   ║
║  @uc-diagram-agent (UC05: Payment) &                                      ║
║  ...                                                                       ║
║  wait                                                                       ║
║                                                                              ║
║  [████████████████████░░░░░░░░░░░░░░] 60%                               ║
╚══════════════════════════════════════════════════════════════════════════════╝
```

### Step 8: Verification + Hoàn thành

```
╔══════════════════════════════════════════════════════════════════════════════╗
║  ✅ SRS HOÀN THÀNH!                                                        ║
║                                                                              ║
║  Project: Hệ thống Quản lý Bán hàng                                       ║
║  Version: v1.0.0                                                           ║
║                                                                              ║
║  📄 SRS_HTTT_BanHang_v1.0.0.md                                             ║
║  📁 diagrams/uc-01/ (6 files)                                             ║
║  📁 diagrams/uc-02/ (6 files)                                             ║
║  📁 diagrams/uc-03/ (6 files)                                             ║
║  📁 diagrams/uc-04/ (6 files)                                             ║
║  📁 diagrams/uc-05/ (6 files)                                             ║
║  📁 diagrams/context-diagram.puml                                          ║
║  📁 diagrams/entity-relationship.puml                                     ║
║                                                                              ║
║  Tiếp theo: /create-sdd "Hệ thống Quản lý Bán hàng"                      ║
╚══════════════════════════════════════════════════════════════════════════════╝
```

---

## User Flow: `/create-sdd MyProject`

### SDD yêu cầu SRS đã hoàn thành

```
User: /create-sdd "Hệ thống Quản lý Bán hàng"

╔══════════════════════════════════════════════════════════════════════════════╗
║  ✅ XÁC MINH SRS                                                          ║
║                                                                              ║
║  ✓ SRS_HTTT_BanHang_v1.0.0.md tồn tại                                    ║
║  ✓ requirements-summary.md tồn tại                                        ║
║  ✓ User đã xác nhận đầy đủ                                               ║
║                                                                              ║
║  Tiếp tục SDD...                                                           ║
╚══════════════════════════════════════════════════════════════════════════════╝
```

### SDD Agent làm việc

```
╔══════════════════════════════════════════════════════════════════════════════╗
║  🔄 ĐANG TẠO SDD                                                          ║
║                                                                              ║
║  SDD Agent đọc SRS và xác định components từ features                    ║
║                                                                              ║
║  Spawn NHIỀU component-agents song song:                                   ║
║  @component-agent (Auth) &                                                 ║
║  @component-agent (Product) &                                              ║
║  @component-agent (Order) &                                                ║
║  @component-agent (Payment) &                                              ║
║  @component-agent (Inventory) &                                            ║
║  @component-agent (Report) &                                               ║
║  wait                                                                       ║
║                                                                              ║
║  [████████████████████░░░░░░░░░░░░░░] 60%                               ║
╚══════════════════════════════════════════════════════════════════════════════╝
```

---

## User Flow: `/generate-docs MyProject` (Cả hai)

```
User: /generate-docs "Hệ thống Quản lý Bán hàng"

┌─────────────────────────────────────────────────────────────┐
│  1. Phỏng vấn BẮT BUỘC (như trên)                       │
│  2. User gõ "XÁC NHẬN"                                   │
│  3. requirements-summary.md được tạo                     │
└─────────────────────────────────────────────────────────────┘
                          │
                          ▼
┌─────────────────────────────────────────────────────────────┐
│  4. SRS + SDD chạy SONG SONG:                            │
│                                                             │
│  SRS Agent path:              SDD Agent path:              │
│  - uc-diagram-agents         - component-agents           │
│  - srs-writer               - db-agent + api-agent       │
│                              - sdd-writer                  │
└─────────────────────────────────────────────────────────────┘
                          │
                          ▼
╔══════════════════════════════════════════════════════════════════════╗
║  ✅ TẤT CẢ TÀI LIỆU HOÀN THÀNH!                                  ║
║                                                                      ║
║  📄 SRS_HTTT_BanHang_v1.0.0.md                                   ║
║  📄 SDD_HTTT_BanHang_v1.0.0.md                                   ║
║  📄 requirements-summary.md                                        ║
║  📁 diagrams/ (50+ files)                                         ║
║  📁 db/schema.sql                                                 ║
║  📁 api/openapi.yaml                                              ║
╚══════════════════════════════════════════════════════════════════════╝
```

---

## Luồng Phỏng vấn Chi tiết (23 câu hỏi)

```
BẮT ĐẦU
   │
   ▼
┌──────────────────────────────────────────────────────────────┐
│  STEP 1: GIỚI THIỆU (Không hỏi)                           │
│  - Agent giới thiệu quy tắc                                 │
│  - User gõ "bắt đầu"                                        │
└──────────────────────────────────────────────────────────────┘
   │
   ▼
┌──────────────────────────────────────────────────────────────┐
│  STEP 2: THÔNG TIN DỰ ÁN (4 câu)                          │
│  - Câu 1: Tên dự án [BẮT BUỘC]                            │
│  - Câu 2: Mô tả hệ thống [BẮT BUỘC]                      │
│  - Câu 3: Hệ thống cũ? [BẮT BUỘC]                         │
│  - Câu 4: Vấn đề kinh doanh [TÙY CHỌN]                     │
└──────────────────────────────────────────────────────────────┘
   │
   ▼
┌──────────────────────────────────────────────────────────────┐
│  STEP 3: NGƯỜI DÙNG & ACTORS (3 câu)                      │
│  - Câu 5: Ai sử dụng? [BẮT BUỘC]                          │
│  - Câu 6: Quyền hạn? [BẮT BUỘC]                            │
│  - Câu 7: Guest users? [TÙY CHỌN]                          │
└──────────────────────────────────────────────────────────────┘
   │
   ▼
┌──────────────────────────────────────────────────────────────┐
│  STEP 4: TÍNH NĂNG & MOSCOW (2 câu)                        │
│  - Câu 8: Danh sách tính năng [BẮT BUỘC]                  │
│  - Câu 9: Phân loại MoSCoW [BẮT BUỘC]                     │
└──────────────────────────────────────────────────────────────┘
   │
   ▼
┌──────────────────────────────────────────────────────────────┐
│  STEP 5: DỮ LIỆU (2 câu)                                  │
│  - Câu 10: Entities [BẮT BUỘC]                             │
│  - Câu 11: Quy mô dữ liệu [TÙY CHỌN]                       │
└──────────────────────────────────────────────────────────────┘
   │
   ▼
┌──────────────────────────────────────────────────────────────┐
│  STEP 6: NỀN TẢNG (2 câu)                                  │
│  - Câu 12: Nền tảng truy cập [BẮT BUỘC]                   │
│  - Câu 13: Yêu cầu giao diện [TÙY CHỌN]                   │
└──────────────────────────────────────────────────────────────┘
   │
   ▼
┌──────────────────────────────────────────────────────────────┐
│  STEP 7: TÍCH HỢP (2 câu)                                 │
│  - Câu 14: Hệ thống khác? [TÙY CHỌN]                      │
│  - Câu 15: Xuất/Nhập dữ liệu? [TÙY CHỌN]                 │
└──────────────────────────────────────────────────────────────┘
   │
   ▼
┌──────────────────────────────────────────────────────────────┐
│  STEP 8: NON-FUNCTIONAL (4 câu)                           │
│  - Câu 16: Performance [BẮT BUỘC]                        │
│  - Câu 17: Số người dùng đồng thời [BẮT BUỘC]           │
│  - Câu 18: Bảo mật dữ liệu [BẮT BUỘC]                    │
│  - Câu 19: Uptime? [TÙY CHỌN]                              │
└──────────────────────────────────────────────────────────────┘
   │
   ▼
┌──────────────────────────────────────────────────────────────┐
│  STEP 9: SECURITY (2 câu)                                  │
│  - Câu 20: Đăng nhập? [BẮT BUỘC]                          │
│  - Câu 21: Tuân thủ pháp luật? [TÙY CHỌN]                 │
└──────────────────────────────────────────────────────────────┘
   │
   ▼
┌──────────────────────────────────────────────────────────────┐
│  STEP 10: BUSINESS RULES (2 câu)                           │
│  - Câu 22: Quy tắc tự động? [TÙY CHỌN]                    │
│  - Câu 23: Ngân sách/thời gian? [TÙY CHỌN]                │
└──────────────────────────────────────────────────────────────┘
   │
   ▼
┌──────────────────────────────────────────────────────────────┐
│  STEP 11: XÁC NHẬN CUỐI CÙNG                              │
│  - Hiển thị bảng TỔNG HỢP                                 │
│  - User gõ "XÁC NHẬN"                                      │
└──────────────────────────────────────────────────────────────┘
   │
   ▼
┌──────────────────────────────────────────────────────────────┐
│  TẠO REQUIREMENTS SUMMARY                                  │
│  Tiếp tục SRS/SDD                                          │
└──────────────────────────────────────────────────────────────┘
```

---

## Time Comparison

| Task | Manual Time | With AgentCode (có phỏng vấn) |
|------|-------------|-------------------------------|
| Phỏng vấn thu thập yêu cầu | 0 (bị bỏ qua) | ~25-45 phút |
| SRS (5 UCs) | ~2 hours | ~2-3 phút |
| SDD (6 components) | ~4 hours | ~2-3 phút |
| Diagrams | ~3 hours | ~1-2 phút |
| **Total** | **~9 hours** (thường phải sửa lại) | **~30-50 phút** |

**ROI:** Đầu tư 30-45 phút phỏng vấn → Tiết kiệm 2-4 giờ sửa lại tài liệu!

---

## Summary: What User Does vs What System Does

| User Action | System Action |
|-------------|---------------|
| Type `/create-srs MyProject` | Chào và giới thiệu quy tắc |
| Gõ "bắt đầu" | Bắt đầu phỏng vấn |
| Trả lời 23 câu hỏi | Hỏi từng câu + đào sâu khi vague |
| Trả lời satisfaction check | Đảm bảo đầy đủ thông tin |
| Gõ "XÁC NHẬN" | Tạo requirements-summary.md |
| Đợi | Spawn 18 agents in parallel |
| Nhận kết quả | Generate 50+ files với quality gates |
