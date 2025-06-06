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
    - file pointer operations are buffered
        - e.g. not all data to write to the stream is written immediately, C will wait to write multiple write calls at once

### reading files

- `fgets(char *buf, size_t n_chars, FILE *stream)`:
    - reads number of characters from stream into character buffer
    - will read line by line
    - returns no. of chars read, NULL if reading after EOF

- `fscanf(FILE *fptr, char *formatted_str, ...addresses)`:
    - reads a line from a file as a formatted string and extracts values based on format specifiers
    - returns number of values read

- `fread(void *buf, size_t size, size_t count, FILE *stream)`:
    - reads up to *count* objects of size *size* from *stream* to *buf*
    - can be used to read binary
    - returns the number of objects read successfully

- `fgetc(FILE *fp)`:
    - returns a single character from the file
    - at EOF returns EOF

### writing to files

- `fprintf(FILE *fp, char *format...)`:
    - writes to the file in the specified format

- `fwrite(const void *buf, size_t size, size_t count, FILE *fp)`:
    - writes *count* objects of size *size* from *buf* to *fp*

### setting position

- `fseek(FILE *fp, int offset, int whence)`:
    - can set file position at an offset from the start, end or current position
    - values for *whence*:
        - *SEEK_SET* - beginning of file
        - *SEEK_CUR* - current position
        - *SEEK_END* - end of file
    - returns 0 on success

- `lseek(int fd, int offset, int whence)`:
    - file descriptor equivalent to `fseek`
---

## stdio

### reading stdin:

- `gets(char *buf)`:
    - reads a line of input from stdin into char array, deprecated since cannot limit number of chars read
    - returns no. of chars read, NULL if reading after EOF

- `scanf(char *formatted_str, address_of_variables)`:
    - reads user input as the format string and extracts values based on format specifiers
    - returns number of values read

### writing to stdout:
- `printf(char format_str, ...format)`:
    - prints formatted string into stdout
    - buffered output
