---
name: interview-stakeholder
description: Phỏng vấn stakeholder để thu thập yêu cầu
agent: doc-coordinator
syntax: /interview-stakeholder [stakeholder_name] [--type user|manager|developer|customer]
examples:
  - "/interview-stakeholder John --type user"
  - "/interview-stakeholder"
parameters:
  - name: stakeholder_name
    type: string
    required: false
    description: Tên stakeholder cần phỏng vấn
  - name: type
    type: string
    required: false
    default: user
    description: Loại stakeholder
---

# Command: /interview-stakeholder

## Mô tả
Hỗ trợ phỏng vấn stakeholder để thu thập yêu cầu phần mềm.

## Quy trình

### Bước 1: Xác định Stakeholder
- Xác định loại stakeholder (User, Manager, Developer, Customer)
- Chuẩn bị câu hỏi phù hợp

### Bước 2: Phỏng vấn
Sử dụng bộ câu hỏi mẫu theo loại:

#### User Stakeholder Questions
1. Bạn sử dụng hệ thống để làm gì?
2. Các tác vụ chính bạn thực hiện hàng ngày?
3. Điều gì làm bạn frustrate với hệ thống hiện tại?
4. Bạn cần những tính năng gì?
5. Các vấn đề bạn gặp phải là gì?

#### Manager Stakeholder Questions
1. Mục tiêu kinh doanh chính?
2. Các KPIs cần đạt được?
3. Budget và timeline?
4. Các ràng buộc nghiệp vụ?
5. Ai là người ra quyết định?

#### Developer Stakeholder Questions
1. Technical constraints?
2. Integration requirements?
3. Performance expectations?
4. Security requirements?
5. Deployment environment?

### Bước 3: Ghi nhận và Tổng hợp
- Lưu câu trả lời
- Trích xuất requirements
- Tạo requirement artifacts

## Output

```
Created: interview_{stakeholder_name}_{date}.md
Location: ./docs/interviews/
```
