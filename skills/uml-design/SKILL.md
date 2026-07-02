---
name: uml-design
description: |
  Tạo các sơ đồ UML cho tài liệu thiết kế phần mềm bằng PlantUML.
  
  SỬ DỤNG KHI:
  - Cần tạo use case diagram
  - Cần tạo screen flow diagram
  - Cần tạo stage/state diagram
  - Cần tạo entity relationship diagram
  - Cần tạo sequence diagram
  - Cần tạo deployment diagram
  - Cần tạo integration diagram
  - Cần tạo component interaction diagram
  - Cần tạo deployment topology per environment
  - Cần tạo data flow diagram (DFD)
version: "3.0.0"
allowed-tools: ["Read", "Write", "Glob"]
mode: false
---

# UML Design Skill (PlantUML)

## Mục đích
Tạo các sơ đồ UML chuyên nghiệp bằng PlantUML cho tài liệu SRS và SDD.

## Output Format

Tất cả UML diagrams được tạo bằng **PlantUML**. Mỗi diagram bao gồm:
- `@startuml` / `@enduml` delimiters
- Diagram code có thể copy-paste trực tiếp vào PlantUML renderer

## Output Files Convention

**QUAN TRỌNG**: Mỗi diagram phải được lưu trong **FILE RIÊNG BIỆT** (.puml). KHÔNG gộp nhiều diagram vào một file.

### Per-UC Diagrams Structure (Theo từng Use Case):
```
diagrams/
├── uc-01-login/
│   ├── uc-01-use-case.puml
│   ├── uc-01-screenflow.puml
│   ├── uc-01-statediagram.puml
│   ├── uc-01-class-backend.puml
│   ├── uc-01-class-frontend.puml
│   └── uc-01-sequence.puml
├── uc-02-register/
│   ├── uc-02-use-case.puml
│   ├── uc-02-screenflow.puml
│   ├── uc-02-statediagram.puml
│   ├── uc-02-class-backend.puml
│   ├── uc-02-class-frontend.puml
│   └── uc-02-sequence.puml
├── uc-03-forgot-password/
│   └── ...
└── README.md (index của tất cả diagrams)
```

### System-Level Diagrams:
```
diagrams/
├── context-diagram.puml
├── system-overview.puml
├── entity-relationship.puml
├── layered-architecture.puml
├── client-server.puml
├── integration.puml
├── deployment.puml
├── deployment-dev.puml
├── deployment-staging.puml
├── deployment-prod.puml
├── dfd-level1.puml
└── components/
    └── component-interaction.puml
```

### File Naming Convention:
- Format: `{uc-XX}-{diagram-type}.puml`
- Use 2-digit UC number: `uc-01`, `uc-02`, etc.
- Diagram types: `use-case`, `screenflow`, `statediagram`, `class-backend`, `class-frontend`, `sequence`
- System diagrams: `context-diagram`, `system-overview`, `entity-relationship`, `layered-architecture`, `client-server`, `integration`, `deployment`

### Required Deliverables:
1. **Mỗi diagram = 1 file .puml riêng** với @startuml/@enduml delimiters
2. **Index README.md** liệt kê tất cả diagrams đã tạo
3. **System-level diagrams** cho tổng quan kiến trúc
4. **Per-UC diagrams** cho chi tiết từng use case

## Các loại Diagram

### 1. Use Case Diagram

**Mục đích**: Mô tả các chức năng hệ thống từ góc nhìn người dùng

**Template**:
```
@startuml
left to right direction
skinparam packageStyle rectangle
actor ActorName as alias
actor "Description" as alias2
rectangle System {
  alias -- (UC1)
  alias2 -- (UC2)
}
@enduml
```

**Ví dụ hoàn chỉnh**:
```
@startuml
left to right direction
skinparam packageStyle rectangle
actor Guest as g
actor Member as m
actor Seller as s
rectangle System {
  g -- (UC1: Browse Products)
  g -- (UC2: Register Account)
  m -- (UC3: Place Order)
  m -- (UC4: View Order History)
  s -- (UC5: Manage Inventory)
  s -- (UC6: Process Orders)
}
@enduml
```

