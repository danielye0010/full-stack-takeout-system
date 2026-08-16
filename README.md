# Full-Stack Java Takeout System

A production-style restaurant ordering backend implemented with **Java 17, Spring Boot 3, MyBatis, MySQL, Redis, JWT, WebSocket, scheduled tasks, reporting, and third-party service integrations**.

The project implements the backend architecture of the Sky Takeout learning system as a multi-module Java application, covering both restaurant-management workflows and consumer ordering APIs. It is a substantial end-to-end backend exercise rather than a single CRUD demo: authentication, order lifecycle, caching, reporting, real-time notifications, file storage, and payment-oriented integrations are all represented in the codebase.

## Highlights

- **Multi-module Maven architecture** with shared utilities, domain objects, and application server modules
- **Admin and consumer API layers** for employees, dishes, categories, set meals, carts, addresses, users, and orders
- **JWT authentication** with separate admin and user token flows
- **MySQL + MyBatis** persistence with XML mappers and PageHelper pagination
- **Redis / Spring Cache** for high-frequency application state
- **WebSocket notifications** for real-time order events
- **Spring Task** scheduling for order-state maintenance
- **Excel reporting** with Apache POI
- **Alibaba Cloud OSS** integration for object/file storage
- **WeChat Mini Program / WeChat Pay configuration hooks**
- **Knife4j / OpenAPI** API documentation

## Architecture

```text
                         +----------------------+
                         |  Admin / User Client |
                         +----------+-----------+
                                    |
                              REST / WebSocket
                                    |
                    +---------------v----------------+
                    |          Spring Boot 3          |
                    | controllers / services / tasks |
                    +--------+-------------+----------+
                             |             |
                       MyBatis/JDBC    Redis/Cache
                             |             |
                         +---v---+     +---v---+
                         | MySQL |     | Redis |
                         +-------+     +-------+

Additional integrations: Alibaba OSS, WeChat APIs, Excel report generation
```

## Modules

| Module | Role |
|---|---|
| `common/` | Shared constants, exceptions, JWT/context utilities, configuration properties, and integration helpers |
| `pojo/` | Entities, DTOs, VOs, and shared domain models |
| `server/` | Spring Boot application, controllers, services, MyBatis mappers, scheduled tasks, WebSocket, and reporting |
| `sql demo/mysql.sql` | MySQL schema and seed data for local development |

The root `pom.xml` aggregates all three Java modules and manages shared dependency versions.

## Functional coverage

### Restaurant management

- employee account management
- category, dish, and set-meal management
- order query, dispatch, cancellation, and completion
- business and operational statistics
- Excel report export
- shop-status and workspace APIs

### Consumer ordering

- user authentication
- menu/category browsing
- shopping cart
- delivery-address management
- order creation and history
- order payment/cancellation flows
- order reminders and real-time status events

## Technology stack

**Backend:** Java 17, Spring Boot 3.1, Spring MVC, Spring Cache, Spring Task, WebSocket  
**Persistence:** MySQL, MyBatis, PageHelper, Druid  
**Caching:** Redis / Spring Data Redis  
**Security:** JWT  
**API:** OpenAPI / Knife4j  
**Integration:** Alibaba OSS, WeChat APIs / WeChat Pay  
**Reporting:** Apache POI  
**Build:** Maven multi-module project

## Local setup

### 1. Requirements

- JDK 17
- Maven
- MySQL
- Redis

Optional integrations such as Alibaba OSS and WeChat require their corresponding developer credentials.

### 2. Initialize MySQL

Create the `sky_take_out` database and run:

```text
sql demo/mysql.sql
```

### 3. Configure runtime secrets

The repository does **not** store database passwords, JWT signing secrets, cloud credentials, or payment credentials in source control.

At minimum, set:

```bash
export DB_PASSWORD='your-mysql-password'
export JWT_ADMIN_SECRET='replace-with-a-long-random-secret'
export JWT_USER_SECRET='replace-with-a-different-long-random-secret'
```

Redis password is optional for a passwordless local Redis instance:

```bash
export REDIS_PASSWORD=''
```

Other supported variables include:

```text
DB_URL
DB_USERNAME
REDIS_HOST
REDIS_PORT
ALIOSS_ENDPOINT
ALIOSS_ACCESS_KEY_ID
ALIOSS_ACCESS_KEY_SECRET
ALIOSS_BUCKET_NAME
WECHAT_APPID
WECHAT_SECRET
WECHAT_MCHID
WECHAT_MCH_SERIAL_NO
WECHAT_PRIVATE_KEY_FILE
WECHAT_API_V3_KEY
WECHAT_PAY_CERT_FILE
WECHAT_NOTIFY_URL
WECHAT_REFUND_NOTIFY_URL
```

### 4. Build

From the repository root:

```bash
mvn clean install -DskipTests
```

### 5. Run the server

```bash
mvn -pl server spring-boot:run
```

By default the application listens on:

```text
http://localhost:8080
```

Swagger UI:

```text
http://localhost:8080/swagger-ui.html
```

## Repository scope

This repository focuses on the **Java backend implementation**. The Vue/ElementUI management client, WeChat Mini Program client, and Nginx deployment assets are architectural counterparts but are not included here.

## Security configuration

Local service credentials and JWT keys are supplied through environment variables. Private keys, certificates, local override files, build outputs, and generated reports are excluded through `.gitignore`.

The committed configuration contains only service structure and non-sensitive defaults such as localhost endpoints.
