---
name: srs-writing
description: |
  Viết tài liệu Software Requirement Specification (SRS) theo chuẩn IEEE, LINK đến external PlantUML files thay vì embed trực tiếp.

  SỬ DỤNG KHI:
  - Cần tạo tài liệu SRS từ đầu
  - Cần hoàn thiện tài liệu SRS đã thu thập
  - Cần review SRS
  - Dự án phần mềm cần tài liệu yêu cầu
version: "4.0.0"
allowed-tools: ["Read", "Write", "Edit", "Glob"]
mode: false
---

# SRS Writing Skill

## Mục đích
Viết tài liệu Software Requirement Specification (SRS) hoàn chỉnh theo template chuẩn IEEE. **LINK đến external PlantUML files** thay vì embed trực tiếp vào tài liệu.

## Nguyên tắc quan trọng

### KHÔNG embed PlantUML trực tiếp
- ❌ KHÔNG viết `@startuml...@enduml` trong body của tài liệu SRS
- ✅ SỬ DỤNG link/reference đến external PlantUML files
- ✅ Diagrams được tạo bởi các `uc-diagram-agent` spawn song song

### Lý do:
1. Tách biệt concerns: SRS document = text, Diagrams = PlantUML files
2. Parallel generation: N agents tạo diagrams song song, không blocking nhau
3. Maintainability: Dễ update diagrams riêng lẻ
4. Renderability: IDE/plugin render PlantUML từ file riêng

---

## Template SRS với External Links

### I. Record of Changes

| Version | Date | A/M/D* | In charge | Change Description |
| ----- | ----- | ----- | ----- | ----- |
| V1.0 | YYYY-MM-DD | A | Name | Initial version |

*A - Added M - Modified D - Deleted

---

### II. Software Requirement Specification

## 1. Product Overview

**1.1 Purpose**
- Mô tả mục đích của tài liệu
- Mô tả sản phẩm được phát triển
- Giới hạn của tài liệu

**1.2 Product Scope**
- Tên sản phẩm
- Các tính năng chính
- Mục tiêu kinh doanh

**1.3 Target Users**
- Người dùng cuối
- Quản trị viên
- Nhân viên hỗ trợ

**1.4 Context Diagram**

*Xem chi tiết:* [Context Diagram](./diagrams/context-diagram.puml)

---

## 2. User Requirements

### 2.1 Actors

| # | Actor | Description |
| :---- | :---- | :---- |
| 1 | Super Admin | Manage and configure global system settings |
| 2 | Store Manager | Manage store-level operations |
| 3 | Staff | Operational users for sales and inventory |

### 2.2 Use Cases

---

#### **2.2.1 [Use Case Group Name]**

**Use Case Diagram:** [UC-XX - Name](./diagrams/uc-xx/uc-xx-use-case.puml)

| ID and Name: | UC-XX [Use Case Name] |  |  |
| ----: | :---- | ----: | :---- |
| Created By: | AuthorName | Date Created: | YYYY-MM-DD |
| Primary Actor: | [Primary Actor] | Secondary Actors: | [Secondary Actors] |
| Description: | [Detailed description of the use case] |  |  |
| Trigger: | [What triggers this use case] |  |  |
| Preconditions: | **PRE-1:** [Precondition 1] **PRE-2:** [Precondition 2] |  |  |
| Postconditions: | **POST-1 (Success):** [Postcondition on success] |  |  |
| Normal Flow: | 1. [Step 1] 2. [Step 2] 3. [Step 3] |  |  |
| Alternative Flows: | **A1. [Alternative Flow 1]** Trigger: [Condition] |  |  |
| Exceptions: | **E1. [Exception 1]** Trigger: [Error condition] |  |  |

**Diagrams:**
- Use Case: [UC-XX](./diagrams/uc-xx/uc-xx-use-case.puml)
- Sequence: [UC-XX](./diagrams/uc-xx/uc-xx-sequence.puml)