**Các thành phần**:
- `actor` - Actor người dùng hoặc hệ thống bên ngoài
- `rectangle System` - Phạm vi hệ thống
- `-->` - Association relationship
- `(text)` - Use case

---

### 2. Screen Flow Diagram (Activity)

**Mục đích**: Mô tả luồng điều hướng màn hình và logic nghiệp vụ

**Template**:
```
@startuml
skinparam activityDotColor red
start
:Screen Name;
if (Condition?) then (yes)
  :Next Action;
else (no)
  :Error Action;
endif
stop
@enduml
```

**Ví dụ hoàn chỉnh**:
```
@startuml
skinparam activityDotColor red
skinparam activityBackgroundColor #FEFEFE
skinparam activityBorderColor #333333
skinparam activityDiamondBackgroundColor #FEFEFE
skinparam activityDiamondBorderColor #333333

title User Authentication Flow

start
:Login Screen;
if (Credentials valid?) then (yes)
  :Dashboard;
  :View Products;
  :Add to Cart;
  :Checkout;
  :Payment Processing;
  if (Payment success?) then (yes)
    :Order Confirmed;
    :Send Confirmation Email;
  else (no)
    :Show Payment Error;
    :Retry Payment;
  endif
else (no)
  :Show Error Message;
  :Login Screen;
endif
stop
@enduml
```

**Các thành phần**:
- `start` / `stop` - Điểm bắt đầu/kết thúc
- `:` activity `;` - Hành động
- `if` / `then` / `else` / `endif` - Điều kiện rẽ nhánh
- `fork` / `end fork` - Xử lý song song

---

### 3. State Diagram

**Mục đích**: Mô tả vòng đời của một entity (trạng thái và chuyển đổi)

**Template**:
```
@startuml
[*] --> State1
State1 --> State2 : Event
State2 --> [*] : Done
@enduml
```

**Ví dụ hoàn chỉnh**:
```
@startuml
title Order State Machine

[*] --> Pending : Create Order

Pending --> Confirmed : Payment Success
Pending --> Cancelled : User Cancel / Timeout

Confirmed --> Processing : Admin Confirm
Confirmed --> Cancelled : Admin Cancel

Processing --> Shipped : Dispatch
Shipped --> Delivered : Delivery Confirmed
Delivered --> [*]
Cancelled --> [*]

note right of Processing
  Inventory reserved
  Payment captured
end note
@enduml
```

---

### 4. Class Diagram — Backend

**Mục đích**: Mô tả cấu trúc lớp phía backend

**Template**:
```
@startuml
class ClassName {
  - field: Type
  + method(): ReturnType
}
ClassA --> ClassB
@enduml
```

**Ví dụ hoàn chỉnh**:
```
@startuml
skinparam classAttributeIconSize 0

class UserController {
  - userService: UserService
  + register(request: RegisterRequest): ResponseEntity
  + login(request: LoginRequest): ResponseEntity
  + getProfile(id: Long): ResponseEntity
}

interface UserService {
  + register(dto: UserDTO): UserDTO
  + login(email: String, password: String): TokenPair
  + findById(id: Long): UserDTO
}

class UserServiceImpl implements UserService {
  - userRepository: UserRepository
  - jwtUtil: JwtUtil
  + register(dto: UserDTO): UserDTO
  + login(email: String, password: String): TokenPair
  + findById(id: Long): UserDTO
}

class UserRepository {
  + findByEmail(email: String): Optional<User>
  + save(user: User): User
}

class User {
  - id: Long
  - email: String
  - passwordHash: String
  - fullName: String
  - createdAt: LocalDateTime
}

UserController --> UserService
UserServiceImpl ..|> UserService
UserServiceImpl --> UserRepository
UserRepository --> User
@enduml
```

---

### 5. Class Diagram — Frontend

**Mục đích**: Mô tả cấu trúc components phía frontend

**Template**:
```
@startuml
class ComponentName {
  + props: PropsType
  + state: StateType
  + render(): JSX
}
@enduml
```

