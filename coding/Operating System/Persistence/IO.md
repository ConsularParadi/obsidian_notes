![[prototypical system architecture.png]]

![[modern sys arch.png]]

#### Canonical Device

##### Structure
![[device structure.png]]
1. <u>Interface</u> - acts as an intermediary that allows the system software to control IO device's operations. For ex: device drivers.
2. <u>Internals</u> - consists of hardware components along with firmware to control the device.

##### Protocol
![[protocol.png]]

> [!info] 
>- OS waits until the device is ready to receive a command by repeatedly reading the status register (basically asking it what is going on) -> **Polling**
>- When the main CPU is involved with the data movement -> Programmed I/O (**PIO**)


#### Interrupt
- To reduce overhead of polling

> [!info] 
>  When an IO device becomes available, it sends an hardware interrupt causing the CPU to context switch to OS to process the corresponding **ISR (Interrupt Service Routine) or interrupt handler** which contains the code on how to proceed when the interrupt occurs (read from disk, store something in buffer, perform an operation).

> [!important] 
>- Interrupt - for slow devices
>- Polling - for fast devices
>- Two-phased - for unknown or varying


#####  DMA
> [!info] 
>- DMA (Direct Memory Access) allows hardware devices to transfer data between the device and system memory without involving the CPU to reduce PIO overhead.
>- The device DMA requests access to the system memory through motherboard's DMA controller, transfers the data to or from the memory directly, and then signals the CPU when the transfer is complete.

![[dma.png]]  


#### Methods of Device Interaction
1. **I/O instructions**
	- Explicit instructions (previously used by IBM mainframe) - these instructions specify a way for the OS to send data to specific device registers and thus allow the construction of the protocols described above.
	- For ex: to send data to a device, the caller specifies a register with the data in it, and a specific port which names the device. Executing the instruction leads to the desired behavior. (x86 arch) -> these instructions are privileged and can only be called by OS which also manages the devices solely.

1. **Memory-mapped I/O**
	- The hardware makes device registers available as if they were memory locations. The hardware then routes the load/store by OS to the device instead of main memory.

#### Device Drivers

> [!question] 
> How to build a device neutral OS -> **Abstraction using device drivers**
> 

> [!info] 
> Device driver is a piece of software in the OS that knows in detail how a device works and encapsulates all its functionalities.
> 

- The OS issues a general command (read/write) oblivious of what the device is to the block layer which sends it to the appropriate device driver and the device driver translates it into instruction comprehensible by the device.

![[driver_architecture.png]]

There is also a raw interface used by low level applications like file-system checker or disk defragmenter to bypass the file abstraction system.

> [!failure] 
> - This encapsulation can have downsides as well. For ex: A device may be capable of multiple special functions but these remain hidden in the driver as the OS only accesses the general functions
> - Example: Rich error reporting in SCSI disks in comparison to ATA/IDE block devices.

Most OS code consists of device driver code (Linux kernel - 70%)

##### IDE Disk Driver
-> Includes registers for control, command, status, error