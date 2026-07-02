---
name: requirements-gathering
description: |
  Thu thập yêu cầu phần mềm bằng cách phỏng vấn CHI TIẾT từng câu hỏi.
  BẮT BUỘC người dùng trả lời đầy đủ TRƯỚC KHI chuyển sang câu hỏi tiếp theo.
  KHÔNG CHO PHÉP bỏ qua - nếu câu trả lời chưa đủ, ĐÀO SÂU cho đến khi user XÁC NHẬN hài lòng.

  SỬ DỤNG KHI:
  - Người dùng muốn thu thập yêu cầu cho dự án mới
  - Cần phỏng vấn stakeholders bằng câu hỏi cụ thể

  QUAN TRỌNG: PHẢI sử dụng tool AskUserQuestion cho TẤT CẢ các câu hỏi interview.
version: "3.2.0"
allowed-tools: ["Read", "Write", "Edit", "Glob", "AskUserQuestion"]
mode: false
---

# Requirements Gathering Skill v3.2.0

## Nguyên tắc BẮT BUỘC

### 0. CÁCH HỎI — AskUserQuestion CHO LỰA CHỌN, PROSE CHO NHẬP TỰ DO

> ⚠️ **QUAN TRỌNG — đọc kỹ trước khi dùng:** Tool `AskUserQuestion` **bắt buộc mỗi câu hỏi có 2–4 `options`**.
> Tool **tự động thêm một lựa chọn "Other"** để user gõ tự do. **KHÔNG có chế độ chỉ-nhập-text.**
> Vì vậy:
> - **Câu hỏi LỰA CHỌN** (chọn từ danh sách: MoSCoW, platform, sync mode, yes/no…) → dùng `AskUserQuestion` với `options`.
> - **Câu hỏi MỞ** (tên dự án, mô tả, liệt kê features/entities, điền bảng…) → **hỏi bằng prose** (text thường) và để user gõ trả lời. KHÔNG ép vào `AskUserQuestion` rỗng options.

```
✅ Câu LỰA CHỌN → AskUserQuestion (có options)
✅ Câu MỞ      → prose: "📝 Câu X/35: Mô tả ngắn gọn hệ thống bạn muốn xây dựng?"
```

### AskUserQuestion — chữ ký ĐÚNG

> Các khối code phía dưới trong skill này là **minh họa**. Khi chạy thực tế, hãy gọi tool thật
> với đúng tên field dưới đây (đặc biệt `multiSelect`, **không phải** `multiSelect`):

```javascript
AskUserQuestion({
  questions: [
    {
      question: "Câu hỏi (có thể xuống dòng bằng \n)",
      header: "Nhãn ngắn (≤ 12 ký tự)",
      multiSelect: false,          // false = chọn 1 · true = chọn nhiều
      options: [                    // BẮT BUỘC 2–4 options
        { label: "Option A", description: "Mô tả A" },
        { label: "Option B", description: "Mô tả B" }
      ]
    }
  ]
})
```

### QUY TẮC SỬ DỤNG:
- Mỗi câu hỏi AskUserQuestion **phải có 2–4 `options`** — nếu không có lựa chọn rõ ràng, hỏi bằng prose.
- `multiSelect: false` cho chọn 1; `multiSelect: true` cho chọn nhiều.
- User luôn có thể chọn "Other" để nhập tự do — không cần thêm option "Khác" thủ công.
- LUÔN dùng `\n` để xuống dòng trong question text.

### 1. HỎI TỪNG CÂU - LOOP CHO ĐẾN KHI ĐẦY ĐỦ
```
❌ SAI: Hỏi → User trả lời ngắn → Chuyển câu tiếp theo
✅ ĐÚNG: Hỏi → User trả lời → Đánh giá → Nếu CHƯA ĐỦ → ĐÀO SÂU → Lặp lại → Đến khi ĐẦY ĐỦ → Hỏi "Bạn có hài lòng?" → Xác nhận → Câu tiếp theo
```

### 2. CƠ CHẾ "SATISFIED?" - BẮT BUỘC XÁC NHẬN
```
Sau MỖI câu trả lời chính, PHẢI hỏi:
"☝️ Tạm ổn rồi. Bạn có muốn bổ sung thêm gì không? Hay chúng ta chuyển sang câu tiếp theo?"
- User đồng ý → Chuyển câu tiếp theo
- User muốn bổ sung → Tiếp tục câu hiện tại
```

### 3. ĐÀO SÂU KHI TRẢ LỜI VAGUE/NGẮN
```
User: "Cần có quản lý users"
→ "Quản lý users có nghĩa là gì cụ thể? Có phải:"
     a) Tạo mới user?
     b) Sửa thông tin user?
     c) Xóa user?
     d) Phân quyền user?
     e) Reset password?
   Bạn muốn tất cả hay chỉ một số tính năng?"

User: "khoảng 1000"
→ "1000 users? 1000 sản phẩm? 1000 đơn hàng? Con số này thuộc loại dữ liệu nào?"
```

### 4. ĐÁNH DẤU TRẠNG THÁI MỖI CÂU HỎI
```
Mỗi câu hỏi phải có trạng thái:
[ ] Pending    - Chưa hỏi
[ ] Asked      - Đã hỏi, đang chờ trả lời
[ ] Probing    - Đang đào sâu
[ ] Satisfied  - User xác nhận đã đầy đủ
[ ] Skipped    - User từ chối trả lời (ghi reason)
```

---

## Interview Flow (12 Steps)

### STEP 1: Giới thiệu + Xác nhận bắt đầu

**SỬ DỤNG AskUserQuestion tool:**
```javascript
tool.Call("AskUserQuestion", {
  questions: [{
    question: "👋 Xin chào! Tôi sẽ giúp bạn thu thập yêu cầu cho dự án.\n\nQUY TẮC:\n• Tôi sẽ hỏi TỪNG câu một\n• Với mỗi câu, tôi cần bạn trả lời ĐẦY ĐỦ và CỤ THỂ\n• Nếu câu trả lời còn thiếu, tôi sẽ đào sâu thêm\n• Sau mỗi câu trả lời, bạn sẽ được hỏi 'Có hài lòng không?'\n• Bạn có thể bổ sung thêm bất cứ lúc nào\n\nBắt đầu nhé?",
    header: "Bắt đầu",
    options: [
      { label: "Bắt đầu", description: "Tôi đã sẵn sàng trả lời phỏng vấn" },
      { label: "Cần tìm hiểu thêm", description: "Tôi muốn biết thêm về quy trình" }
    ],
    multiSelect: false
  }]
})
```

---

### STEP 2: Thông tin Dự án Cơ bản

**Câu hỏi 2.1 (Bắt buộc) - TÊN DỰ ÁN:**

**SỬ DỤNG AskUserQuestion tool:**
```javascript
tool.Call("AskUserQuestion", {
  questions: [{
    question: "📝 Câu hỏi 1/35: Tên dự án của bạn là gì?\n\nVí dụ: 'Hệ thống Quản lý Bán hàng', 'App Đặt món ăn', 'Phần mềm CRM'",
    header: "Tên Dự án"
  }]
})
```

**Probing 2.1a (nếu quá ngắn/chung chung) - DÙNG AskUserQuestion:**
```javascript
tool.Call("AskUserQuestion", {
  questions: [{
    question: "Tên này có phải là tên chính thức của dự án không?\nHay đây chỉ là tên tạm thời để phân biệt với dự án khác?",
    header: "Xác nhận tên"
  }]
})
```

**SATISFACTION CHECK 2.1 - DÙNG AskUserQuestion:**
```javascript
tool.Call("AskUserQuestion", {
  questions: [{
    question: "☝️ Tên dự án: [tên đã nhập]\n\nBạn có hài lòng với tên này không? Có muốn đổi hoặc bổ sung gì thêm không?",
    header: "Xác nhận OK?"
  }]
})
```

---

**Câu hỏi 2.2 (Bắt buộc) - MÔ TẢ HỆ THỐNG:**

**SỬ DỤNG AskUserQuestion tool:**
```javascript
tool.Call("AskUserQuestion", {
  questions: [{
    question: "📝 Câu hỏi 2/35: Mô tả ngắn gọn (2-3 câu) hệ thống bạn muốn xây dựng.\n\nGợi ý trả lời:\n• Hệ thống làm gì?\n• Ai sử dụng?\n• Mục đích chính?\n\nVí dụ: 'Hệ thống quản lý bán hàng cho cửa hàng với 5 nhân viên. Nhân viên có thể tạo đơn hàng, theo dõi tồn kho. Chủ cửa hàng xem báo cáo doanh thu.'",
    header: "Mô tả HT"
  }]
})
```

**Probing 2.2a (nếu mô tả chung chung) - DÙNG AskUserQuestion:**
```javascript
tool.Call("AskUserQuestion", {
  questions: [{
    question: "Bạn nói 'quản lý bán hàng' - cụ thể là:\na) Tạo đơn hàng?\nb) Quản lý tồn kho?\nc) Tính tiền/thanh toán?\nd) In hóa đơn?\ne) Xem báo cáo?\nf) Quản lý khách hàng?\n\nBạn muốn tất cả hay chỉ một số tính năng?",
    header: "Làm rõ tính năng",
    options: [
      { label: "Tất cả (a-f)", description: "Cần tất cả tính năng" },
      { label: "Chỉ a, b, c", description: "Tạo đơn, tồn kho, thanh toán" },
      { label: "Chỉ a, b, e", description: "Tạo đơn, tồn kho, báo cáo" },
      { label: "Khác", description: "Tôi sẽ liệt kê cụ thể" }
    ],
    multiSelect: false
  }]
})
```

**SATISFACTION CHECK 2.2 - DÙNG AskUserQuestion:**
```javascript
tool.Call("AskUserQuestion", {
  questions: [{
    question: "☝️ Mô tả hệ thống:\n[đã nhập]\n\nBạn có hài lòng với mô tả này không? Có cần bổ sung gì về:\n• Chức năng chính của hệ thống?\n• Đối tượng sử dụng?\n• Mục tiêu của hệ thống?",
    header: "Xác nhận OK?"
  }]
})
```

---

**Câu hỏi 2.3 (Bắt buộc) - HỆ THỐNG HIỆN CÓ:**

