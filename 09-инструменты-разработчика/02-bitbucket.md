# Bitbucket

> Платформа Atlassian для Git-репозиториев: хранение кода, Pull Requests, Jira-интеграция, CI/CD (Pipelines).

## Что такое Bitbucket

Bitbucket — аналог GitHub/GitLab. Содержит:
- **Bitbucket Cloud** (облако) и **Bitbucket Server/DataCenter** (self-hosted)
- Git-репозитории, **Pull Requests**, **issues**
- **Bitbucket Pipelines** (CI/CD)
- Интеграция с **Jira** (это нативная экосистема Atlassian)

## Основные возможности

| Возможность | Описание |
|-------------|----------|
| **Репозитории** | Git-хранение кода, ветки, теги |
| **Pull Requests** | Ревью кода, обсуждение, approval |
| **Code Insights** | Результаты проверок (SonarQube, тесты) в PR |
| **Pipelines** | CI/CD (сборка, тесты, деплой) |
| **Jira-интеграция** | Связь веток/PR с задачами (key: ABC-123) |
| **Wiki** | Документация проекта |
| **Permissions** | Права на репозитории, ветки, группы |

## Git-основы (кратко, для контекста)

```bash
git clone git@bitbucket.org:team/repo.git   # скопировать репозиторий
git checkout -b feature/orders-api          # создать ветку
git add .
git commit -m "ABC-123: добавлен API заказов"
git push origin feature/orders-api          # запушить → создать PR
```

### Git Flow — популярная модель веток

```
main (production)
   │
   └── develop
          │
          ├── feature/фича-1
          ├── feature/фича-2
          │
          └── release/1.2.0  →  main (тег v1.2.0)
                 │
                 └── hotfix/критичный-баг → main
```

| Ветка | Назначение |
|-------|-----------|
| `main`/`master` | Стабильная, прод |
| `develop` | Интеграционная |
| `feature/*` | Новые фичи (ответвление от develop) |
| `release/*` | Подготовка релиза |
| `hotfix/*` | Срочные исправления → сразу в main |

## Pull Request (PR) — процесс

```
1. Создали ветку feature/...
2. Внесли изменения, закоммитили, запушили
3. Bitbucket показывает: "Create pull request"
   Дано: base = main, source = feature/orders-api
4. Описываете PR, привязываете Jira-задачу
5. Ревьюер(ы) смотрят diff, комментируют, ставят approval
6. Автоматические проверки (Pipelines) выполняются
7. Слияние (merge) в main
```

## Bitbucket Pipelines (CI/CD)

Конфигурация — файл **bitbucket-pipelines.yml** в корне репозитория.

```yaml
image: node:20

pipelines:
  default:                      # на любой commit в любую ветку
    - step:
        name: Тесты
        script:
          - npm ci
          - npm test
          - npm run build

  branches:
    main:                        # специально для main
      - step:
          name: Деплой на prod
          deployment: production
          script:
            - npm ci
            - npm test
            - npm run deploy:prod
```

Этапы (steps) выполняются на **билд-агентах** в облаке Bitbucket.

### Типовые пайплайны

```yaml
pipelines:
  pull-requests:                # на каждый PR — проверки
    '**':
      - step:
          script:
            - npm test
            - npm run lint
```

## Связка Bitbucket + Jira

```
Задача в Jira: ORD-42 "Добавить эндпоинт"

Ветка:        feature/ORD-42-orders-endpoint
Commit:       ORD-42: добавлен GET /orders
PR:           ORD-42: orders endpoint

Jira сама показывает связанные коммиты и PR на задаче.
По ключу в commit/PR — автоматическая связь.
```

## Альтернативы и сравнение

| | Bitbucket | GitHub | GitLab |
|---|-----------|--------|--------|
| Разработчик | Atlassian | Microsoft | GitLab Inc |
| Интеграция | Jira, Confluence | Actions, Copilot | Полный DevOps (CI, registry) |
| CI/CD | Pipelines (встроен) | Actions (встроен) | GitLab CI (мощный) |
| Self-hosted | Server/DC (платно) | Enterprise Server | ✅ Открытая edtion |
| PR-ревью | ✅ Code Insights | ✅ Checks | ✅ MR approvals |
| Популярность | Корпоративная среда | Коммьюнити | Дискорд/enterprise |

## Вопросы для самопроверки

1. Какие основные возможности Bitbucket?
2. Какие ветки бывают во Git Flow и зачем?
3. Что такое Pull Request и какие этапы он проходит?
4. Как выглядит минимальный bitbucket-pipelines.yml?
5. Как связать коммиты и PR с Jira-задачей?