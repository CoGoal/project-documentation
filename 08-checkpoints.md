# Checkpoint 2

Дата: 30.09.2026

## Цель чекпоинта

Проверка анализа задачи, первичного дизайна, организации GitHub-проекта и наличия базового рабочего прототипа клиент-серверного приложения.

## 1. Анализ бизнес-требований
Подготовлены:
- [описание предметной области;](./01-context.md)
- [основные пользовательские сценарии;](./02-user-stories.md)
- [функциональные требования;](./05-requirements.md)
- [нефункциональные требования;](./05-requirements.md)

## 2. ERD

Разработана принципиальная ERD-диаграмма базы данных проекта.

Количество сущностей: 20.

[ERD Diagram](./architecture/erd.md )

## 3. API Contracts

Подготовлены предварительные API-контракты
между frontend и backend.

[Swagger/OpenAPI](./04-api-contracts.md )

## 4. Hello World

Созданы базовые приложения:

- Spring Boot backend
- React + Vite frontend

Реализован тестовый endpoint:

GET /api/hello

Ответ:

{
  "message": "Hello World!"
}

Frontend выполняет запрос к backend и отображает
полученный ответ.

[ссылка на backend](https://github.com/CoGoal/Backend/tree/main/CoGoal-main)
[ссылка на frontend](https://github.com/CoGoal/Frontend)

## 5. Roadmap

Разработан [график работ](./03-roadmap.md) проекта до начала декабря.

Основные этапы:
- Анализ и проектирование
- Авторизация
- Цели и пакты
- Отчёты и проверка
- Платежи
- Kafka и Email
- Геймификация
- Интеграционное тестирование

## 6. GitHub и командная работа

### Структура репозиториев

- [Backend repository](https://github.com/CoGoal/Backend)
- [Frontend repository](https://github.com/CoGoal/Frontend)
- [Documentation repository](https://github.com/CoGoal/project-documentation)

### Работа с ветками

Используем:
- main — стабильная версия;
- feature/* — разработка отдельных функций.

## 7. Status

| Артефакт | Статус |
|---|---|
| Бизнес требования |  Done |
| User Stories |  Done |
| Функциональные требования |  Done |
| Нефункциональные требования |  Done |
| ERD |  Done |
| API Contracts | Done |
| Spring Boot Hello World | Done |
| React + Vite Hello World | Done |
| Frontend → Backend request | Done |
| Roadmap |  Done |
| Figma | In Progress |
