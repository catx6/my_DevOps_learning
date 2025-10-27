Docker Hub - официциальный облачный репозиторий (**registry**) где хранятся и распространяются Docker образы [[DockerFile]]

---

загружать (`push`)
скачивать (`pull`)

`docker commit <container> repo:tag` - это загрузка образа из уже запущенного контейнера - 
`sha256:e26a8e9bfb7e82d8055de0c40d92ee540a8b2bec81badda079d2fcb6e6865268`

```bash
docker commit -a "author" -m "message" <CONTAINER> [REPO:TAG]
```

| Опция            | Значение                                                |
| ---------------- | ------------------------------------------------------- |
| `-a "Автор"`     | Добавить информацию об авторе                           |
| `-m "Сообщение"` | Добавить комментарий, как commit в git                  |
| `--change`       | Применить инструкции Dockerfile (например `ENV`, `CMD`) |

#### Что происходит под капотом
- Docker берет файловую систему контейнера и все что изменилось
- создает новый слой образа
- записывает это как новый образ

`docker push [OPTIONS] NAME[:TAG]`
войти: `docker login`
**обязательно собрать образ так**
`docker build -t username/app:latest .`
	Название образа должно начинаться с твоего Docker Hub логина  
	иначе `push` не сработает (Docker Hub не разрешит загрузку).
`docker push user/app:latest`

|Команда|Что делает|
|---|---|
|`docker login`|Войти в Docker Hub|
|`docker tag`|Переименовать/добавить тег образу|
|`docker push`|Отправить образ|
|`docker pull`|Скачать образ обратно|

---

```bash
# 1. Создать образ
docker build -t myapp .

# 2. Присвоить имя для Docker Hub
docker tag myapp myusername/myapp:v1

# 3. Залогиниться
docker login

# 4. Отправить
docker push myusername/myapp:v1
```