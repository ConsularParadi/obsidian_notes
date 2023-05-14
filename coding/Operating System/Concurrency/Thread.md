> [!info] 
> Each thread is a different instance of a process, may also be using a different CPU for processing but shares the same address space with the other instances (threads)
> 

- Has its own private set of registers
- Context switch when two threads are running on same processor (address space switch not applicable)
- State stored in TCB (Thread Control Block)
- One stack (thread-local storage) per thread in address space

> [!tip] 
> Execution order of threads is decided by the whim of the scheduler
> 

> [!important] 
> Disassembler 'objdump' to see machine code, other programs like memory profiler (valgrind, purify) and degugger (gdb) - tools to understand working of system

> [!danger] 
> Shared data + indeterminate execution of threads
> 

- A **critical section** is a piece of code that accesses a shared resource, usually a variable or data structure.
- A **race condition** arises if multiple threads of execution enter the critical section at roughly the same time; both attempt to update the shared data structure, leading to a surprising (and perhaps undesirable) outcome.
- **Indeterminate** program produces different results on every run due to inclusion of race conditions.

> [!tip] 
> Mutual exclusion - only a single thread enters a critical section at a particular time (atomicity for critical sections) which helps in **synchronization** between threads

> [!caution] 
> **Problems encountered in multi-threading**
> - Atomicity of critical sections (synchronization of shared resources over multiple threads)
> - Sleeping/Waking interaction between threads when process is too long (reading from io)

``` 
#include <pthread.h>
pthread_t t1, t2; //declaring threads
pthread_create (&t1, pthread_attr_t *t (attributes - stack size, priority - NULL), void* function, args)
pthread_join (&t1, (void**) &ret_val)
```

##### Thread API Guidelines
1. Keep it simple (minimal critical section)
2. Minimize thread interactions
3. Initialize locks and condition variables
4. Check return codes
5. Be careful with how you pass arguments to, and return values from, threads
6. Each thread has its own stack
7. Values to be used later from a thread must be stored in a heap
8. Always use condition variables to signal between threads
9. Use manpages -> `man -k pthread`

[GitHub]