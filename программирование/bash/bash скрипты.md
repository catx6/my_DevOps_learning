### синтаксис

```bash
#bin/bash

name="terra"
echo $name


```

| Переменная      | Значение                       |
| --------------- | ------------------------------ |
| `$0`            | имя скрипта                    |
| `$1`, `$2`, ... | позиционные аргументы          |
| `$#`            | количество аргументов          |
| `$@`            | все аргументы                  |
| `$?`            | код возврата последней команды |
| `$$`            | PID текущего процесса          |

```bash
x=5
y=$((x+3))
echo $y

if [ "$x" -gt 10]; then
	echo "$x больше 10"
if [ "$x" -eq 10]; then
	echo "$x равно 10"
else
	echo "x меньше 10"
fi
```

| Оператор  | Значение           |
| --------- | ------------------ |
| `-eq`     | равно              |
| `-ne`     | не равно           |
| `-lt`     | меньше             |
| `-le`     | ≤                  |
| `-gt`     | больше             |
| `-ge`     | ≥                  |
| `-f file` | файл существует    |
| `-d dir`  | каталог существует |
| `-z str`  | строка пуста       |
| `-n str`  | строка не пуста    |

```bash
for i in 1 2 3 4; do
	echo "$i"
done

for i in {1..10}; do
	echo $i
done

count=1
while [ $count -le 5]; do
	echo "$count"
	((count++))
done

until [ $count -gt 5 ]; do
	echo $count
	((count++))
```


```bash
say_hi() {
	echo $1
}

say_hi terraform
ВЫВОД:
terraform
```


```bash
read -p "name: " name
echo $name
```

```bash
echo "запись" > file.txt #перезаписать файл
echo "добавить" >> file.txt #добавить
```

```bash
set -e #выход при ошибке
set -x #отладка показывает команды
```

```bash
if [[ -d /etc]]; then
	echo "каталог существует"
fi

if [[ $USER == "root"]]; then
	echo "root"
fi
```