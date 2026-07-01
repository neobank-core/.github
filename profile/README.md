# 🚀 NeoBank Core - Next Generation Microservices Banking Platform

Добро пожаловать в главную документацию организации **NeoBank**! 

NeoBank — это современная, распределенная, отказоустойчивая и масштабируемая Cloud-Native платформа цифрового банкинга, построенная на базе микросервисной архитектуры с использованием самых передовых технологий. Проект демонстрирует эталонные практики Enterprise-разработки, применения паттернов распределенных систем (CQRS, Event-Driven Architecture, Database-per-Service, Circuit Breaker), обеспечения безопасности уровня банковских систем и полного покрытия Observability.

---

## 🌟 Детальное описание бизнес-возможностей (Features)

Наша платформа охватывает полный жизненный цикл банковского обслуживания клиентов и работы администраторов.

### 👤 Клиентский функционал (User & Onboarding)
- **Безопасная аутентификация и авторизация** на базе Keycloak (OAuth2, OpenID Connect).
- **KYC (Know Your Customer) Workflow:** Процесс загрузки документов, проверка статусов верификации и автоматическая привязка ролей после одобрения.
- **Управление профилем:** Изменение персональных данных, настройка безопасности профиля.

### 💰 Управление счетами (Account Management)
- **Открытие счетов:** Поддержка множества счетов (текущие, сберегательные).
- **Просмотр баланса:** Высокопроизводительное получение балансов с кэшированием в Redis.
- **Управление статусами счетов:** Блокировка/разблокировка счетов.

### 💳 Карточные продукты (Card Services)
- **Эмиссия карт:** Виртуальные и физические карты.
- **Управление картами:** Установка и изменение PIN-кода, блокировка/разблокировка, перевыпуск.
- **Лимиты:** Настройка дневных/месячных лимитов на траты.

### 💸 Транзакционное ядро (Transaction Engine)
- **Денежные переводы:** Переводы между своими счетами, P2P переводы по номеру телефона/счета.
- **Пополнения и снятия:** Интеграция с внешними шлюзами.
- **ACID транзакции:** Надежная обработка переводов с распределенными блокировками и гарантиями консистентности.
- **Выписки (Statements):** Формирование выписок по счету за выбранный период.

### 🔔 Уведомления (Notification Center)
- **Омниканальность:** Отправка Email, SMS, и Push-уведомлений.
- **Асинхронность:** Обработка событий в реальном времени через **Apache Kafka**.
- **История уведомлений:** Хранение и просмотр всех отправленных сообщений в личном кабинете.

### 🛡 Управление платформой (Admin Panel)
- **Дашборды:** Статистика системы в реальном времени.
- **Управление KYC:** Ручная или полуавтоматическая валидация документов пользователей.
- **Feature Toggles (Unleash):** Бесшовное включение/отключение нового функционала на лету без перезапуска сервисов.

---

## 🛠 Исчерпывающий стек технологий (Tech Stack)

В архитектуре используются последние версии фреймворков и библиотек.

### ☕ Backend (Microservices)
- **Язык и платформа:** Java 21
- **Фреймворк:** Spring Boot 4.0.6
- **Облачная инфраструктура:** Spring Cloud 2025.1.1
- **API Gateway:** Spring Cloud Gateway
- **Service Discovery:** Netflix Eureka (Spring Cloud Netflix)
- **Централизованная конфигурация:** Spring Cloud Config Server
- **REST Клиенты:** OpenFeign (Spring Cloud OpenFeign) с Micrometer метриками
- **Отказоустойчивость:** Resilience4j (Circuit Breaker, Rate Limiter, Retry)
- **Маппинг и кодогенерация:** MapStruct 1.5.5.Final, Lombok
- **Документация API:** Springdoc OpenAPI (Swagger UI) 3.0.x

### 💾 Базы данных и Кэширование (Databases & Caching)
- **Реляционная СУБД:** PostgreSQL (изолированная схема/БД для каждого микросервиса — *Database-per-service*)
- **Миграции БД:** Flyway
- **Кэширование:** Redis (Spring Data Redis, Spring Boot Cache)

### 📨 Брокеры сообщений (Message Brokers)
- **Событийная архитектура:** Apache Kafka
- **Zookeeper:** Координация нод Kafka
- Spring Kafka (Producer / Consumer Factory)

### 🔐 Безопасность (Security)
- **Identity Provider (IAM):** Keycloak
- Spring Security, OAuth2 Resource Server, JWT валидация.
- Защита внутренних межсервисных вызовов через Internal API Keys.

### 👁‍🗨 Наблюдаемость (Observability)
- **Метрики:** Micrometer, Prometheus, Grafana
- **Распределенная трассировка (Distributed Tracing):** Zipkin (Reporter Brave), Micrometer Tracing Bridge
- **Централизованное логирование (ELK Stack):**
  - Logstash Logback Encoder (генерация логов в JSON)
  - Elasticsearch (хранение)
  - Kibana (дашборды и поиск)

### 💻 Frontend (Web)
- **Пользовательский интерфейс (`neobank-frontend`):** Next.js 16.2.9, React 19, Axios, React Hot Toast
- **Админ панель (`neobank-admin-frontend`):** Next.js 16.2.9, React 19, TailwindCSS 4, Lucide React

### 🐳 DevOps & Инфраструктура
- **Оркестрация:** Kubernetes (K8s) (Deployment, Service, ConfigMap, Secret, StatefulSet)
- **Контейнеризация:** Docker
- **Ingress:** NGINX Ingress Controller

---

## 🏗 Архитектура и репозитории

Проект разделен на отдельные модули/микросервисы. В парадигме GitHub Organization каждый из них может быть отдельным репозиторием.

