# c compilation

## compiler pipeline

1. **preprocessing**: the preprocessor performs text insertions/substitutions for all macros and headerfiles
2. **compilation**: source code converted to assembly code (architecture dependent)
    - checks syntax
    - decides memory layout
    - removes redundant code
3. **assembler**: produces object code
4. **linker**: links all modules (object files) together
    - converts object code to binary instruction
    - symbol resolution -> links symbols to memory
    - provides entry point for main function

### multi-file linkage

`extern`:
when the *extern* keyword is used within a C source file, it notifies the compiler to link a variable or function of the same name within the global memory space

`static`:
*static* variables have file scope and internal linkage, these files are not exposed to other files
a *static* variable within a function scope will ensure that the variable's memory is maintained even when the stack space is popped


## various compiler flags

- **Wall**: enables a set of warning flags (not necessarily all)
- **Werror**: treats all warnings as errors
- **Wextra**: enables extra warnings
- **pedantic**: issues warnings in the case that specfic forbidden extensions (by the C standard) are used
- **std=c2x**: the *std=* flag specifies the C standard to use, c2x refers to the fifth version of the C standard, *C23*
- **fsanitize**: allows a sanitizer to be enabled, some examples include:
    - ASAN: fsanitize=address -> detects memory errors and provides more verbose error messages
    - TSAN: fsanitize=thread -> detects concurrency issues such as race conditions etc
- **coverage**: compiles and links code instrumented for code coverage analysis:
    - synonym for *fprofile-arcs*, *ftest-coverage*, *lgcov*
- **O3**: tries to optimize the code very heavily for performance
- **Wpointer-arith**: issues warnings for questionable pointer arithmetic
    - e.g. math operations on pointers to void or pointers to functions
- **Wvla**: issues warnings for presence of variable length arrays
- **g**: produces debugging information in the OS' native format
- **r**: links multiple object files, producing a singular object file
- **c**: only produces object file, not converted to binary