**SỬ DỤNG AskUserQuestion tool:**
```javascript
tool.Call("AskUserQuestion", {
  questions: [{
    question: "📝 Câu hỏi 3/35: Hệ thống này thay thế hay bổ sung cho hệ thống hiện có nào không?\n\nTrả lời một trong các trường hợp:\nA) HOÀN TOÀN MỚI - Xây từ đầu, chưa có hệ thống cũ\nB) THAY THẾ - Có hệ thống cũ, sẽ bỏ hoàn toàn\nC) BỔ SUNG - Có hệ thống cũ, thêm tính năng mới\nD) NÂNG CẤP - Có hệ thống cũ, chuyển đổi công nghệ mới",
    header: "HT Hiện có",
    options: [
      { label: "A) Hoàn toàn mới", description: "Xây từ đầu, chưa có hệ thống cũ" },
      { label: "B) Thay thế", description: "Có hệ thống cũ, sẽ bỏ hoàn toàn" },
      { label: "C) Bổ sung", description: "Có hệ thống cũ, thêm tính năng mới" },
      { label: "D) Nâng cấp", description: "Có hệ thống cũ, chuyển đổi công nghệ mới" }
    ],
    multiSelect: false
  }]
})
```

**Probing 2.3a (nếu chọn B/C/D) - DÙNG AskUserQuestion:**
```javascript
tool.Call("AskUserQuestion", {
  questions: [{
    question: "• Hệ thống hiện tại tên gì?\n• Đang dùng công nghệ gì? (Excel, phần mềm cũ, database...)\n• Có dữ liệu cần di chuyển không?\n• Tính năng nào từ hệ thống cũ PHẢI giữ lại?\n• Tính năng nào từ hệ thống cũ có thể BỎ?",
    header: "Chi tiết HT cũ"
  }]
})
```

**SATISFACTION CHECK 2.3 - DÙNG AskUserQuestion:**
```javascript
tool.Call("AskUserQuestion", {
  questions: [{
    question: "☝️ Hệ thống hiện tại: [đã mô tả]\n\nĐã đầy đủ thông tin chưa? Có muốn bổ sung về:\n• Dữ liệu cần di chuyển?\n• Tính năng bắt buộc phải giữ?\n• Thời gian chuyển đổi?",
    header: "Xác nhận OK?"
  }]
})
```

---

**Câu hỏi 2.4 (Tùy chọn) - VẤN ĐỀ KINH DOANH:**

**SỬ DỤNG AskUserQuestion tool:**
```javascript
tool.Call("AskUserQuestion", {
  questions: [{
    question: "📝 Câu hỏi 4/35: Vấn đề hoặc cơ hội kinh doanh nào dự án này giải quyết?\n\nGợi ý:\n• Đang gặp vấn đề gì với quy trình hiện tại?\n• Dự án này mang lại lợi ích gì?\n• Đo lường thành công bằng gì? (tiết kiệm thời gian, tăng doanh thu, giảm sai sót...)",
    header: "Vấn đề KD"
  }]
})
```

---

### STEP 3: Người dùng & Actors

**Câu hỏi 3.1 (Bắt buộc) - DANH SÁCH NGƯỜI DÙNG:**

**SỬ DỤNG AskUserQuestion tool:**
```javascript
tool.Call("AskUserQuestion", {
  questions: [{
    question: "📝 Câu hỏi 5/35: Ai sẽ sử dụng hệ thống này?\n\nLiệt kê tất cả nhóm người dùng (actors).\n\nTrả lời theo format:\n| Nhóm người dùng | Mô tả ngắn |\n| Admin | Quản lý hệ thống |\n| Nhân viên bán hàng | Tạo đơn hàng |\n| Khách hàng | Đặt hàng online |\n\nVí dụ: 'Admin, Nhân viên kho, Kế toán, Khách hàng'",
    header: "Danh sách Users"
  }]
})
```

**Probing 3.1a (nếu liệt kê chung chung) - DÙNG AskUserQuestion:**
```javascript
tool.Call("AskUserQuestion", {
  questions: [{
    question: "Với mỗi nhóm người dùng, tôi cần biết:\n1. [Nhóm 1]: Làm gì trên hệ thống? (tạo? đọc? sửa? xóa?)\n2. [Nhóm 2]: Làm gì trên hệ thống?\n...\n\nVí dụ:\n• Admin: Toàn quyền quản lý hệ thống\n• Kế toán: Chỉ xem và xuất báo cáo tài chính\n• Khách hàng: Chỉ đặt hàng và xem lịch sử đặt hàng",
    header: "Chi tiết Users"
  }]
})
```

**Probing 3.1b (nếu thiếu actor rõ ràng) - DÙNG AskUserQuestion:**
```javascript
tool.Call("AskUserQuestion", {
  questions: [{
    question: "Tôi thấy bạn chưa liệt kê đầy đủ. Thử xem xét:\n• Người quản lý (xem báo cáo, duyệt phê duyệt)?\n• Người giao hàng (cập nhật trạng thái giao hàng)?\n• Khách hàng tiềm năng (xem sản phẩm trước khi đăng ký)?\n• Đối tác/cung cấp (xem đơn đặt hàng)?",
    header: "Gợi ý Actors"
  }]
})
```

**SATISFACTION CHECK 3.1 - DÙNG AskUserQuestion:**
```javascript
tool.Call("AskUserQuestion", {
  questions: [{
    question: "☝️ Danh sách người dùng:\n[đã liệt kê]\n\nBạn có hài lòng với danh sách này không?\n• Còn nhóm nào khác sử dụng hệ thống không?\n• Mỗi nhóm đã mô tả đủ quyền hạn chưa?\n• Có nhóm nào cần CHIA NHỎ thành nhiều nhóm con không?",
    header: "Xác nhận OK?"
  }]
})
```

---

**Câu hỏi 3.2 (Bắt buộc) - QUYỀN HẠN TỪNG ACTOR:**

**SỬ DỤNG AskUserQuestion tool:**
```javascript
tool.Call("AskUserQuestion", {
  questions: [{
    question: "📝 Câu hỏi 6/35: Mỗi nhóm người dùng có quyền hạn gì khác nhau?\n\nVới mỗi actor, tôi cần biết rõ:\n• Được PHÉP làm gì?\n• KHÔNG được làm gì?\n• Có cần phê duyệt từ ai không?\n\nFormat đề xuất:\n| Actor | Được làm | Không được làm | Cần phê duyệt |\n|-------|---------|----------------|---------------|\n| Admin | Tất cả | Không | Không ai |\n| Staff | CRUD đơn hàng | Xóa đơn hàng đã duyệt | Quản lý |",
    header: "Phân quyền"
  }]
})
```

**Probing 3.2a (nếu quyền hạn mập mờ) - DÙNG AskUserQuestion:**
```javascript
tool.Call("AskUserQuestion", {
  questions: [{
    question: "'Admin có quyền làm mọi thứ' - cụ thể là:\na) Tạo/sửa/xóa tất cả data?\nb) Thay đổi cấu hình hệ thống?\nc) Xem báo cáo nhạy cảm?\nd) Quản lý users khác?\n\nVới Staff:\na) CRUD đơn hàng - CRUD nghĩa là gì? Tạo, xem, sửa, xóa?\nb) Được xóa đơn hàng không?\nc) Được hoàn tiền không?\nd) Được xem báo cáo không?",
    header: "Làm rõ quyền"
  }]
})
```

**SATISFACTION CHECK 3.2 - DÙNG AskUserQuestion:**
```javascript
tool.Call("AskUserQuestion", {
  questions: [{
    question: "☝️ Quyền hạn actors:\n[đã mô tả]\n\nBạn có hài lòng với phân quyền này không?\n• Có xung đột quyền hạn nào không?\n• Có nhóm nào cần quyền cao hơn/thấp hơn không?",
    header: "Xác nhận OK?"
  }]
})
```

---

**Câu hỏi 3.3 (Tùy chọn) - NGƯỜI DÙNG ẨN DANH:**

**SỬ DỤNG AskUserQuestion tool:**
```javascript
tool.Call("AskUserQuestion", {
  questions: [{
    question: "📝 Câu hỏi 7/35: Có người dùng ẩn danh (guest/chưa đăng nhập) không?\n\nNếu CÓ, họ có thể:\n• Xem sản phẩm/dịch vụ?\n• So sánh sản phẩm?\n• Thêm vào giỏ hàng nhưng chưa đặt?\n• Đăng ký tài khoản mới?\n\nNếu KHÔNG, tại sao? (yêu cầu đăng nhập trước khi xem nội dung?)",
    header: "Guest User"
  }]
})
```

---

---

## SECTION C: FUNCTIONAL REQUIREMENTS (Câu 8-12)

### Câu 8 (Bắt buộc) - FEATURES LIST & MOSCOW:

**SỬ DỤNG AskUserQuestion tool:**
```javascript
tool.Call("AskUserQuestion", {
  questions: [{
    question: "📝 Câu hỏi 8/35: Hệ thống cần làm những chức năng gì? Liệt kê tất cả tính năng theo thứ tự ưu tiên.\n\nFormat:\n| STT | Tên tính năng | Mô tả ngắn | Actor chính | MoSCoW |\n|-----|--------------|-------------|-------------|--------|\n| 1 | Đăng nhập | Email + password | User | Must |\n| 2 | Tạo đơn hàng | Tạo đơn hàng mới | Staff | Must |\n...\n\nGợi ý nhóm tính năng:\n• Quản lý tài khoản (đăng ký, đăng nhập, quên password)\n• CRUD cơ bản (tạo, đọc, sửa, xóa data)\n• Tìm kiếm & Lọc & Sắp xếp\n• Báo cáo & Thống kê\n• Thanh toán & Giao dịch\n• Thông báo (email/SMS/push)",
    header: "Tính năng"
  }]
})
```

**Probing 8a (mỗi tính năng đào sâu) - DÙNG AskUserQuestion:**
```javascript
tool.Call("AskUserQuestion", {
  questions: [{
    question: "Với tính năng '[tên tính năng]', tôi cần hiểu rõ:\n\n1. AI sử dụng tính năng này? (Actor nào?)\n2. Tính năng bắt đầu như thế nào?\n   (User click nút? System tự động? Schedule? Event triggered?)\n3. Các bước chính để hoàn thành?\n   (B1: ... → B2: ... → B3: ...)\n4. Kết quả cuối cùng là gì?\n   (Data được lưu? Email được gửi? Page chuyển?)\n5. Có lỗi gì có thể xảy ra?\n   (Network lỗi? Validation fail? Permission denied?)\n6. Khi nào thì BỊ TỪ CHỐI?\n   (Sai input? Không đủ quyền? Data không tồn tại?)",
    header: "Đào sâu tính năng"
  }]
})
```

