# Архитектурная схема

## Общая схема взаимодействия компонентов

![Архитектура системы]( https://img.plantuml.biz/plantuml/png/RLJHQjH057qVc7yOkYyL6rY4Lf-aoMPJH8HIz44GP3OPjs6papgPK8i8xOhreOZuwbUKsfLbg_ONPlx8UvFCRbBTXzsRi-UUSy_C9Rk5vagNqumDyhsSPvwluiDKYrYNIb12IJ59vH5NVCf6F9wCLLxAP91dkMB7o6iJ4l66bvb-BjvfFql7SYgaPZ5y2TMcAH3dSfm9zfuI1f-3MbD9eTY3FYKVq9V76ZnU5DXBoRfdmtmtDsPXvkQtdR7D0m74UnkC5soGfMZO2w8mY8QT7d__2TlZMXppauhQrAmNXk4EShliKXzwzipCxQcvCWjxzafkGZatF_31pg2-jETGVzrYvif-Cd_CzHQpC_XTLdDTr0EX3emJH0_3ViVW6K_bFmRdq0h1GEWBI5sQMti1yhTGBS4IQliP64iFigySK8Zred2uyyZlEEpp4ppzkoOWTmEppq3efrcm-qnCUPfvzYCmvkOQ0lm20aJ0SBL5emGkmwYFBfGaNFCHBy26GyBNoMYLDWpMnxzcWOTqB9pu_woZsiWH6zysbvAtfIXn1Rv1fkgmxMb53bG-WULPNUUTgt-PvqzvKV2AwnuWqS3VzhTH5zUCUTxZPeSeQNO9eMPNaERM1c6CsqKmMXTfCc1hjGkkBmmegQxEjI7W3hhLY73t3pTyHx9Etv9qGBlJOPGqXxKEqGBg32sW5M5No0HU57y1 )

## Описание компонентов
- Клиент (React) - веб-интерфейс пользователя
- API Gateway - единая точка входа, маршрутизация запросов к нужному сервису
- Auth-сервис - регистрация, вход, выдача и обновление JWT-токенов, сброс пароля
- Main-сервис - основная бизнес-логика: цели, этапы, пакты, участники, чек-ины, проверки, магазин, чат, поддержка
- Payment-сервис - обработка залогов и транзакций, а также отправка email-уведомлений (объединены в одном сервисе)
- auth_db / main_db / payment_db - отдельная база данных на каждый сервис (PostgreSQL)
- Брокер - очередь сообщений для асинхронного обмена событиями между сервисами (Kafka)
- API YooKassa - внешний платёжный шлюз

## Поток данных
- User → Клиент (React): пользователь взаимодействует с интерфейсом
- Клиент → API Gateway: все запросы идут через шлюз
- API Gateway → Auth / Main / Payment: маршрутизация по доменам на основе пути запроса
- Каждый сервис → своя БД: сохранение и чтение данных, сервисы не имеют доступа к чужим базам напрямую
- Payment-сервис → API YooKassa: инициация оплаты залога, приём вебхуков о результате
- Auth → Брокер → Main: событие UserRegistered — Main-сервис создаёт профиль пользователя после регистрации
- Payment → Брокер → Main: события DepositPaid / DepositFailed — обновление статуса участника и пакта
- Main → Брокер → Payment: события GoalCompleted / GoalFailed — возврат залога или перечисление в фонд, с последующей отправкой email
