[[утилиты]]
```bash
find [путь] [условия] [действия]
find /home -name "*.txt" #все файлы в home с расширением .txt
find . -iname "*.txt" #тоже самое только не чувствителен к регистру
find . -type f #найти все файлы
find . -type d #найти все директории
find . -type l #найти все символьные ссылки
find . -mtime N #изменен N дней назад
find . -mtime +7 #старше 7 дней
find . -mtime -1 #изменен менее дня назад
find . -atime N #последний доступ 
find . -ctime N #время изменения метаданных 
find . -size +100M #больше 100 мегабайт
find . -size -1k #меньше 1 килобайта
find . -size +1G #больше 1 гигабайта
find . -user username
find . -group groupname
```
логика:
`! -not` - НЕ
`-or` - ИЛИ
`-and  ` - или пробел - И

----

#### действия
```bash
find . -name "*.tmp" -delete #удалить все файлы с расширением .tmp
find . -name "*.tmp" -print #вывести все файлы с расширением .tmp
find . -name "*.log" -exec <command> {} \ #выполнить команду над каждым найденным файлом
find . -name "*.tmp" -exec <command> {} + #над группой файлов
```

---
#### -depth
по умолчанию порядок идет сверху-вниз
```bash
testdir
testdir/subdir
testdir/subdir/file.txt
```
но с `-depth`
```bash
testdir/subdir/file.txt
testdir/subdir
testdir
```