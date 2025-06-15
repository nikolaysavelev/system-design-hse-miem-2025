# Интеграции
## 1. Диаграмма
![sysdesign_hw2 drawio (5)](https://github.com/user-attachments/assets/6befa048-d443-4a17-a7b0-cff099b9809f)



## 2. Обоснование интеграций
### Внешние интеграции

| Интеграция                      | Тип                             | Обоснование                                      |
| ---------------------------------- | ------------------------------- | ------------------------------------------------ |
| Web GUI / Mobile App → API Gateway | Sync (HTTP/REST)                | Стандартное взаимодействие клиентских приложений |
| Administration Panel → API Gateway | Sync (HTTP/REST)                | Управление данными в системе                     |
| API Gateway → Keycloak             | Sync (Token validation, OAuth2) | Аутентификация пользователей                     |

---

### Интеграции внутри микросервисов

| Интеграция                                  | Тип                      | Обоснование                                                      |
| ---------------------------------------------- | ------------------------ | ---------------------------------------------------------------- |
| API Gateway → User Service                     | Sync (HTTP/REST)         | CRUD пользователей                                               |
| API Gateway → Event Service                    | Sync (HTTP/REST)         | CRUD событий                                                     |
| API Gateway → Artist Service                   | Sync (HTTP/REST)         | CRUD артистов                                                    |
| API Gateway → Ticket Service                   | Sync (HTTP/REST)         | CRUD билетов                                                     |
| API Gateway → Booking Service                  | Sync (HTTP/REST)         | Покупка билетов в реальном времени                               |
| Booking Service → Ticket Service               | Sync (HTTP/REST или RPC) | Проверка и резервирование мест при покупке                       |
| Booking Service → User Service                 | Sync (HTTP/REST)         | Получение данных пользователя при бронировании                   |
| Event Service → Artist Service                 | Sync (HTTP/REST)         | Подвязка артиста к событию                                       |
| Notification Service → Email/SMS/PUSH Gateways | Sync (API)               | Отправка уведомлений                                             |

---

### Интеграции с внешними сервисами

| Интеграция                 | Тип             | Обоснование                   |
| ----------------------------- | --------------- | ----------------------------- |
| Artist Service → Yandex Music | Sync (API)      | Получение дискографии артиста |
| Event Service → Yandex Maps   | Sync (API)      | Получение геолокаций площадок |
| Booking Service → ЮKassa      | Sync (API)      | Проведение платежа            |
| ЮKassa → Booking Service      | Async (Webhook) | Подтверждение платежа. Webhook для стабилизации сессии на весь процесс оплаты         |

---

### Асинхронные интеграции

| Интеграция                        | Тип           | Обоснование                                   |
| ------------------------------------ | ------------- | --------------------------------------------- |
| Event Service → Kafka → Yandex Music | Async (Kafka) | Передача событий в Яндекс Музыку для отображения на странице артиста. Асинхронная интеграция выбрана в пользу масштабирования, ретраев в случае ошибки (так как сервис внешний и он вне поля ответственности системы)|
| Ticket Service → Kafka → Notification Service | Async (Kafka) | Отправка уведомлений пользователю (чек с билетом, напоминание о событии и пр.). Асинхронная интеграция так как отправка уведомления это побочное событие покупки билета. Нельзя блокировать им проесс покупки|

