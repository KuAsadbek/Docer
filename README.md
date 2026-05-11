# Docer

## -a Выводить все контейнеры в не зависомосты запущены ли они или нет
```bash
docker ps -a
```

# Выводить только запущение контейнеры
```bash
docker ps
```

# это команда выпольнает и скачивает образы
```bash
docker run python:3.12-alpine
```

# с помощю --name можно указивать название контейнера
```bash
docker run  --name it-python python:3.12-alpine
```

# это команда позволяет запускать уже скачание контейнеры по имени или по идефикатору
```bash
docker start it-python
```

# -i привязивает вывод к стандартномму потоку а имено к терминал 
```bash
docker start -i it-python
```

# чтоб остановыть контейнер используеться stop также нужно прописовать имя или идефикатор контейнера которого нужно остановить
```bash
docker stop it-python
```

# если команда stop не работает то можно с помощю kill можно убить протсес выпольнение контейнера
```bash
docker kill it-pyton
```

# -i означает интерактивный режим весь вывод будет идти в терминал
# -t дает возможность удаленного взаемодействие с тем контейнером который будет запущен
# можно писать вместе -it 
```bash
docker run -i -t it-python
```

# для удаление контейнера используеться команда rm
```bash
docker rm it-python
```

# для удаление всех контейенеров используеться prune
```bash
docker container prune
```

# для просмотра images
```bash
docker images
```

# для удаление оброзов используеться rmi нужно указивать tag
# поправочка образ нельзя удалить если к нему привязан контейнер нужно сначала удалить контейнер потом образ
```bash
docker rmi python:3.12-alpine
```

# для просмотра флагов такие как -i,-t и т.д.
```bash
docker run --help
```

## Для подключение Docker к проекту нужно создать Dockerfile 
# и указть
```bash
FROM python:3.12-apline < тут указиваеться интерпретатор >
WORKDIR /python-app < тут создаеться каталог >
COPY . . < это для копирование каталога первая . это наш фалй откуда будет копиироваться а вторая . это куда будет копироваться а иименно в /python-app >
CDM["python", '<name>.py'] < это команда запуска которая будет выпольнаться при запуске контейнера >
```

# чтоб создать образ на основе Dockerfile нужно запустить каманду 
```bash
docker build < путь к Dockerfile > -t < имя образа >
```

# пример
```bash
docker build . -t myapp:0.1
```

# чтоб запустить 
# чтоб запустить в интерактивном режиме нужно после run написать -it
```bash
docker run -it myapp:0.1
```

# при использование start в  интерактивном режиме используеться просто -i но чтоб оно работало к Dockerfile нужно добавить 
# ENV PYTHONUNBUFFERED=1 < для отключение буферезацию >
# примерь будет таким 
```bash
FROM python:3.12-apline < тут указиваеться интерпретатор >
ENV PYTHONUNBUFFERED=1
WORKDIR /python-app < тут создаеться каталог >
COPY . . < это для копирование каталога первая . это наш фалй откуда будет копиироваться а вторая . это куда будет копироваться а иименно в /python-app >
CDM["python", '<name>.py'] < это команда запуска которая будет выпольнаться при запуске контейнера >
```

# после обезательно нужно пересобрать образ
```bash
docker build . -t myapp:0.2
```

# после нужно создать контейнер к myapp:0.2
```bash
docker run --name myprog -it myapp:0.2
```

# после запустим в интерактивном режиме 
```bash
docker start -i myprog
```

## Бро допустим ты используешь стороные библиотеки которых нужно скачать через pip как жто можно сделать в Dockerfile для этого используеться RUN
# примерь
```bash
FROM python:3.12-apline < тут указиваеться интерпретатор >
RUN pip install --upgrade pip
RUN pip install --no-cache-dir requests
ENV PYTHONUNBUFFERED=1
WORKDIR /python-app < тут создаеться каталог >
COPY . . < это для копирование каталога первая . это наш фалй откуда будет копиироваться а вторая . это куда будет копироваться а иименно в /python-app >
CDM["python", '<name>.py'] < это команда запуска которая будет выпольнаться при запуске контейнера >
```

# Так как мы используем много стороных библиотек то комонда RUN нам не подходить так как нам придеться дублироввать коде так что мы можем ввынести все библиотеки pip к requirements.txt
# чтоб мы могли использовать requirements.txt нам нужно его скопировать в /python-app
# это будет выглядеть примерно так 
# после нам нужно пересобрать образ 
```bash
FROM python:3.12-apline < тут указиваеться интерпретатор >
RUN pip install --upgrade pip
ENV PYTHONUNBUFFERED=1
WORKDIR /python-app < тут создаеться каталог >
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt
COPY . . < это для копирование каталога первая . это наш фалй откуда будет копиироваться а вторая . это куда будет копироваться а иименно в /python-app >
CDM ["python", '<name>.py'] < это команда запуска которая будет выпольнаться при запуске контейнера >
```

## также кроме команды CMD мы можем импользовать ENTRYPOINT
# их отличия команды в ENTRYPOINT не перезаписоваеться и не меняеться в CMD оргументы могуть минаться 
# примерь
```bash
FROM python:3.12-apline < тут указиваеться интерпретатор >
RUN pip install --upgrade pip
ENV PYTHONUNBUFFERED=1
WORKDIR /python-app < тут создаеться каталог >
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt
COPY . . < это для копирование каталога первая . это наш фалй откуда будет копиироваться а вторая . это куда будет копироваться а иименно в /python-app >
ENTRYPOINT ["python", '<name>.py'] < это команда запуска которая будет выпольнаться при запуске контейнера >
```

# не забудь пересобрать образ
```bash
docker run --name myapp03 myapp:0.3
```

# для запуска фоновом режиме нужно добавиить -d
```bash
docker run --name myapp03 -d myapp:0.3
```

## также есть команда stats он будет отображать все работающие контейнеры 
```bash
docker stats
```

## также чтоб посмотреть логи нужно использовать logs
```bash
docker logs <name or id>
```

## Бро мы также можем работать с любым запущеним контейнером 
# но изминение будуть только в этом контейнере а образ не измениться 
# примерь 
```bash
docker exec <name or id> pip install requests
```

## Но мы можем на основе контейнера сформировать новый образ 
```bash
docker commit <container_name> <image_name>
```

## Также очень важно время от времение очищять кешь 
# с -а будет очищяться все кеши без только не используемие
```bash
docker builder prone -a
```

## если нужно удалить все контейнеры образы кеши и т.д. то можно использоввать 
```bash
docker system prone -a
```













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
