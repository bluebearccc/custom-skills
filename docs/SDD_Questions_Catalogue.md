# SDD Questions Catalogue
# Comprehensive Questions for Collecting Information Before Writing Software Design Document

> **Purpose**: Complete set of questions to collect information from customers/stakeholders before starting Software Design Document (SDD).
>
> **Symbols**:
> - `[B]` = Must-have (essential questions that must be answered)
> - `[T]` = Optional (additional questions depending on context)

---

## 0. REQUIREMENTS MAPPING

### 0.1 Use Cases → Architecture

| UC ID | UC Name | Key Design Requirements |
|-------|---------|------------------------|
| UC-01 | (Reference from SRS) | Authentication, Session management |
| UC-02 | (Reference from SRS) | Authorization, RBAC implementation |
| UC-03 | (Reference from SRS) | Data access patterns, caching strategy |
| UC-04 | (Reference from SRS) | API design, integration points |
| UC-05 | (Reference from SRS) | Real-time requirements, async processing |

### 0.2 Data Entities → Database

| Entity | Required DB Design |
|--------|-------------------|
| User | Tables, indexes, relationships for user management |
| (Other entities from SRS) | Schema design based on SRS data requirements |

### 0.3 NFRs → Architecture Decisions

| NFR | Architecture Impact |
|-----|-------------------|
| Performance < 2s | Caching strategy, database indexing, query optimization |
| Scalability | Horizontal scaling, load balancing, database replication |
| Availability 99.9% | Redundancy, failover strategy, monitoring |
| Security | Encryption, authentication, authorization, audit logging |

---

## 1. SYSTEM ARCHITECTURE

### 1.1 Architectural Pattern

| # | Question | Type | Suggested Answers | Examples |
|---|----------|------|-------------------|----------|
| 1.1.1 | What architecture do you want for the system? | [B] | Monolithic / Layered / MVC / Microservices / Event-Driven / Serverless / Hybrid | E-commerce: **Microservices** (Order, Payment, Inventory separate); Internal tool: **Layered** |
| 1.1.2 | Does the system need separate Frontend and Backend? | [B] | Yes (SPA + API) / No (Monolithic full-stack) | **Yes** → React + Node.js; **No** → Next.js full-stack |
| 1.1.3 | Are there Domain-Driven Design (DDD) requirements? | [T] | Yes / No / Need more consultation | Complex domain (Fintech): **Yes**; Simple CRUD: **No** |
| 1.1.4 | Do you need CQRS (Command Query Responsibility Segregation)? | [T] | Yes / No / Need consultation | Read-heavy (reporting): **Yes**; Balanced: **No** |
| 1.1.5 | Do you need Event Sourcing pattern? | [T] | Yes / No / Only for specific modules | Audit-heavy (banking): **Specific modules**; Standard app: **No** |

### 1.2 Deployment Architecture

| # | Question | Type | Suggested Answers | Examples |
|---|----------|------|-------------------|----------|
| 1.2.1 | Where will the system be deployed? | [B] | Cloud (AWS/Azure/GCP) / On-premise / Hybrid Cloud / Multi-cloud | Startup: **AWS/GCP**; Enterprise: **Hybrid**; Government: **On-premise/Azure** |
| 1.2.2 | Do you already have infrastructure or need full consultation? | [B] | Existing infrastructure (describe) / Need full consultation | **Existing**: AWS EKS + RDS; **Need consultation**: Greenfield project |
| 1.2.3 | Are there Geographic distribution (multi-region) requirements? | [T] | Yes / No / Only for specific regions | Global app: **AP-Singapore, EU-Ireland**; Local app: **No** |
| 1.2.4 | Do you need CDN for static content distribution? | [T] | Yes (AWS CloudFront/Azure CDN/Cloudflare) / No | Media-heavy: **Cloudflare**; Internal tool: **No** |
| 1.2.5 | Where is the cache layer placed in the architecture? | [T] | CDN / Application Cache (Redis/Memcached) / Database Cache / Edge Computing | **CDN** (static) → **Redis** (API) → **DB cache** |

### 1.3 High-Level Components

| # | Question | Type | Suggested Answers | Examples |
|---|----------|------|-------------------|----------|
| 1.3.1 | List the main high-level components/services of the system | [B] | List of services with functional descriptions | `UserService`, `ProductService`, `OrderService`, `PaymentService`, `NotificationService` |
| 1.3.2 | Which domain does each component belong to? | [B] | Authentication, Product, Order, Payment, Notification, etc. | **Auth Domain**: User, Role, Permission; **Order Domain**: Cart, Checkout, Invoice |
| 1.3.3 | Who will be responsible for developing each component? | [T] | Team A, Team B, Vendor X, etc. | **Team Alpha**: Auth, Payment; **Team Beta**: Catalog, Inventory |
| 1.3.4 | Do components depend on each other? | [B] | Yes (list dependencies) / No / Need analysis | **Yes**: Order → Payment → Inventory; **No**: Auth and Notification independent |

### 1.4 Communication Patterns

| # | Question | Type | Suggested Answers | Examples |
|---|----------|------|-------------------|----------|
| 1.4.1 | How do services communicate with each other? | [B] | REST API / gRPC / GraphQL / Message Queue / Shared Database / Event Bus | **REST**: CRUD operations; **gRPC**: Internal high-perf; **Kafka**: Async events |
| 1.4.2 | Do you need synchronous or asynchronous communication? | [B] | Synchronous (request-response) / Asynchronous (message-driven) / Both | **Both**: Checkout sync → Payment, Inventory update async |
| 1.4.3 | Do you need Service Discovery mechanism? | [T] | Yes (Consul, Eureka, Kubernetes DNS) / No / Need consultation | **K8s DNS**: Kubernetes; **Consul**: VM-based microservices |
| 1.4.4 | Is API Gateway used? | [T] | Yes (Kong, AWS API Gateway, NGINX) / No / Need consultation | **Kong**: Microservices; **AWS API Gateway**: Serverless |
| 1.4.5 | Do you need API Gateway for rate limiting, authentication, logging? | [T] | Yes / No / Only specific features | **All**: Public API; **Auth + Logging**: Internal; **No**: Private APIs |

---

## 2. FRONTEND ARCHITECTURE

### 2.1 Technology Stack

| # | Question | Type | Suggested Answers | Tech Stack Options | Examples |
|---|----------|------|-------------------|-------------------|----------|
| 2.1.1 | Which frontend framework is used? | [B] | React / Vue.js / Angular / Svelte / Next.js / Nuxt.js / Mobile (React Native, Flutter) | **Web**: React, Vue, Angular, Svelte; **Meta-frameworks**: Next.js, Nuxt.js; **Mobile**: React Native, Flutter | E-commerce: **React + Next.js**; Enterprise: **Vue + Nuxt.js**; Simple: **Svelte** |
| 2.1.2 | Do you need multi-platform support? | [B] | Web only / Mobile (iOS, Android) / Desktop (Electron) / Multi-platform | **Mobile**: React Native, Flutter; **Desktop**: Electron, Tauri | **Multi-platform**: React Native (iOS + Android); **Web + Mobile**: React Web + React Native |
| 2.1.3 | Which build tool is used? | [T] | Vite / Webpack / Parcel / Turbopack / esbuild | - | **Vite**: New projects; **Webpack**: Legacy; **Turbopack**: Next.js |
| 2.1.4 | Are there SSR (Server-Side Rendering) requirements? | [T] | Yes (Next.js, Nuxt.js) / No (SPA) / SSG (Static Site Generation) | **SSR**: Next.js, Nuxt.js; **SSG**: Astro, Next.js static; **SPA**: React/Vue SPA | SEO-heavy: **SSR** (Next.js); Dashboard: **SPA**; Blog: **SSG** |
| 2.1.5 | CSS framework/styling approach? | [T] | Tailwind CSS / Styled-components / CSS Modules / SCSS / Emotion / Vanilla CSS | **Utility-first**: Tailwind CSS; **Component-scoped**: CSS Modules, Styled-components; **Traditional**: SCSS | **Rapid dev**: Tailwind; **Design system**: Styled-components; **Legacy**: SCSS |

### 2.2 Component Architecture

| # | Question | Type | Suggested Answers | Examples |
|---|----------|------|-------------------|----------|
| 2.2.1 | What is the component hierarchy? | [T] | Atomic Design / Feature-based / Domain-driven / Layered components | **Atomic Design**: Atoms → Molecules → Organisms → Templates → Pages |
| 2.2.2 | Is there a common design system/component library? | [T] | Yes (name) / No / Need to create new | **Yes**: Material UI, Chakra UI; **Need to create**: Company X Design System |
| 2.2.3 | Do you need shared components between multiple projects/apps? | [T] | Yes / No / Planned for future | **Yes**: Design System team; **Planned**: Monorepo with shared packages |
| 2.2.4 | Any third-party UI kit/library? | [T] | Material UI / Ant Design / Chakra UI / Radix UI / Shadcn/ui / Tailwind UI | **Enterprise**: Ant Design, Material UI; **Modern**: Radix UI, Shadcn/ui |

### 2.3 State Management

