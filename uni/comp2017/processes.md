# processes

processes are the core building block of operating systems
*every* program is a process

a process is used to manage the **execution** and **memory** of a particular program

a process' memory layout is typically made of (from lowest address to highest):

- program text (instructions) (.text)
- initialised global and static variables (.data)
- uninitialised global and static variables (.bss)
- heap memory (from low to high)
- memory mapped .data and .text regions (shared)
- stack memory (from high to low)
- environment and args

## inter-process communication (ipc)

when we have multiple threads or processes, they often need to pass information to each other

### shared memory

processes can allocate memory to be shared between other processes either within the same family or idependently

we can use posix functions `shm_open` and `mmap`

- `mmap(void *addr, size_t length, int prot, int flags, int fd, off_t offset)`:
    - maps files or devices into memory, allowing processes to access file contents directly through memory addresses
    - **addr**: the preferred starting address for the mapping, typically set to NULL to allow the system to choose
    - **length**: the number of bytes to map
    - **prot**: the desried memory protection of the mapping:
        - *PROT_READ*: pages may be read
        - *PROT_WRITE*: pages may be written
        - *PROT_EXEC*: pages may be executed
        - *PROT_NONE*: pages may not be accessed
    - **flags**: determines the nature of the mapping:
        - *MAP_SHARED*: updates to the mapping are visible to other processes
        - *MAP_PRIVATE*: creates a private copy-on-write mapping, updates are not visible to other processes
        - *MAP_ANONYMOUS*: mapping is not backed by any file, ignores the fd parameter
    - **fd**: file descriptor of the file to map
    - **ofset**: offset in the file from which to start the mapping

- `shm_open(const char *name, int oflag, mode_t mode)`:
    - allows processes to communicate by sharing a region of memory
    - **name**: a null terminated string that names the shared memory object
    - **oflag**: flags that determine the operation:
        - *O_RDONLY*
        - *O_RDWR*
        - *O_CREAT*
        - *O_EXCL*
        - *O_TRUNC*
    - **mode**: permissions to use if a new object is created (e.g. `0666`) for read write permissions for all users
    - we remove the shared memory object via `shm_unlink(const char *name)`

### pipes

we can pass messages between processes via a **pipe**, which is particularly useful for forking processes
the `pipe` function creates a **unidirectional** pipe for the current process
we can create a pipe as such:
```c
int pipefd[2];

if (pipe(pipefd) < 0) {
    perror("pipe");
    return 1;
}
```

the integers `pipefd[0]` and `pipefd[1]` are the file descriptors for the pipe, where data written to `pipefd[1]` can be read from `pipefd[0]`

we can convert the fd to a fp via `FILE *fp = fdopen(pipefd[0], "r");`

---

## forking

we can use `fork` to create a new child process
the function will duiplicate the current calling process and its execution state and allocate separate memory

pointers and variables that are written will eventually be written to a new memory space via *copy-on-write*

a new pid is generated for the sub process that can be referred to by the parent process

- the parent receives the pid of the child
- the child receives 0

we can use these return values to distinguish between parent and child, allowing us to execute distinct logic depending on the process

```c
#include <sys/wait.h>
#include <sys/types.h>
#include <unistd.h>
// the above are part of the POSIX standard not the C standard library

int main(void) {
    pid_t pid;

    pid = fork();

    if (pid < 0) {
        perror("fork");
        return 1;
    }

    if (pid == 0) {
        // ... child logic
    } else {
        // ... parent logic

        wait(NULL); // wait for the child process to complete and terminate before terminating the parent
        // wait(NULL) implicitly waits for one of the children to finish execution
    }
}
```

we call children processes that still exist in the process table but have terminated *zombie processes*

---

## exec 

the **exec** family of functions allow a process to start an executable file
when a process calls exec, it is *replaced* in memory by the new process

- `execvp(const char *file, char *const argv[])`:
    - **file**: executable file we wish to execute
    - **argv**: NULL terminated array of strings that denotes the argument list for the program
    - **p** in execv**p** denotes the ability to run executables in the PATH
- `execv(const char *path, char *const argv[])`:
    - **path**: path to executable we wish to execute, this does not account for the PATH
    - **argv**: NULL terminated array of strings that denotes the argument list for the program

