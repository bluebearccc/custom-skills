---
name: create-diagram
description: Generate a UML diagram from a text description
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

## Description
Generate a UML diagram using Mermaid syntax from a text description.

## Diagram Types

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
| type | string | Yes | Diagram type |
| name | string | Yes | Diagram name |
| description | string | Yes | Detailed description |
| output | string | No | Output path |

## Output

Mermaid code and preview (if supported)

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
</output>
