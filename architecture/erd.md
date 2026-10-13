# ERD-диаграмма

![ERD-Diagram]( https://img.plantuml.biz/plantuml/png/jLXBRzms4BuRy7yWV2bGm4Pp3P8WHlg0qQPmc-GiS2N6orWI1OfQnuqBE0xG5aXQeqM1zj8MRKuzTUFOoTQFzL-W_b5dIArMpqXsqako46U6ttmp_3ZqpLXHgPB0NrnON8WtUHXHIGFY-ZIELvMIV90ejkQAy0x_Yf4FYZO-3vdQZXWHKWr5SIKUKaziyN23tAT-p33qU-JwzUj4_wcVwx_rR_ft-FMN_XK_uYeB5LVRv5xC97cqk438-uHxv2hXeIBNhgr_UkC6VbnUndyJC0ffm61vHAKxf19Cxds-ziMzZwmP2oZtMrSZI6nBICyPqdWujyAbSbJyGaSKw74IwLU-kugFc9CjSyK2fu3tOy1Bq4mAlnBG5JoChQ45inr0xax0W47FnYXQIqOLynoguBlY0ODY1T7YmjS5pcw1nOQGsvsyrRFpMEnA7Yakmk92AJfymV_VMN12jjWW1iRgfFBOiRp2vu40wcXO1lE9e7wlzgWMmt2PyTIqvh5802Q71Y89aMkFkJoWFgx1pjKYjgRcCUhXgIXNYn3uePAuR3lYCHzm7_FgLNF8BF8wks4T6EoaQ0DuRAJ85hjfgIc3Fjs9UDP1OBZB8nfsGul0lcd_jgesuG8WCTser4mn6V2GOisSaL2b7diJwG_Wm7RaU9lxy5b0sIzB_F_LOwu88fzrAiYGkPjhOO_Qv7LiIYAOqT0eXLI1I4RA2PDW02AVAs2S30AkchblIGaLiqslrgKGzpipSJ5Z7ZQM0Y-qqext8Q-wMB672gyXlxxIMhaayWNrEehn9HjnjlKs6KWJ0OvBHxrwijlld7ze5RJV-Uu0rgJ2RY8b2zsALZQb44anufv9tzwjVI_LVW9xLz8mncjAT1-OsRNIgytUBcl8E8X0ri3OsMJRBPI8Y4ahWLLIc9H2EetAsSvNFtbPjtLOJJokFXCRdQofESDDpB4H5hNENplCrjnA8fujN_Q2YbdeDLzH2gJYuWVgpb14Qo2PxtBoYYHtsLnwpTXQ9nAQR-CoqzbwWhPnDuMjTpcat3XBDs2FiiqwI4oY21udTxcxoRgrgGrNQycJmGFcgfxamsV6GiXHDJmdF9XDd4RnlSZWrK_1Ri54Sn4LkoWjkP1bfGAmSPMCFYTWrHroEEo6adUDqHLUTJwiZs1qYygkwterKSExOfDTdaPblCjOQcUIFOouP76J1h-9oSAp-oVzZ_vb_k0sB-sTdULbySW-gwwIfVIndkhN-b2Fqn_JlQN5XVnfMJ7Kns2ofu_qIprDT_K4d2RfRlf47natt1KU-8VwJ8z9-WtOdQJF24G_IFV1T6o_xy9c1_1p2Yi4VgFvH9yIVGuMnn1-gazNJAYVmNyV7OvWvSHiDuQm4psP1vsGArUlcDV6ocp8XeXJC7y5RcS4GoCy06B_602XJJgOsiwEVJTXQiOkIsJs-CtIpsqEqgVw1U0PO-13H6UoDki5s-AxnxW1RoVf3-ctu6WtA5QXmIDzWhRfOo3ZmB9Ba7fzRBRBuOFp_0cIrT4uJQ4iUvZwwnnVSOVCTpR-8yGfU9sRp0umhM91d-U6mD0KQSNOP_g801TF3RF6TMe8V0NO0G3WXmfZdv3qU_Wmnke2zNEyEw8Hn1ccWF0XaNsIPJmpcZ5cPlrPunu2e5s0DSbALpBDfcgCVfvr7bZR1767fLPstkHPh4QZ-qu-5zlwvrc1osmbswUeo6Zy4jlMf5GyMaMhl6_AunKwdnZESySimtA7tixdeIR-YfrPi6nja7nScQ6Q3GE6E0X_353ERUDdS4hDNp0tQ2pBt-KdnRJW7tYWqsTmtD70IX1AIwqXymDgszBkVM8OFOEbVud1Zm76wUvAhbSJEC_csE3QRYdi152TLxTFdzdkFCsQ7BLZGkfs1kO-BguKcRATRsWmobFSObwuhCb8feeJRADQ3ajLJIzFFatYRdj1j5hdOWqYMmjVdI5cLJwnafLn1lwdjerHsp6L5qR18IXiCAw0Q8W9LX3HDYKnP-VCSchkYYDzMWDM71eQG5Msh5f3lhPvZDiCk3wgRvDV-_PWOqw7-Rsnk70JNWD9uFy7 )

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

![State Diagram]( https://img.plantuml.biz/plantuml/png/LO-x2i9044NxGFvXQY7OMqX2N4I83p3OY4L0kwYWczv4qKAR_4b4nUV6NvZv8sU3HEo6oxc7E_PI1WiPJKDepeEmBmCPBKt3D8QL4LXM7HpNRmq4r82sUC4d7O1YpF6APynGOQRPBwDnjzlkUS8N3TtOu4qZkeMAzdW7p2cX7QMKyCcA78-qOg_wbpWTLtXU8S4t2nu5Jmc-D4ifAgW25pEAESpvEd6yfaIl-KtGBlw3er9OHgPXJsPZ_li3 )

- visibility - статус видимости в публичной ленте (PUBLIC, INVITE_PENDING, IN_PACT)
- created_at - дата и время создания цели
- updated_at - когда цель последний раз изменялась

## Таблица Pact - пакт
- id - уникальный идентификатор пакта
- goal_id -  уникальный идентификатор цели
- charity_id -  уникальный идентификатор благотворительного фонда
- status - статус пакта (FORMING, AWAITING_DEPOSITS, ACTIVE, CANCELLED, COMPLETED)

![State Diagram]( https://img.plantuml.biz/plantuml/png/TPBDJi9058NtynHtRIJkNHW8LDC4AB6DBjIOHjqf2obxmCIVO3I-ILEeDYqTN-7UD_BCG45YtCtFFT-SS-_K1r5u7mtlRgaNNaL-54R3WL9dEnTKgntGeNVSSRj7j4_ypJ7dCY4Pi-OldcDRxfxtEPCNJdb1NE84HJB2AkESOwKgkK4rJXjkWCrbo-bvlXlu5ehgZ6TSGFSCZ56cj4UiPG94OK1LLIdlCf7f5hJPw3QTTjjf6LZC2LHQHalvOiKhrjwL-cl2Mci6xebZ40dIfIGFyWH3Oq2MKJBaJB1ESGrFXFmn5ffd_n2tVAsBJOzIgaZomIKsQ2ZYQSxXRkd30hnEh-q45Y2FI5B8gx4tHHODMcd3nLkIts_EqGBBHP8I2eGQhIAXGVnPzHh102ICm52r7MvYHekvlYcriM5UmAzGcrUg4rM_wL_Z0_q0 )

- created_at - создание пакта

## Таблица PactParticipant - участники пакта
- id -  уникальный идентификатор пользователя, как участника пакта
- pact_id -  уникальный идентификатор пакта
- user_id -  уникальный идентификатор пользователя
- status - статус пользователя в пакте: REQUESTED (откликнулся сам, ждёт подтверждения автора), INVITED (приглашён автором, ждёт ответа), ACTIVE, FAILED (нарушил обязательства), REJECTED, LEFT, COMPLETED
  
![State Diagram]( https://img.plantuml.biz/plantuml/png/PL9BJy904BxlhzYZcX0l7em70y4bgK55G2xYeK4F9DeOANSeYOzeGlmdvT7mA2r_OUOV-MqVKF3GJMTdliVCRBvZcszsz-LPLioMNS5_kzL-DIsxPfjsjoF4_T63pENEfN7TCEhgGfv9MdEFvZGXdrp-v5yA9BaqPeT2949QRJ1LTNkdQWagHC4Ic3dEW0VaSv-7all0heH8wJMiKAmR3RLL2dX8_fuiUHGWydQ0LNMfYdjoOLJdosDvSieV4f4N9yVauKHAYAtFNVbDLn07qJH1zMWQuNPXMV4Cy9-FfXKR6Q65MTQAAqGYhUcADrULiehv-1srFlz8q7v7LdpT1put2oaLZ7AiZmJtU80nQKdKZp326IwTc0YZrwfkqpf0Bnu-39KMUfFhQ8ThWB-qmc5MeAnAzMWjA5wo8r7gHbHwyHEzLNQ4s3hN6Bm5aHXBmuWc3JPZIoz4_ibwn8Fy0m00 )

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
- status - статус промежуточного этапа (PENDING, COMPLETED, FAILED). Вычисляется на основе связанных CheckIn: PENDING, пока нет одобренного отчёта; COMPLETED, когда хотя бы один CheckIn по этому этапу получил статус APPROVED или AUTO_APPROVED; FAILED, если дедлайн прошёл, а одобренного отчёта так и не появилось. Сам цикл попыток (отклонили → пересдали → отклонили) живёт внутри CheckIn и не отражается на статусе этапа напрямую

![state diagram]( https://img.plantuml.biz/plantuml/png/PP3V2y5G5CVVxrzn7b7BkmTPssY5BSOBabWHhhBhtUP9ad_5hoLfyo-S-nyvaoNfTcwTq_VxEJzAdXWinMe-WzfavdfYmTsc68YLnrWdrmD5AO9jr7MpNe42oBqCy8XFa3x6UCCh9H5ZgI0HQrRDhXgEeITo7y_oG3fIOIHtU0Tjx0wd9kzo8C2LuYJN66846TMs6rQRVFakLrkErKyBsHz-MJMhRpYvGue7mU_anoU1OletSfi0_phGY_62SJe2K26K0ViEdE1fP_PfaQQivF8H7UW5 )
  
- completed_at - когда закончен этап или NULL (если этап не закончен)

## Таблица CheckIn - отчёт о выполнении
- id -  уникальный идентификатор
- participant_id -  уникальный идентификатор кого, кто добавил доказательство (это поле id из PactPacticipant)
- milestone_id -  уникальный идентификатор промежуточного этапа (может быть NULL, если пользователь не указывал промежуточные этапы)
- attempt_number - номер попытки; растёт после каждого REJECTED, максимум 3
- submitted_at - когда пользователь отправил отчёт
- status -  статус отчёта (PENDING, APPROVED, REJECTED, AUTO_APPROVED)

![State Diagram]( https://img.plantuml.biz/plantuml/png/TP6nQi9W58LtmNUuOrjmwr0SYaL3iOC6QxlKKgGLXBOPQjpL9OD3wPlyXeO6O-8hdF-DF3VIO2mb1CBvptVF-MyQ4t_uuK_VtwGv7Zs_jhrRV-XF9zLAjV9mzYYrsgMuJgVLxbnBNP3PXGtibrtadzZQ6Gn298YGAl9hLUpATNlTUwUbN4hR5eRs537MfF7DTqKXHbBcUiwDq-p_np5rpRmi5vA1TsBdGYN3XWzRyDpaKoDiJeyAtVMxJmUjpYzi85ZH-RCV729I_4yiZDwjw7MqavJX8SsVGYRZJfPvHpFmb2hobE96gPHg579flAw-NfuN2s8xjm4xphGShxhSHpH6tWj_u0u0 )

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
  
![state diagram]( https://img.plantuml.biz/plantuml/png/TP312e9048RlWk-mvoYwTmZ3B8IGm3fL1waEGHdaUZU3w5IlieLIATahpBvHCqi64LsMdM-6Rtx62BYtuU5goGORpm-yAL-iVPTxF0nqJTT6bGchrPgiRpbjs-co1fEnZ4300wxuPd8FDtbYSWi5WaGrH7NiGo5ZUAh92-IG4cOA50W4t74sbP7SqP9IJelSeMbQhekBVgSH8KZGVA4IuGroXkYfIg727BzzdPRTizf_T1GomtW5nYDLdT01lsV4WgALSKY5jz2rj-zJwvenzsTulnS0 )

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

![State Diagram]( https://img.plantuml.biz/plantuml/png/ZT31Qi9040RWUo3li6V15-YX1EmYbc92LdefHSHwABKgPdDlsajxwgl4qX0npVOL_dcZpiGYGJnuMtxcct-P8B6JjKsVvygagzLoRKUFqwUPDNPYqyJtVE-kSw-wtKiLHdgeBXGSDlI2d3vGeCOMXQACmnBVo3bmOffPGOFXE8h3VgoD4Plpq0-RMk4B9NsYueUITTZ0qIkzjMoihtLlfA-E88zXXuehfRtYziQNOdslNRXVOiARs-CbB0fwRpX_nE6N0qVPANxE7wJuK2ICRoR9lpqalXVC5Wzywpy0 )

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
