# adts in c

## structs

with **structs** we can declare a new 'type', which is a composite of existing types within a constant size in memory, defined at compilation
this is somewhat adjacent to what we can achieve with classes in OOP, however, we cannot achieve *encapsulation* or *inheritance*

we can initialise structs, like arrays, with curly braces:
```c
struct coordinate {
    int x;
    int y;
};

struct coordinate point = { 0, 0 };

// or we can specify fields
struct coordinate point = { .x = 0, .y = 0 };

// if we do
struct coordinate point = {0};
// this is equivalent to
struct coordinate point = { .x = 0 };
// p.y will be implicitly be 0'd out -> this is why 0ing out an array works the same
```

when when accessing fields of a struct pointer:
```c
// we can use
*p.x;
// or
p->x;
```

we can associate 'methods' with a struct using file pointers

---

## enums

enums allow us to map fixed names to integer values
e.g.
```c
enum TYPE { FIRE, WATER, FLYING, ROCK, ELECTRIC };
// the actual value of FIRE here is just 0
```

---

## unions

we can use unions to capture a type that might potentially be a range of types
unions can be used as **one** of the multiple types listed in it
the size of a union is the size of the largest possible type it can take on

these are stored in the exact same region of memory, if we change the value using one type representation, then attempt to read it as another type, C simply interprets the bytes as the type we are trying to read so we get garbage
e.g.
```c
union number {
    int i;
    float f;
    double d;
};

union number n;
n.i = 10;
n.f = 10.05;
n.d = 12.02;

// note that when declaring unions like:
union number n = {10.05};
// C will implicitly assume the first type, i.e. int in this case
```
