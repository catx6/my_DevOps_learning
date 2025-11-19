```bash
git branch -r #удаленнгые ветки просмотр
git branch -a #все удаленные+локальные
git checkout -b feature origin/feature #создать локальную ветку отслеживающую удаленную
git push -u origin feature #-u связывает локальную ветку с удаленной и пушит коммит на облако github
git push origin --delete feature #удалить удаленную ветку
git push origin :feature #удалить удаленную ветку
git fetch #обновление веток но не сливаются
git fetch --prune #обновить ссылки на удаленные ветки если локально они сохранились
```

что такое origin/branch
`feature` - локальная ветка
`origin/feature` - удаленная копия этой ветки

---

