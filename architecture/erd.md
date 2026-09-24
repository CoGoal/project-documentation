# ERD-диаграмма

![ERD-диаграмма] ( https://img.plantuml.biz/plantuml/png/lLZVRzCw57wlrFyXRx0h9i6kzX81eco5MzrsbD4YtY8lEMlCdDYodOx2-DyvJjmqIUSaPOo-LFKvEVxETtwcRvGcKcS96mw6WvX6u2MWOnuf-qjfaC3rHe0o6f1ggZVUKe7qVWm77dx-yMZa-TvoEJdtNhwS___wTN5-V5nyPwYRaWHGv_Feyko_qUNJq-T7XGmIGbb5S79wUeH6bXUJZylnrf0WIjro6GKnKN53kT2ufbBfe779Y-VRMnXfav8rGGg2JB8Qmb8SQhg6m6fH3KbGzVTTwM_8QQfGCBbOM5Je6qG1uokQ1aJZaqBN51WFRr0XGqxDtOl9RFnfCPhDhJq9HAEOwBhiPoKEPwYnudBJDnPE4Y9GeQH2KvwQsyPV5lMBtdF23WcuYoEBro76grWLtqahlkASH8oci4yVvhNEr7r9vo8snowYJjBdc19zkLWXRoukOYBHd4DwEFmcn3dMDGsf86blj08VS-1f2QeRwbSiYGwaCwmHfJb6yE6C_gNquObWq5cPCOGtaxG_ysMm7CubMwBsvDx5SZgrgBEhX6fNShVwb23mUqj_BZc_xkj7QDp6TkS848wH-ww_Hj_kQS0Ln-2RDbE4jMkvmfm9nadK4R5BM5Eu_JEEojmMLantNFi7fVTGA1hBRyw4xgAvXIk5Sz0vCQaAYg69qhSVFap7euiwm7CG72qSr9MwYIG9ptAspiTdazbe-lJ5oREJBPMPb926cmhUVu-w-1UIht45Ca7Sc40Kz-vl7m-T9mj9KcKOuxtRHLGmU62PFHv3N80anW4NHo0bbq42If4Ls6og56uRXQv-Qn-l3HDh_-7ZJa4QEGUUncvqS5L-YhcOu0xuQ6Kf90sXkbxckweh5pgBT9v9t51KRpBR5WHqlMrptaCkxDqTZ6P2SAaN5BVetisa3MPszHL2VH3fZxbbvardb4LKJ_cghtzwrqmSRiBQzB96NkpU1DfmjrHn3dquUEAzSdsCy19OtYPKJ8Ngq1uEyhVCkxlZO_wZU07nBJfyj7ppQSYBO_gzU5tDJwr6lhRxc5jNZAhOU6ZCs5bPrr70C15GvPfBOoO_hgXKLslVYusXAjPz5RC8xm7QhRw8cwUQM9tcurPzkr7wTij3tPs5FHsUvofXR1D8C6MIczxAjq3prQv-UqF5RaK-hZGXOBjGJdob9iu2v2rn-t9NRW2-bv0ROtDxO6rLHwBlhI0rumLKCnQpCXDt7bG3iPERVgwQELMsk196sQJtDSgUrBHIRqEL51qEgcwKkL4lQvi-rkHFdjDJml3hVpyOlJVeIPQmNm00 )

# Описание таблиц и полей

## Таблица User - пользователи
- id - уникальный идентификатор пользователя
- username - уникальный публичный идентификатор пользователя
- email - почта пользователя
- password_hash - хэш пароля
- name - имя пользователя
- avatar_url - ссылка на аватар пользователя
- active_avatar_item_id - какой товар из магазина сейчас применяется 
- coins - количество монет пользователя
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
- title - заголовок (название) цели
- description - описание цели
- deadline - дедлайн цели
- status - статус цели (ACTIVE, COMPLETED, CANCELLED) 
- created_at - дата и время создания цели
- updated_at - когда цель последний раз изменялась

## Таблица Pact - пакт
- id - уникальный идентификатор пакта
- goal_id -  уникальный идентификатор цели
- charity_id -  уникальный идентификатор благотворительного фонда
- status - статус пакта (ACTIVE, COMPLETED, CANCELLED)
- created_at - создание пакта

## Таблица PactParticipant - участники пакта
- id -  уникальный идентификатор пользователя, как участника пакта
- pact_id -  уникальный идентификатор пакта
- user_id -  уникальный идентификатор пользователя
- status - статус пользователя в пакте (INVITED, ACTIVE, REJECTED, LEFT, COMPLETED)
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
- submitted_at - когда пользователь отправил отчёт
- status -  статус отчёта (PENDING, APPROVED, REJECTED
- comment - комментарий пользователя к отчёту

## Таблица Proof - доказательство
- id -  уникальный идентификатор доказательства
- checkin_id -  уникальный идентификатор отчёта из таблицы CheckIn
- type - тип доказательства (LINK, FILE)
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
- status - статус (PENDING_PAYMENT, PAID, REFUND_PENDING, REFUNDED, CHARITY_TRANSFER_PENDING, TRANSFERRED_TO_CHARITY, FAILED)
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
- subject - тема обращения
- description - описание обращения
- status - статус обращения (OPEN, IN_PROGRESS, CLOSED)
- created_at - когда создано
- updated_at - когда в последний раз было изменение