**Ví dụ hoàn chỉnh**:
```
@startuml
skinparam classAttributeIconSize 0

class LoginPage {
  - useAuth: AuthHook
  + handleSubmit(data: LoginForm): void
  + render(): JSX
}

class useAuth {
  + login(email: String, password: String): Promise<void>
  + logout(): void
  + isAuthenticated: boolean
  + user: User | null
}

class AuthApiService {
  + login(credentials: LoginRequest): Promise<TokenPair>
  + logout(): Promise<void>
  + refreshToken(token: String): Promise<TokenPair>
}

LoginPage --> useAuth
useAuth --> AuthApiService
@enduml
```

---

### 6. Sequence Diagram

**Mục đích**: Mô tả luồng tương tác giữa các thành phần theo thời gian

**Template**:
```
@startuml
actor User
participant Frontend
participant Backend
database DB

User -> Frontend: Action
Frontend -> Backend: API Call
Backend -> DB: Query
DB -->> Backend: Result
Backend -->> Frontend: Response
Frontend -->> User: Display
@enduml
```

**Ví dụ hoàn chỉnh**:
```
@startuml
actor Customer
participant "Web App" as web
participant "Order Service" as order
participant "Inventory Service" as inventory
participant "Payment Service" as payment
database "Database" as db

Customer -> web : Place Order
web -> order : POST /orders
order -> inventory : Check Stock
inventory -> db : SELECT stock
db -->> inventory : Stock OK
inventory -->> order : Available
order -> payment : Process Payment
payment -> payment : Validate Card
payment -->> order : Payment Success
order -> db : INSERT order
db -->> order : Order Created
order -->> web : 201 Created
web -->> Customer : Order Confirmation
@enduml
```

---

### 7. ER Diagram

**Mục đích**: Mô tả cấu trúc database và quan hệ giữa các bảng

**Ví dụ**:
```
@startuml
entity "Customer" as c {
  * id : UUID
  --
  * email : String
  * password : String
  * full_name : String
  * phone : String
}
entity "Product" as p {
  * id : UUID
  --
  * name : String
  * price : Decimal
  * stock : Integer
}
entity "Order" as o {
  * id : UUID
  --
  * customer_id : UUID
  * status : String
  * total : Decimal
}
entity "OrderItem" as oi {
  * id : UUID
  --
  * order_id : UUID
  * product_id : UUID
  * quantity : Integer
}
c ||--o{ o : places
o ||--o{ oi : contains
p ||--o{ oi : ordered
@enduml
```

---

### 8. Component Interaction Diagram

**Mục đích:** Mô tả cách các components/modules giao tiếp với nhau ở runtime — ai gọi ai, qua giao thức gì, theo hướng nào.

**Khi nào tạo:** Được tạo bởi `sdd-agent` trong Phase 1, sau khi danh sách components đã xác định từ SRS.

**Output file:** `diagrams/components/component-interaction.puml`

