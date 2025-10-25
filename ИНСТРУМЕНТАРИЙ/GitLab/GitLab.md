GitLab - платформа [[DevOps]] полного цикла, которая объединяет [[GIT]]-репозиторий,CI/CD,управление проектами, безопасность и мониторинг в одном инструменте

#### что такое GitLab в двух словах
GitLab - это альтернатива GitHub+Jenkins+Jira+SonarQube
позволяет:
- хранить код
- отслеживать задачи
- собирать тестировать и деплоить приложения
- контролировать доступ и качество кода

---

### функции GitLab для DevOps


| категория                    | что делает                           | инструменты                     |
| ---------------------------- | ------------------------------------ | ------------------------------- |
| VCS (Version Control System) | хранит код, коммиты, ветки, слияния  | Git-репозитории                 |
| Управление проектами         | трекеры задач, борды                 | issues, Milestones, Labels      |
| CI/CD                        | Автоматизация сборки, тестов, деплоя | `.gitlab-ci.yml`, GitLab Runner |
| Registry                     | Хранение контейнеров и пакетов       | GitLab Container Registry       |
| Security compliance          | проверка зависимостей, уязвимостей   | SAST DAST Dependecy Scaning     |
| Monitoring                   | Отслеживание метрик и логов          | Built-in monitoring? Prometheus |
| Acess Controll               | управление пользователями и ролями   | Groups, Members? Roles          |
пример DevOps-пайплайна в GitLab
```yaml
stages:
  - build
  - test
  - deploy

build_app:
  stage: build
  script:
    - echo "Building app..."
    - docker build -t myapp:$CI_COMMIT_SHORT_SHA .

test_app:
  stage: test
  script:
    - echo "Running tests..."
    - pytest tests/

deploy_app:
  stage: deploy
  script:
    - echo "Deploying..."
    - kubectl apply -f k8s/deployment.yaml
  only:
    - main
```

1. собирает Docker-образ
2. запускает тесты
3. деплой в k8s при слиянии в ветку main

| Функция                   | GitLab                   | GitHub                         |
| ------------------------- | ------------------------ | ------------------------------ |
| CI/CD встроен             | ✅ Да                     | ⚙️ Только через GitHub Actions |
| Self-hosted (локально)    | ✅ Да                     | ⚙️ Только GitHub Enterprise    |
| Бесплатные приватные репо | ✅ Да                     | ✅ Да                           |
| Контейнерный Registry     | ✅ Встроен                | ❌ Отдельно (GitHub Packages)   |
| DevSecOps-инструменты     | ✅ SAST, DAST, Compliance | ⚙️ Частично                    |

----
