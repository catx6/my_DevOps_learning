1. создать [[DockerFile]] - простой убунту с пользователем **cat** 
собрать образ и выложить на [[Docker Hub]]
```dockerfile
FROM ubuntu:22.04
WORKDIR /working
RUN useradd -m ubsss
USER ubsss
```
дальше собрал командой: `docker build -t ubs:1.0.0 .`
запустил с контейнером: `docker run -it ubs:1.0.0`
2. создал image и запушил его на docker hub
```dockerfile
FROM alpine:3
CMD ["echo", "11"]
```
-> собрал, затегировал catx6/11:1.0.0