**Template:**
```
@startuml component-interaction
skinparam componentStyle uml2
skinparam backgroundColor #FEFEFE
skinparam component {
  BackgroundColor #F0F4F8
  BorderColor #333333
  FontSize 13
}
skinparam interface {
  BackgroundColor #FFFFFF
  BorderColor #555555
}
skinparam arrow {
  Color #444444
  FontSize 11
}

title {ProjectName} — Component Interaction Diagram

' ─── Frontend Layer ──────────────────────────────
package "Frontend\n(React/Vue/Angular)" as FE #LightBlue {
  component "AuthPage" as AuthUI
  component "ProductPage" as ProductUI
  component "OrderPage" as OrderUI
  component "AdminPage" as AdminUI
}

' ─── API Gateway ────────────────────────────────
component "API Gateway\n/api/v1" as Gateway #Gold

' ─── Backend Services ───────────────────────────
package "Backend Services" as BE #LightGreen {
  component "AuthModule\n:8080/auth" as AuthSvc
  component "ProductModule\n:8080/products" as ProductSvc
  component "OrderModule\n:8080/orders" as OrderSvc
  component "PaymentModule\n:8080/payments" as PaymentSvc
  component "NotificationModule" as NotifSvc
}

' ─── External Services ──────────────────────────
package "External Services" as EXT #LightYellow {
  component "Payment Gateway\n(Stripe/VNPay)" as PayGW
  component "Email Service\n(SendGrid/SES)" as EmailSvc
  component "SMS Service\n(Twilio)" as SMSSvc
  component "Object Storage\n(S3/MinIO)" as Storage
}

' ─── Data Layer ─────────────────────────────────
package "Data Layer" as DATA #LightGray {
  database "MySQL\n(Primary DB)" as DB
  database "Redis\n(Cache + Session)" as Cache
  queue "Message Queue\n(RabbitMQ/Kafka)" as MQ
}

' ─── Connections: Frontend → Gateway ────────────
AuthUI --> Gateway : HTTPS REST
ProductUI --> Gateway : HTTPS REST
OrderUI --> Gateway : HTTPS REST
AdminUI --> Gateway : HTTPS REST

' ─── Connections: Gateway → Services ───────────
Gateway --> AuthSvc : JWT Auth check
Gateway --> ProductSvc : route /products
Gateway --> OrderSvc : route /orders
Gateway --> PaymentSvc : route /payments

' ─── Connections: Service → Service ────────────
OrderSvc --> PaymentSvc : initiate payment\n(sync REST)
OrderSvc --> ProductSvc : check stock\n(sync REST)
PaymentSvc --> PayGW : charge card\n(HTTPS)
PaymentSvc --> MQ : payment.completed event

' ─── Async: MQ → Services ───────────────────────
MQ --> NotifSvc : payment.completed\n(async consume)
MQ --> OrderSvc : payment.completed\n(async consume)

' ─── Connections: Services → Data ───────────────
AuthSvc --> DB : read/write users
AuthSvc --> Cache : session store
ProductSvc --> DB : read/write products
ProductSvc --> Cache : product cache (5min TTL)
OrderSvc --> DB : read/write orders
PaymentSvc --> DB : read/write payments

' ─── Connections: Services → External ───────────
NotifSvc --> EmailSvc : send email
NotifSvc --> SMSSvc : send SMS
ProductSvc --> Storage : upload product images

legend right
  |= Arrow |= Meaning |
  | → solid | Synchronous call |
  | --> dashed | Async / Event |
endlegend

@enduml
```

**Checklist:**
- [ ] Tất cả components đã được xác định trong SDD Section 4 đều có mặt
- [ ] Arrows có label mô tả protocol + data type
- [ ] External services được nhóm riêng
- [ ] Sync vs async calls được phân biệt (solid vs dashed)
- [ ] Data layer (DB, Cache, MQ) được thể hiện

---

### 9. Deployment Topology (Multi-Environment)

**Mục đích:** Mô tả chi tiết hạ tầng triển khai cho từng môi trường (dev/staging/prod) — servers, containers, networks, load balancers.

**Khi nào tạo:** Được tạo bởi `sdd-agent`, thay thế/bổ sung cho `deployment.puml` cơ bản.

**Output files:**
- `diagrams/deployment-dev.puml`
- `diagrams/deployment-staging.puml`
- `diagrams/deployment-prod.puml`