| # | Question | Type | Suggested Answers | Tech Stack Options | Examples |
|---|----------|------|-------------------|-------------------|----------|
| 2.3.1 | Which state management solution is used? | [B] | Redux Toolkit / Zustand / MobX / Jotai / React Context / Vuex / Pinia / Recoil | **React**: Redux Toolkit, Zustand, Jotai, Recoil; **Vue**: Vuex, Pinia | **Complex app**: Redux Toolkit; **Medium**: Zustand; **Simple**: React Context |
| 2.3.2 | Are server state and UI state separated? | [T] | Yes (React Query/SWR for server state) / No / Need consultation | **Server state**: React Query, SWR, Apollo Client; **UI state**: Zustand, Redux | **Yes**: React Query (server) + Zustand (UI); **No**: Redux for everything |
| 2.3.3 | Do you need persistent state (localStorage, IndexedDB)? | [T] | Yes / No / Only for specific use cases | **Simple**: localStorage; **Complex**: IndexedDB, Dexie.js | Cart, preferences: **localStorage**; Offline data: **IndexedDB** |
| 2.3.4 | Form state management? | [T] | React Hook Form / Formik / Vanilla / Zod validation | **React Hook Form**: Performance; **Formik**: Legacy; **Validation**: Zod, Yup | **Modern**: React Hook Form + Zod; **Legacy**: Formik + Yup |

### 2.4 Routing & Navigation

| # | Question | Type | Suggested Answers | Tech Stack Options | Examples |
|---|----------|------|-------------------|-------------------|----------|
| 2.4.1 | Which routing library is used? | [B] | React Router / Vue Router / Angular Router / TanStack Router | **React**: React Router, TanStack Router; **Vue**: Vue Router; **Angular**: Angular Router | **React 6+**: React Router v6; **Large app**: TanStack Router (type-safe) |
| 2.4.2 | Do you need nested routes/parallel routes? | [T] | Yes / No / Need consultation | **Nested**: dashboard/settings, admin/users; **Parallel**: modal routes | **Yes**: Complex dashboard with tabs and sidebars |
| 2.4.3 | Do you need route protection/authentication guard? | [B] | Yes / No / Only for some routes | **React Router**: ProtectedRoute component; **Vue**: Navigation guards | Admin routes → **ProtectedRoute**; Public → No guard |
| 2.4.4 | Do you need lazy loading/code splitting? | [T] | Yes (default) / No / Only for specific routes | **Vite/Webpack**: dynamic import(); **Next.js**: Automatic | Admin panel: **lazy load**; Auth pages: **lazy load** |

### 2.5 Responsive & PWA

| # | Question | Type | Suggested Answers | Examples |
|---|----------|------|-------------------|----------|
| 2.5.1 | What responsive design for devices is required? | [B] | Mobile only / Desktop only / Both (specific breakpoints) | **Breakpoints**: Mobile (<768px), Tablet (768-1024px), Desktop (>1024px) |
| 2.5.2 | Do you need PWA (Progressive Web App) support? | [T] | Yes / No / Possibly in future | **PWA**: Service Worker, Web App Manifest, Offline support |
| 2.5.3 | Do you need offline support/Service Workers? | [T] | Yes / No / Only for some features | Read-only content, cached data: **Offline support** |
| 2.5.4 | Do you need WebSocket for real-time updates? | [T] | Yes / No / Only for some modules | Chat, notifications, live updates: **WebSocket** |
| 2.5.5 | SEO requirements? | [T] | Yes (SSR, meta tags, sitemap) / No / Partial (blog, landing pages) | **Yes**: SSR + meta tags + sitemap + structured data |

---

## 3. BACKEND ARCHITECTURE

### 3.1 Technology Stack

| # | Question | Type | Suggested Answers | Tech Stack Options | Examples |
|---|----------|------|-------------------|-------------------|----------|
| 3.1.1 | Which backend language/framework is used? | [B] | Node.js (Express/NestJS) / Python (Django/FastAPI) / Java (Spring Boot) / Go / Rust / .NET / PHP (Laravel) | **Node.js**: Express, NestJS, Fastify; **Python**: Django, FastAPI; **Java**: Spring Boot; **Go**: Gin, Echo | E-commerce: **Node.js + NestJS**; ML-heavy: **Python + FastAPI**; High-perf: **Go** |
| 3.1.2 | Specific language/technology version? | [T] | Node.js 20.x / Python 3.12 / Java 21 / etc. | **Node.js**: 18 LTS, 20 LTS; **Python**: 3.11, 3.12; **Java**: 17 LTS, 21 LTS | Node 20 LTS, Python 3.12, Java 21 |
| 3.1.3 | Any special runtime environment requirements? | [T] | Docker / Serverless (Lambda, Cloud Functions) / Traditional VMs | **Serverless**: AWS Lambda, GCP Functions, Azure Functions; **Containers**: Docker, Podman | Event-driven: **Serverless**; Steady load: **Docker on K8s** |
| 3.1.4 | Build tool/package manager? | [T] | npm/yarn/pnpm / pip/poetry / Maven/Gradle / Go modules | **Node.js**: npm, yarn, pnpm; **Python**: pip, poetry, uv; **Java**: Maven, Gradle | **pnpm**: monorepo; **Poetry**: Python deps; **Gradle**: Java multi-module |

### 3.2 API Design

| # | Question | Type | Suggested Answers | Examples |
|---|----------|------|-------------------|----------|
| 3.2.1 | Which API design pattern? | [B] | REST / GraphQL / gRPC / tRPC / Mixed (REST for CRUD, GraphQL for complex queries) | **REST**: CRUD resources; **GraphQL**: Flexible queries; **gRPC**: Internal high-perf; **tRPC**: Type-safe end-to-end |
| 3.2.2 | API versioning strategy? | [B] | URL path (/v1/, /v2/) / Header / Query parameter / None | `/api/v1/users`; `Accept: application/vnd.api+json, version=2` |
| 3.2.3 | Standard response format? | [B] | JSON / XML / Protobuf / Mixed | **JSON**: Standard REST; **Protobuf**: gRPC; **XML**: Legacy SOAP |
| 3.2.4 | Is OpenAPI/Swagger documentation required? | [T] | Yes / No / Internal API only | **Yes**: OpenAPI 3.1 + Swagger UI + Redoc |
| 3.2.5 | Pagination strategy? | [T] | Offset-based / Cursor-based / Page-based / None | **Offset**: `?page=1&limit=20`; **Cursor**: `?cursor=abc123&limit=20` |
| 3.2.6 | Standard error response format? | [T] | RFC 7807 Problem Details / Custom format / Legacy format | **RFC 7807**: `{"type": "...", "title": "...", "status": 400}` |
| 3.2.7 | Do you need API rate limiting at backend? | [T] | Yes (specific requests/minute) / No (only at Gateway) | **100 req/min** per user; **1000 req/min** per API key |

### 3.3 Authentication & Authorization

| # | Question | Type | Suggested Answers | Tech Stack Options | Examples |
|---|----------|------|-------------------|-------------------|----------|
| 3.3.1 | Which authentication mechanism? | [B] | JWT (Access + Refresh tokens) / OAuth2 (Authorization Code Flow) / Session-based / API Keys / SAML / LDAP | **JWT**: Stateless; **OAuth2**: Social login; **Session**: Server-side | **JWT**: SPA + API; **OAuth2**: Google/Facebook login; **SAML**: Enterprise SSO |
| 3.3.2 | Token expiration policy? | [B] | Access token: 15 min / Refresh token: 7 days (example) | **Access**: 5-15 min; **Refresh**: 7-30 days | Access: **15 min**, Refresh: **7 days** |
| 3.3.3 | Do you need multi-factor authentication (MFA)? | [T] | Yes / No / Depending on user role | **TOTP**: Google Authenticator; **SMS**: Less secure; **Hardware**: YubiKey | Admin users: **MFA required**; Regular users: **Optional** |
| 3.3.4 | Social login integration? | [T] | Yes (Google, Facebook, Apple, etc.) / No / Future plan | **OAuth2**: Google, Facebook, Apple, GitHub, LinkedIn | E-commerce: **Google, Facebook, Apple** |
| 3.3.5 | Password policy requirements? | [T] | Min 8 chars, uppercase, number, special / NIST guidelines / Custom rules | **NIST**: No complexity rules, check against breached passwords | **NIST**: Min 8 chars, no forced symbol rules |
| 3.3.6 | Account lockout policy? | [T] | 5 failed attempts, 30 min lockout / Custom policy | **5 attempts**, 30 min lockout; **10 attempts**, permanent until admin |
| 3.3.7 | Session management strategy? | [B] | Stateful (server-side session) / Stateless (JWT) / Hybrid | **Stateless**: JWT in memory; **Stateful**: Redis session store; **Hybrid**: Refresh token rotation | **Stateless JWT**: SPA; **Hybrid**: Mobile app + refresh token rotation |

### 3.4 Caching & Queue

| # | Question | Type | Suggested Answers | Tech Stack Options | Examples |
|---|----------|------|-------------------|-------------------|----------|
| 3.4.1 | Caching strategy? | [B] | Redis / Memcached / In-memory / CDN / No cache | **Redis**: Distributed cache, 10GB+; **Memcached**: Simple key-value; **In-memory**: Single instance | **Redis**: Session + API cache; **Memcached**: Simple page fragments |
| 3.4.2 | Cache invalidation strategy? | [T] | TTL-based / Event-driven / Manual / Write-through | **TTL**: 5-60 min expiry; **Event-driven**: Invalidate on write; **Write-through**: Update cache on DB write | Product catalog: **TTL 10 min**; User data: **Event-driven** |
| 3.4.3 | Do you need message queue for async processing? | [B] | Yes (RabbitMQ, Kafka, AWS SQS, Redis Pub/Sub) / No / Need consultation | **Kafka**: High-throughput event streaming; **RabbitMQ**: Flexible routing; **SQS**: AWS fully-managed | Email sending, order processing, real-time updates |
| 3.4.4 | Main queue use cases? | [B] | Email notifications / Background jobs / Event processing / Cross-service communication | **Email**: Welcome, password reset; **Background**: Report generation, image processing |
| 3.4.5 | Do you need scheduled jobs/cron jobs? | [T] | Yes (list jobs) / No / Need consultation | **Daily**: Report generation, data cleanup; **Hourly**: Sync jobs; **Weekly**: Archiving |
| 3.4.6 | Job scheduler framework? | [T] | Bull (Node.js) / Celery (Python) / Spring Scheduler / Custom | **Node.js**: Bull, BullMQ; **Python**: Celery; **Java**: Spring Scheduler + Quartz | **BullMQ**: Node.js with Redis; **Celery**: Python with RabbitMQ |

