# Microservice template (FastAPI + Postgres + SQLAlchemy + Alembic)

Шаблон микросервиса: **FastAPI** (API), **SQLAlchemy async** (ORM), **Postgres** (DB), **Alembic** (миграции), Docker/Docker Compose.

## Быстрый старт (Docker)

1) Поднять сервис вместе с базой:

```bash
make docker-up
```

2) Проверить health-check:

```bash
curl -s http://localhost:8000/health
```

## Быстрый старт (локально, venv)

1) Создать окружение и установить зависимости:

```bash
make install
```

2) Создать `.env` (файл с переменными окружения):

В репозитории лежит пример `example.env` — скопируйте его в `.env` и при необходимости поправьте значения.

3) Запустить сервис:

```bash
make run
```

## Конфигурация

Основные переменные окружения:

- **`DATABASE_URL`**: строка подключения SQLAlchemy (async), пример: `postgresql+asyncpg://postgres:postgres@db:5432/postgres`
- **`APP_NAME`**: имя приложения
- **`API_PREFIX`**: префикс для роутов (по умолчанию пустой)

Настройки читаются в `app/core/config.py`.

## Health-check

Эндпоинт:

- `GET /health` — возвращает `{"status":"ok"}` и делает `SELECT 1` в БД.

## Миграции (Alembic)

Alembic настроен на автогенерацию миграций из `Base.metadata` (модели лежат в `app/models/`).

### Локально (venv)

Перед командами убедитесь что:
- зависимости установлены (`make install`)
- задан `DATABASE_URL` (через `.env` или экспорт переменной)

Создать миграцию (autogenerate):

```bash
make mig-create MSG="create users table"
```

Накатить миграции:

```bash
make mig-up
```

Посмотреть текущую ревизию:

```bash
make mig-current
```

### В Docker

Накатить миграции внутри контейнера приложения:

```bash
docker compose exec app alembic upgrade head
```

Создать миграцию (autogenerate) внутри контейнера:

```bash
docker compose exec app alembic revision --autogenerate -m "message"
```

## Структура

- `app/main.py` — точка входа FastAPI
- `app/api/health.py` — health-check endpoint
- `app/db/session.py` — async engine/session
- `app/models/` — модели (пример: `User`)
- `alembic/` + `alembic.ini` — миграции
- `docker-compose.yml` — app + postgres + healthchecks