**Probing 8b (nếu thiếu tính năng hiển nhiên) - DÙNG AskUserQuestion:**
```javascript
tool.Call("AskUserQuestion", {
  questions: [{
    question: "Tôi thấy bạn chưa liệt kê một số tính năng phổ biến:\n• [ ] Đăng nhập/Đăng xuất\n• [ ] Đăng ký/Tạo tài khoản\n• [ ] Quên mật khẩu/Reset password\n• [ ] Xem danh sách/List data\n• [ ] Tìm kiếm/Search\n• [ ] Lọc/Filter\n• [ ] Sắp xếp/Sort\n• [ ] Phân trang/Pagination\n• [ ] Xuất báo cáo/Export (Excel, PDF)\n• [ ] Import dữ liệu\n• [ ] Backup dữ liệu\n• [ ] Thông báo (email/SMS/push)\n• [ ] Lịch sử hoạt động/Audit log",
    header: "Gợi ý tính năng",
    options: [
      { label: "Thêm tất cả", description: "Bổ sung các tính năng phổ biến" },
      { label: "Chỉ một số", description: "Chọn lọc tính năng phù hợp" },
      { label: "Đủ rồi", description: "Không cần thêm" }
    ],
    multiSelect: false
  }]
})
```

**SATISFACTION CHECK 8 - DÙNG AskUserQuestion:**
```javascript
tool.Call("AskUserQuestion", {
  questions: [{
    question: "☝️ Danh sách tính năng:\n[đã liệt kê]\n\nBạn có hài lòng với danh sách này không?\n• Còn tính năng nào khác không?\n• Tính năng nào chưa mô tả đủ chi tiết?\n• Thứ tự ưu tiên đã đúng chưa?",
    header: "Xác nhận OK?"
  }]
})
```

---

### Câu 9 (Bắt buộc) - MoSCoW PHÂN LOẠI:

**SỬ DỤNG AskUserQuestion tool:**
```javascript
tool.Call("AskUserQuestion", {
  questions: [{
    question: "📝 Câu hỏi 9/35: Tính năng nào là BẮT BUỘC, tính năng nào là 'có cũng được'?\n\nDùng MoSCoW:\n| Ký hiệu |Ý nghĩa | Định nghĩa |\n|---------|---------|-------------|\n| M | Must Have | Không có = KHÔNG thể phát hành |\n| S | Should Have | Nên có = Quan trọng nhưng không bắt buộc |\n| C | Could Have | Có thể có = Nice to have |\n| W | Won't Have | Không có trong phiên bản này |",
    header: "MoSCoW"
  }]
})
```

**Probing 9a (nếu tất cả đều là Must) - DÙNG AskUserQuestion:**
```javascript
tool.Call("AskUserQuestion", {
  questions: [{
    question: "⚠️ Bạn đánh dấu tất cả đều là MUST. Thật ra rất ít hệ thống cần tất cả tính năng ngay lập tức.\n\nHãy nghĩ xem:\n• Nếu KHÔNG có tính năng X, hệ thống có thể chạy được không?\n• Nếu KHÔNG có tính năng Y, user có từ bỏ không?\n\nThường có thể chuyển:\n• Báo cáo chi tiết → có thể export Excel trước\n• Dashboard phức tạp → có thể dùng bảng đơn giản\n• Mobile app → có thể dùng web responsive trước\n• Real-time notifications → có thể dùng email thường",
    header: "Điều chỉnh Must"
  }]
})
```

**SATISFACTION CHECK 9 - DÙNG AskUserQuestion:**
```javascript
tool.Call("AskUserQuestion", {
  questions: [{
    question: "☝️ Phân loại MoSCoW:\nMust Have: [danh sách]\nShould Have: [danh sách]\nCould Have: [danh sách]\n\nBạn có hài lòng với phân loại này không?\n• Có tính năng nào bị đánh giá sai mức độ ưu tiên không?\n• Có cần điều chỉnh gì không?",
    header: "Xác nhận OK?"
  }]
})
```

---

### Câu 10 (Bắt buộc) - BUSINESS RULES & CALCULATIONS:

**SỬ DỤNG AskUserQuestion tool:**
```javascript
tool.Call("AskUserQuestion", {
  questions: [{
    question: "📝 Câu hỏi 10/35: Có quy tắc nghiệp vụ đặc biệt nào cần hệ thống tự động không?\n\nFormat:\n| Rule | Mô tả | Trigger | Action |\n|------|-------|---------|--------|\n| Auto_cancel | Tự hủy đơn sau 24h | Order unpaid > 24h | Cancel + notify |\n| Tier_pricing | Giảm giá theo số lượng | Qty > 10 | 10% off |\n| Auto_refund | Tự hoàn tiền khi hủy | Order cancelled | Refund to original payment |\n\nVí dụ:\n• Tự động gửi email khi có đơn hàng mới\n• Tự động tính hoa hồng cho đại lý (%)\n• Tự động gia hạn subscription\n• Tự động inventory alert khi hết hàng\n• Validation rules (điều kiện validate input)",
    header: "Business Rules"
  }]
})
```

**Probing 10a - DÙNG AskUserQuestion:**
```javascript
tool.Call("AskUserQuestion", {
  questions: [{
    question: "Với mỗi rule:\n1. Rule này áp dụng khi nào? (trigger condition)\n2. Hệ thống làm gì khi trigger? (action)\n3. Có exception không? (khi nào thì không áp dụng)\n4. Ai có thể override manual?\n5. Có log để audit không?",
    header: "Đào sâu Rules"
  }]
})
```

**SATISFACTION CHECK 10 - DÙNG AskUserQuestion:**
```javascript
tool.Call("AskUserQuestion", {
  questions: [{
    question: "☝️ Danh sách Business Rules:\n[đã liệt kê]\n\nBạn có hài lòng với danh sách này không?\n• Còn rule nào khác không?\n• Có rule nào cần điều chỉnh logic không?",
    header: "Xác nhận OK?"
  }]
})
```

---

### Câu 11 (Bắt buộc) - VALIDATION & ERROR HANDLING:

**SỬ DỤNG AskUserQuestion tool:**
```javascript
tool.Call("AskUserQuestion", {
  questions: [{
    question: "📝 Câu hỏi 11/35: Hệ thống validate dữ liệu như thế nào? Error handling ra sao?\n\nVới mỗi loại input, cần biết:\n• Required fields? (bắt buộc nhập)\n• Format validation? (email format, phone format, date format)\n• Range validation? (số lượng min/max, ngày tháng range)\n• Custom validation rules?\n\nError handling:\n• Khi validation fail thì hiển thị gì?\n• Khi network lỗi thì sao?\n• Khi timeout thì sao?\n• Khi permission denied thì sao?\n• Có retry mechanism không?",
    header: "Validation"
  }]
})
```

**Probing 11a - DÙNG AskUserQuestion:**
```javascript
tool.Call("AskUserQuestion", {
  questions: [{
    question: "Với mỗi validation rule:\n1. Rule cụ thể là gì? (VD: email phải đúng format)\n2. Khi fail thì hiển thị message gì?\n3. Có prevent form submission không?\n4. Có highlight field lỗi không?\n\nVới error handling:\n1. Network error → hiển thị retry button?\n2. Timeout → tự động retry mấy lần?\n3. Permission denied → redirect sang login?",
    header: "Đào sâu Validation"
  }]
})
```

**SATISFACTION CHECK 11 - DÙNG AskUserQuestion:**
```javascript
tool.Call("AskUserQuestion", {
  questions: [{
    question: "☝️ Validation & Error Handling:\n[đã mô tả]\n\nBạn có hài lòng với cách xử lý này không?\n• Còn validation nào khác cần thêm?\n• Còn error case nào chưa xử lý?",
    header: "Xác nhận OK?"
  }]
})
```

---

### Câu 12 (Tùy chọn) - REPORTING & ANALYTICS:

**SỬ DỤNG AskUserQuestion tool:**
```javascript
tool.Call("AskUserQuestion", {
  questions: [{
    question: "📝 Câu hỏi 12/35: Cần những báo cáo và analytics gì?\n\nFormat:\n| Report | Mô tả | Ai xem | Tần suất | Format |\n|--------|--------|--------|----------|--------|\n| Doanh thu | Tổng hợp doanh thu theo ngày | Admin, Manager | Daily | PDF, Excel |\n| Tồn kho | Báo cáo tồn kho | Admin, Warehouse | Weekly | Excel |\n\nCần biết:\n• Report real-time hay delayed?\n• Ai được phép xem report nào?\n• Có drill-down không? (click vào xem chi tiết)\n• Export được những format nào?\n• Có scheduled reports gửi email tự động không?",
    header: "Reports"
  }]
})
```

**SATISFACTION CHECK 12 - DÙNG AskUserQuestion:**
```javascript
tool.Call("AskUserQuestion", {
  questions: [{
    question: "☝️ Danh sách Reports/Analytics:\n[đã liệt kê]\n\nBạn có hài lòng với danh sách này không?\n• Còn report nào khác không?\n• Có dashboard/visualization nào đặc biệt không?",
    header: "Xác nhận OK?"
  }]
})
```

---

## SECTION D: DATA MODEL (Câu 13-15)

### Câu 13 (Bắt buộc) - ENTITIES & FIELDS:

**SỬ DỤNG AskUserQuestion tool:**
```javascript
tool.Call("AskUserQuestion", {
  questions: [{
    question: "📝 Câu hỏi 13/35: Hệ thống cần lưu trữ những loại dữ liệu gì? (Entities)\n\nFormat:\n| Entity | Mô tả | Primary Fields | Ai quản lý |\n|--------|--------|-----------------|-------------|\n| User | Tài khoản người dùng | id, name, email, phone, password, role | Admin |\n| Product | Sản phẩm/dịch vụ | id, name, price, stock, category_id | Admin |\n| Order | Đơn hàng | id, user_id, total, status, created_at | Staff |\n\nGợi ý - Nghĩ về DANH TỪ trong hệ thống:\n• Ai? → User, Customer, Employee, Admin",
    header: "Entities"
  }]
})
```

**SATISFACTION CHECK 13 - DÙNG AskUserQuestion:**
```javascript
tool.Call("AskUserQuestion", {
  questions: [{
    question: "☝️ Danh sách Entities:\n[đã liệt kê]\n\nBạn có hài lòng với danh sách này không?\n• Còn entity nào khác không?\n• Entity nào cần thêm fields?",
    header: "Xác nhận OK?"
  }]
})
```

**Probing 13a (với mỗi entity đào sâu):**
```
Với entity "[tên]", tôi cần biết:
1. Cần những fields/thông tin gì?
   Ví dụ: User có: id, name, email, phone, password, role, created_at, updated_at
2. Data type của mỗi field? (string, int, date, boolean, enum...)
3. Required hay optional?
4. Default value?
5. Có validation nào đặc biệt?
```

