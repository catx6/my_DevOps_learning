работа golang с [[PostgreSQL]]

## Подключение к PostgreSQL через database/sql + pgx

установка драйвера **pgx**
```bash
go get github.com/jackc/pgx/v5/stdlib
```
пример подключения
```go
package main

import (
	"database/sql"
	"fmt"
	"log"

	_ "github.com/jackc/pgx/v5/stdlib"
)

func main() {
	connStr := "postgres://postgres:password@localhost:5432/mydb"
	db, err := sql.Open("pgx", connStr)
	if err != nil {
		log.Fatal(err)
	}

	err = db.Ping()
	if err != nil {
		log.Fatal("Cannot connect:", err)
	}

	fmt.Println("Connected!")
}
```