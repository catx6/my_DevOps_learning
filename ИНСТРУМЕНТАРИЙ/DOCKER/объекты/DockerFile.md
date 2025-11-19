DockerFile - это инструкция для сборки [[Image]]
это рецепт из слоев описывающие 
- из какого базового образа начать
- установочные пакеты
- какие файлы скопировать
- какие команды запустить
- какой процесс выполнить при старте контейнера


| команда       | назначение                                                            | пример                                          |
| ------------- | --------------------------------------------------------------------- | ----------------------------------------------- |
| `FROM`        | указывает базовый образ                                               | `FROM ubuntu:22.04`                             |
| `WORKDIR`     | устанавливает рабочую директорию                                      | `WORKDIR /usr/src/app`                          |
| `COPY`        | копирует файлы в контейнер                                            | `COPY . /app`                                   |
| `ADD`         | то же но может скачивать URL или распоковал архив                     | `ADD app.tar.gz /app`                           |
| `RUN`         | выполняет команду при сборке образа                                   | `RUN apt-get update && apt-get install -y curl` |
| `CMD`         | определяет команду запускаемую по умолчанию                           | `CMD ["python", "app.py"]`                      |
| `EMTRYPOINT`  | определяет основную команду, которую нельзя переопределить просто так | `ENTRYPOINT ["nginx", "-g", "daemon off;"]`     |
| `ENV`         | устанавливает переменные окружения                                    | `ENV PORT=8080`                                 |
| `EXPOSE`      | документирует порты которые контейнер слушает                         | `EXPOSE 8080`                                   |
| `VOLUME`      | указывает какие директории должны быть внешними томами                | `VOLUME /data`                                  |
| `USER`        | указывает от какого пользователя выполнять команды                    | `USER appuser`                                  |
| `LABEL`       | добавлять метаданные                                                  | `LABAL maintainer="devops@mail.ru"`             |
| `ARG`         | аргументы сборки, задаются при `docker build`                         | `ARG VERSION=1.0`                               |
| `HEALTHCHECK` | проверяет жив ли контейнер                                            | `HEALTHCHECK CMD curl -f http:localhost:8080`   |
| `SHELL`       | меняет используемую оболочку                                          | `SHELL ["/bin/bash", "-c"]`                     |
`изменение последнего слоя не затронут прошлые, но изменения первого изменят все`

---

### оптимизация

1. использование легких образов - `alpine`, `slim`, `distroless`
2. минимизировать кол-во слоев - объединять `RUN` команды `&&`
3. сначало зависимости потом код
4. удалять лишнее - кеши, логи, временные файлы
5. не запускать с root - создавать нового пользователя
6. хранить конфиги через ENV/ARG а не в коде
7. использовать `.dockerignore` - исключая ненужные файлы

| Ошибка                              | Почему плохо            | Как исправить                                        |
| ----------------------------------- | ----------------------- | ---------------------------------------------------- |
| Копирование `.` без `.dockerignore` | Тянет мусор в образ     | Добавь `.dockerignore`                               |
| Запуск от root                      | Опасно в проде          | Добавь `USER appuser`                                |
| Много `RUN` слоёв                   | Замедляет сборку        | Объедини `RUN apt-get update && apt-get install ...` |
| Использование `latest`              | Неконтролируемая версия | Указывай точный тег                                  |
| Хранение секретов в ENV             | Утечка данных           | Используй Docker Secrets / env-файлы                 |

---

#### docker build

`docker build -t app:version -f /path/to/dockerfile` -
- `-t задает тег`
- `-f указывает путь до dockerfile`

также при сборке можно указать архитектуру через флаг 
- `--platform linux/amd64`
- `--platform linux/arm64`
- `--platform linux/amd64`


---

#### оформление Dockerfile

для **Python**:
```Dockerfile
FROM python:3.12-slim
```
для **GO**:
```Dockerfile
FROM golang:1.23-alpine
```
для **Node.js**:
```Dockerfile
FROM node:22-alpine
```
-> slim / alpine - меньший образ, быстрее билд, меньше уязвимостей
```Dockerfile
WORKDIR /app
```
-> без WORKDIR все будет в `/`

---
сначало копировать зависимости
```Dockerfile
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt
```
для **node**:
```Dockerfile
COPY package.json package-lock.json ./
RUN npm ci
```
-> ускоряет сборку в 10 раз, кешируются зависимости

##### а потом копировать все приложение
```Dockerfile
COPY . .
```

---
#### использование многослойных образов(multi-stage)
важно для **go**, **rust**, **java**
```Dockerfile
FROM golang:1.23-alpine AS builder
WORKDIR /src
COPY . .
RUN go mod download
RUN go build -o app

FROM alpine:latest
WORKDIR /app
COPY --from=builder /src/app .
CMD ["./app"]
```
->> быстрый билд, образ 7-15М и никаких билд-инструментов внутри финального контейнера

----

#### не запускай как **root**
```Dockerfile
RUN adduser --disabled-password --gecos '' appuser
USER appuser
```

---
#### прописывай EXPOSE
```Dockerfile
EXPOSE 8080
```
->> не открывает порт а документирует

---
#### ENTRYPOINT + CMD
```Dockerfile
ENTRYPOINT ["python3"]
CMD ["main.py"]
```
**ENTRYPOINT** - фиксирован
**CMD** - можно переопределять

----

```Dockerfile
FROM python:3.12-slim

ENV PYTHONUNBUFFERED=1 \
    PYTHONDONTWRITEBYTECODE=1

WORKDIR /app

COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

COPY . .

RUN adduser --disabled-password --gecos '' appuser
USER appuser

EXPOSE 8000

CMD ["python", "main.py"]
```

---

#### в общем что такое ENTRYPOINT
- это непереопределяемая команда
при запуске контейнера можно исполнить команду из основного образа
`docker run -it <container> <command(bash)>`
и запустится bash
а с ENTRYPOINT не запустится 

---

1) exec-форма (правильная):
```Dockerfile
ENTRYPOINT ["python", "app.py"]
```
✔ не запускает shell  
✔ сигналы передаются корректно (SIGTERM, SIGINT)  
✔ контейнер правильно завершает работу

2) shell-форма (плохая):
```Dockerfile
ENTRYPOINT python app.py
```
Проблемы:
- создаётся дополнительный `/bin/sh`
- PID = sh, а не твой процесс
- сигналы SIGTERM не доходят → контейнер зависает
- уборка зомби-процессов ломается


### Итог по ENTRYPOINT

ENTRYPOINT — это основной процесс, который всегда будет запущен.

CMD — это аргументы, которые можно заменить при запуске контейнера.

----

### создание пользователей

```Dockerfile
RUN addgroup --system appgroup \
    && adduser --system --ingroup appgroup appuser

USER appuser
```

создают пользователя после копирования файлов
```Dockerfile
COPY . .
RUN adduser appuser
USER appuser
```
->> файлы уде скопированы Root, юзер не сможет прочитать или писать

### Копирование только после создания юзера
```Dockerfile
RUN adduser --disabled-password appuser
USER appuser
COPY . .
```

#### в k8s нейтральный UID важно для K8s

В k8s, OpenShift и Pod Security:
- root запрещен
- неизвестные UID тоже могут запрещаться
- лучше использовать фиксированный UID/GID
```Dockerfile
RUN addgroup --gid 1001 app \
    && adduser --uid 1001 --gid 1001 --disabled-password app
USER 1001
```
✔ воспроизводимый билд
✔ корректные права в volume
✔ безопасно
✔ подходит для cloud/Kubernetes

---
