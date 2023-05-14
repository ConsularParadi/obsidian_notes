> [!info] 
> How does a program run? (Von Neumann)
> 1. CPU fetches instruction from memory
> 2. Decodes it
> 3. Executes it (ex: add two numbers, check condition, jump to function)

System to manage and simplify running of programs is **Operating System**.

Some properties of OS:
- **Virtualization of physical resources** (memory, CPU, disk) to transform into more general, powerful and easy to use virtual form. (Sometimes OS is referred to as virtual machine)
- **System Calls** - interfaces/APIs for programs to access these resources. (OS acts as a standard library for the programs)
- **Resource Manager** - time-sharing, memory-sharing of multiple programs is managed efficiently by OS.

#### Virtualization

##### CPU
- Creates illusion that a computer has very large number of virtual CPUs. 
- **Policy** to decide which program runs when

##### Memory
- Each process accesses its own private virtual address space which the OS somehow maps onto the physical memory of the machine.
- A memory reference within one running program does not affect the address space of other processes.

#### Concurrency
- Addressing concerns that arise due to handling multiple processes simultaneously.
- Problem - set of multiple instructions under a process does not get executed atomically and the program may get stranded in an incomplete state.

#### Persistence
- In system memory, data can be easily lost; Thus, we need hardware and software to be able to store data persistently.

#### Design Goals
- Abstraction
- Minimize overhead (performance)
- Protection/Isolation
- Reliability
- Energy-efficiency
- Multi-device support