**Template (Production):**
```
@startuml deployment-prod
skinparam backgroundColor #FEFEFE
skinparam node {
  BackgroundColor #F0F4F8
  BorderColor #333333
}

title {ProjectName} — Production Deployment Topology

' ─── Internet Layer ─────────────────────────────
actor "End Users" as Users
node "CloudFlare CDN\n(Static Assets + DDoS)" as CDN #LightYellow

' ─── DMZ / Load Balancer ────────────────────────
node "AWS Region: ap-southeast-1" as Region {

  node "Availability Zone A" as AZA {
    node "Public Subnet A" {
      component "Application Load\nBalancer (ALB)" as ALB #Gold
    }
    node "Private Subnet A" {
      node "ECS Cluster" {
        component "API Service\n(2x t3.medium)" as API_A
        component "Worker Service\n(1x t3.small)" as Worker_A
      }
    }
    node "DB Subnet A" {
      database "RDS MySQL\n(Primary)\nt3.large" as RDS_Primary #LightGreen
    }
  }

  node "Availability Zone B" as AZB {
    node "Private Subnet B" {
      node "ECS Cluster" {
        component "API Service\n(2x t3.medium)" as API_B
        component "Worker Service\n(1x t3.small)" as Worker_B
      }
    }
    node "DB Subnet B" {
      database "RDS MySQL\n(Read Replica)\nt3.large" as RDS_Replica #LightBlue
    }
  }

  node "Shared Services" {
    database "ElastiCache Redis\n(r6g.large, Multi-AZ)" as Redis #Orange
    queue "SQS / RabbitMQ" as MQ #LightPink
    storage "S3 Bucket\n(Media Storage)" as S3 #Wheat
    component "ECR\n(Container Registry)" as ECR
  }

  node "Observability" {
    component "CloudWatch\n(Logs + Metrics)" as CW
    component "X-Ray\n(Distributed Tracing)" as XRay
    component "SNS Alerting" as SNS
  }
}

node "External Services" {
  component "Payment Gateway\n(Stripe)" as Stripe
  component "Email\n(SES)" as SES
}

node "CI/CD Pipeline" {
  component "GitHub Actions" as CICD
}

Users --> CDN : HTTPS
CDN --> ALB : HTTPS (origin)
ALB --> API_A : HTTP round-robin
ALB --> API_B : HTTP round-robin
API_A --> RDS_Primary : MySQL 3306 (write)
API_A --> RDS_Replica : MySQL 3306 (read)
API_B --> RDS_Primary : MySQL 3306 (write)
API_B --> RDS_Replica : MySQL 3306 (read)
API_A --> Redis : Redis 6379
API_B --> Redis : Redis 6379
API_A --> MQ : publish events
Worker_A --> MQ : consume events
Worker_A --> Stripe : HTTPS
Worker_A --> SES : HTTPS
API_A --> S3 : SDK
API_A --> CW : logs/metrics
CW --> SNS : alerts
CICD --> ECR : push image
CICD --> API_A : deploy (ECS rolling update)
RDS_Primary --> RDS_Replica : async replication

note top of Region
  Auto Scaling:
  API Service: min=2, max=10 (CPU>70%)
  Worker: min=1, max=5 (queue depth)
end note

@enduml
```

**Template (Development — simplified):**
```
@startuml deployment-dev
skinparam backgroundColor #FEFEFE

title {ProjectName} — Development Environment

node "Developer Laptop" {
  node "Docker Compose" {
    component "API Service\n:8080" as API
    database "MySQL 8.0\n:3306" as DB
    database "Redis 7\n:6379" as Redis
    queue "RabbitMQ\n:5672/:15672" as MQ
    component "MailHog\n:8025 (email mock)" as Mail
    component "MinIO\n:9000 (S3 mock)" as Minio
  }
}

API --> DB
API --> Redis
API --> MQ
API --> Mail
API --> Minio

note right of API
  .env.local:
  DATABASE_URL=mysql://root:root@localhost:3306/dev_db
  REDIS_URL=redis://localhost:6379
  MAIL_HOST=localhost:1025
end note

@enduml
```

---

### 10. Data Flow Diagram (DFD)

**Mục đích:** Mô tả luồng dữ liệu qua hệ thống — data đến từ đâu, được xử lý ở đâu, lưu ở đâu, đi đến đâu. Không phải sequence (không có thời gian), chỉ có data flows.

**Levels:**
- **DFD Level 0** (Context Diagram) — toàn bộ system như 1 hộp đen
- **DFD Level 1** — major processes bên trong system
- **DFD Level 2** — detail của từng process (optional)

**Output files:**
- `diagrams/dfd-level0.puml` (Context — thường đã có từ context-diagram.puml)
- `diagrams/dfd-level1.puml`

