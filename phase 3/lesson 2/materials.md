### Процессы и потоки

| | Процессы | Потоки |
|---|---|---|
| Память | Изолированы | Разделяют heap, globals |
| Создание | fork() — тяжело | std::thread — легко |
| Коммуникация | IPC (медленно) | Shared memory (быстро) |
| Изоляция | Сбой не распространяется | Сбой убивает процесс |
| Использование | Независимые программы, безопасность | Параллельные задачи |

### Анонимный пайп

```c
int fd[2]; pipe(fd);  // fd[0] = чтение, fd[1] = запись
pid_t pid = fork();
if (pid == 0) {     // дочерний
    close(fd[0]);
    const char* msg = "hello from child";
    write(fd[1], msg, strlen(msg));
    close(fd[1]);
    exit(0);
} else {            // родительский
    close(fd[1]);
    char buf[256] = {};
    read(fd[0], buf, sizeof(buf));
    printf("received: %s\n", buf);
    wait(NULL);
}
```

### Разделяемая память через mmap

```c
#include <sys/mman.h>
// Анонимная shared memory между процессами:
int* shared = (int*)mmap(NULL, sizeof(int), PROT_READ|PROT_WRITE,
                          MAP_SHARED|MAP_ANONYMOUS, -1, 0);
*shared = 0;
if (fork() == 0) { (*shared)++; exit(0); }
wait(NULL);
printf("%d\n", *shared);  // 1
munmap(shared, sizeof(int));
```

### POSIX Message Queue

```c
#include <mqueue.h>
// gcc -lrt флаг для компиляции

struct mq_attr attr = {.mq_maxmsg = 10, .mq_msgsize = 256};
mqd_t mq = mq_open("/myq", O_CREAT|O_RDWR, 0666, &attr);

// Отправить:
mq_send(mq, "hello", 5, 0);

// Получить:
char buf[256]; unsigned prio;
mq_receive(mq, buf, 256, &prio);

mq_close(mq); mq_unlink("/myq");
```

POSIX MQ возвращает дескриптор как fd → работает с `select`/`epoll`.

### Сигналы

```c
#include <signal.h>
void handler(int sig) { printf("got SIGINT\n"); }
signal(SIGINT, handler);  // Ctrl+C → вызовет handler
// Не использовать в реальном коде: signal() не thread-safe. Предпочти sigaction().
```

### Endianness

```cpp
bool is_little_endian() {
    uint16_t x = 1;
    return *reinterpret_cast<uint8_t*>(&x) == 1;
}
// x86/x64 = little endian; сеть = big endian
// htonl(x) = host to network byte order
// ntohl(x) = network to host
```
