#### PYDocker
тут описывается взаимодействие с [[DOCKER]] через библиотеку `docker`

`client = docker.from_env()` - подключение локально к сокету `/var/run/docker.sock`
`client = docker.DockerClient(base_url='tcp://host:1234')` - удаленно

---

### контейнеры

```python
# список контейнеров
client.containers.list(all=True)

# создание контейнера
client.containers.run("nginx", name="web", detach=True, ports={'80/tcp': 8080})

# получение контейнера по имени или ID
container = client.containers.get("web")

container.stop()
container.start()
container.remove()
```

---

### образы
```python
# список образов
client.images.list()
# скачивание образа
client.images.pull("nginx:latest")
# создание образа из Dockerfile
client.images.build(path=".", tag="app:tag")
# удаление образа
client.images.remove("app:tag")
```

---

#### тома

```python
# создание тома
vol = client.volumes.create(name="data_vol")
# список
client.volumes.list()
# удаление
vol.remove
```

---

#### сети

```python
# создать сеть
net = client.networks.create("mynet", driver="host")
# подключить контейнер
net.connect("web")
# отключить
net.disconnect("web")
# удалить
net.remove()
```

---

#### логи и exec
```python
container = client.containers.get("web")

# получить логи контейнера
print(container.logs())

# выполнить команду внутри контейнера
exit_code, output = container.exec_run("ls /usr/share/nginx/html")
```