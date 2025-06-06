# memory

## stack

the stack is used to store automatically allocated values and function memory
the stack grows downward - first in, last out

- position within the stack is tracked via the stack pointer
- each function has its own stack frame
    - contains local variables, parameters, return value
    - once execution of a function completes, its stack frame is popped from the stack and the stack pointer returns to the previous stack entry

## heap

instead of automatically allocated values, the heap stores dynamic memory
the heap is much larger than the stack and grows upward
memory in the heap persists regardless of the stack frame in which it was allocated

### dynamic memory

we can use dynamic memory to create a dynamically sized chunk of memory at run time
this is **much** safer than VLAs:
- stack space is small
- it is inefficient
- allocation can exceed the size of the stack space itself

- `malloc(size_t n)`:
    - dynamically allocates heap memory of the specified number of bytes
    - returns a pointer to the allocated memory
- `calloc(size_t size, size_t length)`:
    - dynamically allocates heap memory of the specified number of bytes, initialises all values to 0 (or the equivalent of)
    - returns a pointer to the allocated memory
- `realloc(void *buf, size_t size)`:
    - reallocates a chunk of dynamically allocated memory to be the new size
    - copies all values from the previous chunk
    - returns a pointer to the allocated memory