---

## 4. DATABASE DESIGN

### 4.1 Database Type & Engine

| # | Question | Type | Suggested Answers | Tech Stack Options | Examples |
|---|----------|------|-------------------|-------------------|----------|
| 4.1.1 | Which database type? | [B] | SQL (relational) / NoSQL (document, key-value, column-family, graph) / Mixed (Polyglot) | See **Comparison Table** below | E-commerce: **SQL**; Content management: **NoSQL (document)**; Both: **Polyglot** |
| 4.1.2 | Specific database engine? | [B] | PostgreSQL / MySQL / MariaDB / Oracle / SQL Server (SQL) / MongoDB / Cassandra / DynamoDB / Redis / Elasticsearch (NoSQL) | **SQL**: PostgreSQL, MySQL, MariaDB; **Document**: MongoDB; **Wide-column**: Cassandra; **Key-value**: Redis, DynamoDB | **PostgreSQL**: Most use cases; **MongoDB**: Flexible schema; **Cassandra**: Time-series, high-write |
| 4.1.3 | Database engine version? | [T] | PostgreSQL 16.x / MongoDB 7.x / etc. | **PostgreSQL**: 15, 16; **MySQL**: 8.0; **MongoDB**: 6, 7 | PostgreSQL 16.x, MySQL 8.0, MongoDB 7.0 |
| 4.1.4 | Managed database service or self-hosted? | [T] | Managed (AWS RDS, Aurora, Azure CosmosDB) / Self-hosted / Hybrid | **Managed**: RDS, Aurora, CosmosDB, PlanetScale; **Self-hosted**: Docker, VM | **Managed**: Startup; **Self-hosted**: Enterprise with DBA team |
| 4.1.5 | Do you need database replication? | [T] | Yes (Primary-Replica) / No / Need consultation | **Async**: Primary → Replica; **Sync**: Multi-primary | Read replicas for reporting, async for DR |

#### Database Type Comparison

| Database Type | Best For | Not Suitable For | Example Engines |
|--------------|---------|-------------------|-----------------|
| **SQL (Relational)** | Structured data, ACID transactions, complex joins, relational data | Massive unstructured data, schema-less data | PostgreSQL, MySQL, MariaDB |
| **NoSQL Document** | Flexible schema, nested data, content management, rapid development | Complex multi-document transactions, structured reporting | MongoDB, CouchDB |
| **NoSQL Key-Value** | Caching, sessions, simple lookups, high-speed access | Complex queries, reporting, relationships | Redis, DynamoDB, Memcached |
| **NoSQL Column-Family** | Time-series, high write throughput, analytics, wide tables | Complex joins, point queries | Cassandra, HBase |
| **NoSQL Graph** | Social networks, recommendation engines, fraud detection | Simple CRUD, bulk operations | Neo4j, Amazon Neptune |

### 4.2 Schema Design

| # | Question | Type | Suggested Answers | Examples |
|---|----------|------|-------------------|----------|
| 4.2.1 | Do you already have database schema/design? | [B] | Yes (provide ERD) / No / Need full consultation | ERD diagram, SQL DDL scripts, or **"Need consultation"** |
| 4.2.2 | Main entities/collections? | [B] | Users, Products, Orders, etc. (detailed list) | `users`, `products`, `orders`, `order_items`, `payments`, `notifications` |
| 4.2.3 | Relationships between entities? | [B] | One-to-One / One-to-Many / Many-to-Many / Denormalized | User → Order (1:M); Product ↔ Category (M:M via product_categories) |
| 4.2.4 | Do you need soft delete? | [T] | Yes / No / Depending on entity | User data: **Yes** (audit); Temporary data: **No** |
| 4.2.5 | Audit trail/ history tracking? | [T] | Yes / No / Only for some tables | `orders`, `payments`, `user_changes` tables with history |
| 4.2.6 | Is multi-tenancy required? | [T] | Yes (schema-per-tenant / shared schema with tenant_id) / No | **Shared schema + tenant_id**: Lower cost; **Schema-per-tenant**: Complete isolation |

### 4.3 Indexing & Performance

| # | Question | Type | Suggested Answers | Examples |
|---|----------|------|-------------------|----------|
| 4.3.1 | Indexing strategy? | [T] | Primary indexes defined / Need consultation / Partial indexes / Composite indexes | `CREATE INDEX idx_user_email ON users(email)`; Composite: `(tenant_id, status)` |
| 4.3.2 | Is full-text search needed? | [T] | Yes (Elasticsearch, PostgreSQL full-text) / No / Need consultation | Product search: **Elasticsearch**; User search: **PostgreSQL full-text** |
| 4.3.3 | Do you need read replicas for reporting/analytics? | [T] | Yes / No / Need consultation | **Yes**: 1 primary + 2 read replicas |
| 4.3.4 | Connection pooling configuration? | [T] | Default settings / Custom (min: X, max: Y) / PgBouncer / ProxySQL | **PgBouncer**: `pool_mode = transaction`, `max_client_conn = 100` |

### 4.4 Data Partitioning & Scaling

| # | Question | Type | Suggested Answers | Examples |
|---|----------|------|-------------------|----------|
| 4.4.1 | Do you need sharding/partitioning? | [T] | Yes (by what criteria) / No / Need consultation | **Hash**: `user_id % 4`; **Range**: by date; **List**: by region |
| 4.4.2 | Data retention policy? | [T] | Keep forever / X days / Archive after Y months | Logs: **90 days**; Transactions: **7 years**; Analytics: **3 years** |
| 4.4.3 | Backup and recovery strategy? | [B] | Automated daily backups / Point-in-time recovery / Cross-region backup | Daily full + continuous WAL → PITR within 1 minute |
| 4.4.4 | ORM/Query builder used? | [T] | Prisma / TypeORM / Sequelize / SQLAlchemy / Eloquent / Drizzle / Raw SQL | **Node.js**: Prisma, TypeORM; **Python**: SQLAlchemy; **Raw**: High-perf scenarios |

---

## 5. SECURITY ARCHITECTURE

### 5.1 Authentication & Identity

| # | Question | Type | Suggested Answers | Tech Stack Options | Examples |
|---|----------|------|-------------------|-------------------|----------|
| 5.1.1 | Which identity provider is used? | [B] | Internal (custom) / Auth0 / Okta / AWS Cognito / Azure AD / Keycloak / Firebase Auth | **Managed**: Auth0, Okta, Cognito; **Self-hosted**: Keycloak; **Internal**: Custom + JWT | **Cognito**: AWS integration; **Keycloak**: Enterprise; **Firebase**: Mobile-first |
| 5.1.2 | Do you need SSO (Single Sign-On)? | [T] | Yes (SAML, OAuth2, OIDC) / No / Need consultation | **SAML 2.0**: Enterprise; **OIDC**: Modern apps | Enterprise: **SAML with Okta**; Consumer: **OAuth2 social login** |
| 5.1.3 | What is the user registration flow? | [T] | Email verification / Admin approval / Social only / Invitation-based | **Email verification**: Standard; **Admin approval**: B2B; **Invitation**: Private beta |
| 5.1.4 | Password reset flow? | [T] | Email link / Security questions / Admin-assisted | **Email link**: Standard with JWT token; **Admin-assisted**: Enterprise |

### 5.2 Authorization Model

| # | Question | Type | Suggested Answers | Tech Stack Options | Examples |
|---|----------|------|-------------------|-------------------|----------|
| 5.2.1 | Which authorization model? | [B] | RBAC (Role-Based) / ABAC (Attribute-Based) / PBAC (Permission-Based) / DAC / Custom | **RBAC**: Simple roles; **ABAC**: Complex rules; **PBAC**: Permissions as policies | **RBAC**: Admin, Editor, Viewer; **ABAC**: Resource + environment conditions |
| 5.2.2 | Main roles and permissions? | [B] | Admin, User, Moderator, Guest... (detailed permissions list) | **Admin**: All; **Manager**: CRUD + reports; **User**: Read + own data; **Guest**: Read only |
| 5.2.3 | Do you need resource-level permissions? | [T] | Yes (users can only access their own data) / No / Need consultation | `tenant_id = current_user.tenant_id` filter on all queries |
| 5.2.4 | Do you need permission delegation (impersonation)? | [T] | Yes / No / Need consultation | Support admin impersonating user for debugging |

### 5.3 Data Protection

