Firewall(межсетевой экран) - это фильтр сетевого трафика который контролирует какие пакеты и данные можно пропустить а какие запретить взависимости от **правил**

---

##### где работает
firewall - работает в ядре линукс конкретно в подсистеме(**netFilter**)
`netfilter` - перехватывает сетевые пакеты и обрабатывает их по заданным правилам через цепочки и таблицы


| таблица    | назначение                                          |
| ---------- | --------------------------------------------------- |
| `filter`   | основная, фильтрация пакетов(разрешить / запретить) |
| `nat`      | преобразование адресов([[NAT]], S[[NAT]], D[[NAT]]) |
| `mangle`   | изменение полей пакета                              |
| `raw`      | исключение из трекинга соединений                   |
| `security` | политики безопасности SELinux                       |

| **Цепочка** | **назначение**                                 |
| ----------- | ---------------------------------------------- |
| INPUT       | пакеты направленые в систему                   |
| OUTPUT      | пакеты исходящие из системы                    |
| FORWARD     | пакеты проходящие через систему(маршрутизатор) |
| PREROUTING  | До маршрутизации(часто для [[NAT]])            |
| POSTROUTING | После маршрутизации(часто для S[[NAT]])        |

---

#### утилиты
- ufw(L4) - для убунту/debian систем
- iptables(L3) - для ручной настройки и старых систем
- nftables(L3 современнее iptables) - современные серверы, k8s
- firewalld(L4 работает поверх `nftables` или `iptables`) - для RHEL/CENT OS/FEDORA

DevOps должен уметь:
- проверять открытые порты    
- разрешать/запрещать трафик
- настраивать NAT и маршрутизацию


---

#### команды (nftables)

все правила хранятся в таблицах внутри которых есть цепочки

```nftables
table inet filter {
    chain input {
        type filter hook input priority 0;
        policy accept;
    }
}
```

Таблицы
inet - для ipv4 v6
ip - только ipv4
ip6 - ipv6
arp,bridge - специфические

----

```bash
# Показать все правила
sudo nft list ruleset

# Удалить все текущие правила
sudo nft flush ruleset

# Добавить таблицу
sudo nft add table inet filter

# Добавить цепочку (input)
sudo nft add chain inet filter input { type filter hook input priority 0; policy drop; }

# Добавить правило (пример)
sudo nft add rule inet filter input tcp dport 22 accept
```

- разрешить только 22 80 443
```bash
sudo nft flush ruleset
sudo nft add table inet filter

# Создаём цепочку INPUT с политикой DROP
sudo nft add chain inet filter input { type filter hook input priority 0; policy drop; }

# Разрешаем localhost
sudo nft add rule inet filter input iif lo accept

# Разрешаем уже установленные соединения (важно!)
sudo nft add rule inet filter input ct state established,related accept

# Разрешаем SSH (22), HTTP (80), HTTPS (443)
sudo nft add rule inet filter input tcp dport {22,80,443} accept

# Посмотреть результат
sudo nft list ruleset
```
- разрешить вход только с определенного ip
```bash
sudo nft flush ruleset
sudo nft add table inet filter

# Цепочка INPUT с политикой DROP
sudo nft add chain inet filter input { type filter hook input priority 0; policy drop; }

# Разрешаем localhost
sudo nft add rule inet filter input iif lo accept

# Разрешаем уже установленные соединения
sudo nft add rule inet filter input ct state established,related accept

# Разрешаем SSH только с одного IP
sudo nft add rule inet filter input ip saddr 203.0.113.10 tcp dport 22 accept

# Разрешаем HTTP/HTTPS всем
sudo nft add rule inet filter input tcp dport {80,443} accept
```

------
----
----

### UFW

UFW uncomplicated firewall - упрощенная оболочка над iptables/nftables
ufw работает на l3-l4 уровне osi
все настройки хранятся в /etc/ufw

----

```bash
ufw status - показать состояние и правила
ufw enable - включить firewall 
ufw disable - выключить
ufw reset - сбросить все правила
ufw allow port - разрешить порт
ufw deny port - запретить порт
ufw delete allow port - удалить правило
ufw reload - перезапустить правила
ufw status verbose - подробный список
```

- разрешить только 22 80 443
```bash
# 1. Сбросим старые правила
sudo ufw reset

# 2. Устанавливаем политику "запрещать всё входящее"
sudo ufw default deny incoming

# 3. Устанавливаем политику "разрешать исходящее"
sudo ufw default allow outgoing

# 4. Разрешаем SSH, HTTP и HTTPS
sudo ufw allow 22/tcp
sudo ufw allow 80/tcp
sudo ufw allow 443/tcp

# 5. Включаем UFW
sudo ufw enable

# 6. Проверяем
sudo ufw status verbose
```

```bash
# 1. Сброс
sudo ufw reset

# 2. Запрещаем всё входящее
sudo ufw default deny incoming
sudo ufw default allow outgoing

# 3. Разрешаем SSH только с конкретного IP
sudo ufw allow from 203.0.113.10 to any port 22 proto tcp

# 4. Разрешаем HTTP/HTTPS всем
sudo ufw allow 80/tcp
sudo ufw allow 443/tcp

# 5. Включаем firewall
sudo ufw enable

# 6. Проверяем результат
sudo ufw status numbered
```

---

## Полезные команды
```bash
# Посмотреть нумерованные правила
sudo ufw status numbered

# Удалить правило по номеру
sudo ufw delete <номер>

# Разрешить диапазон портов
sudo ufw allow 10000:20000/tcp

# Разрешить доступ к конкретному интерфейсу
sudo ufw allow in on eth0 to any port 22

# Логирование (по желанию)
sudo ufw logging on
```