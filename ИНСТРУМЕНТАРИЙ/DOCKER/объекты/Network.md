docker Network - это виртуальная сеть в которой контейнеры могут безопасно общаться друг с другом (сети изолированы от хоста и интернета)

тема сетей в Docker является важной для микросервисной архитектуры и интеграции с внешним миром

---
#### что происходит когда я устанавливаю докер

```bash
docker network ls
```
пример вывода:
```bash
NETWORK ID     NAME      DRIVER    SCOPE
d4f1b2e...     bridge    bridge    local
a8c9e1d...     host      host      local
9c8a1f3...     none      null      local
```

----

### типы сетей в Docker


| тип сети   | описание                                                                                                                                                      | применение                             |
| ---------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------------------------------------- |
| **bridge** | по умолчанию: контейнеры на одном хосте могут общаться между собой через [[NAT]] - Bridge сеть **виртуальная** и не имеет доступа в физическую локальную сеть | локальная разработка                   |
| host       | контейнер использует сеть хоста напрямую                                                                                                                      | повышение производительности (без NAT) |
| none       | контейнер изолирован **без сети**                                                                                                                             | полная изоляция                        |
| overlay    | сеть между разными хостами(в Docker Swarm или k8s)                                                                                                            | микросервисы на нескольких серверах    |
| macvlan    | контейнер получает [[MAC]]-адрес как физическая машина                                                                                                        | интеграция с реальной локальной сетью  |

---
### bridge-сеть

По-умолчанию все контейнеры подключаются к сети `bridge`

```bash
docker run -d --name web nginx
docker run -d --name database mysql
```
Контейнеры не видят друг друга по имени, если они не в одной пользовательской сети

### создание сети
чтодбы контейнеры могли дружить по имени создается своя сеть:
```bash
docker network create mynet
```
теперь запускаем контейнеры в ней:
```bash
docker run -d --name web --network=mynet nginx 
docker run -d --name db --network=mynet mysql
```
теперь внутри web контейнера
```bash
ping db
```
или в конфиге Nginx/Mysql везде можно указать [[IP]] или [[DNS]] - Docker разрешает использовать имя как DNS

---

### как посмотреть кто в сети

```bash
docker network inspect mynet
```
вывод покажет Ip контейнеров и их имена

----

#### подключение контейнера к нескольким сетям

контейнер может быть членом нескольких сетей одновременно

```bash
docker network create backend 
docker network create database

docker run --name app --network=backend nginx
docker network connect database app
```
теперь `app` доступен и из backend и из database

---

#### проброс портов (Forwading ports)
чтобы контейнер был виден вне Docker нужен проброс порта:
```bash
docker run -d -p 8080:80 nginx
```
-> порт 80 внутри контейнера становится доступен на `localhost:8080`

---
### host network

```bash
docker run --network=host nginx
```
контейнер использует сетевой стек хоста - без [[NAT]] 
изоляции нету и порты могут конфликтовать

----

#### None network - полная изоляция

```bash
docker run --network=none nginx
```
контейнер без сети - подходит для безопасных sandbox тестов

----

#### overlay network
используется Docker Swarm и kubernetes
позволяет контейнерам на разных физических/виртуальных машинах общаться по одной виртуальной сети
```bash
docker network create -d overlay my_overlay
```
-> контейнер на сервере `А` может пинговать контейнер на сервере `Б`


| команда                        | назначение           |
| ------------------------------ | -------------------- |
| `docker network ls`            | показать все сети    |
| `inspect <name>`               | детали сети          |
| `create <name>`                | создать сеть         |
| `rm <name>`                    | удалить сеть         |
| `connect <net> <container>`    | подключить контейнер |
| `disconnect <net> <container>` | отключить контейнер  |

---
##### как создать сеть с определеным типом

```bash
docker network create --driver bridge bridge_net
docker network create --driver overlay overlay_net
docker network create --driver none none
docker network create --driver host host_net
docker network create --driver macvlan MacVlan_net
```

----

#### как работает DNS внутри Docker
Docker создает встроенный DNS-сервер (127.0.0.1)
каждый контейнер региструет свое имя в этой сети
поэтому ты можешь обращаться к контейнеру по его имени

-----

### безопасность и ограничение сети
- контейнеры в разных сетях изолированы
- можно создавать Firewall
- для ограничений между контейнерами

----

#### что должен знать DevOps

## 15. Что должен знать DevOps про Docker Network

✅ Как создавать и подключать сети (`bridge`, `overlay`)  
✅ Как контейнеры общаются через DNS  
✅ Как работает проброс портов (`-p`)  
✅ Как соединить сервисы (`nginx ↔ backend ↔ db`)  
✅ Как дебажить (`docker exec`, `ping`, `curl`)  
✅ Как ограничить доступ между контейнерами  
✅ Как использовать networks в `docker-compose.yml`