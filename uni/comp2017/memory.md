# memory

## stack

the stack is used to store automatically allocated local variables and function memory

the stack grows downward - last in, first out

- position within the stack is tracked via the stack pointer
- each function has its own stack frame
    - contains local variables, parameters, return value
    - once execution of a function completes, its stack frame is popped from the stack and the stack pointer returns to the previous stack entry
- note that the stack has a fixed size, hence repeated function calls or very large local variables are destroyed
- also note that stack variables are only able to be referenced within the frame they are allocated

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

## static and global data

this part of memory is used for variables that have a fixed location and exist for the entire duration of the program

it is divided into 2 subsections:

- *read only data (.rodata)*
    - this stores const variables and string literals
    - trivially, this section of data is read-only
- *iniditalised data segment (.data)*
    - this section stores all global, `static` and external variables that are explicitly initialised with a non-zero value
- *uninitialised data dsegmnet (.bss)*
    - bss (block started by symbol) holds all global and static variables that are either initialised or initialised to 0
    - the c standard guarantees that variables in this segment will be zero initialised before `main()` is called

## program code

program code stores the compiled machine code of the program, containing the compiled code of all functions, including `main()`, user-defined functions and any library functions that are linked
