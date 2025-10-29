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
