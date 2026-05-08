# Docer

# Django + Docker + Docker Compose Setup

# Цель

Подключить Docker и Docker Compose к Django проекту для:
- запуска backend в контейнере
- изоляции окружения
- удобного запуска проекта
- дальнейшего подключения PostgreSQL, Redis, Celery и Nginx

---

# Что должно быть установлено

## Windows

- Docker Desktop
- WSL2
- Ubuntu (WSL)

Проверка:

```bash
docker --version
docker compose version
```

## Структура проекта

Пример:

```bash
project/
│
├── backend/
│   ├── manage.py
│   ├── requirements.txt
│   ├── Dockerfile
│   ├── docker-compose.yml
│   ├── .dockerignore
│   └── config/
│
└── frontend/
```

## Этап 1 — Создание requirements.txt

Если файл отсутствует:

requirements.txt

Пример:

```bash
Django
djangorestframework
django-cors-headers
psycopg2-binary
Pillow
python-decouple
```

## Этап 2 — Создание Dockerfile

Создать файл:

backend/Dockerfile

Содержимое:
```bash
FROM python:3.12

WORKDIR /app

COPY requirements.txt .

RUN pip install --no-cache-dir -r requirements.txt

COPY . .

CMD ["python", "manage.py", "runserver", "0.0.0.0:8000"]
```

## Объяснение Dockerfile
FROM

Базовый image Python.

FROM python:3.12
WORKDIR

Рабочая директория контейнера.

WORKDIR /app
COPY requirements.txt

Копирование зависимостей.

COPY requirements.txt .
RUN pip install

Установка библиотек.

RUN pip install --no-cache-dir -r requirements.txt
COPY . .

Копирование backend проекта.

COPY . .
CMD

Команда запуска Django.

CMD ["python", "manage.py", "runserver", "0.0.0.0:8000"]

## Этап 3 — Создание .dockerignore

Создать файл:

backend/.dockerignore

Содержимое:

venv
__pycache__
.git
node_modules
media
staticfiles

# Для чего нужен .dockerignore

Docker НЕ будет копировать:

venv
git
кэш
лишние файлы

Это:

ускоряет build
уменьшает размер image

## Этап 4 — Создание docker-compose.yml

Создать файл:

backend/docker-compose.yml

Содержимое:

```bash
services:
  backend:
    build: .
    container_name: django_backend

    ports:
      - "8000:8000"

    volumes:
      - .:/app

    command: python manage.py runserver 0.0.0.0:8000
```
    
# Объяснение docker-compose.yml
build

Сборка image через Dockerfile.

build: .

container_name

Имя контейнера.

container_name: django_backend
ports

Связь портов:

8000:8000

Формат:

HOST:CONTAINER
volumes

Связь локальной папки с контейнером.

volumes:
  - .:/app

Изменения файлов автоматически видны внутри Docker.

command

Команда запуска Django.

command: python manage.py runserver 0.0.0.0:8000

## Этап 5 — Сборка проекта

Перейти в backend:

```bash
cd backend
```

Сборка:

```bash
docker compose build
```

# Этап 6 — Запуск проекта

Запуск:

```bash
docker compose up
```

# Запуск в фоне

```bash
docker compose up -d
```

## Этап 7 — Остановка проекта

```bash
docker compose down
```

## Этап 8 — Проверка контейнеров

```bash
docker ps
```

## Этап 9 — Просмотр логов

```bash
docker logs django_backend
```

## Этап 10 — Вход внутрь контейнера

```bash
docker exec -it django_backend bash
```

## Полезные команды внутри контейнера
Миграции
python manage.py migrate
Создание superuser
python manage.py createsuperuser
Установка новой библиотеки
pip install requests

После установки:

pip freeze > requirements.txt
Этап 11 — Проверка проекта

Открыть:

http://localhost:8000

Возможные ошибки
DisallowedHost

Добавить в settings.py:

ALLOWED_HOSTS = ['*']
CORS ошибка

Установить:

django-cors-headers

settings.py:

INSTALLED_APPS = [
    'corsheaders',
]

middleware:

MIDDLEWARE = [
    'corsheaders.middleware.CorsMiddleware',
]
Этап 12 — Следующие улучшения

После успешного запуска Django через Docker:

PostgreSQL container
Redis container
Celery
Nginx
Gunicorn
Production deployment
Финальная архитектура
Frontend React
       ↓
Nginx
       ↓
Django
       ↓
PostgreSQL

Celery ↔ Redis
Основные Docker команды
Список контейнеров
docker ps
Список images
docker images
Пересборка
docker compose up --build
Остановка контейнеров
docker compose down
Удаление image
docker rmi IMAGE_NAME
Важные правила
Не использовать Python 3.13 пока

Лучше:

FROM python:3.12
Docker хранит окружение проекта

Не нужно:

venv
локальный Python
локальный PostgreSQL

Все работает внутри контейнеров.

Docker workflow
Код
↓
Dockerfile
↓
Image
↓
Container
↓
Docker Compose
