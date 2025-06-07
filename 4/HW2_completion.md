# Кто твой предок?

[ДЗ_1 с ФТ и НФТ](https://github.com/nikolaysavelev/system-design-hse-miem-2025/pull/6/files?short_path=c2b1b13#diff-c2b1b137c8b6980993e87a6c87d68e80f5d9287d3828b50d33049d9581a3d7fc)
---

## Сервисы и архитектура

### Основные сервисы

1. **Auth Service**
   - OAuth 2.0, JWT
   - 2FA
2. **User Profile Service**
3. **Genealogy Service**
   - Граф предков/потомков
4. **Document Management Service**
   - Загрузка, S3, верификация
5. **Search & Discovery Service**
   - Elasticsearch
6. **Verification Service**
   - Подтверждение записей
7. **Notification Service**
   - Email, push, WebSocket
8. **Audit & History Service**
   - Хранение изменений
9. **Admin Panel**
   - Модерация, управление

---

## Интеграции между сервисами

| Связка                                | Тип               | Обоснование                                   |
|--------------------------------------|-------------------|-----------------------------------------------|
| Frontend ↔ Auth Service              | Синхронный REST   | Аутентификация, выдача токенов                |
| Frontend ↔ Genealogy/Search Service  | Синхронный REST   | Быстрые пользовательские запросы              |
| Document → Verification Service      | Асинхронный MQ    | Сканы → проверка вручную                     |
| Verification → Notification          | Асинхронный MQ    | Уведомление о статусе                         |
| Genealogy → Audit                    | Асинхронный       | Журналирование изменений                      |
| Admin Panel ↔ Все сервисы            | Синхронный REST   | Централизованное управление                   |

---

## High-Level Design (HLD)

### Клиенты

- Веб-клиент (React/Vue)
- Мобильные приложения (iOS/Android)
- Сторонние клиенты через API

### Входной слой

- **API Gateway** (Kong/YARP)
- **OAuth Provider / Auth Service**

### Бизнес-сервисы

- Genealogy Service
- Document Service
- Verification Service
- User Profile
- Notification Service
- Audit Service
- Search Service

### Инфраструктура

- PostgreSQL, Redis, S3
- Elasticsearch
- RabbitMQ
- OpenTelemetry, Prometheus, Grafana, Jaeger
- CDN (Yandex CDN / VK Cloud CDN)

---

## Пример сценария: добавление записи

1. Пользователь загружает скан документа.
2. `Document Service` сохраняет в S3, публикует событие.
3. `Verification Service` получает задание, два оператора проверяют.
4. После подтверждения `Genealogy Service` обновляет данные.
5. `Audit Service` фиксирует изменение.
6. `Notification Service` уведомляет пользователя.
