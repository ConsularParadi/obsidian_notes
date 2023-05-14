![[server-client system.png]]

- Successful due to **open protocol** which specified formats client and server need to communicate.

#### NFS_v2 -> Fast Recovery

##### Stateless

> [!info] 
> The server, by design, does not keep track of anything about what is happening at each client. ; rather, the protocol is designed to deliver in each protocol request all the information that is needed in order to complete the request.

- For example, the server does not know which clients are caching which blocks, or which files are currently open at each client, or the current file pointer position for a file, etc.
- Problems with stateful ->
	- How will the server remember the shared state (between client and server) if the server crashes
	- What if client crashes after opening a file.

##### File Handle

> [!info] 
> Together, these three items comprise a unique identifier for a file or directory that a client wishes to access.
>- volume identifier
>- inode number
>- generation number

- The <u>volume identifier</u> informs the server which file system the request refers to (an NFS server can export more than one file system).
- The <u>inode number</u> tells the server which file within that partition the request is accessing.
- The <u>generation number</u> is needed when reusing an inode number; by incrementing it  whenever an inode number is reused, the server ensures that a client with an old file handle can’t accidentally access the newly-allocated file.

![[NFS v2 protocol.png]]

> [!tip] 
> - **Client tracks all relevant state** for the file access (mapping of the integer file descriptor to an NFS file handle as well as the current file pointer) -> to send proper offset during read()
> - If a **long pathname** must be traversed (e.g., /home/remzi/foo.txt), the client would send three LOOKUPs
> - Each server request has **all the information needed** to complete the request in its entirety.

##### Handling Server Failures with Idempotency

> [!info] 
> An operation is called idempotent when the effect of performing the operation multiple times is equivalent to the effect of performing the operation a single time 



![[3 types of loss.png]]

> [!tip] 
> We can improve read/write performance using client side caching
> 

#### Cache Consistency Problem

- **Update visibility**: when do updates from one client become visible at other clients?
- **Stale cache**: when one client updates the server file but other client's program is still using the older cached file of client.

![[cache consistency problem.png]]

##### How NFS deals with these
- Update visibility: <u>flush-on-close</u> - when a file is written to and subsequently closed by a client application, the client flushes all updates (i.e., dirty pages in the cache) to the server.
- Stale cache: NFSv2 clients first check to see whether a file has changed before using its cached contents through **GETATTR**. If changed it invalidates the local copy and fetches the updated copy.
- Server flooded with GETATTR requests: Client side attribute cache - The attributes for a particular file were placed in the cache when the file was first accessed, and then would timeout after a certain amount of time and refetched.

##### Problems with NFS
- flush-on-close reduces performance as even temp files are pushed to server
- Attribute cache made it difficult to understand which version of file was being used - sometimes new, sometimes old - depends on timing

#### Server-side Write Buffering

> [!info] 
> NFS servers absolutely may not return success on a WRITE protocol request until the write has been forced to stable storage (e.g., to disk or some other persistent device). 

> [!important] 
> VFS/Vnode -> multiple file systems can be mounted by the same OS (part of NFS) 

> [!caution] 
> Problem - write performance of servers is major bottleneck
> solution - battery-backed mems
> 

