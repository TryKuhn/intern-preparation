0–10 мин. Зачем процессы vs потоки.
Изоляция: если один процесс упадёт — другой работает. Безопасность: браузер запускает каждую вкладку в отдельном процессе. IPC дороже, но зато сбой в одном процессе не ломает другой. Потоки дешевле, но разделяют память — ошибка в одном потоке может убить весь процесс.

10–30 мин. Процессы, fork, виртуальная память.
- fork(): создать дочерний процесс — копию родителя. COW (copy-on-write): память не копируется физически до записи.
- exec(): заменить образ процесса. fork+exec = запустить программу.
- Виртуальная память: каждый процесс видит свои адреса. Карта виртуальных адресов → физические страницы. Segfault = попытка обратиться к неотображённой странице.
- Системные вызовы: интерфейс к ядру ОС. Переход в kernel mode.

30–60 мин. IPC.
```c
// Анонимный пайп:
int fd[2]; pipe(fd);
if (fork() == 0) {  // child
    close(fd[0]); write(fd[1], "hello", 5);
} else {            // parent
    close(fd[1]); char buf[10]; read(fd[0], buf, 10);
}

// POSIX shared memory:
int shm_fd = shm_open("/myshm", O_CREAT|O_RDWR, 0666);
ftruncate(shm_fd, sizeof(int));
int* p = (int*)mmap(NULL, sizeof(int), PROT_READ|PROT_WRITE, MAP_SHARED, shm_fd, 0);
*p = 42;

// POSIX message queue:
mqd_t mq = mq_open("/myqueue", O_CREAT|O_WRONLY, 0666, &attr);
mq_send(mq, "message", 7, 0);
```

60–75 мин. Endianness.
```cpp
bool is_big_endian() {
    uint16_t x = 0x0102;
    return *(uint8_t*)&x == 0x01;
}
// htonl/ntohl для сетевых протоколов (сеть = big endian)
```

75–90 мин. POSIX сообщения vs System V. Выдача ДЗ.
