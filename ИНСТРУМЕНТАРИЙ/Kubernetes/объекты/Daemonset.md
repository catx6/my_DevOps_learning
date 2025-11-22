DaemonSet - контроллер кубернетес который гарантирует:
-> на каждой ноде в кластере будет запущен 1 экземпляр **Pod**
или
-> на каждой ноде, подходящий по меткам

используется для системных сервисов:
- node-exporter
- filebeat/fluentd/fluent-bit (логирование)
- kube-proxy
- CNI плагины
- мониторинг, агенты, сетевые демоны, хранилища

```yaml
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: node-exporter
spec:
  selector:
    matchLabels:
      app: node-exporter
  template:
    metadata:
      labels:
        app: node-exporter
    spec:
      containers:
        - name: node-exporter
          image: prom/node-exporter:latest
          ports:
            - containerPort: 9100
```
каждый узел -> 1 Pod node-exporter