| # | Question | Type | Suggested Answers | Examples |
|---|----------|------|-------------------|----------|
| 5.3.1 | Encryption at rest? | [B] | Yes (AES-256, database-level encryption) / No / Depending on data type | **Database**: TDE (Transparent Data Encryption); **File**: AES-256 |
| 5.3.2 | Encryption in transit? | [B] | Yes (TLS 1.3 required) / No | **TLS 1.3**: All connections; **TLS 1.2**: Legacy support |
| 5.3.3 | Certificate management? | [T] | Let's Encrypt / Commercial CA / Private CA / Auto-renewal required | **Let's Encrypt**: Auto-renewal with cert-manager; **Commercial**: OV/EV certs |
| 5.3.4 | Sensitive data handling (PII, payment)? | [B] | Encryption + Tokenization / Masking / No storage / Compliance requirements | **PCI-DSS**: Tokenize card data; **PII**: Encrypt + access control |
| 5.3.5 | Secret management solution? | [T] | AWS Secrets Manager / HashiCorp Vault / Azure Key Vault / Kubernetes Secrets / .env files | **Vault**: Dynamic secrets, audit; **K8s Secrets**: Basic; **.env**: Dev only |

### 5.4 API & Application Security

| # | Question | Type | Suggested Answers | Examples |
|---|----------|------|-------------------|----------|
| 5.4.1 | What is the CORS policy? | [B] | Allow specific origins / Wildcard / No CORS (same-origin) | `Access-Control-Allow-Origin: https://app.example.com` |
| 5.4.2 | Input validation strategy? | [B] | Schema validation / Type checking / Allow-list approach | **Zod**: Type-safe validation; **JSON Schema**: Standard; **Allow-list**: Whitelist known values |
| 5.4.3 | CSRF protection? | [B] | CSRF tokens / SameSite cookies / Double Submit Cookie / Not needed | **SameSite=Strict**: Modern browsers; **CSRF token**: Legacy support |
| 5.4.4 | XSS protection? | [B] | Output encoding / Content Security Policy / Sanitization library | **CSP**: `script-src 'self'`; **Output encoding**: React auto-escapes |
| 5.4.5 | SQL Injection protection? | [B] | Parameterized queries / ORM / Input sanitization | **ORM**: Prisma, TypeORM auto-param; **Raw**: `$1` placeholders in pg |
| 5.4.6 | Rate limiting rules? | [T] | X requests/minute per IP / per user / per API key | **100/min** per IP; **1000/min** per authenticated user |
| 5.4.7 | Request size limits? | [T] | Max request body size / Max file upload size | **Body**: 1MB; **File upload**: 10MB (images), 100MB (documents) |

### 5.5 Compliance & Audit

| # | Question | Type | Suggested Answers | Examples |
|---|----------|------|-------------------|----------|
| 5.5.1 | Are there compliance requirements? | [B] | GDPR / PCI-DSS / HIPAA / SOC 2 / ISO 27001 / PDPA / None | **EU users**: GDPR; **Payment**: PCI-DSS; **Healthcare**: HIPAA; **Vietnam**: PDPA |
| 5.5.2 | Data residency requirements? | [T] | Specific regions/countries / EU only / No restrictions | **GDPR**: EU data must stay in EU; **China**: Must stay in China |
| 5.5.3 | Is audit logging needed? | [B] | Yes (all actions / critical actions only) / No | **All actions**: Security-critical; **Critical only**: Login, payment, data access |
| 5.5.4 | Audit log retention period? | [T] | X days / months / years / Forever | **GDPR**: Min 1 year; **PCI**: 1 year; **Security**: 2 years |
| 5.5.5 | Is penetration testing required? | [T] | Yes (before launch / periodic) / No / Need consultation | **Before launch**: Full pentest; **Quarterly**: Vulnerability scan |

---

## 6. INTEGRATION ARCHITECTURE

### 6.1 Third-Party Services

| # | Question | Type | Suggested Answers | Tech Stack Options | Examples |
|---|----------|------|-------------------|-------------------|----------|
| 6.1.1 | List third-party APIs/services to integrate? | [B] | Payment (Stripe, PayPal) / SMS (Twilio) / Email (SendGrid) / Maps (Google, Mapbox) / etc. | **Payment**: Stripe, PayPal, Square; **SMS**: Twilio, AWS SNS; **Email**: SendGrid, AWS SES | Stripe (payments), Twilio (SMS), Google Maps (location) |
| 6.1.2 | Integration approach? | [B] | Direct API calls / SDK / Webhooks / Event-driven / Middleware | **SDK**: Stripe SDK, Twilio SDK; **Webhooks**: Async updates; **Event-driven**: Kafka consumer |
| 6.1.3 | Do third-party dependencies have guaranteed SLA? | [T] | Yes (SLA X%) / No / Need fallback strategy | **Stripe**: 99.99% SLA; **Payment gateway (local)**: 99.9% |
| 6.1.4 | Is there integration testing with sandbox environments? | [T] | Yes / No / Third-party provides sandbox | Stripe: **test mode**; Twilio: **dev account** |

### 6.2 Payment Integration

| # | Question | Type | Suggested Answers | Tech Stack Options | Examples |
|---|----------|------|-------------------|-------------------|----------|
| 6.2.1 | Which payment gateway? | [B] | Stripe / PayPal / Braintree / Square / Adyen / Khalti / Local payment (VN) | **Global**: Stripe, PayPal; **VN**: VNPay, MoMo, ZaloPay; **Regional**: Midtrans (SEA) | E-commerce global: **Stripe + PayPal**; VN domestic: **VNPay + MoMo** |
| 6.2.2 | Payment flow? | [B] | Direct / Redirect / Hosted checkout / Token-based | **Redirect**: PayPal; **Hosted**: Stripe Checkout; **Token**: Saved cards |
| 6.2.3 | Currencies supported? | [B] | VND, USD, EUR... / Multi-currency | **Single**: USD only; **Multi**: Stripe with automatic conversion |
| 6.2.4 | Refund policy and handling? | [T] | Manual / Automatic after X days / Partial refunds | **Auto** after 7 days for failed orders; **Manual** for customer requests |
| 6.2.5 | PCI-DSS compliance level? | [B] | SAQ-A / SAQ-D / Full compliance / Payment gateway handles | **SAQ-A**: Stripe handles everything; **Full**: If storing cards |
| 6.2.6 | Are payment webhooks used? | [T] | Yes / No / Need consultation | Stripe: `payment_intent.succeeded`, `charge.refunded` |

### 6.3 Authentication Providers

| # | Question | Type | Suggested Answers | Tech Stack Options | Examples |
|---|----------|------|-------------------|-------------------|----------|
| 6.3.1 | External identity providers? | [T] | Google / Facebook / Apple / GitHub / LinkedIn / SSO enterprise | **Social**: Google, Apple, Facebook; **Enterprise**: Okta, Azure AD | E-commerce: **Google, Apple**; B2B: **Microsoft (Azure AD)** |
| 6.3.2 | Federation protocol? | [T] | OAuth2/OIDC / SAML 2.0 / LDAP / Custom | **OIDC**: Modern apps; **SAML**: Enterprise; **LDAP**: Internal directory | **OIDC**: Consumer apps; **SAML 2.0**: Enterprise SSO |
| 6.3.3 | User profile sync strategy? | [T] | Pull on login / Webhook on update / Manual sync / Read-only | **Pull on login**: Sync name/email from provider; **Webhook**: Real-time updates |

### 6.4 Event-Driven & Messaging

| # | Question | Type | Suggested Answers | Tech Stack Options | Examples |
|---|----------|------|-------------------|-------------------|----------|
| 6.4.1 | Which message queue/broker? | [B] | RabbitMQ / Apache Kafka / AWS SQS/SNS / Azure Service Bus / Google Pub/Sub / Redis Streams | **Kafka**: High-throughput event streaming; **RabbitMQ**: Flexible routing; **SQS**: AWS fully-managed | **Kafka**: Event sourcing, log aggregation; **SQS**: Simple async tasks |
| 6.4.2 | Is event schema registry needed? | [T] | Yes (Confluent Schema Registry, AWS Glue) / No / Need consultation | **Confluent Schema Registry**: Avro, Protobuf; **AWS Glue**: Data catalog |
| 6.4.3 | Dead letter queue/retry strategy? | [T] | X retries, then DLQ / Exponential backoff / Manual intervention | **3 retries**, then DLQ; **Exponential backoff**: 1s, 2s, 4s |
| 6.4.4 | Is message ordering required? | [T] | Yes (per partition/key) / No / Best-effort | **Per partition**: Kafka preserves order; **Per key**: Same user events ordered |
| 6.4.5 | Idempotency handling? | [T] | Yes (idempotency keys) / No / Message deduplication | **Stripe**: Idempotency key in header; **Kafka**: Deduplication by message key |

### 6.5 External System Integration

| # | Question | Type | Suggested Answers | Tech Stack Options | Examples |
|---|----------|------|-------------------|-------------------|----------|
| 6.5.1 | Do you need ERP/CRM integration? | [T] | Yes (SAP, Oracle, Salesforce) / No / Need consultation | **CRM**: Salesforce, HubSpot; **ERP**: SAP, Oracle; **Middleware**: MuleSoft |
| 6.5.2 | File storage/CDN integration? | [T] | AWS S3 / Azure Blob / Google Cloud Storage / Cloudflare R2 | **S3**: AWS ecosystem; **Cloudflare R2**: Zero egress; **GCS**: GCP native | S3 + CloudFront for static assets |
| 6.5.3 | Email service provider? | [T] | SendGrid / AWS SES / Mailgun / SMTP server | **SendGrid**: High deliverability; **SES**: Cheap + AWS native; **SMTP**: Self-hosted |
| 6.5.4 | SMS service provider? | [T] | Twilio / AWS SNS / Vonage / Local carrier | **Twilio**: Global coverage; **AWS SNS**: Cheap; **Local**: Vietnam: Viettel, Vinaphone |