**Probing 13b (nếu thiếu entity cơ bản):**
```
Tôi thấy bạn chưa liệt kê một số entity phổ biến:
• [ ] User/Account (thông tin đăng nhập)
• [ ] Role/Permission (phân quyền)
• [ ] Category/Type (phân loại)
• [ ] Log/Audit (lịch sử thay đổi)
• [ ] Setting/Config (cấu hình hệ thống)
• [ ] Notification (thông báo)
• [ ] Attachment/File (tệp đính kèm)
```

**SATISFACTION CHECK 13:**
```
☝️ Danh sách Entities:
[đã liệt kê]

Bạn có hài lòng với danh sách này không?
• Còn entity nào khác không?
• Entity nào cần thêm fields?
```

---

### Câu 14 (Bắt buộc) - RELATIONSHIPS & CARDINALITY:

**SỬ DỤNG AskUserQuestion tool:**
```javascript
tool.Call("AskUserQuestion", {
  questions: [{
    question: "📝 Câu hỏi 14/35: Các entities có mối liên hệ với nhau như thế nào?\n\nFormat:\n| Entity A | Relationship | Entity B | Cardinality |\n|----------|--------------|----------|--------------|\n| User | has many | Order | 1:N |\n| Order | contains many | OrderItem | 1:N |\n| OrderItem | belongs to | Product | N:1 |\n| Product | belongs to | Category | N:1 |\n\nCần biết:\n• 1:N hay N:N?\n• Có cascade delete không? (xóa parent → xóa child?)\n• Có soft delete không? (xóa mềm - is_deleted flag)\n• Có timestamp tracking không? (created_at, updated_at)",
    header: "Relationships"
  }]
})
```

**Probing 14a - DÙNG AskUserQuestion:**
```javascript
tool.Call("AskUserQuestion", {
  questions: [{
    question: "Với mỗi relationship:\n1. Entity A và Entity B liên hệ như thế nào?\n2. Một A có bao nhiêu B? (1:N, N:1, N:N)\n3. Khi xóa A thì B có bị xóa không? (Cascade delete)\n4. Có xóa mềm (soft delete) không?",
    header: "Đào sâu Relationships"
  }]
})
```

**SATISFACTION CHECK 14 - DÙNG AskUserQuestion:**
```javascript
tool.Call("AskUserQuestion", {
  questions: [{
    question: "☝️ Relationships:\n[đã liệt kê]\n\nBạn có hài lòng với các mối quan hệ này không?\n• Còn relationship nào chưa rõ không?\n• Có cần thêm junction table cho N:N không?",
    header: "Xác nhận OK?"
  }]
})
```

---

### Câu 15 (Tùy chọn) - DATA VOLUME & GROWTH:

**SỬ DỤNG AskUserQuestion tool:**
```javascript
tool.Call("AskUserQuestion", {
  questions: [{
    question: "📝 Câu hỏi 15/35: Dữ liệu ban đầu khoảng bao nhiêu? Dự kiến tăng trưởng?\n\nƯớc lượng cho mỗi entity:\n• Users: ~ ? (VD: 100, 1000, 10000)\n• Products: ~ ?\n• Orders/Transactions: ~ ?\n• Storage estimate: ~ ? GB\n\nDự kiến tăng trưởng:\n• User growth: % / tháng\n• Data growth: % / tháng\n• Peak load: (VD: 10,000 concurrent users)\n\nĐiều này ảnh hưởng đến:\n• Database design (indexing, partitioning)\n• Caching strategy\n• Scalability requirements",
    header: "Data Volume"
  }]
})
```

**SATISFACTION CHECK 15 - DÙNG AskUserQuestion:**
```javascript
tool.Call("AskUserQuestion", {
  questions: [{
    question: "☝️ Data Volume & Growth:\n[đã ước lượng]\n\nBạn có hài lòng với các con số này không?\n• Cần điều chỉnh gì không?",
    header: "Xác nhận OK?"
  }]
})
```

---

## SECTION E: INTEGRATION & INTERFACES (Câu 16-19)

**Câu 16 (Bắt buộc) - EXTERNAL SYSTEMS:**

**SỬ DỤNG AskUserQuestion tool:**
```javascript
tool.Call("AskUserQuestion", {
  questions: [{
    question: "📝 Câu hỏi 16/35: Hệ thống có cần kết nối với hệ thống khác không?\n\nFormat:\n| Hệ thống | Mục đích kết nối | Method | Data Flow |\n|----------|-----------------|--------|-----------|\n| CRM Salesforce | Đồng bộ khách hàng | API | Bidirectional |\n| Kế toán MISA | Xuất hóa đơn | File export | One-way |\n| Shipping GHN | Tính phí giao hàng | API | Bidirectional |\n| Payment VNPay | Thanh toán | API | Bidirectional |\n\nVí dụ phổ biến:\n• Payment gateway (Stripe, VNPay, MoMo, ZaloPay)\n• Shipping (GHN, GHTK, J&T, Ninja Van)\n• SMS/Email service (Twilio, SendGrid, AWS SES)\n• Storage (AWS S3, Google Drive)",
    header: "External Systems"
  }]
})
```

**Probing 16a (với mỗi hệ thống kết nối):**
```
Với mỗi hệ thống:
1. Hệ thống bên ngoài có API spec chưa?
2. Authentication method? (API key, OAuth, JWT)
3. Rate limits? (VD: 1000 requests/minute)
4. SLA của hệ thống bên ngoài? (uptime guarantee)
5. Khi hệ thống bên ngoài down thì sao?
```

**SATISFACTION CHECK 16 - DÙNG AskUserQuestion:**
```javascript
tool.Call("AskUserQuestion", {
  questions: [{
    question: "☝️ External Systems đã xác định:\n[đã liệt kê]\n\nBạn có hài lòng với danh sách này không?\n• Còn hệ thống nào khác cần kết nối không?\n• Đã hiểu rõ về authentication và SLA của từng hệ thống chưa?",
    header: "Xác nhận OK?"
  }]
})
```

---

**Câu 17 (Bắt buộc) - API SPECS & DATA FORMAT:**

**SỬ DỤNG AskUserQuestion tool:**
```javascript
tool.Call("AskUserQuestion", {
  questions: [{
    question: "📝 Câu hỏi 17/35: Các API integrations dùng format dữ liệu gì?\n\nVới mỗi integration:\n• REST API (JSON) hay GraphQL hay SOAP (XML)?\n• API version hiện tại?\n• Authentication method?\n• Error codes chuẩn không?",
    header: "API Specs"
  }]
})
```

**SATISFACTION CHECK 17 - DÙNG AskUserQuestion:**
```javascript
tool.Call("AskUserQuestion", {
  questions: [{
    question: "☝️ API Specs đã xác định:\n[đã liệt kê]\n\nBạn có hài lòng với thông tin này không?\n• Đã nắm rõ versioning strategy chưa?\n• Có cần bổ sung retry logic hoặc timeout settings không?",
    header: "Xác nhận OK?"
  }]
})
```

---

**Câu 18 (Bắt buộc) - DATA SYNC STRATEGY:**

**SỬ DỤNG AskUserQuestion tool:**
```javascript
tool.Call("AskUserQuestion", {
  questions: [{
    question: "📝 Câu hỏi 18/35: Dữ liệu được sync giữa các hệ thống như thế nào?\n\nChọn sync mode:\nA) Real-time (Webhook, Socket) - Ngay khi có thay đổi\nB) Scheduled (Cron job) - Định kỳ (VD: mỗi 15 phút)\nC) Batch (Manual trigger) - Khi cần\nD) Event-driven (Message queue) - Khi event xảy ra\n\nVới mỗi sync:\n• Sync direction? (Push, Pull, Bidirectional)\n• Conflict resolution khi data mismatch?",
    header: "Data Sync"
  }]
})
```

**SATISFACTION CHECK 18 - DÙNG AskUserQuestion:**
```javascript
tool.Call("AskUserQuestion", {
  questions: [{
    question: "☝️ Data Sync Strategy:\n[đã mô tả]\n\nBạn có hài lòng với chiến lược sync này không?\n• Tần suất sync đã phù hợp với business needs chưa?",
    header: "Xác nhận OK?"
  }]
})
```

---

**Câu 19 (Tùy chọn) - FILE IMPORTS/EXPORTS:**

**SỬ DỤNG AskUserQuestion tool:**
```javascript
tool.Call("AskUserQuestion", {
  questions: [{
    question: "📝 Câu hỏi 19/35: Có cần import/export dữ liệu từ file không?\n\nFormat phổ biến:\n• Excel (.xlsx, .csv) - cho báo cáo, export data\n• PDF - cho hóa đơn, hợp đồng\n• JSON/XML - cho API, data exchange\n• Image/PDF - cho in ấn\n\nVới mỗi loại:\n• Ai được phép export/import?\n• File size limit? (VD: max 10MB)",
    header: "File Import/Export"
  }]
})
```

**SATISFACTION CHECK 19 - DÙNG AskUserQuestion:**
```javascript
tool.Call("AskUserQuestion", {
  questions: [{
    question: "☝️ File Import/Export:\n[đã mô tả]\n\nBạn có hài lòng với thông tin này không?\n• Còn loại file nào khác cần hỗ trợ không?",
    header: "Xác nhận OK?"
  }]
})
```

---

## SECTION F: PLATFORM & UI (Câu 20-22)

**Câu 20 (Bắt buộc) - PLATFORMS:**

**SỬ DỤNG AskUserQuestion tool:**
```javascript
tool.Call("AskUserQuestion", {
  questions: [{
    question: "📝 Câu hỏi 20/35: Người dùng truy cập hệ thống bằng gì?\n\nChọn tất cả applicable:\nA) Web Browser (Chrome, Safari, Firefox, Edge...)\nB) Mobile Web (trình duyệt trên điện thoại)\nC) Native iOS App\nD) Native Android App\nE) Desktop App (Windows/Mac/Linux)\nF) API cho hệ thống khác gọi\n\nNếu chọn nhiều, cho biết tỷ lệ sử dụng:\nVD: Web 70%, Mobile Web 20%, API 10%",
    header: "Platforms",
    options: [
      { label: "A) Web Browser", description: "Desktop browser" },
      { label: "B) Mobile Web", description: "Trình duyệt trên điện thoại" },
      { label: "C) Native iOS", description: "iPhone/iPad app" },
      { label: "D) Native Android", description: "Android app" },
      { label: "E) Desktop App", description: "Windows/Mac/Linux" },
      { label: "F) API", description: "Cho hệ thống khác gọi" }
    ],
    multiSelect: true
  }]
})
```

**SATISFACTION CHECK 20 - DÙNG AskUserQuestion:**
```javascript
tool.Call("AskUserQuestion", {
  questions: [{
    question: "☝️ Platforms: [đã chọn]\nTỷ lệ sử dụng: [đã xác định]\n\nBạn có hài lòng với platforms này không?\n• Có cần bổ sung platform nào khác không?",
    header: "Xác nhận OK?"
  }]
})
```

