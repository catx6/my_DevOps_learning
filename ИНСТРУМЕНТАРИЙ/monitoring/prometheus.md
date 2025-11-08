prometheus - это система мониторинга и сбора **метрик**, созданная в **soundcloud** и ставшая частью **CNCF**

Он:
- собирает метрики с систем и приложений через HTTP `/metrics`
- хранит их во встроенной временной базе данных time-series DB
- позволяет делать запросы через язык PromQL
- отправляет оповещения через **Alertmanager**
- имеет веб интерфейс и легко интегрируется с **Grafana**
---
#### архитектура prometheus
```arch
[Приложения / Экспортеры]
        ↓
   HTTP endpoint /metrics
        ↓
[Prometheus Server] ← scrape (по расписанию)
        ↓
[Time Series DB]
        ↓
[PromQL / Grafana / Alertmanager]
```

-> Pull-модель
Prometheus сам опрашивает (**scrape**) метрики с сервисов по HTTP (в отличии от push где данные отправляются сами)

-> Метрики - это пары `имя_метрики+лэйблы`
```pgsql
http_requests_total{method="GET", handler="/login"} 42
```
---
#### Node exporter
`node_exporter` - это готовый экспортер **системных метрик** Linux (CPU,RAM,диск,сеть)
Он:
- ставится как служба(systemd)
- открывает порт(обычно 9100)
- отдает `/metrics`, которые Prometheus собирает
```bash
curl http://localhost:9100/metrics
```
вывод:
```bash
node_cpu_seconds_total{cpu="0",mode="user"} 13452.02
node_memory_MemFree_bytes 841572352
```
---
#### библиотеки
чтобы сделать собственный endpoint `/metrics` в python
1. установка пакета
```bash
pip install prometheus-client
```
2. код
```python
from prometheus_client import start_http_server, Summary, Counter
import random, time

REQUEST_TIME = Summary('request_processing_seconds', 'Time spent processing request')
REQUEST_COUNT = Counter('request_count', 'Total number of requests')

@REQUEST_TIME.time()
def process_request():
    REQUEST_COUNT.inc()
    time.sleep(random.random())

if __name__ == '__main__':
    start_http_server(8000)
    while True:
        process_request()
```

#### GO
Prometheus написан на Golang, и для Go есть **официальный SDK**
```bash
go get github.com/prometheus/client_golang/prometheus
```
код:
```go
package main

import (
    "net/http"
    "github.com/prometheus/client_golang/prometheus"
    "github.com/prometheus/client_golang/prometheus/promhttp"
)

var opsProcessed = prometheus.NewCounter(prometheus.CounterOpts{
    Name: "myapp_processed_ops_total",
    Help: "The total number of processed events",
})

func main() {
    prometheus.MustRegister(opsProcessed)
    http.Handle("/metrics", promhttp.Handler())

    go func() {
        for {
            opsProcessed.Inc()
        }
    }()

    http.ListenAndServe(":2112", nil)
}
```

---

#### сбор логов
- Loki (от Grafana Labs) - аналог Prometheus, но для логов
- ELK-стек (Elastiansearch+Logstash+Kibana)
- Fluentd / Fluentd Bit - сбор и пересылка логов
```
Prometheus  → метрики
Alertmanager → алерты
Loki / ELK  → логи
Grafana     → визуализация всего этого
```

---
### yml
1. синтаксис и структура
2. ключевые блоки (global, targets, rules, alerts, relabeling)
```yml
global:
	...                  #общие настройки
scape_configs:
	- job_name: ...      #сбор метрик
rule_files:
	- "alerts.yml"       #алерты (отдельные файлы)
alerting:
	alertmanagers:
		- static_configs:
			  - targets: [...] #адрес Alertmanager
remote_write: [...]            #передача данных наружу
remote_read: [...]             #чтение из внешнего источника
```

#### global
общие параметры для всех jobs (если не переопределены в job_name)
```yml
global:
	scrape_interval: 15s     #как часто собирать метрики
	evaluation_interval: 15s #как часто проверять правила
	scrape_timeout: 10s      #таймаут сбора метрик
	external_labels:         #добавляются ко всем метрикам
		enviroment: "prod"
		region: "eu-west"
```

#### scrape_configs
определяет, откуда и как собирать метрики
```yml
scrape_configs:
	- job_name: "prometheus"
	  static_configs:
		- targets: ["localhost:9090"]
	- job_name: "node"
	  static_configs:
		- targets: ["localhost:9100"]
	- job_name: "python_app"
	  metrics_path: /metrics
	  static_configs:
		- targets: ["192.168.1.10:8000"]
```
