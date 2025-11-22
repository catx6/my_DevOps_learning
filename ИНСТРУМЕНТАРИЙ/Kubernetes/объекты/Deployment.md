Deployment - контроллер управляющий **pods** через ReplicaSet
- реплики - нужное кол-во подов
- rolling updates - плавное обновление версий
- автоматический старт если **Pod** умер
- rollback - откат на прошлую версию
- селекторы и шаблоны Pod

---

минимальный рабочий пример:
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-deploy
spec:
  replicas: 3
  selector:
    matchLabels:
      app: web
  template:
    metadata:
      labels:
        app: web
    spec:
      containers:
        - name: web
          image: nginx:latest
          ports:
            - containerPort: 80
```
spec.selector - spec:
  replicas: 3
  selector:
    matchLabels:
      app: web

Deployment должен знать какие pods принадлежат ему
**Важно:** метки здесь ДОЛЖНЫ совпадать с теми, что в template.

Template - шаблон пода
```yaml
template:
  metadata:
    labels:
      app: web
  spec:
    containers:
      - name: web
        image: nginx
```

---
### Resource requests/limits

чтобы kube-scheldure знал куда ставить под:
```yaml
resources:
  requests:
    cpu: 100m
    memory: 128Mi
  limits:
    cpu: 500m
    memory: 256Mi
```
