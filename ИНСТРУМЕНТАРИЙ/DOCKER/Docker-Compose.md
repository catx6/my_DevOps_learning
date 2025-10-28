Docker-Compose - это инструмент для определения и запуска множетсва контейнеров с помощью одного **YAML** файла. Для DevOps инженера он служит удобным "оркестратором" для локальной разработки

---

#### концепции
1. `docker-compose.yaml` описывает
	- [[Network]]
	- сервисы
	- [[volume]]
	- зависимости между сервисами
	- переменные окружения
	- порты и т.д
2. Services
каждый сервис это отдельный контейнер
- `web` - nginx
- `database` - база данных
- `reddis` - кэш бд в оперативной памяти
1. docker-compose
```bash
docker-compose up        # запуск всех сервисов
docker-compose down      # остановка и удаление контейнеров, сетей
docker-compose build     # сборка образов
docker-compose logs      # просмотр логов
```

----

1. пример
```yaml
version: '3.8'

services:
  web:
    build: ./app
    ports:
      - "8000:8000"
    environment:
      - DATABASE_URL=postgresql://user:pass@db:5432/mydb
    depends_on:
      - db
      - redis

  db:
    image: postgres:15
    environment:
      POSTGRES_DB: mydb
      POSTGRES_USER: user
      POSTGRES_PASSWORD: pass
    volumes:
      - db_/var/lib/postgresql/data

  redis:
    image: redis:7-alpine

volumes:
  db_
```

---

docker-compose -> не является оркестратором а удобным координатором
docker-swarm  -> оркестратор, имеет секреты хорошая поддержка и до 100 нод, может использоваться в проде
kubernetes -> Оркестратор, является промышленым и используется в проде всегда и везде, несколько сотни тысяч подов также имеет кучу систем, прокси, балансировщики, master ноды и т.д

----

### yaml файл

`version: 1.0.0` - обозначает версию
`services: ` - описывает сервисы
`service: ` - сам сервис к примеру `web`
	`build: ` - описывает сборку
	`context: ` - путь к Dockerfile
	`dockerfile: ` - имя сборного [[dockerfile]]
`image: ` - или сборка с образом
`container-name: ` - имя контейнера
`ports: - 8080:8080` - порты с локального до контейнерного
`enviroment: ` - переменные окружения
`env_file: ` - загрузка переменных из файлов
volumes:
      - static_data:/app/static     # Именованный том
      - ./logs:/app/logs            # Bind-mount
      - (локальнаяпапка → контейнер)
`restart: ` - описывает политика перезапуска
`networks: ` - описывает сети
healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:8000/health"]
      interval: 30s
      timeout: 10s
      retries: 3

----

```yaml
version: '3.8'  # Рекомендуется использовать 3.8 или новее

services:
  # === 1. Сервис: веб-приложение ===
  web:
    build:
      context: ./app                # Путь к Dockerfile
      dockerfile: Dockerfile.prod   # Имя Dockerfile (опционально)
    image: myapp:latest             # Или используй готовый образ вместо build
    container_name: myapp-web       # Имя контейнера (необязательно)
    ports:
      - "8000:8000"                 # HOST:CONTAINER — проброс порта
    environment:
      - NODE_ENV=production         # Переменная окружения
      - DB_HOST=db                  # Имя другого сервиса как хост
      - REDIS_URL=redis://redis:6379
    env_file:
      - .env                        # Загрузка переменных из файла
    volumes:
      - static_data:/app/static     # Именованный том
      - ./logs:/app/logs            # Bind-mount (локальная папка → контейнер)
    depends_on:
      - db                          # Запускать ПОСЛЕ db и redis
      - redis
    networks:
      - backend                     # Подключить к сети backend
    restart: unless-stopped         # Политика перезапуска
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:8000/health"]
      interval: 30s
      timeout: 10s
      retries: 3

  # === 2. Сервис: база данных ===
  db:
    image: postgres:15
    environment:
      POSTGRES_DB: myapp_db
      POSTGRES_USER: user
      POSTGRES_PASSWORD: securepassword123
    volumes:
      - db_data:/var/lib/postgresql/data  # Сохранение данных между перезапусками
    networks:
      - backend
    restart: always

  # === 3. Сервис: Redis ===
  redis:
    image: redis:7-alpine
    command: redis-server --appendonly yes
    volumes:
      - redis_data:/data
    networks:
      - backend

# === Тома (Volumes) ===
volumes:
  db_data:          # Именованный том для PostgreSQL
  redis_data:       # Именованный том для Redis
  static_data:      # Общий том для статики

# === Сети (Networks) ===
networks:
  backend:
    driver: bridge  # По умолчанию bridge, но можно overlay в Swarm
    # В production (Swarm/k8s) можно настроить внутренние DNS-имена и изоляцию
```

---

