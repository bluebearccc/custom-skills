---
name: create-diagram
description: Tạo UML diagram từ mô tả text
syntax: /create-diagram [type] --name [name] --description [desc]
examples:
  - "/create-diagram use-case --name OrderProcess --description 'Customer orders product'"
  - "/create-diagram sequence --name Login --description 'User login flow'"
types:
  - use-case
  - class
  - sequence
  - component
  - deployment
  - activity
---

# Tool: create-diagram

## Mô tả
Tạo UML diagram sử dụng Mermaid syntax từ mô tả text.

## Các loại Diagram

### 1. Use Case Diagram
```mermaid
graph TD
    A[Actor] -->|action| B[Use Case]
```

### 2. Class Diagram
```mermaid
classDiagram
    Class01 <|-- Class02
    Class03 *-- Class04
```

### 3. Sequence Diagram
```mermaid
sequenceDiagram
    A->>B: Message
    B-->>A: Response
```

### 4. Component Diagram
```mermaid
componentDiagram
    ComponentA --> ComponentB
```

### 5. Deployment Diagram
```mermaid
graph LR
    A[Node A] --> B[Node B]
```

### 6. Activity Diagram
```mermaid
graph TD
    A[Start] --> B{Decision}
    B -->|Yes| C[Activity]
    B -->|No| D[End]
```

## Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| type | string | Yes | Loại diagram |
| name | string | Yes | Tên diagram |
| description | string | Yes | Mô tả chi tiết |
| output | string | No | Đường dẫn output |

## Output

Mermaid code và preview (nếu hỗ trợ)

## Usage Example

```
User: /create-diagram sequence --name UserLogin --description "User enters credentials, system validates, returns token"

Agent: Creating sequence diagram...
Agent: Generated Mermaid code:
```mermaid
sequenceDiagram
    participant User
    participant System
    participant Auth
    
    User->>System: Enter credentials
    System->>Auth: Validate credentials
    Auth-->>System: Token
    System-->>User: Login success
```
```