---

## 7. DEPLOYMENT & INFRASTRUCTURE

### 7.1 CI/CD Pipeline

| # | Question | Type | Suggested Answers | Tech Stack Options | Examples |
|---|----------|------|-------------------|-------------------|----------|
| 7.1.1 | Which CI/CD platform? | [B] | GitHub Actions / GitLab CI / Jenkins / CircleCI / Azure DevOps / AWS CodePipeline | **GitHub Actions**: GitHub native; **GitLab CI**: GitLab native; **Jenkins**: Self-hosted flexible | **GitHub Actions**: Most projects; **Jenkins**: Enterprise with existing infra |
| 7.1.2 | Deployment strategy? | [B] | Direct deploy / Blue-Green / Canary / Rolling / Feature flags | See **Comparison Table** below | **Blue-Green**: Zero-downtime; **Canary**: Gradual rollout; **Feature flags**: Continuous delivery |
| 7.1.3 | Deployment frequency? | [B] | Multiple times/day / Daily / Weekly / On-demand | **Multiple/day**: CI/CD matured; **Weekly**: Traditional; **On-demand**: Urgent fixes |
| 7.1.4 | Automated testing gates before deploy? | [B] | Yes (unit, integration, e2e) / Partial / No | **Unit**: Fast, 80%; **Integration**: 15%; **E2E**: 5% (slow) | **Yes**: All 3 levels pass; **Partial**: Unit + smoke tests |
| 7.1.5 | Environment promotion flow? | [T] | Dev → Staging → Production / Hot fixes directly to prod / Multi-environment | **Standard**: Dev → Staging → UAT → Prod; **Hotfix**: Dev → Prod directly |
| 7.1.6 | Database migration strategy? | [T] | Automated (Liquibase, Flyway) / Manual / Blue-green database migration | **Flyway**: SQL migrations; **Liquibase**: XML/YAML, rollback support | **Automated**: Flyway in CI pipeline |

#### Deployment Strategy Comparison

| Strategy | Best For | Risk | Downtime | Rollback Speed |
|----------|---------|------|----------|----------------|
| **Blue-Green** | Zero-downtime, critical systems | Low | None | Instant |
| **Canary** | Gradual rollout, A/B testing | Medium | None | Fast |
| **Rolling** | Kubernetes, gradual | Low-Medium | None (if >1 replica) | Slow |
| **Feature Flags** | Continuous deployment, quick toggle | Low | None | Instant |
| **Direct** | Dev/QA environments | High | Brief | N/A |

### 7.2 Containerization & Orchestration

| # | Question | Type | Suggested Answers | Tech Stack Options | Examples |
|---|----------|------|-------------------|-------------------|----------|
| 7.2.1 | Is containerization used? | [B] | Docker / Podman / No (traditional deployment) | **Docker**: Industry standard; **Podman**: Rootless, K8s compatible | **Docker**: Most cases; **Podman**: Enhanced security |
| 7.2.2 | Container registry? | [T] | Docker Hub / AWS ECR / Azure ACR / Google GCR / Self-hosted | **ECR**: AWS native, IAM; **ACR**: Azure native; **Harbor**: Self-hosted | **ECR**: AWS workloads; **Harbor**: Enterprise on-prem |
| 7.2.3 | Orchestration platform? | [B] | Kubernetes (EKS, AKS, GKE) / Docker Swarm / ECS / HashiCorp Nomad / None | **EKS**: AWS managed K8s; **AKS**: Azure managed; **GKE**: GCP managed; **ECS**: AWS simpler | **EKS**: AWS + need K8s flexibility; **ECS**: AWS + simpler needs |
| 7.2.4 | Are Helm charts or Kustomize used? | [T] | Helm / Kustomize / Raw Kubernetes manifests / Skaffold | **Helm**: Package manager, templating; **Kustomize**: Overlay-based | **Helm**: Reusable charts; **Kustomize**: Env overlays |
| 7.2.5 | Is service mesh needed? | [T] | Yes (Istio, Linkerd) / No / Need consultation (observability, mTLS) | **Istio**: Full-featured; **Linkerd**: Lightweight; **Nothing**: CNCF alternatives | **Istio**: Complex microservices; **No**: Simple K8s |

### 7.3 Environment Configuration

| # | Question | Type | Suggested Answers | Examples |
|---|----------|------|-------------------|----------|
| 7.3.1 | Which environments are needed? | [B] | Dev / Local / Staging / QA / UAT / Production | Dev → Staging → UAT → Production |
| 7.3.2 | Is environment parity required? | [T] | Yes (identical configs) / Similar (same tooling, different scale) / No | **Yes**: Docker compose dev = prod mini; **Similar**: Same K8s, different scale |
| 7.3.3 | Configuration management approach? | [T] | Environment variables / Config files / Configuration server (Spring Config, Consul) / Secrets manager | **Env vars**: 12-factor apps; **Consul**: Dynamic config; **Vault**: Secrets |
| 7.3.4 | Feature flags solution? | [T] | LaunchDarkly / Unleash / In-house / Environment variables only | **LaunchDarkly**: Full-featured; **Env vars**: Simple toggle |

### 7.4 Infrastructure as Code

| # | Question | Type | Suggested Answers | Tech Stack Options | Examples |
|---|----------|------|-------------------|-------------------|----------|
| 7.4.1 | Which IaC tool? | [B] | Terraform / Pulumi / AWS CDK / Ansible / ARM templates / CloudFormation | **Terraform**: Multi-cloud, declarative; **Pulumi**: Code-based; **CDK**: AWS native code | **Terraform**: Multi-cloud; **CDK**: AWS-only + TS/Python |
| 7.4.2 | State management for IaC? | [T] | Remote state (S3 + DynamoDB, Terraform Cloud) / Local / Git-based | **S3 + DynamoDB**: Self-managed remote; **Terraform Cloud**: Managed service | **S3 + DynamoDB**: Standard; **Terraform Cloud**: Team collaboration |
| 7.4.3 | Infrastructure provisioning workflow? | [T] | GitOps (ArgoCD, Flux) / CI/CD triggered / Manual apply | **GitOps**: ArgoCD watches repo; **CI/CD**: Pipeline applies changes | **GitOps**: ArgoCD for K8s; **CI/CD**: GitHub Actions for AWS |

### 7.5 Monitoring & Logging Infrastructure

| # | Question | Type | Suggested Answers | Tech Stack Options | Examples |
|---|----------|------|-------------------|-------------------|----------|
| 7.5.1 | Metrics collection? | [B] | Prometheus / Datadog / CloudWatch / New Relic / Custom | **Prometheus**: Open-source, K8s native; **Datadog**: SaaS, APM + logs + traces | **Prometheus + Grafana**: Open-source stack; **Datadog**: SaaS all-in-one |
| 7.5.2 | Log aggregation? | [B] | ELK Stack (Elasticsearch, Logstash, Kibana) / Loki / CloudWatch Logs / Splunk / Datadog | **ELK**: Full-featured; **Loki**: Cheap, Prometheus-like; **CloudWatch**: AWS native | **ELK**: Enterprise; **Loki + Grafana**: Cost-effective; **CloudWatch**: AWS only |
| 7.5.3 | Distributed tracing? | [T] | Jaeger / Zipkin / AWS X-Ray / OpenTelemetry / No | **Jaeger**: CNCF, K8s native; **X-Ray**: AWS native; **OpenTelemetry**: Vendor-agnostic | **OpenTelemetry**: Future-proof; **Jaeger**: CNCF standard |
| 7.5.4 | Visualization/Dashboard? | [T] | Grafana / Kibana / Datadog Dashboard / CloudWatch Dashboards | **Grafana**: Metrics + logs; **Kibana**: ELK stack; **Datadog**: SaaS dashboards | **Grafana**: Prometheus + Loki; **Kibana**: ELK |

---

## 8. PERFORMANCE & SCALABILITY

### 8.1 Capacity Planning

| # | Question | Type | Suggested Answers | Examples |
|---|----------|------|-------------------|----------|
| 8.1.1 | Expected maximum concurrent users? | [B] | X simultaneous users | **1000** concurrent users; **10000** peak during sale events |
| 8.1.2 | Expected requests per second (RPS)? | [B] | Peak RPS / Average RPS / Burst capacity | **100 RPS** average; **1000 RPS** peak; **2000 RPS** burst |
| 8.1.3 | Data volume estimates? | [B] | Daily new records / Total storage / Growth rate | **10,000 new users/day**; **100GB** total; **20% YoY** growth |
| 8.1.4 | Geographic user distribution? | [T] | Regional (VN only) / Asia-Pacific / Global | **VN only**; **APAC**: Singapore, Thailand, Indonesia; **Global** |
| 8.1.5 | Peak traffic times/seasonality? | [T] | Daily peak hours / Seasonal spikes / Event-driven peaks | **9-11 AM, 2-4 PM** daily; **11.11**, **Black Friday** seasonal |

### 8.2 Performance Requirements

