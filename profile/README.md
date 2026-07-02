# 🚀 NeoBank Core - Next Generation Microservices Banking Platform

Welcome to the main documentation of the **NeoBank** organization!

NeoBank is a modern, distributed, fault-tolerant, and scalable Cloud-Native digital banking platform built on a microservices architecture using cutting-edge technologies. The project demonstrates enterprise-grade development practices, the application of distributed system patterns (CQRS, Event-Driven Architecture, Database-per-Service, Circuit Breaker), bank-level security implementation, and comprehensive Observability coverage.

---

## 🌟 Detailed Business Features

Our platform covers the full lifecycle of banking services for customers and administrative operations.

### 👤 Customer Features (User & Onboarding)
- **Secure Authentication and Authorization** powered by Keycloak (OAuth2, OpenID Connect).
- **KYC (Know Your Customer) Workflow:** Document upload process, verification status tracking, and automated role assignment upon approval.
- **Profile Management:** Modifying personal data, configuring profile security.

### 💰 Account Management
- **Account Opening:** Support for multiple account types (checking, savings).
- **Balance Retrieval:** High-performance balance retrieval with Redis caching.
- **Account Status Management:** Blocking/unblocking accounts.

### 💳 Card Services
- **Card Issuance:** Virtual and physical cards.
- **Card Management:** Setting and changing PIN codes, blocking/unblocking, reissuance.
- **Limits:** Configuring daily/monthly spending limits.

### 💸 Transaction Engine
- **Money Transfers:** Transfers between own accounts, P2P transfers by phone number/account number.
- **Deposits and Withdrawals:** Integration with external gateways.
- **ACID Transactions:** Reliable transfer processing with distributed locks and consistency guarantees.
- **Statements:** Generating account statements for a selected period.

### 🔔 Notification Center
- **Omnichannel Delivery:** Sending Email, SMS, and Push notifications.
- **Asynchronous Processing:** Real-time event handling via **Apache Kafka**.
- **Notification History:** Storing and viewing all sent messages in the user's dashboard.

### 🛡 Platform Management (Admin Panel)
- **Dashboards:** Real-time system statistics.
- **KYC Management:** Manual or semi-automated validation of user documents.
- **Feature Toggles (Unleash):** Seamlessly enabling/disabling new features on the fly without restarting services.

---

## 🛠 Comprehensive Tech Stack

The architecture utilizes the latest versions of frameworks and libraries.

### ☕ Backend (Microservices)
- **Language & Platform:** Java 21
- **Framework:** Spring Boot 4.0.6
- **Cloud Infrastructure:** Spring Cloud 2025.1.1
- **API Gateway:** Spring Cloud Gateway
- **Service Discovery:** Netflix Eureka (Spring Cloud Netflix)
- **Centralized Configuration:** Spring Cloud Config Server
- **REST Clients:** OpenFeign (Spring Cloud OpenFeign) with Micrometer metrics
- **Fault Tolerance:** Resilience4j (Circuit Breaker, Rate Limiter, Retry)
- **Mapping & Code Generation:** MapStruct 1.5.5.Final, Lombok
- **API Documentation:** Springdoc OpenAPI (Swagger UI) 3.0.x

### 💾 Databases & Caching
- **Relational DBMS:** PostgreSQL (isolated schema/DB for each microservice — *Database-per-service*)
- **Database Migrations:** Flyway
- **Caching:** Redis (Spring Data Redis, Spring Boot Cache)

### 📨 Message Brokers
- **Event-Driven Architecture:** Apache Kafka
- **Zookeeper:** Kafka node coordination
- Spring Kafka (Producer / Consumer Factory)

### 🔐 Security
- **Identity Provider (IAM):** Keycloak
- Spring Security, OAuth2 Resource Server, JWT validation.
- Protection of internal inter-service calls via Internal API Keys.

### 👁‍🗨 Observability
- **Metrics:** Micrometer, Prometheus, Grafana
- **Distributed Tracing:** Zipkin (Reporter Brave), Micrometer Tracing Bridge
- **Centralized Logging (ELK Stack):**
  - Logstash Logback Encoder (JSON log generation)
  - Elasticsearch (storage)
  - Kibana (dashboards and search)

### 💻 Frontend (Web)
- **User Interface (`neobank-frontend`):** Next.js 16.2.9, React 19, Axios, React Hot Toast
- **Admin Panel (`neobank-admin-frontend`):** Next.js 16.2.9, React 19, TailwindCSS 4, Lucide React

### 🐳 DevOps & Infrastructure
- **Orchestration:** Kubernetes (K8s) (Deployment, Service, ConfigMap, Secret, StatefulSet)
- **Containerization:** Docker
- **Ingress:** NGINX Ingress Controller

---

## 🏗 Architecture and Repositories

The project is divided into individual modules/microservices. Within the GitHub Organization paradigm, each can be a separate repository.

