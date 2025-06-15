# device io

as per unix philosophy:

> *everything is a file* 

processes running inside unix have a table keeping track of what files they have open - file descriptor table

---

## blocking vs non-blocking io

- **blocking io**:
    - blocking functions will wait until the io action has been fully completed before proceeding to the following logic
        - e.g. `scanf` will white until we type and press enter in the terminal

- **non-blocking io**:
    - in this case, we do not wait for the io operation to complete, it will immediately grab the data it can, before proceeding
    - sometimes, it doesn't extract data at the time at all, and just queues a request to read/write to the kernel

---

## signals

signals are a way to communicate with programs that are currently running
we can press *ctrl+c* or *ctrl+z* in the shell to send SIGINT or SIGSTOP to the currently foreground program

### signal safety 

many C standard library functions are **not safe** to use inside a signal handling function (due to complex logic), e.g.

- io functions like `printf`, `fgets`, etc
- memory management `malloc`, `realloc`, `free`

if *another* signal arrives while the signal handler is running, it would not be able to be handled straight away
we also risk interacting with inconsistent or stale data if execution is interrupted, then the signal handler is called again

for the most rudimentary signal handling, we can use the *signal.h* header file
e.g.
```c
#include <signal.h>

void handle_sigint(int sig) {
    // some logic
}

int main(void) {
    signal(SIGINT, handle_sigint);

    // we can rudimentary send signals with kill
    kill(getpid(), SIGINT) // sends a SIGINT to this process
}
```

we can also encode and receive information via signals
e.g.
```c
void signint_handler(int signo, siginfo_t *info, void *context) {
    int val = info->si_value.sival_int; // we can access the encoded value as such
}

int main(void) {
    struct sigaction sig;
    memset(&sig, 0, sizeof(struct sigaction));

    sig.sa_sigaction = sigint_handler;
    sig_sa_flags = SA_SIGINFO;

    // we can encode info in a signal
    union sigval val;
    val.sival_int = 10;
    sigqueue(getpid(), SIGINFO, val); // sends sigint with 10 to this process
}
```

### signal blocking

if we want to prevent a signal from interrupting critical sections of a process we can use **signal masks**
e.g.
```c
int main(void) {
    sigset_t set, oldset;

    // initialise the signal set to empty
    sigemptyset(&set);
    // add sigint to the set
    sigaddset(&set, SIGINT);

    // block sigint and save the current signal mask
    sigprocmask(SIG_BLOCK, &set, &oldset);

    /*
     * ...critical logic
     */

    // restore the previous signal mask
    sigprocmask(SIG_SETMASK, &oldset, NULL)
}
```

---

## epoll

with an **epoll** instance, we can register various file descriptors (sockets, pipes, etc) via the kernel
we can call `epoll_wait()`, which **blocks** until *any* of the registered descriptors is ready, giving a list of 'ready' descriptors we can read/write without blocking

this simplifies instances where we want to wait for several different events, using a single loop, while being highly scalable
however, there is slight latency (usually negligible), and we are required to open descriptors in non-blocking mode and loop until they are drained

---

## select

`select()` is part of the original *Berkeley sockets API* and waits for one or more fds to become 'ready' for reading, writing or have error conditions
to use **select**, we build three fd_set bitmasks (read, write exception), add descriptors with `FD_SET()`, then call
```c
int n = select(max_fd + 1, &read_fds, &write_fds, &err_fds, &timeout);
```

after it returns, we test each set with `FD_ISSET()` to see which descriptors are ready

this approach is not very scalable, as the kernel must linearly scan all file descriptors and we must rebuild and pass the full fd_set masks every time

---

## fifos (named pipes)

a **fifo special file** in unix systems, is a kernel object that can be opened for reading/writing, but behavies like a pipe

we can use fifos to communicate between individual processes

e.g.
```c
#define FIFO_PATH "my_fifo"
if (mkfifo(FIFO_PATH, 0666) == -1) {
    perror("fifo");
    // maybe it exists; ignore or handle
}

// to write to the fifo
int fd = open(FIFO_PATH, O_WRONLY); // this blocks until a reader opens it
char *msg = "Hello\n";
write(fd, msg, sizeof(char) * strlen(msg));

close(fd); // close the fifo, this sends eof from the write end

// to read from the fifo
int fd = open(FIFO_PATH, O_RDONLY); // this blocks until a reader opens it
char buf[128];
ssize_t n = read(fd, buf, sizeof(buf)-1);
buf[n] = '\0' // null terminate the message

close(fd);
```
