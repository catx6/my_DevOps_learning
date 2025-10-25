system call - точка входа из user space в kernel space
когда программа хочет 
- прочитать/записать данные 
- выделить память 
- запустить процесс

---

|Категория|Примеры|
|---|---|
|Работа с файлами|`open()`, `read()`, `write()`, `close()`|
|Процессы|`fork()`, `execve()`, `waitpid()`|
|Память|`mmap()`, `brk()`|
|Сеть|`socket()`, `connect()`, `sendto()`, `recvfrom()`|
|Управление|`chmod()`, `chown()`, `kill()`|
|Синхронизация|`futex()`, `clone()`|
для DevOps инженера syscalls нужны не часто но чтобы выявить ошибки например в процессах почему висит приложение или зависает i/o и т.д они полезны

----

#### пример на C

```C
write(1, "Hello\n", 6);
```
на самом деле функция `write()` — это **обёртка** из стандартной библиотеки glibc,  
которая внутри вызывает **системный вызов ядра** `syscall(SYS_write, ...)`.

```C
#include <unistd.h>     // для syscall()
#include <sys/syscall.h> // для констант SYS_write
#include <stdio.h>

int main() {
    const char *msg = "Hello from syscall!\n";
    syscall(SYS_write, 1, msg, 20);  // (номер syscalls, fd=1, buf, len)
    return 0;
}
```
компиляция:
```bash
gcc syscall_example.c -o syscall_example
./syscall_example
```

### кратко
syscalls - системный вызов для разделения **user space** и **kernel space**