| Service / Module | Description and Role |
|---|---|
| **`ms-api-gateway`** | Single entry point, request routing (Reverse Proxy), JWT token termination and validation, CORS. |
| **`ms-discovery-server`** | Service Registry (Eureka) for dynamic service discovery within the cluster. |
| **`config-server`** | Configuration repository. Pulls settings from `neobank-config` and distributes them to microservices. |
| **`ms-user-service`** | User management, Keycloak integration (Admin REST Client), KYC workflows. |
| **`ms-account-service`** | Accounts, balances, account blocking. |
| **`ms-card-service`** | Card issuance, linking to accounts, limits. |
| **`ms-transaction-service`** | Transaction engine ensuring ACID properties for transfers, transaction history. |
| **`ms-notification-service`** | Kafka listener (Consumer). Sends notifications (SMS/Email) upon specific system events. |
| **`ms-admin-service`** | Data aggregator for admins (BFF - Backend for Frontend). User and feature management. |
| **`neobank-frontend`** | User SPA application built on Next.js. |
| **`neobank-admin-frontend`** | Bank administrator interface (Next.js + Tailwind). |
| **`neobank-config`** | Git repository containing YML configs for Spring Cloud Config. |
| **`neobank-infra` / `k8s`** | K8s manifests for deploying the entire infrastructure and services (Infrastructure as Code). |

---

## 🚀 Local Deployment (Quick Start)

To deploy the entire NeoBank cluster locally, follow these steps:

### 1. Environment Requirements
- **Docker Desktop** (with Kubernetes enabled or Minikube)
- **kubectl**
- **git** utility
- Java 21 and Maven (for local builds)
- Node.js 20+ (for frontend)

### 2. Cloning and Configuring Secrets
Clone the configuration repository:
```bash
git clone https://github.com/YourOrganization/NeoBank-Core.git
cd NeoBank-Core
```

**CRITICAL — Secrets Configuration:**
All database passwords, tokens, and encryption keys are stored in K8s secrets.
1. Navigate to the `k8s/` folder.
2. Create a secrets file based on the template: 
   ```bash
   cp infra-secrets.example.yaml infra-secrets.yaml
   ```
3. Open `infra-secrets.yaml` and replace the placeholder values (e.g., `eW91cl9wYXNzd29yZA==`) with real strings encoded in Base64 (`echo -n "my_password" | base64`). The file is added to `.gitignore` for security.

### 3. Infrastructure Deployment
Apply the manifests in the correct order.
```bash
# 1. Apply secrets
kubectl apply -f k8s/infra-secrets.yaml

# 2. Start databases (PostgreSQL)
kubectl apply -f k8s/infra-databases.yaml

# 3. Start Kafka (Event Streaming)
kubectl apply -f k8s/infra-kafka.yaml

# 4. Start Monitoring Systems (ELK + Prometheus + Grafana)
kubectl apply -f k8s/infra-observability.yaml

# 5. Apply pre-configured Grafana dashboards
kubectl apply -f k8s/observability-configmaps.yaml
```
Ensure all infrastructure pods transition to the `Running` state:
```bash
kubectl get pods -w
```

### 4. Configuring Local Domains (Hosts)
For Ingress and cookies to work correctly, add the following entries to your `hosts` file:
- **Windows:** `C:\Windows\System32\drivers\etc\hosts`
- **Linux/Mac:** `/etc/hosts`

```text
127.0.0.1   keycloak.neobank.local
127.0.0.1   unleash.neobank.local
127.0.0.1   grafana.neobank.local
127.0.0.1   kibana.neobank.local
127.0.0.1   api.neobank.local
```
*(If using minikube, replace 127.0.0.1 with the IP from the `minikube ip` command)*.

### 5. Microservices Deployment
You can run the microservices via your IDE (IntelliJ IDEA) using the `dev` profile or deploy them to Kubernetes using the provided PowerShell script:
```powershell
./deploy-all.ps1
```

### 6. Starting Frontend Applications
In separate terminals, start the UI:
```bash
# User Portal
cd neobank-frontend
npm install
npm run dev

# Admin Panel
cd ../neobank-admin-frontend
npm install
npm run dev
```

### 7. Access Points (Endpoints)
- **Client Frontend:** http://localhost:3000
- **Admin Frontend:** http://localhost:3001
- **API Gateway:** http://api.neobank.local
- **Keycloak IAM:** http://keycloak.neobank.local
- **Unleash Feature Toggles:** http://unleash.neobank.local
- **Grafana (Metrics):** http://grafana.neobank.local
- **Kibana (Logs):** http://kibana.neobank.local

---

## 🤝 Contributing
We welcome Pull Requests! Before you start developing, please:
1. Familiarize yourself with the architectural style (we enforce strict CQRS at the transaction level).
2. Use MapStruct for DTO mapping.
3. Cover new functionality with logs (slf4j) for ELK and custom Micrometer metrics.

---
*Powered by Spring Boot 4 & Kubernetes | Designed for Enterprise Scale.*