---

**Câu 21 (Bắt buộc) - RESPONSIVE & ACCESSIBILITY:**

**SỬ DỤNG AskUserQuestion tool:**
```javascript
tool.Call("AskUserQuestion", {
  questions: [{
    question: "📝 Câu hỏi 21/35: Có yêu cầu đặc biệt về giao diện và accessibility không?\n\nResponsive Design:\n• Mobile-first hay desktop-first?\n• Breakpoints? (480px, 768px, 1024px, 1440px)\n• Touch-friendly targets? (min 44x44px)\n\nAccessibility (WCAG compliance):\n• Screen reader support? (alt text, ARIA labels)\n• Keyboard navigation?\n• Color contrast? (4.5:1 ratio minimum)\n• Dark mode support?\n\nUI/UX Standards:\n• Design system có sẵn không? (Material, Bootstrap, Tailwind, custom)",
    header: "UI/UX"
  }]
})
```

**SATISFACTION CHECK 21 - DÙNG AskUserQuestion:**
```javascript
tool.Call("AskUserQuestion", {
  questions: [{
    question: "☝️ Responsive & Accessibility:\n[đã mô tả]\n\nBạn có hài lòng với các yêu cầu UI/UX này không?\n• WCAG level nào? (A, AA, AAA)\n• Design system đã có sẵn hay cần create từ đầu?",
    header: "Xác nhận OK?"
  }]
})
```

---

**Câu 22 (Tùy chọn) - MULTI-LANGUAGE & INTERNATIONALIZATION:**

**SỬ DỤNG AskUserQuestion tool:**
```javascript
tool.Call("AskUserQuestion", {
  questions: [{
    question: "📝 Câu hỏi 22/35: Có cần hỗ trợ nhiều ngôn ngữ hoặc internationalization không?",
    header: "i18n"
  }]
})
```

> Nếu CÓ i18n, hỏi tiếp (prose) các chi tiết sau:

```
Languages:
• Primary language? (VD: Tiếng Việt)
• Additional languages? (VD: English, Chinese)
• RTL support? (Right-to-left cho tiếng Ả Rập, Hebrew)

Localization considerations:
• Date/Time format? (DD/MM/YYYY vs MM/DD/YYYY)
• Number format? (1,000.00 vs 1.000,00)
• Currency? (VND, USD, EUR)
• Phone number format?
• Address format?

i18n implementation:
• Translation management system?
• Content management approach?
• Dynamic language switching?
```

**Probing 22a:**
```
Với mỗi ngôn ngữ:
1. Content có sẵn chưa? (cần translate bao nhiêu?)
2. UI strings đã externalized chưa?
3. Plural forms xử lý thế nào?
4. Gender-specific translations có không?
5. Date/time timezone handling?

Technical considerations:
1. Font support có đủ không? (VD: CJK characters)
2. Text expansion (VD: German có thể dài hơn 30%)
3. RTL layout mirroring có cần không?
```

**SATISFACTION CHECK 22 - DÙNG AskUserQuestion:**
```javascript
tool.Call("AskUserQuestion", {
  questions: [{
    question: "☝️ Multi-language & i18n:\n[đã mô tả]\n\nBạn có hài lòng với thông tin này không?\n• Số lượng languages và priority đã đúng chưa?",
    header: "Xác nhận OK?"
  }]
})
```

---

## SECTION G: NON-FUNCTIONAL REQUIREMENTS (Câu 23-27)

### Câu 23 (Bắt buộc) - PERFORMANCE:

**SỬ DỤNG AskUserQuestion tool:**
```javascript
tool.Call("AskUserQuestion", {
  questions: [{
    question: "📝 Câu hỏi 23/35: Hệ thống cần phản hồi nhanh như thế nào?\n\nTrả lời cụ thể cho mỗi operation:\n| Operation | Acceptable | Ideal | Measurement |\n|-----------|------------|-------|------------|\n| Page load | < 3s | < 1s | P95 |\n| API call | < 1s | < 200ms | P95 |\n| Search query | < 2s | < 500ms | P95 |\n\nVí dụ:\n• 'API phải respond trong < 500ms với P95'\n• 'Page load phải < 2 giây với 1000 concurrent users'",
    header: "Performance"
  }]
})
```

**SATISFACTION CHECK 23 - DÙNG AskUserQuestion:**
```javascript
tool.Call("AskUserQuestion", {
  questions: [{
    question: "☝️ Performance Requirements:\n• Page load: [target]\n• API response: [target]\n• Search: [target]\n\nBạn có hài lòng với các con số này không?",
    header: "Xác nhận OK?"
  }]
})
```

---

### Câu 24 (Bắt buộc) - SCALABILITY & CONCURRENCY:

**SỬ DỤNG AskUserQuestion tool:**
```javascript
tool.Call("AskUserQuestion", {
  questions: [{
    question: "📝 Câu hỏi 24/35: Hệ thống cần scale như thế nào? Bao nhiêu users đồng thời?\n\nConcurrent Users:\n| Metric | Value |\n|--------|-------|\n| Peak concurrent users | ~? |\n| Average concurrent users | ~? |\n| Peak hour | VD: 9-11h, 14-16h |\n\nData Growth:\n• User growth rate: % / tháng\n• Storage needed: ~? GB/TB",
    header: "Scalability"
  }]
})
```

**Probing 24a:**
```
Scalability models:
• Vertical (scale up): Thêm CPU/RAM vào server
• Horizontal (scale out): Thêm more servers

Auto-scaling triggers:
• CPU > 70% for 5 minutes → Add 1 instance
• CPU < 30% for 15 minutes → Remove 1 instance
• Request count > 1000/min → Add instances

Database scaling:
• Read replicas for read-heavy workload
• Sharding for write-heavy
• Partitioning for large tables
```

**SATISFACTION CHECK 24 - DÙNG AskUserQuestion:**
```javascript
tool.Call("AskUserQuestion", {
  questions: [{
    question: "☝️ Scalability:\n• Concurrent users: [peak / average]\n• Auto-scaling: [có/không]\n• Data growth: [% / tháng]\n\nBạn có hài lòng với scalability plan này không?",
    header: "Xác nhận OK?"
  }]
})
```

---

### Câu 25 (Bắt buộc) - AVAILABILITY & SLA:

**SỬ DỤNG AskUserQuestion tool:**
```javascript
tool.Call("AskUserQuestion", {
  questions: [{
    question: "📝 Câu hỏi 25/35: Hệ thống cần uptime như thế nào?\n\nSLA Levels:\n| SLA | Downtime/year | Downtime/month | Use Case |\n|-----|---------------|----------------|----------|\n| 99% | 3.65 days | 7.3 hours | Internal tools |\n| 99.9% | 8.76 hours | 43.8 min | Standard apps |\n| 99.95% | 4.38 hours | 21.9 min | E-commerce |\n| 99.99% | 52.6 min | 4.4 min | FinTech/Healthcare |\n\nMaintenance windows:\n• Planned maintenance: Ngày giờ nào?\n• Emergency maintenance: Có thể làm khi nào?",
    header: "Availability"
  }]
})
```

**SATISFACTION CHECK 25 - DÙNG AskUserQuestion:**
```javascript
tool.Call("AskUserQuestion", {
  questions: [{
    question: "☝️ Availability:\n• SLA target: [X%]\n• Max downtime allowed: [X hours/year]\n\nBạn có hài lòng với availability plan này không?",
    header: "Xác nhận OK?"
  }]
})
```

---

### Câu 26 (Bắt buộc) - BACKUP & RECOVERY:

**SỬ DỤNG AskUserQuestion tool:**
```javascript
tool.Call("AskUserQuestion", {
  questions: [{
    question: "📝 Câu hỏi 26/35: Backup và recovery như thế nào?\n\nBackup Strategy:\n| Type | Frequency | Retention |\n|------|-----------|-----------|\n| Full backup | Weekly | 4 weeks |\n| Incremental | Daily | 7 days |\n\nRPO (Recovery Point Objective):\n• Chấp nhận mất bao nhiêu data?\n• VD: RPO = 1 hour → Max acceptable data loss = 1 hour\n\nRTO (Recovery Time Objective):\n• Hệ thống down → Bao lâu phải lên lại?",
    header: "Backup"
  }]
})
```

**SATISFACTION CHECK 26 - DÙNG AskUserQuestion:**
```javascript
tool.Call("AskUserQuestion", {
  questions: [{
    question: "☝️ Backup & Recovery:\n• RPO: [X hours]\n• RTO: [X hours]\n• Backup frequency: [daily/weekly/etc]\n\nBạn có hài lòng với backup plan này không?",
    header: "Xác nhận OK?"
  }]
})
```

---

### Câu 27 (Bắt buộc) - SECURITY & AUTHENTICATION:

**SỬ DỤNG AskUserQuestion tool:**
```javascript
tool.Call("AskUserQuestion", {
  questions: [{
    question: "📝 Câu hỏi 27/35: Yêu cầu bảo mật và xác thực như thế nào?\n\nAuthentication Methods:\n| Method | Use Case | Security Level |\n|--------|----------|----------------|\n| Email + Password | Standard users | Basic |\n| Password + 2FA/TOTP | Sensitive apps | Strong |\n| OAuth/Social Login | Consumer apps | Strong |\n| SSO (SAML/OIDC) | Enterprise | Very Strong |\n\nPassword Requirements:\n• Minimum length: 8 / 12 / 16 characters?\n• Complexity requirements?\n• 2FA required?",
    header: "Security"
  }]
})
```

**SATISFACTION CHECK 27 - DÙNG AskUserQuestion:**
```javascript
tool.Call("AskUserQuestion", {
  questions: [{
    question: "☝️ Security:\n• Auth method: [phương thức]\n• Password policy: [requirements]\n• 2FA required: [có/không]\n\nBạn có hài lòng với security plan này không?",
    header: "Xác nhận OK?"
  }]
})
```

---

## SECTION H: COMPLIANCE & RISK (Câu 28-30)

### Câu 28 (Bắt buộc) - REGULATORY COMPLIANCE:
```
📝 Câu hỏi 28/35: Có yêu cầu tuân thủ pháp luật nào không?

Common Compliance Frameworks:
| Framework | Region | Industry | Key Requirements |
|-----------|--------|----------|------------------|
| GDPR | EU | All | Data privacy, consent, right to deletion |
| PDPA | Thailand/VN | All | Data protection, consent |
| PCI-DSS | Global | Payment | Credit card data security |
| HIPAA | US | Healthcare | PHI protection |
| SOC 2 | US | SaaS | Security, availability, confidentiality |
| ISO 27001 | Global | All | Information security management |
| NIST | US | Government | Cybersecurity framework |

Data subject rights:
• Right to access (users can see their data)
• Right to rectification (correct errors)
• Right to erasure (delete account/data)
• Right to data portability (export data)
• Right to object (opt-out of processing)

Compliance evidence needed:
• Audit logs
• Data processing agreements
• Privacy policies
• Consent records
• Security certifications
```