**Template DFD Level 1:**
```
@startuml dfd-level1
skinparam backgroundColor #FEFEFE
skinparam rectangle {
  BackgroundColor #E3F2FD
  BorderColor #1565C0
  RoundCorner 10
}
skinparam database {
  BackgroundColor #F3E5F5
  BorderColor #6A1B9A
}
skinparam actor {
  BackgroundColor #E8F5E9
  BorderColor #2E7D32
}

title {ProjectName} — Data Flow Diagram Level 1

' ─── External Entities (Actors) ──────────────
actor "Customer\n[External]" as Customer
actor "Admin\n[External]" as Admin
actor "Payment Gateway\n[External]" as PayGW

' ─── Processes ───────────────────────────────
rectangle "P1\nUser\nAuthentication" as P1
rectangle "P2\nProduct\nManagement" as P2
rectangle "P3\nOrder\nProcessing" as P3
rectangle "P4\nPayment\nProcessing" as P4
rectangle "P5\nNotification\nService" as P5

' ─── Data Stores ─────────────────────────────
database "D1: Users\n& Sessions" as D1
database "D2: Products\n& Inventory" as D2
database "D3: Orders\n& Items" as D3
database "D4: Payments\n& Transactions" as D4
database "D5: Audit\nLogs" as D5

' ─── Data Flows ──────────────────────────────
Customer --> P1 : credentials\n(email, password)
P1 --> Customer : auth token\n(JWT)
P1 --> D1 : store/retrieve\nuser session
P1 --> D5 : login audit log

Customer --> P2 : search query\n& filters
P2 --> Customer : product list\n& details
P2 <--> D2 : read product data

Customer --> P3 : cart items\n& shipping info
P3 --> Customer : order confirmation\n& tracking
P3 --> D3 : create order record
P3 --> D2 : update inventory\n(reserve stock)
P3 --> P4 : payment request\n(amount, method)

P4 --> PayGW : payment details\n(encrypted)
PayGW --> P4 : transaction result\n(success/fail)
P4 --> D4 : store transaction
P4 --> D3 : update order status
P4 --> P5 : payment event\n(success/fail)

P5 --> Customer : email/SMS\nnotification
P5 --> D5 : notification audit log

Admin --> P2 : product data\n(CRUD)
P2 --> D2 : update product/stock
Admin --> P3 : order management
P3 --> D3 : update/cancel order

note right of P4
  PCI-DSS Zone:
  Card data never stored
  Tokenized via gateway
end note

legend right
  |= Shape |= Meaning |
  | Actor (stick) | External entity |
  | Rectangle (rounded) | Process |
  | Cylinder | Data store |
  | Arrow | Data flow |
endlegend

@enduml
```

---

## Diagram Type Summary

| # | Diagram Type | Output File | Created By |
|---|-------------|-------------|-----------|
| 1 | Context Diagram | context-diagram.puml | srs-agent |
| 2 | System Overview | system-overview.puml | srs-agent/sdd-agent |
| 3 | Layered Architecture | layered-architecture.puml | sdd-agent |
| 4 | Deployment (basic) | deployment.puml | srs-agent |
| 5 | Integration | integration.puml | sdd-agent |
| 6 | Screen Flow | screen-flow.puml | srs-agent |
| 7 | ER Diagram | entity-relationship.puml | db-agent |
| 8 | Component Interaction | components/component-interaction.puml | sdd-agent |
| 9 | Deployment Topology | deployment-{env}.puml | sdd-agent |
| 10 | Data Flow (DFD) | dfd-level1.puml | sdd-agent |

## Rendering PlantUML

Để xem PlantUML diagrams:
1. **VS Code**: Cài extension "PlantUML"
2. **Online**: https://www.plantuml.com/plantuml/uml/
3. **GitHub**: Sử dụng GitHub Actions với plantuml-action
4. **Markdown**: Dùng plugin hỗ trợ PlantUML

## Best Practices

1. **Consistent Naming**: Sử dụng naming convention nhất quán
2. **Appropriate Level**: Chi tiết vừa đủ, không over-engineer
3. **Clear Boundaries**: Xác định rõ phạm vi system boundary
4. **Proper Stereotypes**: Sử dụng <<client>>, <<server>>, <<external>>
5. **Include Notes**: Thêm notes để giải thích quan trọng
6. **Align with SRS**: Diagrams phải nhất quán với requirements