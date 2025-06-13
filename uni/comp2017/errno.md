# errno

`errno` is an integer variable declared in `<errno.h>`, which is a *thread-local variable*, where each thread gets its own copy

- `perror(const char *s)`:
    - defined in `<stdio.h>`, this prints the string `s` provided and the system's error message for the current `errno` value
    - prints directly to `stderr`

if we wish to reset the value of `errno` after an error condition, we simply use `errno = 0;`