- `execlp(const char *file, const char *arg,... , (char *) NULL)`:
    - **file**: executable file we wish to execute
    - we include a list of one or more pointers to null terminated strings that represent the argument list for the program
    - **p** in execl**p** denotes the ability to run executables in the PATH
- `execv(const char *path, char *const argv[])`:
    - **path**: path to executable we wish to execute, this does not account for the PATH
    - we include a list of one or more pointers to null terminated strings that represent the argument list for the program

---

## popen & pclose

we can use `popen` to receive output from the stdout a specified process and pipe it into our own program
we pass an arg to describe what we want to do with the pipe, just like opening a file

e.g.
```c
FILE *f = popen("<program>", "r");
fread(...);

// close
pclose(f);
```

---

## threads

threads are simply a line of execution
we can create additional threads to run independently in a process until completion or it is killed off

threads have their own:

- thread id
- stack
- registers
- program counter

threads share the virtual memory of the process, which includes static memory, heap memory and program code

note that `strtok` is **not** thread safe, while `strtok_r` is

### pthreads

pthreads is a library specified by the posix standard, accessible via the `pthread.h` header file

to create a new thread we can call `int pthread_create(pthread_t *thread, const pthread_attr_t *attr, void (*start_routine), void *arg)`

- the first argument of pthread_t is used to store the thread ID
- the 2nd argument is a pointer to a pthread_attr_t struct, which can be used to specify additional options, this can be left as NULL
- the 3rd argument is a pointer to a function that the thread will execute
- the last argument is the parameter passed to the thread function

we can join threads with `int pthread_join(pthread_t thread, void **retval)`, forcing the calling thread to wait for a particular thread id to finish

### critical sections

a critical section is a piece of code that accesses a resource that is shared amongst multiple threads

#### mutex

to address critical sections we can use a mutex (mutual exclusion) lock
a **mutex** is an object in code that forces a thread to take ownership and *lock* before entering a critical section
once the mutex is locked, until unlocked by the same thread, no other thread can take ownership of the mutex

e.g.
```c
pthread_mutex_t mutex = PTHREAD_MUTEX_INITIALIZER;

void thread_handler(void *arg) {
    pthread_mutex_lock(&mutex);

    // ... critical section

    pthread_mutex_unlock(&mutex);
}
```

#### deadlocks

a deadlock is a situation in which 2 or more threads are stuck waiting trying to access a shared resource held by another, creating a cyclic dependency

a deadlock requires:

- a hold and wait
- a mutual exclusion
- a circular wait
- no pre-emption

this is especially prone to happen as we cannot determine the execution order of threads

#### semaphores

a **semaphore** is a construct that allows you to control the number of threads that have access to the shared resource

we can use semaphores for threads as such:
```c
sem_t *sem;

void *thread_handler(void *arg) {
    sem_wait(&sem); // wait, decrement the semaphore

    // ... critical section logic

    sem_post(&sem) // signal, increment the semaphore
}

int main() {
    sem_init(&sem, 0, 1); // set the value of 1, shared between threads of the same process (pshared = 0)

    sem_destroy(&sem);
}
```

#### condition variables

**condition variables** are used along with conditional checks, allowing us to block and wake threads, we use:

- `pthread_cond_wait(pthread_cond_t *restrict cond, pthread_mutex_t *restrict mutex)`:
    - this function takes a reference to the condition variable and the mutex, forcing the calling thread to sleep
    - the mutex that it had access to is atomically released, allowing another thread blocked on the same mutex to gain access
- `pthread_cond_signal(pthread_cond_t *cond)`:
    - randomly signals to wake one of the threads blocked by the condition variable
    - on being woken up, a thread regains access of the mutex that was used in it's `pthread_cond_wait()`
- `pthread_cond_broadcast(pthread_cond_t *cond)`:
    - signals all threads blocked by the condition to be awoken

---

## limits of parallelisation

**amdahl's law** gives limits on the maximum possible speedup of programs

$$
overall speedup = \frac{old execution time}{new execution time} = \frac{1}{[(1-fraction_{enhanced}) + \frac{fraction_{enhanced}}{speedup_{enhanced}}]}
$$

---

## race conditions

the order of execution of processes and threads is **unknown**
**race conditions** occur when we attempt to access/modify the same piece of data from multiple processes/threads, causing unexpected behaviour