| Сервис / Модуль | Описание и Роль |
|---|---|
| **`ms-api-gateway`** | Единая точка входа, маршрутизация запросов (Reverse Proxy), терминация и проверка JWT токенов, CORS. |
| **`ms-discovery-server`** | Service Registry (Eureka) для динамического обнаружения сервисов внутри кластера. |
| **`config-server`** | Хранилище конфигураций. Вытягивает настройки из `neobank-config` и раздает микросервисам. |
| **`ms-user-service`** | Управление пользователями, интеграция с Keycloak (Admin REST Client), KYC-флоу. |
| **`ms-account-service`** | Счета, балансы, блокировки счетов. |
| **`ms-card-service`** | Эмиссия карт, привязка к счетам, лимиты. |
| **`ms-transaction-service`** | Транзакционное ядро, гарантирующее ACID при переводах, история транзакций. |
| **`ms-notification-service`** | Слушатель Kafka (Consumer). Рассылает уведомления (SMS/Email) при определенных событиях в системе. |
| **`ms-admin-service`** | Агрегатор данных для админов (BFF - Backend for Frontend). Управление пользователями и фичами. |
| **`neobank-frontend`** | Пользовательское SPA приложение на Next.js. |
| **`neobank-admin-frontend`** | Интерфейс администратора банка (Next.js + Tailwind). |
| **`neobank-config`** | Git-репозиторий с YML-конфигами для Spring Cloud Config. |
| **`neobank-infra` / `k8s`** | K8s манифесты для деплоя всей инфраструктуры и сервисов (Infrastructure as Code). |

---

## 🚀 Как запустить платформу локально (Quick Start)

Чтобы развернуть весь кластер NeoBank локально, выполните шаги:

### 1. Требования к окружению
- **Docker Desktop** (с включенным Kubernetes или Minikube)
- **kubectl**
- Утилита **git**
- Java 21 и Maven (для локальной сборки)
- Node.js 20+ (для фронтенда)

### 2. Клонирование и настройка секретов
Склонируйте конфигурационный репозиторий:
```bash
git clone https://github.com/YourOrganization/NeoBank-Core.git
cd NeoBank-Core
```

**ОЧЕНЬ ВАЖНО — Настройка секретов:**
Все пароли от баз данных, токены и ключи шифрования хранятся в секретах K8s.
1. Перейдите в папку `k8s/`.
2. Создайте файл секретов на основе шаблона: 
   ```bash
   cp infra-secrets.example.yaml infra-secrets.yaml
   ```
3. Откройте `infra-secrets.yaml` и замените значения-заглушки (вида `eW91cl9wYXNzd29yZA==`) на реальные строки в формате Base64 (`echo -n "my_password" | base64`). Файл добавлен в `.gitignore` для безопасности.

### 3. Развертывание Инфраструктуры
Примените манифесты в правильном порядке.
```bash
# 1. Применяем секреты
kubectl apply -f k8s/infra-secrets.yaml

# 2. Запускаем базы данных (PostgreSQL)
kubectl apply -f k8s/infra-databases.yaml

# 3. Запускаем Kafka (Event Streaming)
kubectl apply -f k8s/infra-kafka.yaml

# 4. Запускаем Системы Мониторинга (ELK + Prometheus + Grafana)
kubectl apply -f k8s/infra-observability.yaml

# 5. Применяем преднастроенные дашборды для Grafana
kubectl apply -f k8s/observability-configmaps.yaml
```
Убедитесь, что все инфраструктурные поды перешли в статус `Running`:
```bash
kubectl get pods -w
```

### 4. Настройка локальных доменов (Hosts)
Для корректной работы Ingress и куков добавьте записи в файл `hosts`:
- **Windows:** `C:\Windows\System32\drivers\etc\hosts`
- **Linux/Mac:** `/etc/hosts`

```text
127.0.0.1   keycloak.neobank.local
127.0.0.1   unleash.neobank.local
127.0.0.1   grafana.neobank.local
127.0.0.1   kibana.neobank.local
127.0.0.1   api.neobank.local
```
*(Если используете minikube, то вместо 127.0.0.1 укажите IP из команды `minikube ip`)*.

### 5. Деплой Микросервисов
Вы можете запустить микросервисы через IDE (IntelliJ IDEA) с профилем `dev` или задеплоить их в Kubernetes с помощью предоставленного PowerShell скрипта:
```powershell
./deploy-all.ps1
```

### 6. Запуск Фронтенд-приложений
В отдельных терминалах запустите UI:
```bash
# Пользовательский портал
cd neobank-frontend
npm install
npm run dev

# Админ-панель
cd ../neobank-admin-frontend
npm install
npm run dev
```

### 7. Точки доступа (Endpoints)
- **Client Frontend:** http://localhost:3000
- **Admin Frontend:** http://localhost:3001
- **API Gateway:** http://api.neobank.local
- **Keycloak IAM:** http://keycloak.neobank.local
- **Unleash Feature Toggles:** http://unleash.neobank.local
- **Grafana (Метрики):** http://grafana.neobank.local
- **Kibana (Логи):** http://kibana.neobank.local

---

## 🤝 Контрибьютинг (Contributing)
Мы приветствуем Pull Requests! Прежде чем начать разработку, пожалуйста:
1. Ознакомьтесь с архитектурным стилем (у нас строгий CQRS на уровне транзакций).
2. Используйте MapStruct для маппинга DTO.
3. Покрывайте новый функционал логами (slf4j) для ELK и кастомными метриками Micrometer.

---
*Powered by Spring Boot 4 & Kubernetes | Designed for Enterprise Scale.*
