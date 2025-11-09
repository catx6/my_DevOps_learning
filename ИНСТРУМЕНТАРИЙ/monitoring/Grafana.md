```bash
sudo apt-get install -y apt-transport-https software-properties-common wget
wget -q -O - https://packages.grafana.com/gpg.key | sudo apt-key add -
echo "deb https://packages.grafana.com/oss/deb stable main" | sudo tee /etc/apt/sources.list.d/grafana.list
sudo apt-get update
sudo apt-get install grafana -y
```
Запуск
```bash
sudo systemctl start grafana-server
sudo systemctl enable grafana-server
```
-> http://localhost:3000

----

##### добавление Prometheus как источник данных
1. **Grafana->Configuration->Data Sources->Add data source**
2. select Prometheus
3. в поле **URL** ввести
```net
http://localhost:9090
```
4. **Save & Test** - должно показать **Data source is working**

---
#### добавление Dashboard
Grafana поддерживает готовые дашборды
1. в Grafana зайди в **Dashboards** -> **Import**
2. вставь ID дашборда из [https://grafana.com/grafana/dashboards/](https://grafana.com/grafana/dashboards/)
3. примеры популярных:
	- Node Exporter Full (Linux метрики) ID `1860`
	- Prometheus 2.0 Overview ID `3662`
	- kubernetes Cluster Monitoring ID `6417`
4. выбери источник данных - Prometheus