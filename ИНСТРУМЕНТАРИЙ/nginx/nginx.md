nginx (engine-x)

NGINX - это:
- **веб-сервер ([[HTTP]]/[[HTTPS]])**
- **reverse-proxy** 
- **load balancer**
- **HTTP-cache**
- **mail-proxy (SMTP/IMAP/POP3)**

`Клиент -> nginx -> Backend (Flask,net/http,spring)`

конфигурационные файлы NGINX обычно здесь:
```Fs
/etc/nginx/nginx.conf
/etc/nginx/donf.d/*.conf
```

---

### Синтаксис
1. каждая директива заканчивается `;`
`worker_processes auto;`
2. блоки заключаются в `{ }`
```nginx
http {
	server {
		...
	}
}
```
3. коментарии начинаются с `#`

### основные контексты (contexts)

| контекст   | где используется       | назначение                                      |
| ---------- | ---------------------- | ----------------------------------------------- |
| `main`     | в корне файла main     | глобальные параметры NGINX                      |
| `events`   | внутри `nginx.conf`    | настройки соединений и воркеров                 |
| `http`     | для [[HTTP]]/[[HTTPS]] | конфигурация веб-серверов                       |
| `server`   | внутри `http`          | настройки отдельного сайта (виртуальный хост)   |
| `location` | внутри `server`        | поведение для конкретных URL-путей              |
| `upstream` | внутри `http`          | определение backend-серверов (для балансировки) |

---

### структура

```nginx
# === MAIN CONTEXT ===
user www-data;
worker_processes auto;
pid /run/nginx.pid;

# === EVENTS CONTEXT ===
events {
    worker_connections 1024;
}

# === HTTP CONTEXT ===
http {
    include /etc/nginx/mime.types;
    default_type application/octet-stream;

    # Логирование
    access_log /var/log/nginx/access.log;
    error_log /var/log/nginx/error.log;

    # Сжатие
    gzip on;

    # === UPSTREAM CONTEXT ===
    upstream backend_servers {
        server backend1:8080;
        server backend2:8080;
    }

    # === SERVER CONTEXT ===
    server {
        listen 80;
        server_name example.com www.example.com;

        # === LOCATION CONTEXT ===
        location / {
            proxy_pass http://backend_servers;
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
        }

        # Отдача статических файлов
        location /static/ {
            root /var/www/html;
        }

        # Ошибки
        error_page 404 /404.html;
    }
}
```

---

| Директива                  | Где применяется      | Описание                                    |
| -------------------------- | -------------------- | ------------------------------------------- |
| `user`                     | main                 | От имени какого пользователя работает NGINX |
| `worker_processes`         | main                 | Количество процессов обработки              |
| `worker_connections`       | events               | Максимум соединений на процесс              |
| `include`                  | http/server/location | Включение других файлов                     |
| `listen`                   | server               | Порт, на котором слушает сервер             |
| `server_name`              | server               | Доменное имя                                |
| `root`                     | location/server      | Корневая директория файлов                  |
| `index`                    | location/server      | Главный файл по умолчанию                   |
| `proxy_pass`               | location             | Прокси для backend-сервера                  |
| `access_log` / `error_log` | http/server          | Пути логов                                  |
| `gzip`                     | http                 | Включает сжатие                             |
| `rewrite`                  | location             | Перенаправление URL                         |
| `return`                   | location             | Возврат ответа (например, редирект)         |