| # | Question | Type | Suggested Answers | Examples |
|---|----------|------|-------------------|----------|
| 8.2.1 | Response time SLA? | [B] | p95 < Xms / p99 < Yms / Average < Zms | **p95 < 200ms**; **p99 < 500ms**; **Average < 100ms** |
| 8.2.2 | Page load time requirements? | [T] | First Contentful Paint < Xs / Largest Contentful Paint < Ys | **FCP < 1.8s**; **LCP < 2.5s**; **FID < 100ms** |
| 8.2.3 | API latency requirements? | [T] | GET < Xms / POST < Yms / Complex queries < Zms | **GET < 100ms**; **POST < 300ms**; **Complex < 1s** |
| 8.2.4 | Database query timeout? | [T] | X seconds / Dynamic based on query type | **Simple queries**: 5s; **Complex reports**: 30s |
| 8.2.5 | Batch job completion time? | [T] | Daily report < X minutes / Data sync < Y minutes | **Daily report < 10 min**; **Data sync < 5 min** |

### 8.3 Caching Strategy

| # | Question | Type | Suggested Answers | Tech Stack Options | Examples |
|---|----------|------|-------------------|-------------------|----------|
| 8.3.1 | Which cache layers are used? | [B] | Browser / CDN / Application (Redis) / Database query cache / Full-page cache | **Browser**: Service Worker, localStorage; **CDN**: CloudFront; **Redis**: API cache | **CDN** (static) → **Redis** (API) → **DB query cache** |
| 8.3.2 | Cache hit rate target? | [T] | X% for application cache / Y% for CDN | **Redis**: 90%+ hit rate; **CDN**: 95%+ for static |
| 8.3.3 | Cache warming strategy? | [T] | On-demand / Scheduled / Pre-warming on deploy | **Scheduled**: Top products daily; **Pre-warming**: On deploy for critical data |
| 8.3.4 | Stale-while-revalidate strategy? | [T] | Yes / No / Need consultation | Serve stale data while revalidating in background |

### 8.4 Scalability

| # | Question | Type | Suggested Answers | Examples |
|---|----------|------|-------------------|----------|
| 8.4.1 | Auto-scaling triggers? | [T] | CPU > X% / Memory > Y% / Request count / Custom metrics | **CPU > 70%** for 5 min; **Request count > 1000 RPS** |
| 8.4.2 | Auto-scaling bounds? | [T] | Min X instances / Max Y instances / Scale up/down rate | **Min: 2, Max: 20** instances; **Scale up**: 2/min, **Scale down**: 1/min |
| 8.4.3 | Load balancing strategy? | [T] | Round-robin / Least connections / IP hash / Weighted / Latency-based | **Least connections**: Long-lived connections; **Latency-based**: Route to fastest |
| 8.4.4 | Database connection pooling? | [T] | PgBouncer / ProxySQL / Application-level pooling / Connection limits | **PgBouncer**: `pool_mode = transaction`, `max_client_conn = 100` |
| 8.4.5 | Read replicas for scaling reads? | [T] | Yes / No / Need consultation | **Yes**: 1 primary + 3 read replicas |
| 8.4.6 | Sharding strategy if needed? | [T] | Hash-based / Range-based / Directory-based / Not needed | **Hash**: `user_id % 4`; **Range**: by date for time-series |

---

## 9. RELIABILITY & MONITORING

### 9.1 Availability & SLA

| # | Question | Type | Suggested Answers | SLA Comparison |
|---|----------|------|-------------------|----------------|
| 9.1.1 | Uptime SLA requirement? | [B] | 99.9% (8.7h downtime/year) / 99.95% / 99.99% (52min/year) / 99.999% (5min/year) | 99.9% = 8.7h/yr; 99.99% = 52min/yr; 99.999% = 5min/yr |
| 9.1.2 | Planned maintenance window? | [T] | Allowed (X hours/week) / Zero-downtime required / Maintenance mode acceptable | **Weekly**: 2-hour window Sun 2-4 AM; **Zero-downtime**: Blue-green deploys |
| 9.1.3 | Geographic redundancy? | [T] | Single region / Multi-region active-active / Multi-region active-passive | **Single**: Dev/QA; **Multi-region active-passive**: DR; **Active-active**: Global perf |
| 9.1.4 | Fallback/failover strategy? | [T] | Automatic failover / Manual intervention / Graceful degradation | **Auto failover**: RDS Multi-AZ; **Manual**: DNS switch |

### 9.2 Error Handling