---

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
> **AC-01-01 (Happy Path):**
> ```
> Given: User chưa đăng nhập, tài khoản active và email đã verify
> When:  User nhập email + password đúng và submit
> Then:  Hệ thống trả về access_token và refresh_token
> And:   User được chuyển đến Dashboard
> ```
>
> **AC-01-02 (Wrong Password):**
> ```
> Given: User chưa đăng nhập
> When:  User nhập đúng email nhưng sai password
> Then:  Hệ thống hiển thị lỗi "Email hoặc mật khẩu không đúng"
> And:   Không tiết lộ field nào sai
> ```
>
> **AC-01-03 (Account Disabled):**
> ```
> Given: Tài khoản bị disable (is_active = false)
> When:  User nhập đúng email + password
> Then:  Hệ thống hiển thị "Tài khoản đã bị vô hiệu hóa"
> ```

---

## 3. Software Features

**3.1 Functional Overview**

**3.2 <<Feature Name>>**

| Item | Description |
|------|-------------|
| Feature ID | F1 |
| Feature Name | |
| Description | |
| Priority | Must / Should / Could / Won't |

**Functional Requirements:**
- FR1.1:
- FR1.2:

**User Interactions:**
1. [Step 1]
2. [Step 2]

**Diagrams:**
- Screen Flow: [UC-XX Screenflow](./diagrams/uc-xx/uc-xx-screenflow.puml)
- State: [UC-XX State](./diagrams/uc-xx/uc-xx-statediagram.puml)

---

## 4. Non-Functional Requirements

### 4.1 Performance Requirements
- Response time: < 200ms (P95)
- Throughput: 1000 concurrent users

### 4.2 Security Requirements
- Authentication: JWT tokens
- Authorization: RBAC

### 4.3 Availability
- Uptime: 99.9%

### 4.4 Scalability
- Horizontal scaling support

---

## 5. Requirement Appendix

**5.1 Business Rules**

| Rule ID | Rule | Description |
|---------|------|-------------|
| BR1 | | |

**5.2 Common Requirements**

| Requirement ID | Description |
|----------------|-------------|
| CR1 | |

**5.3 Application Messages List**

| Message Type | Format | Description |
|--------------|--------|-------------|
| | | |

**5.4 Other Requirements**
> [Các yêu cầu khác]

### 5.5 Requirements Traceability Index (RTI)

> Bảng tóm tắt linking — chi tiết đầy đủ xem `traceability/RTM.md`.

| FR ID | Feature | UC | Priority | Status |
|-------|---------|-----|----------|--------|
| FR-1.1 | {Feature name} | UC-01, UC-02 | Must | Draft |
| FR-1.2 | {Feature name} | UC-03 | Must | Draft |
| FR-2.1 | {Feature name} | UC-04 | Should | Draft |

*Cột Status: Draft → Reviewed → Approved → Implemented → Tested → Done*

---

## Output File
Tài liệu SRS được lưu với format: `SRS_{ProjectName}_v{Version}.md`

## Diagrams Directory Structure

```
diagrams/
├── context-diagram.puml              # System context
├── entity-relationship.puml           # Overall ER diagram
├── screen-flow.puml                   # System-wide screen flow
├── uc-01/
│   ├── uc-01-use-case.puml           # Use case diagram
│   ├── uc-01-screenflow.puml         # Screen flow
│   ├── uc-01-statediagram.puml       # State machine
│   ├── uc-01-sequence.puml            # Sequence diagram
│   ├── uc-01-class-backend.puml       # Backend classes
│   └── uc-01-class-frontend.puml      # Frontend classes
├── uc-02/
│   └── ...
└── ...
```

## Quality Checklist

- [ ] Tất cả stakeholders đã được phỏng vấn
- [ ] Tất cả actors đã được xác định
- [ ] Context Diagram: [context-diagram.puml](./diagrams/context-diagram.puml) ✓
- [ ] ER Diagram: [entity-relationship.puml](./diagrams/entity-relationship.puml) ✓
- [ ] Screen Flow: [screen-flow.puml](./diagrams/screen-flow.puml) ✓
- [ ] Mỗi UC có đầy đủ 6 diagram files
- [ ] Mỗi UC có ít nhất 3 Acceptance Criteria (Given-When-Then)
- [ ] Functional requirements đầy đủ
- [ ] Non-functional requirements cụ thể, đo lường được
- [ ] Section 5.5 RTI đã điền đủ FR → UC mapping
- [ ] Tài liệu đã được review bởi stakeholders

## Commands

Sử dụng command `/create-srs` để trigger skill này.