**Probing 28a:**
```
Compliance implications:
• GDPR: €20M fine or 4% global revenue
• PCI-DSS: $500K-$5M fine per breach
• PDPA: Up to 5% of annual revenue

Compliance roadmap:
1. Identify applicable regulations
2. Data inventory and mapping
3. Gap analysis
4. Implement controls
5. Documentation and evidence
6. Audit and certification

If unsure about compliance:
• Consult legal counsel
• Hire compliance consultant
• Start with GDPR/PDPA as baseline
```

**SATISFACTION CHECK 28 - DÙNG AskUserQuestion:**
```javascript
tool.Call("AskUserQuestion", {
  questions: [{
    question: "☝️ Compliance:\n• Frameworks: [danh sách]\n• Audit requirements: [có/không]\n\nBạn có hài lòng với compliance plan này không?",
    header: "Xác nhận OK?"
  }]
})
```

---

### Câu 29 (Bắt buộc) - DATA CLASSIFICATION & PRIVACY:

**SỬ DỤNG AskUserQuestion tool:**
```javascript
tool.Call("AskUserQuestion", {
  questions: [{
    question: "📝 Câu hỏi 29/35: Dữ liệu được phân loại và bảo vệ như thế nào?\n\nData Classification:\n| Level | Examples | Handling |\n|-------|----------|----------|\n| Public | Marketing materials | Open |\n| Internal | Company policies | Internal only |\n| Confidential | Customer data | Restricted |\n| Restricted | Financial records, PII | Highly restricted |\n\nPII (Personally Identifiable Information):\n• What PII is collected? (name, email, phone, ID, IP)\n• How is PII stored?\n• PII retention period?",
    header: "Data Privacy"
  }]
})
```

**SATISFACTION CHECK 29 - DÙNG AskUserQuestion:**
```javascript
tool.Call("AskUserQuestion", {
  questions: [{
    question: "☝️ Data Classification:\n• Classification levels: [danh sách]\n• PII collected: [danh sách]\n\nBạn có hài lòng với data classification này không?",
    header: "Xác nhận OK?"
  }]
})
```

---

### Câu 30 (Bắt buộc) - AUDIT TRAIL & LOGGING:
```
📝 Câu hỏi 30/35: Cần logging và audit trail như thế nào?

Audit Trail Requirements:
| Event Type | Log Details | Retention |
|------------|-------------|-----------|
| Authentication | User, IP, time, success/fail | 1 year |
| Authorization | User, action, resource, result | 1 year |
| Data access | User, data, operation, timestamp | 1 year |
| Data modification | Before/after values, user | 3 years |
| System events | Server, service, error, severity | 6 months |
| Security events | Failed logins, permission denied | 1 year |

Log Management:
• Centralized logging? (ELK, CloudWatch, Datadog)
• Log aggregation method?
• Real-time alerting on patterns?
• Log correlation for incident investigation?
• Log retention and archival?

Compliance logging:
• Who did what, when, where, from where
• Immutable logs (tamper-evident)
• Regular log reviews
• Automated anomaly detection

Privacy logging:
• Consent changes
• Data access requests
• Data deletion requests
• Data exports
```

**Probing 30a:**
```
Logging best practices:
• Use structured logging (JSON)
• Include correlation IDs for tracing
• Don't log sensitive data (PII, passwords, tokens)
• Log enough context for investigation
• Real-time alerting for critical events

Log analysis tools:
• ELK Stack (Elasticsearch, Logstash, Kibana)
• AWS CloudWatch Logs
• Google Cloud Logging
• Datadog Logs
• Splunk

Log retention:
• Hot storage: 30-90 days (fast query)
• Warm storage: 3-6 months (medium query)
• Cold storage: 1-7 years (slow query, compliance)
```

**SATISFACTION CHECK 30 - DÙNG AskUserQuestion:**
```javascript
tool.Call("AskUserQuestion", {
  questions: [{
    question: "☝️ Audit Trail:\n• Events logged: [danh sách]\n• Retention: [duration]\n• Alerting: [có/không]\n\nBạn có hài lòng với audit plan này không?",
    header: "Xác nhận OK?"
  }]
})
```

---

### STEP 13: Operations & Support (Questions 31-33)

---

**Câu hỏi 31 (Bắt buộc) - DEPLOYMENT & INFRASTRUCTURE:**
```
📝 Câu hỏi 31/35: Môi trường deployment và infrastructure như thế nào?

Infrastructure:
• Cloud provider? (AWS, Azure, GCP, On-premise, Hybrid)
• Region? (VD: Singapore, Vietnam, US)
• Compute type? (VMs, Containers, Serverless)
• Database? (RDS, self-managed, managed service)

Environment Tiers:
| Environment | Purpose | Config |
|-------------|---------|--------|
| Development | Coding và testing | Dev configs |
| Staging | Pre-production testing | Production-like |
| Production | Live users | Full production |

CI/CD:
• CI/CD pipeline có chưa? (Jenkins, GitHub Actions, GitLab CI)
• Deployment strategy? (blue-green, canary, rolling)
• Automated testing in pipeline?
• Rollback procedure?

Environment-specific configs:
• Feature flags?
• Environment variables management?
• Secrets management?
```

**Probing 31a (nếu chưa có infrastructure plan):**
```
Bạn đã có cloud account chưa? Nếu chưa, tôi có thể recommend:
• AWS: Phổ biến nhất, nhiều dịch vụ
• Azure: Tích hợp tốt với Microsoft
• GCP: Tốt cho container/Kubernetes
• On-premise: Nếu data phải ở local

Với region, chọn gần users nhất:
• Vietnam users → Singapore hoặc Vietnam region
• US users → US region
• Global → Multi-region
```

**Probing 31b (nếu chưa có CI/CD):**
```
CI/CD pipeline giúp tự động hóa deployment và giảm lỗi human error.
Nếu chưa có, chúng ta có thể setup:
• GitHub Actions: Miễn phí cho repo public, cheap cho private
• GitLab CI: Built-in với GitLab
• Jenkins: Self-hosted, miễn phí nhưng cần server

Deployment strategy:
• Rolling: Lần lượt replace instances (đơn giản)
• Blue-green: 2 environments, switch đột ngột (an toàn hơn)
• Canary: Deploy cho % small users trước (ít risk)
```

**SATISFACTION CHECK 31:**
```
☝️ Infrastructure:
• Cloud: [provider]
• Region: [region]
• Environments: [số lượng]
• CI/CD: [có/không]

Bạn có hài lòng với infrastructure plan này không?
• Có cần bổ sung môi trường nào khác không?
• Có yêu cầu đặc biệt về region/data residency không?
```

---

**Câu hỏi 32 (Tùy chọn) - MONITORING & ALERTING:**
```
📝 Câu hỏi 32/35: Có yêu cầu monitoring và alerting như thế nào?

Monitoring:
• Infrastructure monitoring? (CPU, RAM, Disk, Network)
• Application monitoring? (APM - Application Performance Monitoring)
• Database monitoring? (queries, connections, slow logs)
• Uptime monitoring?

Metrics to track:
• Response time (latency)
• Error rate
• Throughput (requests/second)
• Resource utilization
• Business metrics (orders, revenue)

Alerting:
• Alert on what conditions?
  - Error rate > X%
  - Response time > Y seconds
  - CPU > Z%
  - Disk space < threshold
• Alert channels? (Email, SMS, Slack, PagerDuty)
• Alert severity levels? (Critical, High, Medium, Low)
• On-call rotation?
• Escalation procedure?

Dashboards:
• Real-time dashboard?
• Historical data retention?
• Custom dashboards cho stakeholders?
```

**Probing 32a (nếu chưa có monitoring):**
```
Monitoring giúp phát hiện vấn đề TRƯỚC KHI users phàn nàn.

Tool recommendations:
• Infrastructure: CloudWatch (AWS), Azure Monitor, Stackdriver (GCP)
• APM: New Relic, Datadog, Sentry (for errors)
• Uptime: Pingdom, UptimeRobot, StatusPage
• Log: ELK Stack, CloudWatch Logs, Datadog

Cost consideration:
• Basic monitoring: Miễn phí (built-in cloud tools)
• APM: $50-500/month tùy traffic
• Uptime monitoring: Miễn phí - $50/month
```

**Probing 32b (nếu cần alerting chi tiết):**
```
Alerting cần cân bằng giữa "đủ thông tin" và "không spam".

Mô hình alerting phổ biến:
• Critical: P1 - 24/7 on-call, immediate response (VD: site down)
• High: P2 - Business hours response, 4h resolution (VD: error rate > 5%)
• Medium: P3 - Next business day (VD: disk > 70%)
• Low: P4 - Backlog, investigate when able (VD: slow queries)

Alert channels:
• Slack: Nhanh, miễn phí, mọi người thấy
• SMS: Cho Critical alerts, cần phone number
• Email: Cho non-urgent, audit trail tốt
• PagerDuty: Cho on-call rotation chuyên nghiệp
```

**SATISFACTION CHECK 32:**
```
☝️ Monitoring & Alerting:
• Monitoring tools: [danh sách]
• Metrics tracked: [danh sách]
• Alert channels: [channels]
• Severity levels: [levels]

Bạn có hài lòng với monitoring plan này không?
• Có cần track thêm metrics nào không?
• Có stakeholders nào cần dashboard access không?
```

---

**Câu hỏi 33 (Tùy chọn) - SUPPORT MODEL & TRAINING:**
```
📝 Câu hỏi 33/35: Support model và training plan như thế nào?

Support Model:
• Tier 1: First line support (helpdesk)
• Tier 2: Technical support (engineers)
• Tier 3: Development team (bugs, complex issues)

Support Channels:
• Email support?
• Phone support?
• Chat/WhatsApp?
• Ticketing system? (Jira, Zendesk)
• Knowledge base?

Service Hours:
• Business hours support? (9-5, Mon-Fri)
• Extended hours?
• 24/7 support?
• SLA for response time?

Training:
• User training required?
• Training materials? (docs, videos, hands-on)
• Admin training for system administrators?
• Training schedule?

Documentation:
• User documentation?
• Admin documentation?
• API documentation?
• Deployment runbook?
```

