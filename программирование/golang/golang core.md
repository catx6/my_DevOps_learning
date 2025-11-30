golang core - это 
- базовые конструкции
- функции
- указатели
- методы и интерфейсы

----

# Циклы
```go
for {
} // бесконечный цикл
for num := range nums {
} // перебор nums в num
for n < 10 {
} //цикл по условию
```
->> в go нету while поэтому используется for

---

```go
m := map[string]int{"a": 1, "b":2, "c":3}
for key, value := range m {
	fmt.Println(key, value)
} 
```
1. m := map[string]int{"a": 1, "b":2, "c":3}
map - функция создающая key-value структуру 
[string] - ключ по которому получают значение
[string]int - int обозначает тип данных значения
{"s": 1} - объект key-value
2. for key, value := range m 
key - переменная КЛЮЧА по которому получается значение
value - переменная значения из m


```go
m := make(map[string]int) // создание через make
m["age"] = 20 // добавление
delete(m, "age") // удаление
```
-> также можно через литерал m := map[int]int{1:2}
к литералам можно добавлять

```go
value, ok := m["key"]
if ok {
	fmt.Println(value)
} else {
	fmt.Println("не найдено")
}
```
-> поиск по ключу

---

#### массивы

```go
var a [5]int // массив - длина и тип данных
s := [5]int{1,1,1,1,1} 
arr := [...]int{10,20,30,40,50} // автоопределение размеров  
```
-> arr[0] = 10

```go
for i := 0; i < len(arr); i++ {
} // перебор по индексу
for index, value := range arr {
} // перебор значения и индекса
```
-> как enumerate(arr)

---
#### указатели
**указатели** - это переменная, которая хранит адрес другой переменной в памяти

```go
var p *int
```
-> указатель на тип данных int

```go
x := 10 //x = 10
p := &x //0xc000012120
```
-> p хранит адрес x, 0xc000012120
& - это оператор получения адреса

```go
fmt.Println(*p) // 10
*p = 20 // меняет значение x
fmt.Println(x) // 20
```
то есть `*p` - это доступ к значению по адресу
`*` - оператор разыменования

### Зачем нужны указатели в Go
1. чтобы изменять данные внутри функции
```go
func change(x int) {
	x = 100
}
func main() {
	a := 1
	change(a)
	fmt.Println(a)
}
```
-> неправильно, функция не изменит значение

```go
func change(x int) {
	*x = 100
}
func main() {
	a := 1
	change(a)
	fmt.Println(a)
}
```
-> правильно

---

# struct

#### что такое структура
struct - это набор полей, объединенных в один тип

Аналог: *объект без методов или просто контейнер данных*
```go
type User struct {
	Name string
	Age int
}
```
теперь `User` - новый тип

---

#### для чего нужны структуры
1. для объединения данных в логическую сущность
```go
user := User{Name: "Tom", Age:20}
```
2. для описания моделей API / JSON
```go
type Product struct {
	ID    int    'json:"id"'
	Title string 'json:"title"'
	Price float64 'json:"price"'
}
```
3. для хранения конфигураций
```go
type Config struct {
    Port int
    DBUrl string
}
```
4. для работы с БД (**ORM**, **SQL драйверы**)
```go
type Order struct {
    ID     int
    Amount float64
}
```
5. как контейнер состояния в программах
```go
type Server struct {
    DB *sql.DB
    Logger *log.Logger
}
```
---
# инициализация структур
1. через литерал
```go
u := User{"Tom", 18}
```
2. через именованные поля
```go
u := User{Name: "Tom", Age: 18}
```
3. через указатель
```go
u := &User{Name: "Tom", Age: 18}
```

----

# Добавление методов
Go не имеет классов но структуры могут иметь методы
```go
func (u User) SayHello() {
	fmt.Println("Hello, ", u.Name)
}
```
Или метод, меняющий данные:
```go
func (u *User) SetAge(age int) {
    u.Age = age
}
```
Метод с `*User` изменяет сам объект - как метод классa в Java

#### передача struct: по значению и указателю
По умолчанию:
```go
u1 := User{Name: "Tom", Age: 18}
u2 := u1 // копия
```
`u2` - отдельный объект 
чтобы передать одну сущность:
```go
u := &User{Name: "Tom"}
```
---
```go
type Address struct {
    City string
    Zip  int
}

type User struct {
    Name string
    Addr Address
}
```