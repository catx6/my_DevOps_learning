[[HTTP]]/[[HTTPS]]
это основа для библиотеки "net/http"

##### основа GO net/http

1. http.Handler
Интерфейс, который должен реализовать любой обработчик
```go
type Handler interface {
	serveHTTP(ResponceWriter, *Request)
}
```
2. htpp.ResponceWriter
позволяет отправлять данные клиенту
- писать тело ответа (`write`)
- устанавливать заголовки (`Header().Set`)
- Устанавливать код ответа (`WriteHeader`)
2. http.Request
структура с информацией
- метод(`Method`)
- URL (`URL`)
- Headers(`Header`)
- тело (`Body`)
- Query параметры (`URL.Query`)
---
# Минимальный сервер
```go
package main
import (
	"fmt"
	"net/http"
)

func main() {
	http.HandleFunc("/", func(w http.ResponseWriter, r *http.Request) {
		fmt.Fprintf(w, "Hello World")
	})
	
	http.ListenAndServe(":8080", nil)
}
```
# Маршрутизация
```go
http.HandleFunc("/about", aboutHandler)
```
#### Собственный Handler
```go
type MyHandler struct {}
func (h MyHandler) ServeHTTP(w http.ResponseWriter,r *http.Request) {
	w.Write([]byte("Custom handler"))
}

func main() {
	http.Handle("/custom", MyHandler{})
}
```
-> пройдя тему структур из [[golang core]] я понял что:
1. создаётся структура MyHandler
2. добавляется функция к структуре
3. вызов
---

# Чтение из Request

#### Query параметры
```go
r.URL.Query().Get("id")
```
#### Чтение JSON из Body
```go
var data MyStruct
json.NewDecoder((r.Body).Decode(&data)) 
```
#### Чтение формы
```go
r.ParseForm()
r.Form.Get("username")
```

---
# отправка ответа

#### Текст/HTML
```go
w.Header().Set("Content-Type", "text/html")
w.Write([]byte("<h1>Hello</h1>"))
```

#### JSON
```go
w.Header().Set("Content-Type", "application/json")
json.NewEncoder(w).Encode(data)
```

----

## Middleware (прослойка)
```go
func Logger(next http.Handler) http.Handler {
    return http.HandlerFunc(func(w http.ResponseWriter, r *http.Request) {
        fmt.Println(r.Method, r.URL.Path)
        next.ServeHTTP(w, r)
    })
}

func main() {
    http.Handle("/", Logger(http.HandlerFunc(index)))
    http.ListenAndServe(":8080", nil)
}
```

---

## Клиент (http.Client)

### GET запрос
```go
resp, _ := http.Get("https://example.com")
body, _ := io.ReadAll(resp.Body)
```

### POST JSON
```go
jsonData := `{"name":"Alex"}`
resp, _ := http.Post(
    "https://api.example.com",
    "application/json",
    bytes.NewBuffer([]byte(jsonData)),
)
```

### Кастомный клиент
```go
client := &http.Client{
	Timeout: 5 * time.Second,
}
```

----

# !важная часть!
```go
package main

import (
    "html/template"
    "net/http"
)

func main() {
    // 1. Раздача static файлов (css, js)
    fs := http.FileServer(http.Dir("static"))
    http.Handle("/static/", http.StripPrefix("/static/", fs))

    // 2. Маршрут на главную страницу
    http.HandleFunc("/", func(w http.ResponseWriter, r *http.Request) {
        tmpl, _ := template.ParseFiles("templates/index.html")
        tmpl.Execute(w, nil)
    })

    // 3. Запуск сервера
    http.ListenAndServe(":8080", nil)
}
```
