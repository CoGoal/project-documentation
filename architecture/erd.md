# ERD-диаграмма

![ERD-Diagram]( https://img.plantuml.biz/plantuml/png/jLZDZjis4BuRy3i8kHG8u1S8WY31fWc2f82HRiy4JStQZ4MH82blZ6mFUOA-Io_jOspUgCCVsQ94HLhlzc9GC_oP-MRupT2lZQNQDg8ghkHxIQlLhv_VBXVBXTdpMb5DHL7n6knHGI6rtKcdWzfoUooU_M50FokeAHeS5D-MYw9uNl2oU57msXOlNwu_ldhbXAkL-tMJQYe0rGUgsOvg9mL1UPMA53NcLkgIxAZPfQhXUdgWw01fT6yJoYm_08bgRa6GZcNlecLQLhtzcEIr2VE2BKU1xX81w7iPjkZCla7ZeIILtFAQK8kdADjPNKcsHtM3U3dpB1U0S0lb3z90BIgfJJL_TX5-qzZTjTn3xM6c-4Mi-vm5TixXi3hnmSSsZSbNnJMOWMapZRx2ALkfZzvc5ZycBHw6jWJ3D5UMoyJYv2oNilwSBGukHgKrCgl3G_6edAe49GstX0g98KPr2OmBMdbKkUsdreW_Ja5BTylwQEF0DYQTU-26RtZbd4_pTYGowBGgfsEsnllYSLGucCJHWVr0h7A-pCecPzaQAOephcXzDfAeit3IOByWOzLOGkIiHVCCtVPYQa4BNbCNKtAGrcat4ac5rZyFoXUacIdFVqDkCLRMu7qMxTV5qVc_Kf99eIgOfeKTiFs7m6JCZVaqZNLYdFFeX4bE6UcOr8tOO7awaR9fDeBRV5X6t7CbO9I2rbhAcv2MRZJfK_Gz6w415T-WXyFY1b-jgwNLK3CQq4PLafPJCVeC1mwttXdjXu_n9kmm_u9bW9vRSGBlnDJouwUhDqLn2nka-KmSEDP8tsTqhtrYsTjG8hnbiLpC8wk9pC-L7DPe3JNh5OOSqfYe1rvYPDhsfj_NFBAQN6jQ6uUC3DVTdhydlM-BwgphyJWO40AXaBHnCjd3SGGZgK07Wk-Z15fBJR9rOHp0cMov3f5vqvJSVfBRMFP2jAWJkgPp4imEy3b0uU2s6y8wRlseosdb2lfGj-BSIqkqWReSwMMH1YyWYzztKdt0qk2jC_ZXNmTFoHTASmRdWkCV7qEGCL-t5t7Akd6JJO1NnO-BUzyJ0ZbbpvFsi2c46wNmiNZDyVKJgCy42R5UTB6jepdIRBu0ipF3WsA06lsscMTZYUqSoLQYXtImCtdo2hl0FbUw1oXv264-5AmJPyQWeWkc3z6ynodRSMGGXrHudfw_dzne9ij-vptiPkajrQCjpY_5Z-_tV__uw_wV-t-N2_-FnTtTQYM_TPxLgTprqSl5wG6RIcENaGZz6pHAJgrfAdR-3LO7eNzvTIJT5Y0rRAWzFmxkf9yIsxAmQ_9KYdkS9hIjUBktws47qj3AZUysfOz5VRbeUw0ex-JvSj5D-fEdlOgNmXHhIufELjWort9nPlo1ghiWPQpsM9dLTTVrhs-wSgXD4l5yWT9bYQBZGD3wBLxa__5ocVyQAsWZgly0 )

# Описание таблиц и полей

## Таблица User - пользователи
- id - уникальный идентификатор пользователя
- username - уникальный публичный идентификатор пользователя
- email - почта пользователя
- password_hash - хэш пароля
- name - имя пользователя
- avatar_url - ссылка на аватар пользователя
- active_avatar_item_id - какой товар из магазина сейчас применяется
- role - роль пользователя (USER, ADMIN)
- coins - количество монет пользователя
- payment_method_id - идентификатор способа оплаты, выданный платёжным провайдером; полные реквизиты карты система не хранит
- failed_login_attempts - последовательных неудачных попыток входа
- locked_until - до какого момента аккаунт заблокирован
- created_at - дата и время создания аккаунта

## Таблица Category - категории целей
- id - уникальный идентификатор категории
- name - название категории
- description - описание категории

## Таблица Goal - цель
- id - уникальный идентификатор цели
- user_id -  уникальный идентификатор пользователя
- category_id - уникальный идентификатор категории
- deposit_amount - согласованная сумма залога для этого пакта
- charity_id - уникальный идентификатор благотворительного фонда
- title - заголовок (название) цели
- description - описание цели
- deadline - дедлайн цели
- status - статус цели (ACTIVE, COMPLETED, CANCELLED)

```mermaid
@startuml GoalStatus

[*] --> ACTIVE : цель создана

ACTIVE --> COMPLETED : финальный отчёт одобрен
ACTIVE --> CANCELLED : пакт отменён / расторгнут

COMPLETED --> [*]
CANCELLED --> [*]

@enduml
```
- visibility - статус видимости в публичной ленте (PUBLIC, INVITE_PENDING, IN_PACT)
- created_at - дата и время создания цели
- updated_at - когда цель последний раз изменялась

## Таблица Pact - пакт
- id - уникальный идентификатор пакта
- goal_id -  уникальный идентификатор цели
- charity_id -  уникальный идентификатор благотворительного фонда
- status - статус пакта (FORMING, AWAITING_DEPOSITS, ACTIVE, CANCELLED, COMPLETED)
- created_at - создание пакта

## Таблица PactParticipant - участники пакта
- id -  уникальный идентификатор пользователя, как участника пакта
- pact_id -  уникальный идентификатор пакта
- user_id -  уникальный идентификатор пользователя
- status - статус пользователя в пакте: REQUESTED (откликнулся сам, ждёт подтверждения автора), INVITED (приглашён автором, ждёт ответа), ACTIVE, FAILED (нарушил обязательства), REJECTED, LEFT, COMPLETED
- message - текст, который автор прикладывает к приглашению (может быть NULL)
- termination_vote - голос участника за досрочное расторжение пакта по взаимному согласию
- created_at - момент отклика или приглашения; от него отсчитываются 48 часов на ответ
- joined_at - время и дата присоединения пользователя к пакту

## Таблица Milestone - промежуточные этапы
- id -  уникальный идентификатор промежуточного этапа
- goal_id -  уникальный идентификатор цели
- title - заголовок (название) промежуточного этапа
- description - описание промежуточного этапа
- deadline - дедлайн промежуточного этапа
- status - статус промежуточного этапа (PENDING, IN_PROGRESS, COMPLETED, FAILED)
- completed_at - когда закончен этап или NULL (если этап не закончен)

## Таблица CheckIn - отчёт о выполнении
- id -  уникальный идентификатор
- participant_id -  уникальный идентификатор кого, кто добавил доказательство (это поле id из PactPacticipant)
- milestone_id -  уникальный идентификатор промежуточного этапа (может быть NULL, если пользователь не указывал промежуточные этапы)
- attempt_number - номер попытки; растёт после каждого REJECTED, максимум 3
- submitted_at - когда пользователь отправил отчёт
- status -  статус отчёта (PENDING, APPROVED, REJECTED, AUTO_APPROVED)
- comment - комментарий пользователя к отчёту

## Таблица Proof - доказательство
- id -  уникальный идентификатор доказательства
- checkin_id -  уникальный идентификатор отчёта из таблицы CheckIn
- type - тип доказательства (LINK, FILE, TEXT)
- file_url - ссылка на загруженный файл(может быть NULL, если используется другой тип доказательства)
- external_url - внешняя ссылка (может быть NULL, если используется другой тип доказательства)
- description - описание доказательства
- uploaded_at - время и дата загруженного доказательства

## Таблица Review - проверка отчёта
- id -  уникальный идентификатор проверки
- checkin_id -  уникальный идентификатор отчёта
- reviewer_id - кто проверяет(id из таблицы pactparticipant)
- status - статус проверки (APPROVED, REJECTED)
- comment - комментарий
- created_at - время и дата проверки отчёта

## Таблица Charity - благотворительные фонды
- id -  уникальный идентификатор благотворительного фонда
- name - название фонда
- description - описание фонда
- website_url - внешняя ссылка на web-сайт фонда
- is_active - доступен этот фонд для выбора или нет (TRUE, FALSE)

## Таблица Deposit - депозит
- id -  уникальный идентификатор депозита
- pact_participant_id - кто вносит депозит
- amount - сумма депозита
- currency - валюта
- status - статус ((PENDING_PAYMENT, PAID, REFUND_PENDING, REFUNDED, CHARITY_TRANSFER_PENDING, TRANSFERRED_TO_CHARITY, FAILED)

![State Diagram]( https://img.plantuml.biz/plantuml/png/VPFDIiD04CVlWRp37bKitZr8WqaqeB6ayL2iX8e5HMt5fdYrBVX1XUARf27HsfZq5MRVo9bDx7Sai6ncTtxpd__k5bjkxS5jtzqojNxVR5sxPRVcjbko94jdM-UiKDXZ9SrK3VF0AIcLOysqsIw304AOG09VCE9TnZjY6e07yNQrmJluzI62CNWCOXeIt1s1nxkyny37HR4NF2gpZ1Sb5KEbEjCyWb310AS-XFm9FeK8ecyyrY-kcisRpVKiNJ6Ej5LQ324Y4PJmLugIb6nhJjDKtyVa19EmBX-aeGdlOt2ys6PVT4PT4CpIz5DJTJ8cilWpQe_uUse6KI8I9DhPgJOmui4OdSLQNVYX1Vu1yHnn_r2n3BlYs9PYbdNDccCBNmBI0TyGPptYsEDl129XItfc4bEVV76SFkPvf67XiCceDUbp9gET8nYcXYoGKezpbHFcBsXfgcEVEDdUrHk7Kxe38N_1tmxsAXhx5vsZS0q8PuEPIzbzmCSWIpdofkkoLAmtBl4n_G80 )
  
- provider_payment_id - id платежа в YooKassa
- created_at - когда залог содан
- updated_at - когда состояние залог изменилось в последний раз 

## Таблица Transaction - транзакции
- id -  уникальный идентификатор транзакции
- deposit_id -  уникальный идентификатор депозита
- type - тип транзакции (DEPOSIT, REFUND, CHARITY_TRANSFER)
- amount - сумма операции
- currency - валюта
- status - статус (PENDING, SUCCESS, FAILED)
- idempotency_key - уникальный ключ идемпотентности; предотвращает создание двух транзакций по одному и тому же запросу при повторной отправке
- provider_operation_id - уникальный идентификатор операции у платёжного провайдера
- error_message - сообщение об ошибки, если есть (иначе NULL)
- created_at - когда создана операция
- completed_at - когда завершилась операция

## Таблица PaymentAuditLog - журнал транзакций
- id - уникальный идентификатор записи журнала
- transaction_id - уникальный идентификатор транзакции
- event_type - что именно произошло с транзакцией (PAYMENT_CREATED, PAYMENT_CONFIRMED, REFUND_REQUESTED, REFUND_COMPLETED)
- created_at - когда событие произошло
- error_message - сообщение об ошибке, если произошла (иначе NULL)

## Таблица Message - сообщения (для чата)
- id - уникальный идентификатор сообщения
- pact_id - уникальный идентификатор пакта
- sender_id - уникальный идентификатор того, кто отправил сообщение
- text - текст сообщения
- created_at - когда создано

## Таблица ShopItem - товары из магазина
- id - уникальный идентификатор товара
- name - название товара
- description - описание товара
- price - цена товара
- item_type - тип предмета (AVATAR, FRAME, BACKGROUND)
- imageUrl - ссылка на картинку товара
- is_active - можно ли купить товар

## Таблица Purchase - покупки из магазина
- id - уникальный идентификатор покупки
- user_id - уникальный идентификатор пользователя
- shop_item_id - уникальный идентификатор товара в магазине
- price - за сколько был куплен товар
- purchased_at - когда куплен товар

## Таблица SupportTicket -  обращение в техподдержку
- id - уникальный идентификатор обращения в поддержку
- user_id - уникальный идентификатор пользователя, который обратился
- pact_id - уникальный идентификатор пакта, к которому привязано обращение (может быть NULL, если проблема не связана с конкретным пактом)
- subject - тема обращения
- description - описание обращения
- screenshot_url - ссылка на прикреплённый скриншот (может быть NULL)
- admin_comment - комментарий администратора по итогу рассмотрения (может быть NULL)
- status - статус обращения (OPEN, IN_PROGRESS, RESOLVED, REJECTED)
- created_at - когда создано
- updated_at - когда в последний раз было изменение

## Таблица CoinTransaction - история операций с монетами
- id - уникальный идентификатор операции
- user_id - уникальный идентификатор пользователя
- pact_id - уникальный идентификатор пакта, за который начислены монеты (NULL для покупки в магазине)
- amount - сумма операции (положительная для начисления, отрицательная для списания)
- reason - причина операции (GOAL_COMPLETED, PURCHASE)
- created_at - когда произошла операция

## Таблица Achievement — достижения пользователя
- id - уникальный идентификатор достижения
- user_id - уникальный идентификатор пользователя, получившего достижение
- pact_id - уникальный идентификатор пакта, за который выдано достижение (может быть NULL для достижений не привязанных к конкретному пакту)
- code - машинный код достижения
- title - отображаемое название достижения
- awarded_at - когда достижение выдано

## Таблица AuthToken — токены авторизации
- id - уникальный идентификатор токена
- user_id - уникальный идентификатор пользователя, которому принадлежит токен
- token - сам токен, должен храниться в виде хэша, а не в открытом виде (так же, как пароль)
- type - тип токена: REFRESH (обновление access-токена), PASSWORD_RESET (сброс пароля), EMAIL_CONFIRM (подтверждение смены email))
- expires_at - момент, после которого токен считается недействительным
- created_at - когда токен был выдан
