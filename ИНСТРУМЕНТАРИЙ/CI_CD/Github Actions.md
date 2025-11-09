GhA - это CI/CD инструмент встроеный в GitHub
- собирает код
- тестирует код
- деплой сборки
-> при событиях push, pull request, release

больше используется для локальной разработки

пайплайн описывается в **yaml-структуре** который хранится в репозиторие:
```bash
.github/workflows/<name>.yml
```

```yml
name: CI Pipeline

on:
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout репозитория
        uses: actions/checkout@v4
      - name: Установка Go
        uses: actions/setup-go@v5
        with:
          go-version: '1.22'
      - name: Установка зависимостей
        run: go mod tidy
      - name: Запуск тестов
        run: go test ./...
      - name: Сборка бинарника
        run: go build -o app .
```

|Ключ|Описание|
|---|---|
|`name`|Имя пайплайна (показывается в GitHub Actions UI).|
|`on`|Событие, запускающее pipeline (push, pull_request, schedule и др).|
|`jobs`|Набор задач (jobs). Могут выполняться параллельно.|
|`runs-on`|Операционная система runner’а (`ubuntu-latest`, `windows-latest`, `macos-latest`).|
|`steps`|Последовательные шаги внутри job.|
|`uses`|Использование готового **action** (например, `actions/checkout`).|
|`run`|Запуск команды в shell.|

----
