Ansible - инструмент для автоматизации IT-инфаструктуры.
управляет серверами, разворачивает приложения, настраивать сети и т.д

Особенности:
- **безагентная архитектура** - работает по ssh
- простое описание конфигурации через **YAML**
- идемпотентность применение одного и того же плейбука несколько раз, результат будет одинаковым
- расширяемость - много готовых модулей для Linux, Windows, облаков

---

1. Control Node
машина с которой запускаются команды Ansible, ноутбук основная машина
2. Managed Nodes
Серверы которые Ansible будет настраивать нужен только **[[python]], [[SSH]]**
3. Inventory
файл или скрипт где указаны управляемые хосты. `inventory.ini`
```ini
[web]
web1.example.com
web2.example.com

[db]
db1.example.com
```
4. Playbooks
Основной способ автоматизации в YAML. каждый **playbook** содержит **plays**, а каждый **play**, описывает **tasks**, для группы серверов
```yaml
- name: Установка Nginx на web-сервера
  hosts: web
  become: yes
  tasks:
    - name: Установить Nginx
      apt:
        name: nginx
        state: present
```
5. Modules
Модули - готовые блоки кода для выполнения задач. Например:
- `apt` - установка пакетов на Debian/Ubuntu
- `yum` - установка пакетов на CentOS/RHEL
- `copy` - копирование файлов
- `template` - работа с шаблонами [[Jinja]]2
5. Roles
- позволяют структурировать большой playbook, разделяя конфигурацию на логические блоки (например роль `nginx`, `db`)

---

#### как работает Ansible
1. ты пишешь инвентарь и playbook
2. на control node запускаешь команду
```bash
ansible-playbook site.yml
```
3. Ansible подключается по SSH к серверам, проверяет текущее состояние и выполняет необходимые действия
4. если сервер настроен так как нужно изменений не произойдет тут и **идемпотентность**
---
### примеры использования
- развертывает приложения (`nginx`,`docker`,`базы данных`)
- настройка прав доступа и пользователей
- обновление пакетов и ОС
- настройка брэндмауеров и сетевых правил
- автоматизация облаков

---

### почему Ansible популярен
- не требует установки агента
- прост в изучении и чтении
- поддержка множества платформ
- можно интегрировать с CI/CD
- Масштабируемый и безопасный

----

## написание playbook & inventory
```ini
[name]
ip ansible_user=user ansible_password=pass

[web]
192.168.22.1 ansible_user=root

[db]
192.168.122.101 ansible_user=root
```
проверка:
```
ansible -i inventory.ini all -m ping
```
Если все ок то:
```stdout
192.168.122.101 | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
```

#### playbook

```yaml
---
- name: Установка и настройка Nginx
  hosts: web
  become: yes
  tasks:
    - name: Установить Nginx
      apt:
        name: nginx
        state: present
      when: ansible_os_family == "Debian"

    - name: Запустить и включить Nginx
      service:
        name: nginx
        state: started
        enabled: yes
```
запуск:
```bash
ansible-playbook -i inventory.ini site.yml
```
-> Ansible покажет шаги выполнения и отметит зеленым(OK) или желтым(changed)

-> пароли в inventory.ini опасно хранить открытом виде лучше использовать Ansible vault

---

#### ansible vault

Vault - способ шифрования файлы yaml с чувствительными данными
```bash
ansible-vault create vault.yml
```
после предложат выбрать пароль, к примеру:
```yaml
db_password: "SuperSecret123"
api_token: "ghp_xxxxx"
```
Файл сохранится в зашифрованном виде:
```bash
$ANSIBLE_VAULT;1.1;AES256
61626364...
```
Использовать в playbook можно так:
```yaml
- name: Настройка приложения
  hosts: web
  vars_files:
    - vault.yml
  tasks:
    - name: Вывести токен
      debug:
        msg: "API token: {{ api_token }}"
```
запуск:
```bash
ansible-playbook -i inventory.ini site.yml --ask-vault-pass
```
-> `--vault-password-file` если надо в отдельном файле хранить пароль

---

#### Как Ansible выполняет команды на хостах

Когда ты запускаешь playbook, Ansible:

1. Читает inventory
2. Подключается к каждому серверу по SSH
3. Копирует на него временный `.py`-скрипт
4. Выполняет его (модуль) через Python на сервере
5. Удаляет временные файлы