| # | Question | Type | Suggested Answers | Examples |
|---|----------|------|-------------------|----------|
| 9.2.1 | Standard error response format? | [B] | RFC 7807 / Custom format / Include error codes | `{"type": "https://api.example.com/errors/validation", "title": "Validation Error", "status": 400}` |
| 9.2.2 | Global error handling strategy? | [T] | Centralized error handler / Per-module / Middleware-based | **Middleware**: Express error middleware; **Centralized**: Spring `@ControllerAdvice` |
| 9.2.3 | Retry policy? | [T] | X retries with exponential backoff / Circuit breaker / No retry | **3 retries**: 1s, 2s, 4s backoff; **Circuit breaker**: Polly (C#), circuit-breaker-js |
| 9.2.4 | Circuit breaker pattern? | [T] | Yes (framework support) / No / Need consultation | **Yes**: Hystrix, Resilience4j, Polly |
| 9.2.5 | Graceful degradation features? | [T] | Read-only mode / Cached data fallback / Feature toggles | **Read-only mode**: DB down → serve from cache; **Feature toggle**: Disable non-essential features |

### 9.3 Logging Strategy

| # | Question | Type | Suggested Answers | Tech Stack Options | Examples |
|---|----------|------|-------------------|-------------------|----------|
| 9.3.1 | Logging framework/library? | [B] | Winston / Pino (Node.js) / Logback / Python logging / Structured logging | **Node.js**: Pino (fast), Winston (features); **Java**: Logback, SLF4J | **Pino**: High-perf JSON logs; **Logback**: Java standard |
| 9.3.2 | Log levels used? | [T] | DEBUG / INFO / WARN / ERROR / FATAL / Custom levels | **DEBUG**: Development; **INFO**: Business events; **WARN**: Slow queries; **ERROR**: Exceptions |
| 9.3.3 | Sensitive data masking in logs? | [B] | Yes (passwords, tokens, PII) / No | Mask: `password`, `token`, `ssn`, `credit_card`, `email` |
| 9.3.4 | Log retention period? | [T] | X days in hot storage / Y days in cold / Z days total | **Hot**: 30 days (Elasticsearch); **Cold**: 1 year (S3); **Total**: 7 years |
| 9.3.5 | Log aggregation and analysis? | [T] | ELK / Loki / CloudWatch / Splunk / Datadog | **Loki + Grafana**: Cost-effective; **ELK**: Full-text search; **Splunk**: Enterprise |

### 9.4 Monitoring & Alerting

| # | Question | Type | Suggested Answers | Tech Stack Options | Examples |
|---|----------|------|-------------------|-------------------|----------|
| 9.4.1 | Monitoring tools? | [B] | Prometheus + Grafana / Datadog / New Relic / CloudWatch / Custom | **Prometheus + Grafana**: Open-source; **Datadog**: SaaS APM | **Prometheus + Grafana**: Standard; **Datadog**: SaaS + APM |
| 9.4.2 | Key metrics monitored? | [B] | Infrastructure (CPU, Memory, Disk) / Application (latency, errors, throughput) / Business metrics | **Infra**: CPU > 80%, Memory > 85%; **App**: Error rate > 1%, Latency p99 > 500ms |
| 9.4.3 | Alerting channels? | [B] | Email / Slack / PagerDuty / SMS / OpsGenie / Webhooks | **Slack**: Non-critical; **PagerDuty**: Critical | **Slack**: Warning; **PagerDuty**: Critical P1/P2 |
| 9.4.4 | Alert severity levels? | [T] | P1 (Critical) / P2 (High) / P3 (Medium) / P4 (Low) | **P1**: Site down, payment failing; **P2**: Degraded perf; **P3**: Non-critical |
| 9.4.5 | Is on-call rotation needed? | [T] | Yes (X people team) / No / Vendor support | **Yes**: 5-person team, weekly rotation |
| 9.4.6 | SLO/SLI definitions? | [T] | Yes (document SLOs) / No / Need consultation | **SLO**: 99.9% availability, p99 latency < 500ms |

### 9.5 Disaster Recovery

| # | Question | Type | Suggested Answers | Examples |
|---|----------|------|-------------------|----------|
| 9.5.1 | Backup strategy? | [B] | Automated daily / Point-in-time / Cross-region / Continuous replication | **Daily full + WAL continuous** → Point-in-time recovery |
| 9.5.2 | Recovery time objective (RTO)? | [B] | X minutes / X hours / X days acceptable | **RTO: 4 hours** - max acceptable downtime |
| 9.5.3 | Recovery point objective (RPO)? | [B] | 0 (no data loss) / X minutes / X hours of data loss acceptable | **RPO: 1 minute** - max acceptable data loss |
| 9.5.4 | Is disaster recovery plan documented? | [T] | Yes / No / Need to create | DR runbook with step-by-step recovery procedures |
| 9.5.5 | DR testing frequency? | [T] | Monthly / Quarterly / Annually / Never tested | **Quarterly**: Tabletop exercise; **Annually**: Full failover test |
| 9.5.6 | Failover procedure? | [T] | Automatic with runbook / Manual with runbook / No DR site | **Automatic**: RDS Multi-AZ failover; **Manual**: DNS switch with runbook |
| 9.5.7 | Data archival strategy? | [T] | Archive to cold storage / Delete after X days / Legal hold requirements | **Archive**: 7-year retention to S3 Glacier; **Legal hold**: Indefinite |

---

## 10. DEVELOPMENT WORKFLOW

### 10.1 Code Quality

| # | Question | Type | Suggested Answers | Tech Stack Options | Examples |
|---|----------|------|-------------------|-------------------|----------|
| 10.1.1 | Coding standards/style guide? | [B] | Yes (link/style document) / Airbnb style / Google style / Internal standard / No | **Airbnb**: JS/TS; **Google**: Multiple languages; **Standard**: PEP 8 (Python) | **Airbnb**: React projects; **PEP 8**: Python |
| 10.1.2 | Linting configuration? | [B] | ESLint / Prettier / Ruff / golangci-lint / SwiftLint | **ESLint + Prettier**: JS/TS; **Ruff**: Python fast; **golangci-lint**: Go | **ESLint + Prettier + Husky**: Pre-commit lint |
| 10.1.3 | Static code analysis? | [T] | SonarQube / CodeClimate / ESLint + plugins / No | **SonarQube**: Full SAST; **CodeClimate**: GitHub integration | **SonarQube**: Enterprise; **ESLint plugins**: Lightweight |
| 10.1.4 | Code complexity limits? | [T] | Max X cyclomatic complexity / Max Y lines per function / No | **Max complexity**: 10; **Max function length**: 50 lines |
| 10.1.5 | Dependency scanning? | [T] | Snyk / Dependabot / Renovate / Manual / No | **Dependabot**: GitHub native; **Snyk**: Deep scanning | **Dependabot**: Daily updates; **Snyk**: Security-focused |

### 10.2 Testing Requirements

| # | Question | Type | Suggested Answers | Tech Stack Options | Examples |
|---|----------|------|-------------------|-------------------|----------|
| 10.2.1 | Testing pyramid strategy? | [B] | Unit tests focus / Integration tests / E2E tests / Balanced | **Pyramid**: 70% unit, 20% integration, 10% E2E; **Ice cream cone**: Opposite |
| 10.2.2 | Coverage requirements? | [B] | X% line coverage / Y% branch coverage / No requirement | **Min 80%** line coverage; **Critical paths**: 100% |
| 10.2.3 | Unit testing framework? | [B] | Jest / Mocha / Vitest / pytest / JUnit / Go testing / XCTest | **Jest/Vitest**: React; **pytest/JUnit**: Backend; **Go testing**: Native | **Vitest**: Fast Vite projects; **Jest**: Legacy React |
| 10.2.4 | Integration testing approach? | [T] | Testcontainers / Docker compose / Real services with test DB / Contract testing | **Testcontainers**: DB in Docker; **Docker compose**: Full stack | **Testcontainers**: PostgreSQL, Redis in tests |
| 10.2.5 | E2E testing framework? | [T] | Playwright / Cypress / Selenium / TestCafe / No | **Playwright**: MS, cross-browser; **Cypress**: Developer experience | **Playwright**: TypeScript, parallel; **Cypress**: Simple API |
| 10.2.6 | Performance/load testing? | [T] | k6 / JMeter / Gatling / Locust / No | **k6**: Script in JS; **Locust**: Python | **k6**: Scripting + Grafana; **JMeter**: GUI-based |
| 10.2.7 | Security testing? | [T] | SAST (Semgrep, Bandit) / DAST (OWASP ZAP) / Dependency scan / Manual pentest | **SAST**: Semgrep, Bandit; **DAST**: OWASP ZAP; **Dependency**: Snyk |
| 10.2.8 | Mutation testing? | [T] | Yes (Stryker, Pitest) / No / Need consultation | **Stryker**: JS/TS; **Pitest**: Java |
| 10.2.9 | Snapshot testing? | [T] | Yes (Jest, Playwright) / No / Only for some components | UI components với stable markup: Jest snapshots |

### 10.3 Code Review

| # | Question | Type | Suggested Answers | Examples |
|---|----------|------|-------------------|----------|
| 10.3.1 | Code review process? | [B] | Required approvals (X reviewers) / Optional / No formal review | **1 approval**: Non-critical; **2 approvals**: Critical services |
| 10.3.2 | PR merge strategy? | [B] | Squash and merge / Rebase and merge / Merge commit / Linear history | **Squash**: Clean history; **Rebase**: Preserves commits; **Linear**: Required for some teams |
| 10.3.3 | Branch protection rules? | [T] | Require PR / Require status checks / Require signed commits / No | **main**: Require PR + CI passing; **develop**: Require PR |
| 10.3.4 | Is there a review checklist? | [T] | Yes (link checklist) / No / Need to create | Security review, performance impact, backward compatibility |
| 10.3.5 | Automated review tools? | [T] | GitHub PR reviews / CodeClimate / SonarQube / No | **SonarQube**: Auto-comment on PR; **CodeClimate**: GitHub integration |

### 10.4 Version Control

| # | Question | Type | Suggested Answers | Tech Stack Options | Examples |
|---|----------|------|-------------------|-------------------|----------|
| 10.4.1 | Git platform? | [B] | GitHub / GitLab / Bitbucket / Azure DevOps / Self-hosted | **GitHub**: Open source, GH Actions; **GitLab**: DevOps platform; **Azure DevOps**: MS ecosystem | **GitHub**: Most projects; **GitLab**: Complete DevOps; **Azure DevOps**: MS shops |
| 10.4.2 | Repository structure? | [B] | Monorepo / Multi-repo (per service) / Hybrid | **Monorepo**: NX, Turborepo; **Multi-repo**: Per service | **Monorepo**: Shared packages, easier atomic changes; **Multi-repo**: Service independence |
| 10.4.3 | Branching strategy? | [B] | GitFlow / Trunk-based development / GitHub Flow / Custom | **GitFlow**: Release branches; **Trunk-based**: Small frequent merges | **Trunk-based**: CI/CD mature teams; **GitFlow**: Scheduled releases |
| 10.4.4 | Release versioning strategy? | [T] | SemVer / Calendar versioning / Git hash / Custom | **SemVer**: `v1.2.3`; **CalVer**: `2024.01.15`; **Git hash**: `abc1234` | **SemVer**: Libraries, APIs; **CalVer**: Frontend releases |
| 10.4.5 | Changelog generation? | [T] | Automated (Conventional Commits) / Manual / Keep a Changelog | **Conventional Commits**: `feat:`, `fix:`; **Keep a Changelog**: CHANGELOG.md | **Automated**: Release please, semantic-release |

### 10.5 Documentation

| # | Question | Type | Suggested Answers | Tech Stack Options | Examples |
|---|----------|------|-------------------|-------------------|----------|
| 10.5.1 | Documentation framework? | [T] | GitHub Wiki / Confluence / Notion / MkDocs / Docusaurus / Read the Docs | **Docusaurus**: Markdown + versioning; **MkDocs**: Python projects; **Confluence**: Enterprise | **Docusaurus**: Public docs; **Confluence**: Internal |
| 10.5.2 | Are ADR (Architecture Decision Records) used? | [T] | Yes / No / Need consultation | **Yes**: `docs/adr/NNN-title.md` |
| 10.5.3 | API documentation? | [B] | OpenAPI/Swagger / GraphQL schema / Auto-generated / Manual | **OpenAPI 3.1**: REST; **GraphQL schema**: GraphQL APIs | **Swagger UI + Redoc**: `api.example.com/docs` |
| 10.5.4 | README requirements? | [T] | Standard README per project / Internal documentation / No | Setup, development, deployment, testing instructions |
| 10.5.5 | Inline documentation standard? | [T] | JSDoc / Docstrings / TSDoc / Not required | **TSDoc**: TypeScript; **JSDoc**: JavaScript; **Docstrings**: Python |
| 10.5.6 | Runbook documentation? | [T] | Yes (deployment, rollback, incident response) / No / Need to create | Deployment, rollback, on-call procedures, incident response |

---

## APPENDIX A: Quick Reference

### Required Questions Summary (Must-Answer)

| Category | Priority Questions |
|----------|-------------------|
| **System Architecture** | 1.1.1, 1.2.1, 1.3.1, 1.4.1 |
| **Frontend** | 2.1.1, 2.3.1, 2.4.1 |
| **Backend** | 3.1.1, 3.2.1, 3.3.1 |
| **Database** | 4.1.1, 4.2.1 |
| **Security** | 5.1.1, 5.2.1, 5.3.1, 5.3.2, 5.5.1 |
| **Integration** | 6.1.1, 6.2.1 |
| **Deployment** | 7.1.1, 7.2.1, 7.3.1 |
| **Performance** | 8.1.1, 8.1.2, 8.2.1 |
| **Reliability** | 9.1.1, 9.3.1, 9.4.1, 9.5.1 |
| **Workflow** | 10.1.1, 10.2.1, 10.3.1, 10.4.1 |

### Questions by Project Type

| Project Type | Focus Areas |
|--------------|-------------|
| **E-commerce** | Payment (6.2), Inventory, Order management, Scalability (8.x) |
| **SaaS/Multi-tenant** | Security (5.x), Multi-tenancy (4.2.6), RBAC (5.2) |
| **Real-time/Chat** | WebSocket (2.5.4), Message queue (6.4), Presence detection |
| **Mobile App** | API security (5.4), Offline support (2.5.3), Push notifications |
| **Microservices** | Service mesh (7.2.5), Distributed tracing (7.5.3), Event-driven (6.4) |
| **Data-intensive/ML** | Database scaling (4.4), ETL pipeline, Data warehousing |

---

## APPENDIX B: Tech Stack Quick Reference

### Frontend Stack Options

| Category | Options |
|----------|---------|
| **Framework** | React, Vue.js, Angular, Svelte, Next.js, Nuxt.js |
| **Mobile** | React Native, Flutter, Swift (iOS), Kotlin (Android) |
| **State Management** | Redux Toolkit, Zustand, MobX, Jotai, Vuex, Pinia |
| **Routing** | React Router, Vue Router, TanStack Router |
| **Styling** | Tailwind CSS, Styled-components, CSS Modules, SCSS |
| **Build Tool** | Vite, Webpack, Turbopack, Parcel |
| **UI Library** | Material UI, Ant Design, Chakra UI, Radix UI, Shadcn/ui |

### Backend Stack Options

| Category | Options |
|----------|---------|
| **Language** | Node.js, Python, Java, Go, Rust, .NET, PHP |
| **Framework** | Express, NestJS, FastAPI, Django, Spring Boot, Gin |
| **API Style** | REST, GraphQL, gRPC, tRPC |
| **Runtime** | Docker, Serverless (Lambda), Traditional VMs |
| **Message Queue** | RabbitMQ, Kafka, AWS SQS, Redis Pub/Sub |

### Database Stack Options

| Category | Options |
|----------|---------|
| **SQL** | PostgreSQL, MySQL, MariaDB, Oracle, SQL Server |
| **NoSQL Document** | MongoDB, CouchDB |
| **NoSQL Key-Value** | Redis, DynamoDB, Memcached |
| **NoSQL Column** | Cassandra, HBase |
| **NoSQL Graph** | Neo4j, Amazon Neptune |
| **Search** | Elasticsearch, PostgreSQL full-text, Meilisearch |
| **Managed Services** | AWS RDS/Aurora, Azure CosmosDB, PlanetScale, Supabase |

### Cloud & Infrastructure Options

| Category | Options |
|----------|---------|
| **Cloud Providers** | AWS, Azure, GCP, Multi-cloud |
| **CDN** | CloudFront, Azure CDN, Cloudflare, Fastly |
| **Container** | Docker, Podman |
| **Orchestration** | Kubernetes (EKS, AKS, GKE), ECS, Docker Swarm |
| **IaC** | Terraform, Pulumi, AWS CDK, CloudFormation |
| **CI/CD** | GitHub Actions, GitLab CI, Jenkins, CircleCI |
| **Monitoring** | Prometheus + Grafana, Datadog, CloudWatch |
| **Logging** | ELK Stack, Loki, CloudWatch, Splunk |
| **Container Registry** | Docker Hub, AWS ECR, Azure ACR, Harbor |

---

## APPENDIX C: Common Technology Stack Recommendations

### For E-commerce Platform

| Layer | Recommended Stack |
|-------|------------------|
| **Frontend** | React + Next.js (SSR/SSG), Tailwind CSS, Shadcn/ui |
| **Backend** | Node.js + NestJS, TypeScript |
| **Database** | PostgreSQL (primary) + Redis (cache) |
| **Search** | Elasticsearch (product search) |
| **Payment** | Stripe + PayPal + Local (VNPay/MoMo) |
| **Queue** | Redis Pub/Sub (notifications), Kafka (events) |
| **CDN** | Cloudflare |
| **Monitoring** | Prometheus + Grafana + Loki |
| **CI/CD** | GitHub Actions + Docker + Kubernetes (EKS) |

### For SaaS/Multi-tenant Application

| Layer | Recommended Stack |
|-------|------------------|
| **Frontend** | Vue.js + Nuxt.js, Tailwind CSS |
| **Backend** | Python + FastAPI (rapid dev) or Node.js + NestJS |
| **Database** | PostgreSQL (shared schema + tenant_id) |
| **Auth** | JWT + OAuth2 (Google, Microsoft) + MFA |
| **Security** | RBAC + ABAC hybrid |
| **Billing** | Stripe Billing (subscriptions) |
| **Monitoring** | Datadog (APM + logs) |

### For Real-time Chat/Collaboration

| Layer | Recommended Stack |
|-------|------------------|
| **Frontend** | React + Socket.io client, Quill (editor) |
| **Backend** | Node.js + Socket.io or Pusher |
| **Database** | PostgreSQL + Redis (presence, caching) |
| **Queue** | Redis Pub/Sub (real-time) + Kafka (durable events) |
| **Search** | Elasticsearch (message search) |
| **Media** | AWS S3 + CloudFront (images/files) |
| **WebSocket** | Socket.io or Ably |

### For Mobile-First Application

| Layer | Recommended Stack |
|-------|------------------|
| **Mobile** | React Native (cross-platform) or Flutter |
| **Backend** | Node.js + Express or Go |
| **Database** | PostgreSQL + Redis |
| **Push** | Firebase Cloud Messaging (FCM) + APNs |
| **Offline** | AsyncStorage / WatermelonDB |
| **Auth** | JWT + Biometrics (Face ID, Fingerprint) |

### For Data-intensive/ML Platform

| Layer | Recommended Stack |
|-------|------------------|
| **Frontend** | React + D3.js / Apache ECharts |
| **Backend** | Python + FastAPI + Celery (jobs) |
| **Database** | PostgreSQL + TimescaleDB (time-series) |
| **Analytics** | ClickHouse or BigQuery |
| **ML** | MLflow, Kubeflow, SageMaker |
| **Pipeline** | Apache Airflow, Prefect |
| **Feature Store** | Feast, Tecton |

### For Microservices Architecture

| Layer | Recommended Stack |
|-------|------------------|
| **API Gateway** | Kong / AWS API Gateway |
| **Service Mesh** | Istio / Linkerd |
| **Communication** | gRPC (sync) + Kafka (async) |
| **Tracing** | OpenTelemetry + Jaeger |
| **Container** | Docker + Kubernetes |
| **Service Discovery** | Consul / Kubernetes DNS |
| **Secrets** | HashiCorp Vault |

---

## APPENDIX D: SQL vs NoSQL Decision Matrix

| Criteria | SQL (PostgreSQL) | NoSQL MongoDB | NoSQL Cassandra | NoSQL Redis |
|----------|------------------|---------------|-----------------|-------------|
| **Best For** | Structured data, ACID, complex joins | Flexible schema, rapid dev | High write, time-series | Caching, sessions |
| **Transactions** | Full ACID | Single document ACID | Tunable consistency | Lua scripts |
| **Joins** | Yes (complex) | Limited ($lookup) | No (denormalize) | No |
| **Scaling** | Vertical + read replicas | Horizontal (sharding) | Horizontal (native) | Vertical + clustering |
| **Schema** | Fixed + migrations | Flexible (no migrations) | Fixed per column family | Schema-less |
| **Query Language** | SQL | MongoDB query | CQL | Commands |

### When to Choose Each

| Choose... | When... |
|-----------|---------|
| **PostgreSQL** | ACID compliance, complex relationships, data integrity critical |
| **MongoDB** | Rapid iteration, flexible schema, nested documents |
| **Cassandra** | High write throughput, time-series, multi-region |
| **Redis** | Sub-millisecond latency, caching, sessions, pub/sub |
| **Elasticsearch** | Full-text search, log analytics, Kibana integration |
| **DynamoDB** | Serverless, AWS-native, pay-per-request |

---

## APPENDIX E: Architecture Pattern Comparison

| Pattern | Best For | Pros | Cons |
|---------|----------|------|------|
| **Monolithic** | Small teams, MVPs, simple apps | Simple, fast dev, easy debugging | Hard to scale, deploy coupling |
| **Layered** | Enterprise apps, clear separation | Organized, testable | Overhead for small apps |
| **Microservices** | Large teams, complex domains, scaling | Independent deploy, tech flexibility | Complexity, network issues |
| **Event-Driven** | Real-time, async workflows, auditing | Decoupled, scalable | Hard to debug, eventual consistency |
| **Serverless** | Variable load, cost optimization | No server management, pay-per-use | Cold starts, vendor lock-in |
| **CQRS** | Read-heavy, complex domains | Optimized read/write separately | Complexity, eventual consistency |
| **DDD** | Complex business domains | Clear bounded contexts | Steep learning curve |

---

## APPENDIX F: Security Checklist by Project Type

### General Web Application
- [ ] HTTPS everywhere (TLS 1.3)
- [ ] Input validation (Zod/Joi)
- [ ] SQL injection prevention (ORM/parameterized)
- [ ] XSS protection (output encoding, CSP)
- [ ] CSRF protection (SameSite cookies, tokens)
- [ ] Rate limiting
- [ ] Secure session management (JWT with refresh)
- [ ] Dependency scanning (Snyk/Dependabot)
- [ ] Security headers (HSTS, X-Frame-Options)

### E-commerce / Payment Processing
- [ ] PCI-DSS compliance (if handling cards)
- [ ] Tokenization for card data
- [ ] 3D Secure for authentication
- [ ] Fraud detection integration
- [ ] Secure webhooks (signature verification)
- [ ] Order confirmation emails
- [ ] Refund automation

### Healthcare / HIPAA
- [ ] PHI encryption at rest and in transit
- [ ] Audit logging for all PHI access
- [ ] Role-based access control
- [ ] BAA with vendors
- [ ] Data retention policies
- [ ] Breach notification procedures

### GDPR Compliance
- [ ] Data consent management
- [ ] Right to deletion (GDPR delete)
- [ ] Data portability export
- [ ] Cookie consent banner
- [ ] EU data residency
- [ ] DPO appointment

---

> **Notes**:
> - Questions marked `[B]` must be answered before starting SDD.
> - Questions marked `[T]` are optional, depending on project context and specific requirements.
> - Some questions may need adjustment or additional questions based on project domain and complexity.
> - Recommended to use alongside other requirements gathering techniques such as stakeholder interviews, workshops, and prototype reviews.

(End of file)
