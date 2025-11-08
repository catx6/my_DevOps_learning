при создании уникального CLI приложения на питоне нужно парсить флаги для опций как во всех фалагах  в питоне это делается через `argparse`
```python
import argparse # импорт argparse
# создание парсера
parser = argparse.ArgumentParser(description="message") 
# добавление аргумента -m или --message
parser.add_argument("-m", "--message", type=str, help="message")
# парсинг аргументов
args = parser.parse_args()
# вывод args.message
print(f"print {args.message}")
```

`type=str` - строка
`type=int` - целое число
`type=float` - число с плавающей точкой

---

#### калькулятор CLI

```python
import argparse
parser = argparse.ArgumentParser(description="message")

parser.add_argument("-v1", "--value1", type=int, help="value1")
parser.add_argument("-v2", "--value2", type=int, help="value2")
parser.add_argument("-op", "--operation", type=str, help="value2")

args = parser.parse_args()

if args.operation == "+":
	print(f"{args.value1} {args.operation} {args.value2} = {args.value1 + args.value2}")
if args.operation == "-":
	print(f"{args.value1} {args.operation} {args.value2} = {args.value1 - args.value2}")
if args.operation == "/":
	if args.value2 == 0:
		print("err zero")
	else:
		print(f"{args.value1} {args.operation} {args.value2} = {args.value1 / args.value2}")
if args.operation == "*":
	print(f"{args.value1} {args.operation} {args.value2} = {args.value1 * args.value2}")
```
