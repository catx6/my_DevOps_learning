Ingress - это объект kubernetes, который управляет внешним доступом к сервисам внутри кластера
Он работает через ingress controller:
- Nginx Ingress Controller
- Traefik
- HAProxy
- Istio Gateway
- Kong
- Cilium ingress
в общем маршрутизирует [[HTTP]]/[[HTTPS]] трафик
```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: my-ingress
spec:
  rules:
    - host: example.com
      http:
        paths:
          - path: /
            pathType: Prefix
            backend:
              service:
                name: web-service
                port:
                  number: 80
```

- host - домен
- paths - маршруты `/`, `/api`, `/admin`
- PathType
	- Prefix - путь начинается с 
	- Exact - точное совпадение
	- ImplementationSpecific - специфично для контроллера
- backend - куда отправлять запрос
	- service name
	- port

---

## Ingress с TLS (HTTPS)
чтобы включить https
1. создать secret:
```bash
kubectl create secret tls my-tls --cert=cert.crt --key=cert.key
```
2. Ingress
```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: secure-ingress
spec:
  tls:
    - hosts:
        - example.com
      secretName: my-tls
  rules:
    - host: example.com
      http:
        paths:
          - path: /
            pathType: Prefix
            backend:
              service:
                name: web-service
                port:
                  number: 80
```
