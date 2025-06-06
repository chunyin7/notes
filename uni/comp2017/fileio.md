# FILEIO IN C

## STDIO

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
