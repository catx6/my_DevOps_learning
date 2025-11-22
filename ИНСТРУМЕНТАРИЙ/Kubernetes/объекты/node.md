Node - это единица вычислительного ресурса в кластере 
запускает под, сеть, хранение, мониторинг
на нем 
- kubelet
- kube-proxy
- container Runtime

Каждый Node описывается в API следующими свойствами:
- CPU
- Memory
- Labels (метки)
- Conditions (состояние узла)
- Capacity / Allocatable
- Список Pod’ов, запущенных на нём
- Taints (таинты)
