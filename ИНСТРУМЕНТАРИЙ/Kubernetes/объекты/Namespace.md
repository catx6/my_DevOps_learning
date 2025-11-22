Namespace - это логичное разделение ресурсов внутри кластера Kubernetes
как папка

каждый Namespace изолирует:
- Pods
- Services 
- Deployments
- ConfigMap
- Secret
- Ingress
- RBAC (права доступа)
- Quotas (лимиты ресурсов)
Namespace - не изоляция безопасности

---

#### Зачем
1. разделение окружений
- `Dev`
- `Stage`
- `Prod`
2. разделение команд/проектов
- `frontend`
- `backend`
- `analytics`
3. разделение ресурсов
ограничить CPU/RAM для проекта
4. RBAC
разные пользователи с правами доступа
5. чистая архитектура

---

`kubectl get ns`

|Название|Для чего|
|---|---|
|**default**|Если ничего не указано|
|**kube-system**|Системные поды K8s|
|**kube-public**|Публичные данные для всех|
|**kube-node-lease**|Внутренние heartbeat-ноды|
|**ingress-nginx** (если установлен)|Ingress controller|

### создать

```bash
kubectl create ns dev
```
или через манифест
```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: dev
```
Использование [[Deployment]] в yaml
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: web
  namespace: dev
```

просмотр ресурсов 
```bash
kubectl get pods -n dev
kubectl describe svc web -n backend
kubectl get all -n prod
```