**Probing 33a (nếu chưa có support model):**
```
Support model phụ thuộc vào criticality của hệ thống.

Mô hình phổ biến:
• Startup/SMB: Developer trực tiếp → Fast response nhưng không scalable
• Growth: Helpdesk → Jira/Zendesk → Dev team
• Enterprise: Formal Tiers → SLA contracts → Professional services

Tool choices:
• Jira Service Management: Best for IT teams
• Zendesk: Best for customer-facing support
• Freshdesk: Affordable, easy to setup
• Simple: Email + WhatsApp for small teams
```

**Probing 33b (nếu cần training):**
```
Training cần thiết nếu users không quen với system mới.

Training components:
1. User Documentation
   • How-to guides với screenshots
   • Video tutorials (5-10 phút/topic)
   • FAQ

2. Admin Documentation
   • System administration guide
   • Troubleshooting guide
   • Configuration reference

3. API Documentation (nếu có API)
   • OpenAPI/Swagger spec
   • Integration examples
   • Postman collection

Training delivery:
• Live training: Q&A tốt, nhưng cần schedule
• Self-paced: Users learn theo tốc độ riêng
• Documentation: Luôn có sẵn, nhưng có thể out of date
```

**SATISFACTION CHECK 33:**
```
☝️ Support Model:
• Tiers: [T1/T2/T3 hoặc single level]
• Channels: [danh sách]
• Hours: [business hours/24-7]
• Ticketing: [tool]

Training & Documentation:
• Training required: [có/không]
• Doc types: [user/admin/API]
• Knowledge base: [có/không]

Bạn có hài lòng với support model này không?
• SLA requirements đã rõ chưa?
• Training schedule đã xác định chưa?
```

---

### STEP 14: Project Governance (Questions 34-35)

---

**Câu hỏi 34 (Bắt buộc) - SUCCESS METRICS & ACCEPTANCE:**
```
📝 Câu hỏi 34/35: Làm sao để đo lường thành công của dự án?

Success Metrics (KPIs):
| KPI | Current | Target | Measurement |
|-----|---------|--------|-------------|
| Page load time | 5s | < 2s | P95 |
| Order completion rate | 60% | > 80% | % orders completed |
| Support tickets | 100/week | < 50/week | Weekly count |
| System uptime | 99% | 99.9% | % availability |

Baseline:
• Có baseline metrics hiện tại để so sánh không?
• Đo lường trong bao lâu trước launch?

Acceptance Criteria:
• Khi nào thì project được coi là thành công?
• User acceptance criteria (UAT) có defined không?
• Sign-off process?
• Definition of Done?

Post-implementation:
• Post-implementation review khi nào?
• Lessons learned session?
• Continuous improvement plan?
```

**Probing 34a (nếu chưa có KPIs):**
```
KPIs giúp đo lường ROI của dự án và xác định success.

Cách xác định KPIs tốt (SMART):
• Specific: Cụ thể cái gì
• Measurable: Đo lường được
• Achievable: Có thể đạt được
• Relevant: Liên quan đến business goals
• Time-bound: Có thời hạn

Examples:
• Performance: "Page load < 2s (P95)" thay vì "nhanh"
• Conversion: "Order completion rate > 80%" thay vì "nhiều đơn hàng"
• Adoption: "50% users active weekly within 1 month" thay vì "users dùng nhiều"
```

**Probing 34b (nếu cần UAT process):**
```
User Acceptance Testing (UAT) là bước cuối trước go-live.

UAT Process:
1. Preparation (Week before UAT)
   • UAT test cases written và approved
   • Test environment ready
   • Users notified và scheduled

2. Execution (1-2 weeks)
   • Users execute test cases
   • Bugs logged và triaged
   • Daily standups để track progress

3. Sign-off
   • All P1/P2 bugs fixed
   • User sign-off document signed
   • Go/No-go meeting

Definition of Done:
• Code complete
• Unit tests pass
• Integration tests pass
• UAT pass
• Documentation complete
• Deployment approved
```

**SATISFACTION CHECK 34:**
```
☝️ Success Metrics:
• KPIs: [số lượng] defined
• Baseline: [có/không - giá trị]
• Targets: [rõ ràng/mơ hồ]

Acceptance:
• UAT defined: [có/không]
• Sign-off process: [mô tả]
• Definition of Done: [mô tả]

Bạn có hài lòng với success metrics và acceptance criteria này không?
• KPIs đã align với business goals chưa?
• UAT process đã rõ ràng chưa?
```

---

**Câu hỏi 35 (Bắt buộc) - TIMELINE & PHASES:**
```
📝 Câu hỏi 35/35: Timeline và các phases của dự án như thế nào?

Timeline:
• Start date:
• Target end date:
• Total duration: ? months / ? weeks

Phases/Milestones:
| Phase | Deliverables | Target Date |
|-------|--------------|-------------|
| Discovery | SRS, Architecture | Week 1-2 |
| Design | UI/UX, SDD | Week 3-4 |
| Development | MVP | Week 5-10 |
| Testing | UAT, Bug fixes | Week 11-12 |
| Go-Live | Deployment | Week 13 |
| Stabilization | Post-launch support | Week 14-16 |

Constraints:
• Hard deadline? (VD: phải xong trước Tết, before fiscal year end)
• Dependencies on external parties?
• Holiday periods where work stops?

Budget:
• Approved budget:
• Contingency budget:
• Cost breakdown by phase?
```

**Probing 35a (nếu timeline chưa rõ ràng):**
```
Timeline cần realistic và có buffer cho unexpected delays.

Rule of thumb:
• 1 week development → 0.5-1 week buffer
• Complex project → 20-30% time buffer
• Dependencies → Add 1-2 weeks buffer

Phase duration estimates:
• Discovery (SRS): 1-2 weeks
• Design (UI/UX, SDD): 2-4 weeks
• Development (MVP): 4-12 weeks (tùy scope)
• Testing (UAT): 2-4 weeks
• Go-Live: 1 week
• Stabilization: 2-4 weeks

Minimum viable timeline:
• Small project (1-2 features): 6-8 weeks
• Medium project (5-10 features): 12-16 weeks
• Large project (10+ features): 20+ weeks
```

**Probing 35b (nếu có constraints đặc biệt):**
```
Constraints ảnh hưởng đến timeline và scope.

Hard deadlines phổ biến:
• "Must ship before Tết" → Count backwards từ Tết
• "Must launch before competitor" → Compress timeline
• "Fiscal year end" → Q4 deadline pressure
• "Trade show demo" → MVP only acceptable

Dependencies cần track:
• External APIs → Need API spec before dev
• Third-party vendors → Lead time for contracts
• Data migration → Need old data export
• User training → Need materials before UAT

Budget considerations:
• Hourly rate vs fixed price
• Contingency (10-20% recommended)
• Phase-based payments
```

**SATISFACTION CHECK 35:**
```
☝️ Timeline:
• Start: [date]
• End: [date]
• Duration: [X weeks/months]

Phases:
• Discovery: [date range]
• Design: [date range]
• Development: [date range]
• Testing: [date range]
• Go-Live: [date]
• Stabilization: [date range]

Constraints:
• Hard deadline: [có/không - mô tả]
• Dependencies: [danh sách]
• Budget: [approved amount]

Bạn có hài lòng với timeline này không?
• Deadline có realistic không?
• Có phase nào cần adjust không?
```

---

### STEP 36: FINAL CONFIRMATION (Sau khi hoàn thành phỏng vấn)

---

**FINAL CONFIRMATION - XÁC NHẬN YÊU CẦU CUỐI CÙNG:**
```
╔══════════════════════════════════════════════════════════════════════════════════════╗
║  📋 KIỂM TRA CUỐI CÙNG - XÁC NHẬN YÊU CẦU                                      ║
╠══════════════════════════════════════════════════════════════════════════════════════╣
║                                                                                      ║
║  1. PROJECT CONTEXT (4 câu)                                                       ║
║     ✅ Tên dự án: [tên]                                                            ║
║     ✅ Mô tả hệ thống: [mô tả]                                                     ║
║     ✅ Hệ thống cũ: [có/không]                                                     ║
║     ✅ Vấn đề kinh doanh: [mô tả]                                                  ║
║                                                                                      ║
║  2. USERS & ACTORS (3 câu)                                                          ║
║     ✅ Actors: [danh sách với permissions]                                         ║
║     ✅ Quyền hạn: [mô tả chi tiết]                                                 ║
║     ✅ Guest users: [có/không]                                                      ║
║                                                                                      ║
║  3. FUNCTIONAL REQUIREMENTS (5 câu)                                                ║
║     ✅ Features: [số lượng] tính năng đã liệt kê                                   ║
║     ✅ MoSCoW: Must: [X], Should: [Y], Could: [Z]                                   ║
║     ✅ Business rules: [số lượng] quy tắc                                           ║
║     ✅ Error handling: [mô tả]                                                     ║
║                                                                                      ║
║  4. DATA MODEL (3 câu)                                                             ║
║     ✅ Entities: [số lượng] entities                                                ║
║     ✅ Fields: [mô tả]                                                              ║
║     ✅ Relationships: [mô tả]                                                     ║
║                                                                                      ║
║  5. INTEGRATION & INTERFACES (4 câu)                                                ║
║     ✅ External systems: [danh sách]                                               ║
║     ✅ API specs: [mô tả]                                                          ║
║     ✅ Data exchange: [format, frequency]                                          ║
║     ✅ Import/Export: [formats]                                                     ║
║                                                                                      ║
║  6. PLATFORM & UI (3 câu)                                                           ║
║     ✅ Platforms: [Web/Mobile/Desktop/API]                                          ║
║     ✅ Responsive: [có/không]                                                      ║
║     ✅ Accessibility/i18n: [mô tả]                                                  ║
║                                                                                      ║
║  7. NON-FUNCTIONAL (5 câu)                                                          ║
║     ✅ Performance: [response time targets]                                         ║
║     ✅ Scalability: [concurrent users]                                              ║
║     ✅ Availability: [SLA %]                                                        ║
║     ✅ Backup & Recovery: [RPO/RTO]                                                 ║
║     ✅ Security: [auth methods, encryption]                                          ║
║                                                                                      ║
║  8. COMPLIANCE & RISK (3 câu)                                                      ║
║     ✅ Regulatory compliance: [frameworks]                                          ║
║     ✅ Data classification: [levels]                                                ║
║     ✅ Audit trail: [mô tả]                                                         ║
║                                                                                      ║
║  9. OPERATIONS & SUPPORT (3 câu)                                                    ║
║     ✅ Infrastructure: [cloud/provider/region/environments]                         ║
║     ✅ CI/CD: [có/không, tool]                                                      ║
║     ✅ Monitoring: [tools/alerts]                                                   ║
║     ✅ Support model: [tiers/hours/channels]                                       ║
║     ✅ Training: [có/không, materials]                                              ║
║                                                                                      ║
║  10. PROJECT GOVERNANCE (2 câu)                                                    ║
║     ✅ Success metrics: [KPIs với targets]                                          ║
║     ✅ Acceptance: [UAT/sign-off/DOD]                                                ║
║     ✅ Timeline: [phases/dates]                                                     ║
║     ✅ Budget: [approved amount]                                                    ║
║                                                                                      ║
╠══════════════════════════════════════════════════════════════════════════════════════╣
║                                                                                      ║
║  📊 SUMMARY:                                                                        ║
║  ────────────────────────────────────────────────────────────────────────────────    ║
║  Tổng số câu hỏi: 35                                                                ║
║  Bắt buộc ✅ đã trả lời: [X]/26                                                    ║
║  Tùy chọn ⚠️ đã trả lời: [Y]/9                                                     ║
║  Tùy chọn ❌ đã bỏ qua: [Z]/9                                                      ║
║                                                                                      ║
║  ⚠️ Cảnh báo: [Nếu có câu bắt buộc chưa trả lời]                                   ║
║                                                                                      ║
╠══════════════════════════════════════════════════════════════════════════════════════╣
║                                                                                      ║
║  ✅ Nếu mọi thứ đã CHÍNH XÁC, gõ "XÁC NHẬN" để tạo SRS                            ║
║  📝 Nếu cần SỬA, cho tôi biết câu hỏi số mấy cần sửa                               ║
║  ➕ Nếu muốn BỔ SUNG, cho tôi biết thêm gì                                          ║
║  ❌ Nếu muốn BỎ QUA câu nào, cho biết lý do                                        ║
║                                                                                      ║
╚══════════════════════════════════════════════════════════════════════════════════════╝
```

