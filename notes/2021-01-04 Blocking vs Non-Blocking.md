### Blocking vs Non-Blocking

- blocking / non-blocking describes the situation of one module.
- synchronous / asynchronous describes the relation between two modules.

1. Blocking, synchronous: blocking the execution until an action is done (for example [read](https://man7.org/linux/man-pages/man2/read.2.html), [write](https://man7.org/linux/man-pages/man2/write.2.html))

```c
char buf[100];
read(fd , buf, sizeof(buf)); // fd is tcp socket
```

2. Non-block, synchronous: checking if the condition is met, then we do an action (for example [read](https://man7.org/linux/man-pages/man2/read.2.html) + [poll](https://man7.org/linux/man-pages/man2/poll.2.html) / [select](https://man7.org/linux/man-pages/man2/select.2.html)).

```c
char buf[100];
struct pollfd pfds[1] = {{.id = fd, .events = POLLIN }};

while(poll(pfds, 1, -1)) {
	read(fd, buf, sizeof(buf));
}
```

3. Non-block, asynchronous: queue an action, the program runs without any blocking, scheduler will notify and run that action if the condition is met later (for example [aio_read](https://man7.org/linux/man-pages/man3/aio_read.3.html), [aio_write](https://man7.org/linux/man-pages/man3/aio_write.3.html)).
