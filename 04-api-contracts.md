# CoGoal API — справочник эндпоинтов

Справочник собран из `openapi.yaml`. Сначала — путь пользователя от регистрации до возврата залога, дальше все эндпоинты по разделам и схемы данных.

**57** эндпоинтов · **51** схема данных · **JWT** Bearer-авторизация

---

## Содержание

1. [Путь пользователя](#1-путь-пользователя)
2. [Общие правила API](#2-общие-правила-api)
3. [Что добавить в ERD](#3-что-добавить-в-erd)
4. [Эндпоинты по разделам](#4-эндпоинты-по-разделам)
5. [Схемы данных](#5-схемы-данных)

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

Каждый участник оплачивает залог на странице YooKassa. Результат приходит вебхуком. Когда оплатили все — пакт становится `ACTIVE`.

| Метод | Путь |
|---|---|
| `POST` | `/pacts/{pactId}/deposits/me/payment` |
| `POST` | `/webhooks/yookassa` |
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
- Авторизация — JWT в заголовке `Authorization: Bearer <accessToken>` (backend stateless, требование 2.3.1).
- Даты — ISO 8601 в UTC (`2026-10-01T12:00:00Z`).
- Деньги — строка-десятичное число в рублях (`"500.00"`), чтобы не терять копейки на float.
- Ошибки всегда возвращаются в формате `Error` с сообщением на русском (требование 2.5.2).
- Списки возвращаются постранично в формате `{ items, page, size, totalItems, totalPages }`.
- Операции, связанные с деньгами, принимают заголовок `Idempotency-Key` (требования 1.1.19, 2.2.7).

---

## 3. Что добавить в ERD

- Сумма залога нигде не хранится — добавить `deposit_amount` в `Pact` (или `Goal`).
- `Pact.status` — добавить `FORMING` (ищем напарника) и `AWAITING_DEPOSITS` (ждём залогов, требование 1.3.10).
- `PactParticipant.status` — добавить `REQUESTED` (отклик из ленты) и `FAILED` (нарушил обязательства); добавить `created_at`, чтобы считать 48 часов на ответ, и `termination_vote` (bool) для расторжения.
- `Proof.type` — добавить `TEXT` (требование 1.3.15).
- `CheckIn` — добавить `attempt_number` (лимит 3 попытки) и статус `AUTO_APPROVED`.
- `User` — добавить `role` (`USER`/`ADMIN`); отдельные таблицы для refresh-токенов и токенов сброса пароля / смены email.
- Нет истории монет (1.6.4) — таблица `CoinTransaction` (`user_id, amount, reason, pact_id, created_at`). Нет таблицы достижений (1.1.7).
- `SupportTicket` — добавить `pact_id, screenshot_url, admin_comment`; статусы `OPEN, IN_PROGRESS, RESOLVED, REJECTED` вместо `CLOSED`.
- `Transaction` — добавить `idempotency_key` (уникальный индекс); `User` или `Deposit` — `payment_method_id` провайдера (1.1.17).

---

## 4. Эндпоинты по разделам

### 4.1 Авторизация — Auth

Регистрация, вход, токены, восстановление пароля (1.1.1–1.1.5, 1.1.8, 2.2.6).

| Метод | Путь | Описание |
|---|---|---|
| `POST` | `/auth/register` | Регистрация нового пользователя |
| `POST` | `/auth/login` | Вход по email и паролю |
| `POST` | `/auth/refresh` | Обновить access-токен |
| `POST` | `/auth/logout` | Выход |
| `POST` | `/auth/password/forgot` | Запросить ссылку на сброс пароля |
| `POST` | `/auth/password/reset` | Установить новый пароль по токену из письма |
| `POST` | `/auth/email/confirm` | Подтвердить смену email |

### 4.2 Профиль — Profile

Свой профиль и публичные профили других пользователей (1.1.6, 1.1.7, 1.1.9–1.1.11).

| Метод | Путь | Описание |
|---|---|---|
| `GET` | `/users/me` | Мой профиль (приватные данные) |
| `PATCH` | `/users/me` | Изменить имя, username или аватар |
| `POST` | `/users/me/email-change` | Запросить смену email |
| `PUT` | `/users/me/active-item` | Применить купленный предмет к профилю |
| `GET` | `/users/{username}` | Публичный профиль по username |

### 4.3 История операций — Operations

История операций с монетами и залогами, экспорт в PDF (1.1.12–1.1.16).

| Метод | Путь | Описание |
|---|---|---|
| `GET` | `/users/me/operations` | История операций (монеты и залоги) |
| `GET` | `/users/me/operations/export` | Экспорт истории в PDF |

### 4.4 Справочники — Dictionaries

Справочники — категории целей и благотворительные фонды (1.2.9, 1.4.11).

| Метод | Путь | Описание |
|---|---|---|
| `GET` | `/categories` | Список категорий целей |
| `GET` | `/charities` | Список благотворительных фондов |

### 4.5 Цели — Goals

Создание и редактирование целей и этапов, публичная лента (1.2.x, 1.3.1).

| Метод | Путь | Описание |
|---|---|---|
| `GET` | `/goals` | Публичная лента целей |
| `POST` | `/goals` | Создать цель |
| `GET` | `/goals/my` | Мои цели |
| `GET` | `/goals/{goalId}` | Детали цели |
| `PATCH` | `/goals/{goalId}` | Редактировать цель |
| `DELETE` | `/goals/{goalId}` | Удалить цель |
| `POST` | `/goals/{goalId}/milestones` | Добавить этап к цели |
| `PATCH` | `/goals/{goalId}/milestones/{milestoneId}` | Изменить этап |
| `DELETE` | `/goals/{goalId}/milestones/{milestoneId}` | Удалить этап |

### 4.6 Приглашения и отклики — Invitations

Приглашения по username и отклики из ленты — путь к заключению пакта (1.3.2–1.3.9).

| Метод | Путь | Описание |
|---|---|---|
| `POST` | `/goals/{goalId}/invitations` | Пригласить напарника по username |
| `POST` | `/goals/{goalId}/join-requests` | Откликнуться на цель из ленты |
| `GET` | `/invitations` | Мои приглашения и отклики |
| `GET` | `/invitations/{invitationId}` | Детали приглашения |
| `POST` | `/invitations/{invitationId}/accept` | Принять приглашение / отклик |
| `POST` | `/invitations/{invitationId}/decline` | Отклонить приглашение / отклик |
| `POST` | `/invitations/{invitationId}/cancel` | Отозвать своё приглашение / отклик |
| `POST` | `/goals/{goalId}/publish` | Вернуть цель в общую ленту |

### 4.7 Пакты — Pacts

Просмотр пактов и досрочное расторжение (1.3.10–1.3.14, 1.3.27–1.3.30).

| Метод | Путь | Описание |
|---|---|---|
| `GET` | `/pacts` | Мои пакты |
| `GET` | `/pacts/{pactId}` | Детали пакта |
| `POST` | `/pacts/{pactId}/termination-votes` | Проголосовать за досрочное расторжение |
| `DELETE` | `/pacts/{pactId}/termination-votes` | Отозвать свой голос за расторжение |

### 4.8 Залоги и оплата — Deposits

Залоги участников и оплата через YooKassa (1.3.9–1.3.11, 1.4.x).

| Метод | Путь | Описание |
|---|---|---|
| `GET` | `/pacts/{pactId}/deposits/me` | Статус моего залога в пакте |
| `POST` | `/pacts/{pactId}/deposits/me/payment` | Начать оплату залога |

### 4.9 Чек-ины — CheckIns

Отчёты о выполнении этапов, доказательства и их проверка напарниками (1.3.15–1.3.18, 1.3.29).

| Метод | Путь | Описание |
|---|---|---|
| `GET` | `/pacts/{pactId}/checkins` | История чек-инов пакта |
| `POST` | `/pacts/{pactId}/checkins` | Отправить отчёт с доказательствами |
| `GET` | `/checkins/{checkinId}` | Детали чек-ина |
| `POST` | `/checkins/{checkinId}/reviews` | Принять или отклонить отчёт напарника |

### 4.10 Файлы — Files

Загрузка файлов (доказательства, аватары, скриншоты для поддержки).

| Метод | Путь | Описание |
|---|---|---|
| `POST` | `/files` | Загрузить файл |

### 4.11 Чат — Chat

Чат участников пакта (1.5.1).

| Метод | Путь | Описание |
|---|---|---|
| `GET` | `/pacts/{pactId}/messages` | История чата пакта |
| `POST` | `/pacts/{pactId}/messages` | Отправить сообщение |

### 4.12 Магазин — Shop

Магазин предметов за виртуальные монеты (1.6.x).

| Метод | Путь | Описание |
|---|---|---|
| `GET` | `/shop/items` | Каталог магазина |
| `POST` | `/shop/items/{itemId}/purchase` | Купить предмет за монеты |
| `GET` | `/users/me/inventory` | Мои купленные предметы |

### 4.13 Техподдержка — Support

Обращения в техподдержку со стороны пользователя (1.7.1, 1.7.2).

| Метод | Путь | Описание |
|---|---|---|
| `GET` | `/support/tickets` | Мои обращения |
| `POST` | `/support/tickets` | Создать обращение |
| `GET` | `/support/tickets/{ticketId}` | Детали обращения |

### 4.14 Администрирование — Admin

Действия администратора / поддержки (1.7.3, 1.7.4).

| Метод | Путь | Описание |
|---|---|---|
| `GET` | `/admin/support/tickets` | Все обращения (для поддержки) |
| `PATCH` | `/admin/support/tickets/{ticketId}` | Сменить статус обращения |
| `GET` | `/admin/transactions` | Финансовые операции с ошибками |
| `POST` | `/admin/transactions/{transactionId}/retry` | Повторить неуспешную финансовую операцию |

### 4.15 Вебхуки — Webhooks

Входящие уведомления от платёжного провайдера (1.4.9, 1.4.10, 2.2.8).

| Метод | Путь | Описание |
|---|---|---|
| `POST` | `/webhooks/yookassa` | Уведомление от YooKassa |

---

## 5. Схемы данных

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
| `id` | integer (int64) | |
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
| `id` | integer (int64) | |
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

Публичный профиль. Без email, баланса и платёжных данных (1.1.11).

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
| `id` | integer (int64) |
| `name` | string |
| `description` | string |

### Charity

| Поле | Тип |
|---|---|
| `id` | integer (int64) |
| `name` | string |
| `description` | string |
| `websiteUrl` | string (uri) |

### GoalStatus

Значения: `ACTIVE`, `COMPLETED`, `CANCELLED`

### GoalVisibility

`PUBLIC` — в ленте, `INVITE_PENDING` — скрыта, ждём ответа приглашённого (1.3.4), `IN_PACT` — в активном пакте, в ленте не показывается.

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
| `id` | integer (int64) | |
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
| `categoryId` | integer (int64) |
| `deadline` | string (date-time) |
| `depositAmount` | Money |
| `charityId` | integer (int64) |

### GoalCard

Короткая карточка для ленты и списков.

| Поле | Тип |
|---|---|
| `id` | integer (int64) |
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
| `id` | integer (int64) | |
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
| `pactId` | integer (int64) | Пакт, созданный вместе с целью |
| `editable` | boolean | `false`, если есть активный пакт — фронт блокирует форму |
| `createdAt` | string (date-time) | |
| `updatedAt` | string (date-time) | |

### Invitation

Приглашение или отклик. Физически это запись `PactParticipant` в статусе `INVITED` / `REQUESTED`.

| Поле | Тип | Описание |
|---|---|---|
| `id` | integer (int64) | |
| `kind` | string | `INVITE` \| `JOIN_REQUEST` — `INVITE` — автор позвал человека, `JOIN_REQUEST` — человек сам откликнулся из ленты |
| `status` | string | `PENDING` \| `ACCEPTED` \| `DECLINED` \| `EXPIRED` |
| `from` | UserShort | |
| `to` | UserShort | |
| `goal` | Goal | |
| `pactId` | integer (int64) | |
| `message` | string | может быть `null` |
| `createdAt` | string (date-time) | |
| `expiresAt` | string (date-time) | `createdAt` + 48 часов (1.3.6) |

### PactStatus

`FORMING` — ищем напарника, `AWAITING_DEPOSITS` — все согласились, ждём залогов, `ACTIVE` — все залоги внесены (1.3.10), `COMPLETED` — завершён, `CANCELLED` — расторгнут.

Значения: `FORMING`, `AWAITING_DEPOSITS`, `ACTIVE`, `COMPLETED`, `CANCELLED`

### ParticipantStatus

Значения: `INVITED`, `REQUESTED`, `ACTIVE`, `REJECTED`, `LEFT`, `COMPLETED`, `FAILED`

### Participant

Участник с индивидуальными статусами (1.3.28).

| Поле | Тип | Описание |
|---|---|---|
| `participantId` | integer (int64) | |
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
| `id` | integer (int64) | |
| `goal` | GoalCard | |
| `status` | PactStatus | |
| `participants` | массив UserShort | |
| `nextDeadline` | string (date-time) | может быть `null` |
| `myDepositStatus` | DepositStatus | |
| `pendingReviewsCount` | integer | Сколько чужих отчётов ждут моей проверки |

### PactDetails

| Поле | Тип | Описание |
|---|---|---|
| `id` | integer (int64) | |
| `status` | PactStatus | |
| `goal` | Goal | |
| `participants` | массив Participant | |
| `financialTerms` | object | |
| `recentCheckIns` | массив CheckIn | Последние чек-ины, полная история — `GET /pacts/{id}/checkins` |
| `termination` | TerminationState | |
| `myParticipantId` | integer (int64) | Мой `PactParticipant.id` в этом пакте |
| `createdAt` | string (date-time) | |

### TerminationState

| Поле | Тип | Описание |
|---|---|---|
| `canTerminate` | boolean | `false`, если есть нарушения |
| `votedParticipantIds` | массив integer (int64) | |
| `requiredVotes` | integer | |

### DepositStatus

`PENDING_PAYMENT` — «Ожидает оплаты», `PAID` — «Залог внесён» (заблокирован), `REFUND_PENDING` — «Ожидается возврат», `REFUNDED` — «Залог возвращён», `CHARITY_TRANSFER_PENDING` — «Подлежит перечислению в фонд», `TRANSFERRED_TO_CHARITY` — «Перечислен в фонд», `FAILED` — «Ошибка операции».

Значения: `PENDING_PAYMENT`, `PAID`, `REFUND_PENDING`, `REFUNDED`, `CHARITY_TRANSFER_PENDING`, `TRANSFERRED_TO_CHARITY`, `FAILED`

### Deposit

| Поле | Тип | Описание |
|---|---|---|
| `id` | integer (int64) | |
| `pactId` | integer (int64) | |
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
| `depositId` | integer (int64) | |
| `transactionId` | integer (int64) | |
| `confirmationUrl` | string (uri) | Страница оплаты YooKassa — сюда редирект |
| `status` | TransactionStatus | |

### TransactionStatus

Значения: `PENDING`, `SUCCESS`, `FAILED`

### Transaction

| Поле | Тип | Описание |
|---|---|---|
| `id` | integer (int64) | |
| `depositId` | integer (int64) | |
| `type` | string | `DEPOSIT` \| `REFUND` \| `CHARITY_TRANSFER` |
| `amount` | Money | |
| `currency` | string | |
| `status` | TransactionStatus | |
| `providerOperationId` | string | может быть `null` |
| `errorMessage` | string | может быть `null` |
| `createdAt` | string (date-time) | |
| `completedAt` | string (date-time) | может быть `null` |

### YooKassaWebhook

Формат задаётся YooKassa, здесь — основные поля.

| Поле | Тип | Описание |
|---|---|---|
| `type` | string | |
| `event` | string | `payment.succeeded` \| `payment.canceled` \| `payment.waiting_for_capture` \| `refund.succeeded` \| `payout.succeeded` \| `payout.canceled` |
| `object` | object | |

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
| `id` | integer (int64) | |
| `uploadedAt` | string (date-time) | |

### CreateCheckInRequest

| Поле | Тип | Описание |
|---|---|---|
| `milestoneId` | integer (int64) | может быть `null` |
| `comment` | string | макс. длина: 2000 |
| `proofs*` | массив ProofInput | мин. элементов: 1 · макс. элементов: 10 |

### Review

| Поле | Тип | Описание |
|---|---|---|
| `id` | integer (int64) | |
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
| `id` | integer (int64) | |
| `participantId` | integer (int64) | |
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
| `id` | integer (int64) |
| `pactId` | integer (int64) |
| `sender` | UserShort |
| `text` | string |
| `createdAt` | string (date-time) |

### ShopItemType

Значения: `AVATAR`, `FRAME`, `BACKGROUND`

### ShopItem

| Поле | Тип | Описание |
|---|---|---|
| `id` | integer (int64) | |
| `name` | string | |
| `description` | string | |
| `price` | integer | Цена в монетах |
| `itemType` | ShopItemType | |
| `imageUrl` | string | |
| `owned` | boolean | |

### Purchase

| Поле | Тип | Описание |
|---|---|---|
| `id` | integer (int64) | |
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
| `pactId` | integer (int64) | может быть `null` |
| `screenshotUrl` | string | url из `POST /files`; может быть `null` |

### SupportTicket

| Поле | Тип | Описание |
|---|---|---|
| `id` | integer (int64) | |
| `user` | UserShort | |
| `subject` | string | |
| `description` | string | |
| `pactId` | integer (int64) | может быть `null` |
| `screenshotUrl` | string | может быть `null` |
| `status` | TicketStatus | |
| `adminComment` | string | может быть `null` |
| `createdAt` | string (date-time) | |
| `updatedAt` | string (date-time) | |