**Probing 36a (nếu có câu bắt buộc chưa trả lời):**
```
⚠️ Cảnh báo: Có [X] câu hỏi BẮT BUỘC chưa được trả lời đầy đủ:

[Danh sách câu hỏi chưa complete]

Những câu hỏi này có thể ảnh hưởng đến:
• Độ chính xác của SRS
• Ước lượng timeline/budget
• Technical decisions

Bạn có muốn:
A) Tiếp tục trả lời những câu này ngay
B) Tạm thời đánh dấu là [TBD] và proceed
C) Chấp nhận risk và tiếp tục với assumptions
```

**Probing 36b (nếu user muốn sửa câu cũ):**
```
Không vấn đề! Cho tôi biết:
• Câu hỏi số mấy cần sửa?
• Thông tin cũ là gì?
• Thông tin mới là gì?

Tôi sẽ cập nhật và cho bạn xác nhận lại.
```

---

### STEP 37: TẠO REQUIREMENTS SUMMARY (Sau khi user xác nhận "XÁC NHẬN")

**CHỈ SAU KHI USER XÁC NHẬN "XÁC NHẬN"** → Tạo tài liệu SRS:

```markdown
# Software Requirements Specification (SRS) - [Project Name]
**Version:** 1.0
**Date:** [Timestamp]
**Author:** Requirements Gathering Agent v3.2.0

---

## 1. Introduction

### 1.1 Purpose
[Mô tả mục đích của tài liệu này]

### 1.2 Scope
[Mô tả phạm vi hệ thống]

### 1.3 Definitions, Acronyms, and Abbreviations
| Term | Definition |
|------|------------|
| SRS | Software Requirements Specification |
| MVP | Minimum Viable Product |
| UAT | User Acceptance Testing |

### 1.4 References
[Tài liệu tham khảo]

---

## 2. Overall Description

### 2.1 Product Perspective
[Mô tả hệ thống mới trong bối cảnh]

### 2.2 System Overview
| Component | Description |
|-----------|-------------|
| [Component 1] | [Description] |
| [Component 2] | [Description] |

### 2.3 Users and Characteristics
| User Type | Description | Count (approx) |
|-----------|-------------|----------------|
| [Type 1] | [Description] | [X] |
| [Type 2] | [Description] | [Y] |

### 2.4 Operational Environment
- **Platform:** [Web/Mobile/Desktop/API]
- **Cloud Provider:** [AWS/Azure/GCP/On-premise]
- **Region:** [Region]
- **Environments:** Dev, Staging, Production

### 2.5 Dependencies
| External System | Purpose | Interface |
|-----------------|---------|-----------|
| [System 1] | [Purpose] | [API/File/Other] |
| [System 2] | [Purpose] | [API/File/Other] |

---

## 3. Functional Requirements

### 3.1 Feature List (MoSCoW)

#### Must Have (Bắt buộc - Blockers nếu thiếu)
| ID | Feature | Description | Priority |
|----|---------|-------------|----------|
| F01 | [Feature Name] | [Description] | Must |
| F02 | [Feature Name] | [Description] | Must |

#### Should Have (Nên có - Important nhưng không block)
| ID | Feature | Description | Priority |
|----|---------|-------------|----------|
| F10 | [Feature Name] | [Description] | Should |

#### Could Have (Có thể có - Nice to have)
| ID | Feature | Description | Priority |
|----|---------|-------------|----------|
| F20 | [Feature Name] | [Description] | Could |

### 3.2 User Interactions and Flows

#### [Flow 1: Tên flow]
```
Actor: [Actor name]
Trigger: [What starts this flow]
Steps:
1. [Step 1]
2. [Step 2]
3. [Step 3]
Result: [Expected outcome]
Errors: [Error cases]
```

### 3.3 Data Handling

#### Data Entities
| Entity | Fields | Relationships |
|--------|--------|---------------|
| [Entity 1] | [field1, field2, ...] | [Relationships] |
| [Entity 2] | [field1, field2, ...] | [Relationships] |

#### Data Storage
- **Database Type:** [RDS/Self-managed/Managed service]
- **Estimated Size:** [Size]
- **Retention Policy:** [Policy]

### 3.4 Business Rules
| Rule ID | Rule | Condition | Action |
|---------|------|-----------|--------|
| BR01 | [Rule name] | [When] | [Action] |

### 3.5 Error Handling
| Error Case | Handling |
|------------|----------|
| [Case 1] | [Handling approach] |

---

## 4. Non-Functional Requirements

### 4.1 Performance
| Metric | Target | Measurement |
|--------|--------|-------------|
| Page load | < [X]s | P95 |
| API response | < [X]ms | P95 |
| Concurrent users | [X] users | Peak |

### 4.2 Scalability
- **Current capacity:** [X] users
- **Expected growth:** [%/month]
- **Scaling strategy:** [Horizontal/Vertical/Auto-scaling]

### 4.3 Availability
- **SLA:** [X]% uptime
- **Downtime allowed:** [X] hours/year
- **Maintenance window:** [Time slot]

### 4.4 Security
- **Authentication:** [Methods]
- **Authorization:** [Model]
- **Data encryption:** [At-rest/In-transit]
- **Audit logging:** [Yes/No]

### 4.5 Backup & Recovery
- **RPO (Recovery Point Objective):** [X] hours
- **RTO (Recovery Time Objective):** [X] minutes
- **Backup frequency:** [Daily/Weekly]

### 4.6 Compliance
| Regulation | Requirement | Implementation |
|------------|-------------|----------------|
| [GDPR/PDPA/PCI-DSS] | [Requirement] | [How to implement] |

---

## 5. Interface Requirements

### 5.1 User Interfaces
| Screen | Purpose | Key Features |
|--------|---------|--------------|
| [Screen 1] | [Purpose] | [Features] |

### 5.2 API Interfaces
| Endpoint | Method | Purpose |
|----------|--------|---------|
| /api/v1/[resource] | GET/POST/PUT/DELETE | [Purpose] |

### 5.3 Integration Interfaces
| External System | Interface Type | Data Flow |
|-----------------|----------------|-----------|
| [System] | [API/File] | [Direction] |

---

## 6. Supporting Processes

### 6.1 Deployment
- **Cloud Provider:** [AWS/Azure/GCP]
- **Region:** [Region]
- **Environments:** Dev, Staging, Production
- **CI/CD:** [Tool]
- **Deployment Strategy:** [Blue-green/Canary/Rolling]

### 6.2 Monitoring & Alerting
- **Monitoring Tools:** [Tools]
- **Alert Channels:** [Channels]
- **Severity Levels:** [P1/P2/P3/P4]

### 6.3 Support Model
| Tier | Description | Response Time |
|------|-------------|---------------|
| Tier 1 | [Description] | [SLA] |
| Tier 2 | [Description] | [SLA] |
| Tier 3 | [Description] | [SLA] |

### 6.4 Training
- **User Training:** [Yes/No - materials]
- **Admin Training:** [Yes/No - materials]
- **Documentation:** [User/Admin/API docs]

---

## 7. Success Metrics & Acceptance

### 7.1 Success Metrics (KPIs)
| KPI | Baseline | Target | Measurement |
|-----|----------|--------|-------------|
| [KPI 1] | [Value] | [Value] | [Method] |

### 7.2 Acceptance Criteria
- **UAT:** [Defined/Not defined]
- **Sign-off Process:** [Process description]
- **Definition of Done:** [Criteria]

### 7.3 Timeline
| Phase | Start | End | Deliverables |
|-------|-------|-----|--------------|
| Discovery | [Date] | [Date] | SRS, Architecture |
| Design | [Date] | [Date] | UI/UX, SDD |
| Development | [Date] | [Date] | MVP |
| Testing | [Date] | [Date] | UAT, Bug fixes |
| Go-Live | [Date] | [Date] | Deployment |
| Stabilization | [Date] | [Date] | Post-launch support |

### 7.4 Budget
- **Approved Budget:** [Amount]
- **Contingency:** [Amount]

---

## 8. Open Questions
| # | Question | Status | Notes |
|---|----------|--------|-------|
| 1 | [Question] | [Open/Resolved/TBD] | [Notes] |

---

## 9. Assumptions & Dependencies
| Category | Item | Impact |
|----------|------|--------|
| [Category] | [Assumption] | [Impact] |

---

## 10. Appendix

### A. Interview Summary
- **Interview Date:** [Date]
- **Duration:** [Duration]
- **Participants:** [Names/Roles]
- **Questions Answered:** [X]/35

### B. Change History
| Version | Date | Author | Changes |
|---------|------|--------|---------|
| 1.0 | [Date] | [Agent] | Initial SRS |

---

**Document Status:** Draft → Review → Approved → Final
**Next Review Date:** [Date]
```

---

## Reference

Sử dụng SRS_Questions_Catalogue.md để:
- Tìm câu hỏi chi tiết hơn cho từng category
- Tra cứu câu hỏi kỹ thuật (Technical probing)
- Bổ sung câu hỏi nếu cần
