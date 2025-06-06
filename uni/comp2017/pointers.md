# POINTERS

## memory semantics

- initialising a pointer with no address, does not set it to NULL but some arbitrary garbage memory
    - it is still dangerous to access this pointer -> will prompt segfault

- when arrays are passed to functions, the compiler only preserves knowledge that they are an array
    - hence, things like *sizeof* are not useful

- note that array indexing is fundamentally pointer arithmetic
    - `a[i] == *(a + i)`

- on x86 systems, *32-bit* vs *64-bit* refers to **the size of pointers**
