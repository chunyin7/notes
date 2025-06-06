# fileio in c

## reading and writing from files

there are 2 simple ways in which we can interact with files in C:
- **file descriptors**: an integer number which the OS uniquely associates with a given file
    - stdin = 0
    - stdout = 1
    - stderr = 2
- **file pointers**: we can declare a file pointer with `FILE *f = fopen(const char *path, const char *mode)`
    - modes:
        - r: read
        - r+: read and write
        - w: write, creates an empty file if file doesn't exist
        - w+: read and write, ''
        - a: append, '' 
        - a+: read and append, ''
        - rb: read binary content
        - rb+: read and write binary
        - wb: write binary, creates an empty file if file doesn't exist
        - wb+: read and write binary, ''
    - `stdin`, `stdout`, `stderr` are all file pointers declared in C

---

## stdio

### reading stdin:

- `gets(char *buf)`:
    - reads a line of input from stdin into char array, deprecated since cannot limit number of chars read

- `scanf(char \*formatted_str, address_of_variables)`:
    - reads user input as the format string and extracts values based on format specifiers
    - returns number of values read

- `fgets(char \*buf, size_t n_chars, FILE \*stream)`:
    - reads number of characters from stream into character buffer
    - returns no. of chars read, NULL if reading after EOF

### writing to stdout:
- `printf(char \*format_str, ...format)`:
    - prints formatted string into stdout
    - buffered output
