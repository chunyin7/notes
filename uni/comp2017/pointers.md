# pointers

## memory semantics

- initialising a pointer with no address, does not set it to NULL but some arbitrary garbage memory
    - it is still dangerous to access this pointer -> will prompt segfault

- when arrays are passed to functions, the compiler only preserves knowledge that they are an array
    - hence, things like *sizeof* are not useful

- note that array indexing is fundamentally pointer arithmetic
    - `a[i] == *(a + i)`

- on x86 systems, *32-bit* vs *64-bit* refers to **the size of pointers**

- we can use void pointers to mimic generics, allows us to handle memory where we don't necessarily know the type

## function pointers

function pointers allow us to use variables to hold an address to a function
the function signature forms part of the type information for the variable, allowing the compiler to confirm an appropriate function with the same signature has been assigned

e.g.
```c
int add(int a, int b) {
    return a + b;
}

int main(void) {
    // these are the same thing
    int (*a)(int, int) = add;
    int (*a)(int, int) = &add;

    // then we can do
    int result = a(1, 3); // 4
}
```

it is common to use funciton pointers as a parameter to a function foer generalising certain operations on a collection and to act as a callback
e.g.
```c
int calc(int a, int b, int (*c)(int, int)) {
    return c(a, b);
}

int add(int a, int b) {
    return a + b;
}

int main(void) {
    int result = calc(1, 3, &add); // 4
}
```
