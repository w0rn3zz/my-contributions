# Личный вклад в командный проект

Автор: **Антон Загайнов** ([`w0rn3zz`](https://github.com/w0rn3zz))  
Стек: Go, PostgreSQL, SQL-миграции, HTTP API, JWT, Docker Compose, Ollama, React, TypeScript, SCSS, Playwright, GitHub Actions.

Этот репозиторий — самостоятельная подборка моего личного вклада в командный проект. Он не заменяет полный исходный код продукта: в папках находятся исходники законченных функций и тесты/миграции, а в `provenance/original-commits.patch` — точные патчи моих ключевых коммитов. Так можно посмотреть и итоговую структуру кода, и историю каждого изменения.

## Масштаб вклада

- **77 коммитов (non-merge)** от `w0rn3zz <w0rn3zz@gmail.com>`;
- **865 изменений файлов**, суммарно около **24 990** добавленных и **9 694** удалённых строк во всех этих коммитах;
- работа от начального каркаса и архитектуры до Go-бэкенда, PostgreSQL-миграций, AI-интеграции, React-интерфейса, e2e-тестов, Docker-окружения и CI.

Полный список со ссылками на каждый исходный коммит: [`provenance/commit-index.md`](provenance/commit-index.md).

Полный экспорт всех затронутых исходников, тестов, миграций и конфигурации: [`full-contribution`](full-contribution).

## Роль в команде: технический лидер

Я не только реализовывал отдельные задачи, но и брал на себя отвественность за принятие технических решений на протяжении всего пути проекта.

- определил базовый технологический стек: Go и стандартный `net/http` для backend, PostgreSQL для постоянных данных, Docker Compose + Makefile для воспроизводимого локального запуска, Ollama для локальной генеративной модели;
- участвовал и принимал финальные решениея в проектировке границ модулей, доменных моделей, HTTP-контрактов до параллельной разработки frontend и backend;
- декомпозировал работу на самостоятельные задачи и принимал интеграционные решения между API, БД, AI, UI/UX
- проводил техническое ревью изменений, выравнивал реализацию с архитектурой и закрывал интеграционные/регрессионные проблемы;
- отвечал за готовность продукта к запуску и демо: окружение (локальный туннель + Ollama), документацию, seed-контент, проверки качества и предсказуемый сценарий демонстрации.

## Архитектура и старт проекта

- подготовил первоначальную структуру репозитория, правила локальной разработки и единый Docker Compose + Makefile;
- сформировал предметную модель: Ролевые ветки, Темы, Уровни, Сценарии, Прохождения, Result, Прогресс и Достижения;
- продумал layered архтиктуру на backend, разделил на `core` и независимые продуктовые возможности (`auth`, `scenarios`, `attempts`, `learning`), где HTTP транспорт, сервис и PostgreSQL-репозиторий имеют ясные границы;
- зафиксировал существенные решения через ADR: локальный AI через Ollama, JWT в HttpOnly-cookie, разделённые AI-роли, правила структуры feature-модулей;
- описал OpenAPI-контракт и связал его с HTTP-contract тестами, чтобы frontend и backend могли разрабатываться согласованно.

Исходные коммиты: [`04d8289`](https://github.com/Codiki-lab/anti-scam-trainer/commit/04d8289), [`0c5d225`](https://github.com/Codiki-lab/anti-scam-trainer/commit/0c5d225), [`c52b920`](https://github.com/Codiki-lab/anti-scam-trainer/commit/c52b920), [`c73ff0f`](https://github.com/Codiki-lab/anti-scam-trainer/commit/c73ff0f), [`312abf1`](https://github.com/Codiki-lab/anti-scam-trainer/commit/312abf1).

## Инфраструктура и DevOps

- настроил Docker Compose c PostgreSQL, API, Nginx gateway, socat и отдельным профилем Ollama; подготовил Dockerfile'ы для сборки front-end и back-end
- подготовил Makefile для настройки окружения, сборки, миграций, старта/остановки сервисов, запуска Ollama, очистки и demo reset;
- организовал versioned SQL-миграции с проверкой `up/down/up`, включая миграции на исторических данных;
- добавил Nginx-проксирование `/api`, credentialed CORS и безопасную передачу клиентского IP только через trusted proxy;
- внедрил CI, `golangci-lint`, Go/frontend-тесты, gateway regression и проверки для соблюдения чистоты локальной разработки.

Исходные коммиты: [`a9b766a`](https://github.com/Codiki-lab/anti-scam-trainer/commit/a9b766a), [`bb61188`](https://github.com/Codiki-lab/anti-scam-trainer/commit/bb61188), [`9ab4806`](https://github.com/Codiki-lab/anti-scam-trainer/commit/9ab4806), [`df989c6`](https://github.com/Codiki-lab/anti-scam-trainer/commit/df989c6), [`614fa45`](https://github.com/Codiki-lab/anti-scam-trainer/commit/614fa45).

## Backend, данные и API

### Аутентификация и роли доступа

- локальная регистрация и вход Пользователя;
- JWT в защищённой HttpOnly-cookie;
- middleware аутентификации и разграничение ролей `user` / `admin`;
- PostgreSQL-миграция локальных учётных записей;
- архитектурное решение о хранении signed access token в cookie.

Исходный коммит: [`5de2cc2`](https://github.com/Codiki-lab/anti-scam-trainer/commit/5de2cc2) — `feat: add local authentication and access roles`.

Код: [`contributions/authentication-and-access`](contributions/authentication-and-access).

### Игровой цикл тренажёра и управление контентом

- старт и восстановление Прохождения, последовательные Шаги сценария и подсчёт Балла/Звёзд;
- варианты ответа и свободный текст, история диалога, завершение и Result;
- PostgreSQL-репозитории для Прохождений и Сценариев;
- административные HTTP-методы управления учебным контентом;
- JSON-сериализация DTO, единый decode/validate слой запросов и стабильные HTTP-ошибки для frontend-клиента;
- миграции игрового цикла и полного MVP.

Исходные коммиты: [`1de77d6`](https://github.com/Codiki-lab/anti-scam-trainer/commit/1de77d6), [`1dba119`](https://github.com/Codiki-lab/anti-scam-trainer/commit/1dba119).

Код: [`contributions/training-game-cycle`](contributions/training-game-cycle).

### Обучение, Ежедневное задание и надёжность API

- Dashboard, Теория, Quiz, Прогресс и Достижения;
- персонализированное Ежедневное задание и учёт Серии дней;
- сохранённая `continue_action` для следующего учебного действия;
- bounded rate limits регистрации, входа и AI-операций;
- SQL-миграции, HTTP-contract и content acceptance-тесты.

Исходные коммиты: [`d8288a6`](https://github.com/Codiki-lab/anti-scam-trainer/commit/d8288a6), [`8eea4e8`](https://github.com/Codiki-lab/anti-scam-trainer/commit/8eea4e8), [`4805a4e`](https://github.com/Codiki-lab/anti-scam-trainer/commit/4805a4e).

Код: [`contributions/learning-and-daily-task`](contributions/learning-and-daily-task).

## Локальная AI-интеграция и безопасность сценариев

- выбрал и интегрировал Ollama как локальный AI provider, что позволяет запускать демонстрацию без внешнего облачного API;
- реализовал два раздельных контура: генератор реплик виртуального собеседника и оценку Ответов пользователя;
- спроектировал структурированный JSON-контракт между приложением и моделью, валидацию каждого ответа до записи в БД и безопасный fallback при некорректном выводе;
- ограничил историю и контекст AI, ввёл политику для генерации и защиту от параллельных запросов/перегрузки;

Исходные коммиты: [`ebbf0c8`](https://github.com/Codiki-lab/anti-scam-trainer/commit/ebbf0c8), [`2eeae63`](https://github.com/Codiki-lab/anti-scam-trainer/commit/2eeae63), [`d1def88`](https://github.com/Codiki-lab/anti-scam-trainer/commit/d1def88), [`3447e77`](https://github.com/Codiki-lab/anti-scam-trainer/commit/3447e77).

Код: [`contributions/ai-training-engine`](contributions/ai-training-engine).

## Frontend и пользовательский опыт (доработка)

- доработал адаптивный чат тренировки с восстановлением состояния, обработкой ошибок и подтверждением действий;
- доработак экран Result с разбором решений и безопасными альтернативами;
- редизайн состояния прогресса, списка Уровней и общая UI-основа;
- e2e-проверка preview flow;
- CI pipeline: Go-тесты, frontend-проверки и статический анализ.

Исходные коммиты: [`cf14df4`](https://github.com/Codiki-lab/anti-scam-trainer/commit/cf14df4), [`6d04a72`](https://github.com/Codiki-lab/anti-scam-trainer/commit/6d04a72), [`0b5abf1`](https://github.com/Codiki-lab/anti-scam-trainer/commit/0b5abf1), [`614fa45`](https://github.com/Codiki-lab/anti-scam-trainer/commit/614fa45).

Код: [`contributions/frontend-learning-experience`](contributions/frontend-learning-experience), CI — [`contributions/ci-and-quality`](contributions/ci-and-quality).

## Тестирование, наблюдаемость и готовность к демо

- добавил unit-тесты доменных правил и сервисов, HTTP-contract тесты, integration/content acceptance-тесты и frontend e2e-проверки;
- реализовал middleware наблюдаемости: `X-Request-ID`, структурное логирование, измерение задержки и recovery от panic;
- добавил Swagger UI с HTTP Basic Auth и версионированный OpenAPI-файл;
- подготовил детерминированного demo-пользователя и `demo-reset`, чтобы демонстрация всегда начиналась в воспроизводимом состоянии;
- проверил устойчивость сценариев: stale step, cooldown/rate limit, восстановление Прохождения, rollback миграций и AI fallback.

Исходные коммиты: [`39f1b78`](https://github.com/Codiki-lab/anti-scam-trainer/commit/39f1b78), [`5aa82e7`](https://github.com/Codiki-lab/anti-scam-trainer/commit/5aa82e7), [`46465b1`](https://github.com/Codiki-lab/anti-scam-trainer/commit/46465b1), [`1d7f453`](https://github.com/Codiki-lab/anti-scam-trainer/commit/1d7f453), [`614fa45`](https://github.com/Codiki-lab/anti-scam-trainer/commit/614fa45).

## Структура

```text
contributions/
├── authentication-and-access/       # Snapshot исходного коммита по auth
├── training-game-cycle/             # игровая механика и контент
├── ai-training-engine/              # Ollama, AI-политики и учебные материалы
├── learning-and-daily-task/         # обучение, задание дня, rate limits
├── frontend-learning-experience/    # React UI тренажёра
└── ci-and-quality/                  # CI, линтер и команды проверки
full-contribution/                   # полный срез всех затронутых файлов
├── current/                          # 237 файлов в актуальном состоянии
└── historical/                       # 64 перенесённых/удалённых исходника с provenance
provenance/
├── original-commits.patch           # точные патчи 13 ключевых личных коммитов
└── commit-index.md                  # все 77 прямых личных коммитов со ссылками
```

## Как сверить авторство

Каждый раздел выше содержит ссылки на оригинальные коммиты в командном репозитории. Локально список моих коммитов можно получить так:

```bash
git log --author='w0rn3zz@gmail.com' --no-merges --oneline
```
