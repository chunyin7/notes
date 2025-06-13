# debugging

## gdb

the **gnu project debugger** is a debugging tool for C along with other languages, allowing for users to step through a program's execution

we can use gdb with a c program by compiling with the `-g` flag and then run gdb with the executable

- the `-g` flag instructs the compiler to embed additional data in the executable, creating a map between machine code and the high level source code, including:
    - file names and line numbers
    - names, types and locations of variables and functions

in unix systems, the debugger gets control over execution via the `ptrace` system call, giving the ability for gdb to read and write to the process' memory and registers and be notified of events such as signals

to step line by line through the source code, which may correspond to several machine instructions, gdb uses breakpoints

- *software breakpoints*: gdb replaces the machine instruciton at a specific memory address with a special "trap" condition, which causes a software interrupt when the cpu attempts to execute this instruciton, causing the OS to suspend the program and notify GDB, which then restores the original instruction
- *hardware breakpoints*: modern cpus provide special debug registers, which gdb can program with memory addresses to watch
    - if the cpu's program counter matches an address in one of these registers, it triggers a debug exception

a **segmentation fault** occurs when a program attempts to access a memory address outside of its allocated space

when this happens, the cpu detects the invalid access and raises an exception, the kernel handles this exception and sends a signal - `SIGSEGV` on linux - to the offending process

when a program is being traced by `ptrace`, the program is not terminated, instead the tracer is notified *before* the signal's default action is taken, allowing gdb to 'catch' the seg fault
