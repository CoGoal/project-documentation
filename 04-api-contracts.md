# CoGoal API — справочник эндпоинтов

## Содержание

1. [Путь пользователя](#1-путь-пользователя)
2. [Общие правила API](#2-общие-правила-api)
3. [Эндпоинты по разделам](#3-эндпоинты-по-разделам)
4. [Схемы данных](#4-схемы-данных)

---

## 1. Путь пользователя

Порядок, в котором эндпоинты вызываются в основном сценарии.

### 1.1 Регистрация и вход

Пользователь создаёт аккаунт, получает JWT и стартовые монеты. Токен кладётся в заголовок каждого запроса.

| Метод | Путь |
|---|---|
| `POST` | `/auth/register` |
| `POST` | `/auth/login` |
| `POST` | `/auth/refresh` |

### 1.2 Создание цели

Фронт подгружает категории и фонды для формы, затем отправляет цель с этапами и суммой залога. Вместе с целью создаётся пакт в статусе `FORMING`.

| Метод | Путь |
|---|---|
| `GET` | `/categories` |
| `GET` | `/charities` |
| `POST` | `/goals` |

### 1.3 Поиск напарника

Либо автор приглашает человека по username, либо кто-то находит цель в ленте и откликается.

| Метод | Путь |
|---|---|
| `POST` | `/goals/{goalId}/invitations` |
| `GET` | `/goals` |
| `POST` | `/goals/{goalId}/join-requests` |

### 1.4 Ответ на приглашение

Тот, от кого ждут решения, принимает или отклоняет. После принятия пакт ждёт залогов (`AWAITING_DEPOSITS`). Без ответа 48 часов — автоотклонение.

| Метод | Путь |
|---|---|
| `GET` | `/invitations` |
| `POST` | `/invitations/{invitationId}/accept` |
| `POST` | `/invitations/{invitationId}/decline` |

### 1.5 Внесение залога

Каждый участник оплачивает залог на странице платёжного провайдера. Результат приходит вебхуком. Когда оплатили все — пакт становится `ACTIVE`.

| Метод | Путь |
|---|---|
| `POST` | `/pacts/{pactId}/deposits/me/payment` |
| `POST` | `/webhooks/payment-provider` |
| `GET` | `/pacts/{pactId}/deposits/me` |

### 1.6 Отчёты и проверка

До дедлайна этапа участник загружает доказательства, напарник принимает или отклоняет с причиной. До 3 попыток, авто-принятие через 48 часов.

| Метод | Путь |
|---|---|
| `POST` | `/files` |
| `POST` | `/pacts/{pactId}/checkins` |
| `POST` | `/checkins/{checkinId}/reviews` |

### 1.7 Завершение

Фоновая задача проверяет дедлайны. Успех — возврат залога и монеты, провал — перечисление в фонд. Всё видно в истории операций, монеты тратятся в магазине.

| Метод | Путь |
|---|---|
| `GET` | `/pacts/{pactId}` |
| `GET` | `/users/me/operations` |
| `POST` | `/shop/items/{itemId}/purchase` |

---

## 2. Общие правила API

REST API сервиса взаимной подотчётности CoGoal.

- Все пути начинаются с `/api/v1`.
- Авторизация — JWT в заголовке `Authorization: Bearer <accessToken>` (backend stateless).
- Даты — ISO 8601 в UTC (`2026-10-01T12:00:00Z`).
- Идентификаторы сущностей (`id`, `goalId`, `pactId` и другие ID) имеют тип UUID в соответствии с ERD.
- Деньги — строка-десятичное число в рублях (`"500.00"`), чтобы не терять копейки на float.
- Ошибки всегда возвращаются в формате `Error` с сообщением на русском (требование 2.5.2).
- Списки возвращаются постранично в формате `{ items, page, size, totalItems, totalPages }`.
- Операции, связанные с деньгами, принимают заголовок `Idempotency-Key` (требования 1.1.19, 2.2.7).
- Query-параметры GET-запросов и тела запросов POST/PUT/PATCH перечислены в разделе 3. Для DELETE тело запроса не используется.
- Коллекции поддерживают `search` и пагинацию там, где это указано в контракте; `search` не применяется к запросам, возвращающим один ресурс.

---

## 3. Эндпоинты по разделам

Все пути в таблицах указаны относительно `/api/v1`. Вход разделён на path-параметры, query-параметры и тело запроса. Если тело запроса обозначено как `EmptyRequest`, клиент отправляет пустой JSON-объект `{}`. Для `DELETE` тело запроса не используется.

Для списков поддерживаются общие query-параметры `page` (номер страницы, начиная с 1; по умолчанию `1`), `size` (размер страницы; по умолчанию `20`, максимум `100`) и `sort` (поле и направление, например `createdAt,desc`), если они указаны в таблице. `search` — строка поиска без учёта регистра. Все фильтры необязательные, если не указано иное.

#### Типы query-параметров

| Параметр | Тип | Назначение |
|---|---|---|
| `search` | string | Поиск по текстовым полям ресурса: названию/описанию цели, username, тексту сообщения, теме обращения или названию предмета — в зависимости от endpoint |
| `page` | integer | Номер страницы, начиная с 1 |
| `size` | integer | Количество записей на странице; максимум 100 |
| `sort` | string | Сортировка в формате `field,asc` или `field,desc` |
| `status` | string / enum | Фильтр по статусу соответствующего ресурса |
| `type` | string / enum | Фильтр по типу операции |
| `kind` | string | Тип приглашения: `INVITE` или `JOIN_REQUEST` |
| `categoryId`, `pactId`, `goalId`, `userId`, `milestoneId`, `participantId` | UUID | Фильтры по связанным сущностям |
| `deadlineFrom`, `deadlineTo`, `dateFrom`, `dateTo`, `createdFrom`, `createdTo`, `submittedFrom`, `submittedTo` | string (date-time) | Нижняя и верхняя границы диапазона дат/времени в ISO 8601 UTC |
| `minDeposit`, `maxDeposit` | Money | Нижняя и верхняя границы суммы залога |
| `minPrice`, `maxPrice` | integer | Нижняя и верхняя границы цены предмета в монетах |
| `itemType` | `ShopItemType` | Тип предмета магазина |
| `owned` | boolean | Фильтр предметов по факту владения пользователем |
| `currency` | string | `RUB` или `COINS` |

### 3.1 Авторизация — Auth

| Наименование | Метод и путь | Вход | Выход |
|---|---|---|---|
| Регистрация пользователя | `POST /auth/register` | Body: `RegisterRequest` | `201 Created`: `AuthResponse` |
| Вход пользователя | `POST /auth/login` | Body: `LoginRequest` | `200 OK`: `AuthResponse` |
| Обновление токена | `POST /auth/refresh` | Body: `RefreshTokenRequest` | `200 OK`: `AuthResponse` |
| Выход пользователя | `POST /auth/logout` | Body: `LogoutRequest` | `204 No Content` |
| Запрос сброса пароля | `POST /auth/password/forgot` | Body: `ForgotPasswordRequest` | `202 Accepted`: `{ message }` |
| Установка нового пароля | `POST /auth/password/reset` | Body: `ResetPasswordRequest` | `204 No Content` |
| Подтверждение email | `POST /auth/email/confirm` | Body: `ConfirmEmailRequest` | `200 OK`: `MyProfile` |

### 3.2 Профиль — Profile

| Наименование | Метод и путь | Вход | Выход |
|---|---|---|---|
| Просмотр своего профиля | `GET /users/me` | Авторизованный пользователь; query-параметры не нужны | `200 OK`: `MyProfile` |
| Редактирование профиля | `PATCH /users/me` | Body: `UpdateProfileRequest` | `200 OK`: `MyProfile` |
| Запрос смены email | `POST /users/me/email-change` | Body: `ChangeEmailRequest` | `202 Accepted`: `{ message }` |
| Применение купленного предмета | `PUT /users/me/active-item` | Body: `ApplyShopItemRequest` | `200 OK`: `MyProfile` |
| Просмотр публичного профиля | `GET /users/{username}` | Path: `username` | `200 OK`: `PublicProfile` |

### 3.3 История операций — Operations

| Наименование | Метод и путь | Вход | Выход |
|---|---|---|---|
| Получение истории операций | `GET /users/me/operations` | Query: `search`, `type`, `status`, `currency`, `pactId`, `dateFrom`, `dateTo`, `page`, `size`, `sort` | `200 OK`: `PageResponse<Operation>` |
| Экспорт истории операций в PDF | `GET /users/me/operations/export` | Query: `search`, `type`, `status`, `currency`, `pactId`, `dateFrom`, `dateTo` | `200 OK`: файл `application/pdf` |

### 3.4 Справочники — Dictionaries

| Наименование | Метод и путь | Вход | Выход |
|---|---|---|---|
| Поиск и получение категорий | `GET /categories` | Query: `search`, `page`, `size`, `sort` | `200 OK`: `PageResponse<Category>` |
| Поиск и получение благотворительных фондов | `GET /charities` | Query: `search`, `page`, `size`, `sort` | `200 OK`: `PageResponse<Charity>` |

### 3.5 Цели — Goals

| Наименование | Метод и путь | Вход | Выход |
|---|---|---|---|
| Поиск целей в публичной ленте | `GET /goals` | Query: `search`, `categoryId`, `deadlineFrom`, `deadlineTo`, `minDeposit`, `maxDeposit`, `status`, `visibility`, `page`, `size`, `sort` | `200 OK`: `PageResponse<GoalCard>` |
| Создание цели и связанного пакта | `POST /goals` | Body: `CreateGoalRequest` | `201 Created`: `Goal` |
| Получение своих целей | `GET /goals/my` | Query: `search`, `categoryId`, `status`, `deadlineFrom`, `deadlineTo`, `page`, `size`, `sort` | `200 OK`: `PageResponse<GoalCard>` |
| Получение деталей цели | `GET /goals/{goalId}` | Path: `goalId` | `200 OK`: `Goal` |
| Редактирование цели | `PATCH /goals/{goalId}` | Path: `goalId`; Body: `UpdateGoalRequest` | `200 OK`: `Goal` |
| Удаление цели | `DELETE /goals/{goalId}` | Path: `goalId`; тело отсутствует | `204 No Content` |
| Добавление этапа к цели | `POST /goals/{goalId}/milestones` | Path: `goalId`; Body: `MilestoneInput` | `201 Created`: `Milestone` |
| Изменение этапа цели | `PATCH /goals/{goalId}/milestones/{milestoneId}` | Path: `goalId`, `milestoneId`; Body: `UpdateMilestoneRequest` | `200 OK`: `Milestone` |
| Удаление этапа цели | `DELETE /goals/{goalId}/milestones/{milestoneId}` | Path: `goalId`, `milestoneId`; тело отсутствует | `204 No Content` |

### 3.6 Приглашения и отклики — Invitations

| Наименование | Метод и путь | Вход | Выход |
|---|---|---|---|
| Приглашение пользователя по username | `POST /goals/{goalId}/invitations` | Path: `goalId`; Body: `CreateInvitationRequest` | `201 Created`: `Invitation` |
| Отклик на цель из публичной ленты | `POST /goals/{goalId}/join-requests` | Path: `goalId`; Body: `CreateJoinRequest` | `201 Created`: `Invitation` |
| Поиск и получение своих приглашений и откликов | `GET /invitations` | Query: `search`, `kind`, `status`, `createdFrom`, `createdTo`, `page`, `size`, `sort` | `200 OK`: `PageResponse<Invitation>` |
| Получение деталей приглашения | `GET /invitations/{invitationId}` | Path: `invitationId` | `200 OK`: `Invitation` |
| Принятие приглашения или отклика | `POST /invitations/{invitationId}/accept` | Path: `invitationId`; Body: `EmptyRequest` (`{}`) | `200 OK`: `Invitation` |
| Отклонение приглашения или отклика | `POST /invitations/{invitationId}/decline` | Path: `invitationId`; Body: `InvitationDecisionRequest` | `200 OK`: `Invitation` |
| Отзыв своего приглашения или отклика | `POST /invitations/{invitationId}/cancel` | Path: `invitationId`; Body: `EmptyRequest` (`{}`) | `200 OK`: `Invitation` |
| Публикация цели в общей ленте | `POST /goals/{goalId}/publish` | Path: `goalId`; Body: `EmptyRequest` (`{}`) | `200 OK`: `GoalCard` |

### 3.7 Пакты — Pacts

| Наименование | Метод и путь | Вход | Выход |
|---|---|---|---|
| Поиск и получение моих пактов | `GET /pacts` | Query: `search`, `status`, `createdFrom`, `createdTo`, `page`, `size`, `sort` | `200 OK`: `PageResponse<PactSummary>` |
| Получение деталей пакта | `GET /pacts/{pactId}` | Path: `pactId` | `200 OK`: `PactDetails` |
| Голосование за досрочное расторжение | `POST /pacts/{pactId}/termination-votes` | Path: `pactId`; Body: `EmptyRequest` (`{}`) | `200 OK`: `TerminationState` |
| Отзыв голоса за досрочное расторжение | `DELETE /pacts/{pactId}/termination-votes` | Path: `pactId`; тело отсутствует | `204 No Content` |

### 3.8 Залоги и платежи — Deposits

| Наименование | Метод и путь | Вход | Выход |
|---|---|---|---|
| Получение статуса своего залога | `GET /pacts/{pactId}/deposits/me` | Path: `pactId` | `200 OK`: `Deposit` |
| Инициация оплаты залога | `POST /pacts/{pactId}/deposits/me/payment` | Path: `pactId`; Body: `CreatePaymentRequest`; Header: `Idempotency-Key` | `201 Created`: `PaymentInitResponse` |
| Получение уведомления от платёжного провайдера | `POST /webhooks/payment-provider` | Body: `PaymentProviderWebhook`; проверка подписи/подлинности провайдера, без пользовательского JWT | `200 OK`: `WebhookAcknowledgement` |

### 3.9 Отчёты — CheckIns

| Наименование | Метод и путь | Вход | Выход |
|---|---|---|---|
| Поиск отчётов пакта | `GET /pacts/{pactId}/checkins` | Path: `pactId`; Query: `search`, `milestoneId`, `participantId`, `status`, `submittedFrom`, `submittedTo`, `page`, `size`, `sort` | `200 OK`: `PageResponse<CheckIn>` |
| Отправка отчёта с доказательствами | `POST /pacts/{pactId}/checkins` | Path: `pactId`; Body: `CreateCheckInRequest` | `201 Created`: `CheckIn` |
| Получение деталей отчёта | `GET /checkins/{checkinId}` | Path: `checkinId` | `200 OK`: `CheckIn` |
| Проверка отчёта напарника | `POST /checkins/{checkinId}/reviews` | Path: `checkinId`; Body: `CreateReviewRequest` | `201 Created`: `Review` |

### 3.10 Файлы — Files

| Наименование | Метод и путь | Вход | Выход |
|---|---|---|---|
| Загрузка файла | `POST /files` | Body: `multipart/form-data`, схема `FileUploadRequest` | `201 Created`: `FileUploadResponse` |

### 3.11 Чат — Chat

| Наименование | Метод и путь | Вход | Выход |
|---|---|---|---|
| Поиск и получение истории сообщений пакта | `GET /pacts/{pactId}/messages` | Path: `pactId`; Query: `search`, `createdFrom`, `createdTo`, `page`, `size`, `sort` | `200 OK`: `PageResponse<Message>` |
| Отправка сообщения в чат | `POST /pacts/{pactId}/messages` | Path: `pactId`; Body: `CreateMessageRequest` | `201 Created`: `Message` |

### 3.12 Магазин — Shop

| Наименование | Метод и путь | Вход | Выход |
|---|---|---|---|
| Поиск предметов в магазине | `GET /shop/items` | Query: `search`, `itemType`, `minPrice`, `maxPrice`, `owned`, `page`, `size`, `sort` | `200 OK`: `PageResponse<ShopItem>` |
| Покупка предмета за монеты | `POST /shop/items/{itemId}/purchase` | Path: `itemId`; Body: `EmptyRequest` (`{}`); Header: `Idempotency-Key` | `201 Created`: `Purchase` |
| Поиск предметов в инвентаре пользователя | `GET /users/me/inventory` | Query: `search`, `itemType`, `page`, `size`, `sort` | `200 OK`: `PageResponse<ShopItem>` |

### 3.13 Техподдержка — Support

| Наименование | Метод и путь | Вход | Выход |
|---|---|---|---|
| Поиск своих обращений | `GET /support/tickets` | Query: `search`, `status`, `pactId`, `createdFrom`, `createdTo`, `page`, `size`, `sort` | `200 OK`: `PageResponse<SupportTicket>` |
| Создание обращения в поддержку | `POST /support/tickets` | Body: `CreateTicketRequest` | `201 Created`: `SupportTicket` |
| Получение деталей обращения | `GET /support/tickets/{ticketId}` | Path: `ticketId` | `200 OK`: `SupportTicket` |

### 3.14 Администрирование — Admin

| Наименование | Метод и путь | Вход | Выход |
|---|---|---|---|
| Поиск обращений для поддержки | `GET /admin/support/tickets` | Query: `search`, `status`, `userId`, `createdFrom`, `createdTo`, `page`, `size`, `sort` | `200 OK`: `PageResponse<SupportTicket>` |
| Изменение статуса обращения | `PATCH /admin/support/tickets/{ticketId}` | Path: `ticketId`; Body: `UpdateSupportTicketRequest` | `200 OK`: `SupportTicket` |
| Поиск финансовых операций с ошибками | `GET /admin/transactions` | Query: `search`, `type`, `status`, `userId`, `pactId`, `createdFrom`, `createdTo`, `page`, `size`, `sort` | `200 OK`: `PageResponse<Transaction>` |
| Повтор неуспешной финансовой операции | `POST /admin/transactions/{transactionId}/retry` | Path: `transactionId`; Body: `RetryTransactionRequest`; Header: `Idempotency-Key` | `202 Accepted`: `Transaction` |

---
## 4. Схемы данных

Объекты, которые передаются в запросах и ответах. Звёздочка (`*`) — обязательное поле.

### Error

Единый формат ошибки.

| Поле | Тип | Описание |
|---|---|---|
| `code*` | string | Машинный код для фронта |
| `message*` | string | Текст для пользователя на русском |
| `details` | массив object | Ошибки по конкретным полям формы |

### PageMeta

| Поле | Тип |
|---|---|
| `page` | integer |
| `size` | integer |
| `totalItems` | integer |
| `totalPages` | integer |

### Money

Сумма в рублях, строкой с 2 знаками после точки. Тип: `string`.

### UserShort

Минимум данных о пользователе для карточек и списков.

| Поле | Тип | Описание |
|---|---|---|
| `id` | UUID | |
| `username` | string | |
| `name` | string | |
| `avatarUrl` | string | может быть `null` |

### RegisterRequest

| Поле | Тип | Описание |
|---|---|---|
| `email*` | string (email) | |
| `username*` | string | шаблон: `^[a-zA-Z0-9_]{3,32}$` |
| `name*` | string | макс. длина: 100 |
| `password*` | string | мин. длина: 8 |

### LoginRequest

| Поле | Тип |
|---|---|
| `email*` | string (email) |
| `password*` | string |

### AuthResponse

| Поле | Тип | Описание |
|---|---|---|
| `accessToken` | string | JWT, живёт ~15 минут |
| `refreshToken` | string | Живёт ~30 дней |
| `expiresIn` | integer | Секунд до истечения access-токена |
| `user` | MyProfile | |

### MyProfile

Приватный профиль — только для самого пользователя.

| Поле | Тип | Описание |
|---|---|---|
| `id` | UUID | |
| `username` | string | |
| `email` | string (email) | |
| `name` | string | |
| `avatarUrl` | string | может быть `null` |
| `activeItem` | ShopItem | |
| `coins` | integer | |
| `role` | string | `USER` \| `ADMIN` |
| `achievements` | массив Achievement | |
| `stats` | UserStats | |
| `createdAt` | string (date-time) | |

### PublicProfile

Публичный профиль. Без email, баланса и платёжных данных.

| Поле | Тип | Описание |
|---|---|---|
| `username` | string | |
| `name` | string | |
| `avatarUrl` | string | может быть `null` |
| `activeItem` | ShopItem | |
| `achievements` | массив Achievement | |
| `stats` | UserStats | |
| `memberSince` | string (date) | |

### UserStats

| Поле | Тип |
|---|---|
| `completedGoals` | integer |
| `activePacts` | integer |
| `failedGoals` | integer |

### Achievement

| Поле | Тип |
|---|---|
| `code` | string |
| `title` | string |
| `receivedAt` | string (date-time) |

### UpdateProfileRequest

| Поле | Тип | Описание |
|---|---|---|
| `name` | string | макс. длина: 100 |
| `username` | string | шаблон: `^[a-zA-Z0-9_]{3,32}$` |
| `avatarUrl` | string | может быть `null` |

### OperationType

`COINS_EARNED` — начисление монет, `COINS_SPENT` — покупка в магазине, `DEPOSIT` — внесение залога, `REFUND` — возврат залога, `CHARITY_TRANSFER` — перечисление в фонд.

Значения: `COINS_EARNED`, `COINS_SPENT`, `DEPOSIT`, `REFUND`, `CHARITY_TRANSFER`

### Operation

Запись истории. Для финансовых — обязательно дата, тип, сумма, пакт, статус (1.1.13).

| Поле | Тип | Описание |
|---|---|---|
| `id` | string | Префикс `tx-` — транзакция залога, `coin-` — операция с монетами |
| `type` | OperationType | |
| `createdAt` | string (date-time) | |
| `amount` | Money | |
| `currency` | string | `RUB` или `COINS` |
| `status` | TransactionStatus | |
| `statusLabel` | string | |
| `pact` | object | может быть `null` |
| `description` | string | |

### Category

| Поле | Тип |
|---|---|
| `id` | UUID |
| `name` | string |
| `description` | string |

### Charity

| Поле | Тип |
|---|---|
| `id` | UUID |
| `name` | string |
| `description` | string |
| `websiteUrl` | string (uri) |

### GoalStatus

Значения: `ACTIVE`, `COMPLETED`, `CANCELLED`

### GoalVisibility

`PUBLIC` — в ленте, `INVITE_PENDING` — скрыта, ждём ответа приглашённого, `IN_PACT` — в активном пакте, в ленте не показывается.

Значения: `PUBLIC`, `INVITE_PENDING`, `IN_PACT`

### MilestoneInput

| Поле | Тип | Описание |
|---|---|---|
| `title*` | string | макс. длина: 200 |
| `description` | string | макс. длина: 2000 |
| `deadline*` | string (date-time) | |

### Milestone

| Поле | Тип | Описание |
|---|---|---|
| `id` | UUID | |
| `title` | string | |
| `description` | string | |
| `deadline` | string (date-time) | |
| `status` | string | `PENDING` \| `IN_PROGRESS` \| `COMPLETED` \| `FAILED` |
| `completedAt` | string (date-time) | может быть `null` |

### CreateGoalRequest

| Поле | Тип | Описание |
|---|---|---|
| `title*` | string | макс. длина: 200 |
| `description` | string | макс. длина: 5000 |
| `categoryId*` | integer (int64) | |
| `deadline*` | string (date-time) | |
| `depositAmount*` | Money | Сумма залога для каждого участника |
| `charityId*` | integer (int64) | |
| `milestones` | массив MilestoneInput | |
| `inviteUsername` | string | Если указан — сразу приглашение этому человеку, цель скрыта из ленты; может быть `null` |

### UpdateGoalRequest

| Поле | Тип |
|---|---|
| `title` | string, макс. длина 200 |
| `description` | string, макс. длина 5000 |
| `categoryId` | UUID |
| `deadline` | string (date-time) |
| `depositAmount` | Money |
| `charityId` | UUID |

### GoalCard

Короткая карточка для ленты и списков.

| Поле | Тип |
|---|---|
| `id` | UUID |
| `title` | string |
| `category` | Category |
| `author` | UserShort |
| `deadline` | string (date-time) |
| `depositAmount` | Money |
| `milestonesCount` | integer |
| `participantsCount` | integer |
| `status` | GoalStatus |
| `visibility` | GoalVisibility |

### Goal

| Поле | Тип | Описание |
|---|---|---|
| `id` | UUID | |
| `title` | string | |
| `description` | string | |
| `category` | Category | |
| `author` | UserShort | |
| `deadline` | string (date-time) | |
| `depositAmount` | Money | |
| `charity` | Charity | |
| `status` | GoalStatus | |
| `visibility` | GoalVisibility | |
| `milestones` | массив Milestone | |
| `pactId` | UUID | Пакт, созданный вместе с целью |
| `editable` | boolean | `false`, если есть активный пакт — фронт блокирует форму |
| `createdAt` | string (date-time) | |
| `updatedAt` | string (date-time) | |

### Invitation

Приглашение или отклик. Физически это запись `PactParticipant` в статусе `INVITED` / `REQUESTED`.

| Поле | Тип | Описание |
|---|---|---|
| `id` | UUID | |
| `kind` | string | `INVITE` \| `JOIN_REQUEST` — `INVITE` — автор позвал человека, `JOIN_REQUEST` — человек сам откликнулся из ленты |
| `status` | string | `PENDING` \| `ACCEPTED` \| `DECLINED` \| `EXPIRED` |
| `from` | UserShort | |
| `to` | UserShort | |
| `goal` | Goal | |
| `pactId` | UUID | |
| `message` | string | может быть `null` |
| `createdAt` | string (date-time) | |
| `expiresAt` | string (date-time) | `createdAt` + 48 часов (1.3.6) |

### PactStatus

`FORMING` — ищем напарника, `AWAITING_DEPOSITS` — все согласились, ждём залогов, `ACTIVE` — все залоги внесены (1.3.10), `COMPLETED` — завершён, `CANCELLED` — расторгнут.

Значения: `FORMING`, `AWAITING_DEPOSITS`, `ACTIVE`, `COMPLETED`, `CANCELLED`

### ParticipantStatus

Значения: `INVITED`, `REQUESTED`, `ACTIVE`, `REJECTED`, `LEFT`, `COMPLETED`, `FAILED`

### Participant

Участник с индивидуальными статусами.

| Поле | Тип | Описание |
|---|---|---|
| `participantId` | UUID | |
| `user` | UserShort | |
| `isAuthor` | boolean | |
| `status` | ParticipantStatus | |
| `depositStatus` | DepositStatus | |
| `depositStatusLabel` | string | |
| `completedMilestones` | integer | |
| `joinedAt` | string (date-time) | может быть `null` |

### PactSummary

| Поле | Тип | Описание |
|---|---|---|
| `id` | UUID | |
| `goal` | GoalCard | |
| `status` | PactStatus | |
| `participants` | массив UserShort | |
| `nextDeadline` | string (date-time) | может быть `null` |
| `myDepositStatus` | DepositStatus | |
| `pendingReviewsCount` | integer | Сколько чужих отчётов ждут моей проверки |

### PactDetails

| Поле | Тип | Описание |
|---|---|---|
| `id` | UUID | |
| `status` | PactStatus | |
| `goal` | Goal | |
| `participants` | массив Participant | |
| `financialTerms` | object | |
| `recentCheckIns` | массив CheckIn | Последние чек-ины, полная история — `GET /pacts/{id}/checkins` |
| `termination` | TerminationState | |
| `myParticipantId` | UUID | Мой `PactParticipant.id` в этом пакте |
| `createdAt` | string (date-time) | |

### TerminationState

| Поле | Тип | Описание |
|---|---|---|
| `canTerminate` | boolean | `false`, если есть нарушения |
| `votedParticipantIds` | массив UUID | |
| `requiredVotes` | integer | |

### DepositStatus

`PENDING_PAYMENT` — «Ожидает оплаты», `PAID` — «Залог внесён» (заблокирован), `REFUND_PENDING` — «Ожидается возврат», `REFUNDED` — «Залог возвращён», `CHARITY_TRANSFER_PENDING` — «Подлежит перечислению в фонд», `TRANSFERRED_TO_CHARITY` — «Перечислен в фонд», `FAILED` — «Ошибка операции».

Значения: `PENDING_PAYMENT`, `PAID`, `REFUND_PENDING`, `REFUNDED`, `CHARITY_TRANSFER_PENDING`, `TRANSFERRED_TO_CHARITY`, `FAILED`

### Deposit

| Поле | Тип | Описание |
|---|---|---|
| `id` | UUID | |
| `pactId` | UUID | |
| `amount` | Money | |
| `currency` | string | |
| `status` | DepositStatus | |
| `statusLabel` | string | |
| `nextAction` | string | Что пользователь может сделать дальше (2.5.7); может быть `null` |
| `createdAt` | string (date-time) | |
| `updatedAt` | string (date-time) | |

### PaymentInitResponse

| Поле | Тип | Описание |
|---|---|---|
| `depositId` | UUID | |
| `transactionId` | UUID | |
| `confirmationUrl` | string (uri) | URL страницы оплаты, предоставленный платёжным провайдером — сюда выполняется редирект |
| `status` | TransactionStatus | |

### TransactionStatus

Значения: `PENDING`, `SUCCESS`, `FAILED`

### Transaction

| Поле | Тип | Описание |
|---|---|---|
| `id` | UUID | |
| `depositId` | UUID | |
| `type` | string | `DEPOSIT` \| `REFUND` \| `CHARITY_TRANSFER` |
| `amount` | Money | |
| `currency` | string | |
| `status` | TransactionStatus | |
| `providerOperationId` | string | может быть `null` |
| `errorMessage` | string | может быть `null` |
| `createdAt` | string (date-time) | |
| `completedAt` | string (date-time) | может быть `null` |

### CheckInStatus

Значения: `PENDING`, `APPROVED`, `REJECTED`, `AUTO_APPROVED`

### ProofInput

Заполняется одно из полей в зависимости от `type`.

| Поле | Тип | Описание |
|---|---|---|
| `type*` | string | `FILE` \| `LINK` \| `TEXT` |
| `fileUrl` | string | Для `FILE` — url из `POST /files` |
| `externalUrl` | string (uri) | Для `LINK` |
| `description` | string | Для `TEXT` — сам текст, для остальных — подпись; макс. длина: 2000 |

### Proof

Заполняется одно из полей в зависимости от `type`.

| Поле | Тип | Описание |
|---|---|---|
| `type*` | string | `FILE` \| `LINK` \| `TEXT` |
| `fileUrl` | string | Для `FILE` — url из `POST /files` |
| `externalUrl` | string (uri) | Для `LINK` |
| `description` | string | Для `TEXT` — сам текст, для остальных — подпись; макс. длина: 2000 |
| `id` | UUID | |
| `uploadedAt` | string (date-time) | |

### CreateCheckInRequest

| Поле | Тип | Описание |
|---|---|---|
| `milestoneId` | UUID | может быть `null` |
| `comment` | string | макс. длина: 2000 |
| `proofs*` | массив ProofInput | мин. элементов: 1 · макс. элементов: 10 |

### Review

| Поле | Тип | Описание |
|---|---|---|
| `id` | UUID | |
| `reviewer` | UserShort | |
| `status` | string | `APPROVED` \| `REJECTED` |
| `comment` | string | может быть `null` |
| `createdAt` | string (date-time) | |

### CreateReviewRequest

| Поле | Тип | Описание |
|---|---|---|
| `status*` | string | `APPROVED` \| `REJECTED` |
| `comment` | string | Обязательно при `REJECTED`; макс. длина: 2000 |

### CheckIn

| Поле | Тип | Описание |
|---|---|---|
| `id` | UUID | |
| `participantId` | UUID | |
| `author` | UserShort | |
| `milestone` | Milestone | |
| `attemptNumber` | integer | Какая это попытка по этапу (1..3) |
| `attemptsLeft` | integer | Сколько попыток останется, если этот отчёт отклонят |
| `status` | CheckInStatus | |
| `comment` | string | может быть `null` |
| `proofs` | массив Proof | |
| `reviews` | массив Review | |
| `submittedAt` | string (date-time) | |
| `autoApproveAt` | string (date-time) | `submittedAt` + 48 часов |
| `canReview` | boolean | Может ли текущий пользователь проверить этот отчёт |

### Message

| Поле | Тип |
|---|---|
| `id` | UUID |
| `pactId` | UUID |
| `sender` | UserShort |
| `text` | string |
| `createdAt` | string (date-time) |

### ShopItemType

Значения: `AVATAR`, `FRAME`, `BACKGROUND`

### ShopItem

| Поле | Тип | Описание |
|---|---|---|
| `id` | UUID | |
| `name` | string | |
| `description` | string | |
| `price` | integer | Цена в монетах |
| `itemType` | ShopItemType | |
| `imageUrl` | string | |
| `owned` | boolean | |

### Purchase

| Поле | Тип | Описание |
|---|---|---|
| `id` | UUID | |
| `item` | ShopItem | |
| `price` | integer | Сколько монет списано |
| `purchasedAt` | string (date-time) | |
| `coinsLeft` | integer | |

### TicketStatus

`OPEN` — «Открыто», `IN_PROGRESS` — «В работе», `RESOLVED` — «Решено», `REJECTED` — «Отклонено».

Значения: `OPEN`, `IN_PROGRESS`, `RESOLVED`, `REJECTED`

### CreateTicketRequest

| Поле | Тип | Описание |
|---|---|---|
| `subject*` | string | макс. длина: 200 |
| `description*` | string | макс. длина: 5000 |
| `pactId` | UUID | может быть `null` |
| `screenshotUrl` | string | url из `POST /files`; может быть `null` |

### SupportTicket

| Поле | Тип | Описание |
|---|---|---|
| `id` | UUID | |
| `user` | UserShort | |
| `subject` | string | |
| `description` | string | |
| `pactId` | UUID | может быть `null` |
| `screenshotUrl` | string | может быть `null` |
| `status` | TicketStatus | |
| `adminComment` | string | может быть `null` |
| `createdAt` | string (date-time) | |
| `updatedAt` | string (date-time) | |

### PageResponse<T>

Общий формат ответа для списков. `items` содержит элементы указанного типа.

| Поле | Тип | Описание |
|---|---|---|
| `items` | массив `T` | Элементы текущей страницы |
| `page` | integer | Номер текущей страницы, начиная с 1 |
| `size` | integer | Размер страницы |
| `totalItems` | integer | Общее количество найденных элементов |
| `totalPages` | integer | Количество страниц |

### RefreshTokenRequest

| Поле | Тип | Описание |
|---|---|---|
| `refreshToken*` | string | Refresh-токен, выданный при входе |

### LogoutRequest

| Поле | Тип | Описание |
|---|---|---|
| `refreshToken*` | string | Refresh-токен, который требуется отозвать |

### ForgotPasswordRequest

| Поле | Тип | Описание |
|---|---|---|
| `email*` | string (email) | Email аккаунта. Ответ не раскрывает, существует ли такой аккаунт |

### ResetPasswordRequest

| Поле | Тип | Описание |
|---|---|---|
| `token*` | string | Одноразовый токен из письма |
| `newPassword*` | string | Новый пароль; минимум 8 символов |

### ConfirmEmailRequest

| Поле | Тип | Описание |
|---|---|---|
| `token*` | string | Токен подтверждения email |

### ChangeEmailRequest

| Поле | Тип | Описание |
|---|---|---|
| `newEmail*` | string (email) | Новый email |
| `password*` | string | Текущий пароль для подтверждения действия |

### ApplyShopItemRequest

| Поле | Тип | Описание |
|---|---|---|
| `shopItemId*` | UUID | Идентификатор уже купленного предмета |

### CreateInvitationRequest

| Поле | Тип | Описание |
|---|---|---|
| `username*` | string | Username приглашаемого пользователя |
| `message` | string | Необязательное сообщение приглашённому; максимум 500 символов |

### CreateJoinRequest

| Поле | Тип | Описание |
|---|---|---|
| `message` | string | Необязательное сообщение автора отклика; максимум 500 символов |

### InvitationDecisionRequest

| Поле | Тип | Описание |
|---|---|---|
| `comment` | string | Необязательный комментарий к решению; максимум 2000 символов |

### EmptyRequest

Пустой JSON-объект `{}`. Используется только для команд, где действие однозначно определяется путём запроса и авторизованным пользователем.

### UpdateMilestoneRequest

Все поля необязательные; передаются только изменяемые значения.

| Поле | Тип | Описание |
|---|---|---|
| `title` | string | Название этапа, максимум 200 символов |
| `description` | string | Описание этапа, максимум 2000 символов |
| `deadline` | string (date-time) | Новый дедлайн |

### CreatePaymentRequest

Сумма и валюта залога определяются сервером по условиям пакта; клиент не передаёт платёжные реквизиты и не может самостоятельно изменить сумму.

| Поле | Тип | Описание |
|---|---|---|
| `returnUrl` | string (uri) | URL frontend, на который пользователь вернётся после оплаты; может быть `null` |

### PaymentProviderWebhook

Абстрактный внутренний формат уведомления о финансовом событии. Адаптер конкретного провайдера должен преобразовать его исходный payload к данной модели.

| Поле | Тип | Описание |
|---|---|---|
| `eventId*` | string | Уникальный идентификатор события для защиты от повторной обработки |
| `eventType*` | string | `PAYMENT_SUCCEEDED` \| `PAYMENT_FAILED` \| `REFUND_SUCCEEDED` \| `TRANSFER_SUCCEEDED` \| `TRANSFER_FAILED` |
| `providerOperationId` | string | Идентификатор операции у провайдера; может быть `null` для события, где он ещё неизвестен |
| `providerPaymentId` | string | Идентификатор исходного платежа; может быть `null` |
| `status` | string | Статус операции у провайдера после нормализации |
| `amount` | Money | Сумма операции, если присутствует в уведомлении |
| `currency` | string | Валюта операции, если присутствует |

### WebhookAcknowledgement

| Поле | Тип | Описание |
|---|---|---|
| `accepted` | boolean | Принято ли уведомление к обработке |

### FileUploadRequest

Тип содержимого запроса: `multipart/form-data`.

| Поле | Тип | Описание |
|---|---|---|
| `file*` | binary | Загружаемый файл |
| `purpose*` | string | `PROOF`, `AVATAR` или `SUPPORT_SCREENSHOT` |

### FileUploadResponse

| Поле | Тип | Описание |
|---|---|---|
| `fileUrl*` | string (uri) | Ссылка, которую можно передавать в последующих запросах |
| `fileName` | string | Имя файла |
| `contentType` | string | MIME-тип файла |
| `sizeBytes` | integer | Размер файла в байтах |

### CreateMessageRequest

| Поле | Тип | Описание |
|---|---|---|
| `text*` | string | Текст сообщения; от 1 до 4000 символов |

### UpdateSupportTicketRequest

| Поле | Тип | Описание |
|---|---|---|
| `status*` | TicketStatus | Новый статус обращения |
| `adminComment` | string | Комментарий поддержки; может быть `null`, максимум 5000 символов |

### RetryTransactionRequest

| Поле | Тип | Описание |
|---|---|---|
| `reason` | string | Причина повторного запуска операции; необязательное поле, максимум 1000 символов |
