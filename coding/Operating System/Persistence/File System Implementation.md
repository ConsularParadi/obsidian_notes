> [!info] 
> Mental Model of File System
> -  **Data structures**: what types of on-disk structures are utilized by the file system to organize its data and metadata.
> - **Access methods**:  How does it map the calls made by a process, such as open(), read(), write(), etc., onto its structures? Which structures are read during the execution of a particular system call? Which are written? How efficiently are all of these steps performed?

#### Components of a file system
![[vsfs.png]]

- <u>Data region</u> - user data (56 blocks)
- <u>Inode Table</u> - stores inodes for dirs and files (5 blocks)
> [!tip] 
> Assuming inode size of 256 bytes, we can store 80 inodes in the table -> represents maximum no. of files we can have in our fs.
> 
- <u>Inode & Data Bitmap</u> - simple structure where each bit is used to indicate whether the corresponding object/block is free (0) or in-use (1).
- <u>Superblock</u> - The superblock contains information about this particular file system, including, for example, how many inodes and data blocks are in the file system (80 and 56, respectively in this instance), where the inode table begins (block 3), and so forth. It will likely also include a magic number of some kind to identify the file system type.

#### Inode

> [!info] 
> The inode (index node due to array indexing) is the generic name that is used in many file systems to describe the structure that holds the metadata for a given file, such as its length, permissions, and the location of its constituent blocks.

![[inode table closeup.png]]

![[inode formulas.png]]

![[inode metadata.png]]

##### Inode Data Block Addressing Approaches

- **Multiple direct pointers to data blocks**
> [!fail] 
> what if file to be stored is larger than no. pointers * dblock size
> 

- **Multi-Level Indexing**
	- Some fixed pointers and an indirect pointer that points to a data block containing direct pointers to data block.
	- Assuming 4 byte ptrs, avlbl file size (12+1024)*4KB
	- Shift to double/triple indirect pointers for increased file size.
- Extent
	- Instead of using pointers, use extent (data ptr + length of contiguous blocks allocated to file)
	- More than one extent as contiguous memory of large size is difficult to find.

##### Directory Organization

![[dir organization.png]]

##### Free Space Management

> [!info] 
> Free space mgmt using bitmap and pre-allocation policy (allocate more contiguous memory at start)
> 

##### Reading from a Disk

![[reading from a disk.png]]

- Traverse through the absolute path starting from root (generally inode=2)
- The final step of open() is to read bar’s inode into memory; the FS then does a final permissions check, allocates a file descriptor for this process in the per-process open-file table, and returns it to the user.

##### Writing a File to a Disk
![[writing to disk io.png]]

> [!warning] 
> How much work it is to create the file: 10 I/Os in this case, to walk the pathname and then finally create the file. You can also see that each allocating write costs 5 I/Os.

##### Caching and Buffering
- Earlier -> fixed-size cache (LRU) -> **static partitioning**
- Now -> dynamic partitioning -> modern operating systems integrate virtual memory pages and file system pages into a **unified page cache**
- <u>Benefits</u>: (Write buffering)
	- Batch - processing of IO
	- Optimized scheduling of subsequent IOs
	- Temp files created and destroyed in mem itself
- Drawbacks can be overcome by using fsync or raw disk interface