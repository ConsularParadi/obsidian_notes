> [!info] 
> - The idea was to design the file system structures and allocation policies to be “disk aware” and thus improve performance 
> - By keeping the same interface to the file system (the same APIs, including open(), read(), write(), close(), and other file system calls) but changing the internal implementation.

#### Organizing Structure

##### Cylinders
> [!info] 
> A single cylinder is a set of tracks on different surfaces of a hard drive that are the same distance from the center of the drive.

![[cylinder groups.png]]
> [!caution] 
> Modern drives do not export enough information for the file system to truly understand whether a particular cylinder is in use.

##### Blocks
> [!info] 
>  Modern fs (such as Linux ext2, ext3, and ext4) instead organize the drive into block groups, each of which is just a consecutive portion of the disk’s address space.

![[blocks.png]]

- FFS keeps a copy of the **super block** (S) in each group for reliability reasons. The super block is needed to mount the file system; by keeping multiple copies, if one copy becomes corrupt, you can still mount and access the file system by using a working replica.
- A per-group inode bitmap (ib) and data bitmap (db)
- The inode and data block regions are just like those in the previous very-simple file system (VSFS)

#### Policies

> [!important] 
> keep related stuff together
> 

- **Directories** - find the cylinder group with a low number of allocated directories (to balance directories across groups) and a high number of free inodes (to subsequently be able to allocate a bunch of files), and put the directory data and inode in that group.
- **Files** - First, it makes sure to allocate the data blocks of a file in the same group as its inode, thus preventing long seeks between inode and data. Second, it places all files that are in the same directory in the cylinder group of the directory they are in.
- Below is placement for 3 directories (/, /a, /b) and 4 files (a/c, a/d, a/e, b/f)
	![[ffs file policy.png]]  FFS allocation policy

	![[Old allocation policy.png]] Old UNIX file allocation 

##### Large Files

After some number of blocks are allocated into the first block group (e.g., 12 blocks, or the number of direct pointers available within an inode), FFS places the next “large” chunk of the file (e.g., those pointed to by the first indirect block) in another block group (perhaps chosen for its low utilization). Then, the next chunk of the file is placed in yet another different block group.

![[old large file polciy.png]]
![[new large file policy.png]]

> [!important] 
> Process of reducing an overhead by doing more work per overhead paid is called **amortization**


![[amortization.png]]

> [!hint] 
> The trend in disk drives is that transfer rate improves fairly rapidly but the mechanical aspects of drives related to seeks (disk arm speed and the rate of rotation) improve rather slowly.
> - Mechanical costs become relatively more expensive, and thus, to amortize said costs, you have to transfer more data between seeks.

##### Other innovations by FFS
- **Internal Fragmentation**: block (4kb), file(2kb) -> introduced sub-blocks (512b)
> [!tip] 
> Copying sub-blocks to new block after it had occupied the whole block was very IO costly -> FFS updated `c-lib` library to store sub-block write in memory and then give reduced IO operation to disk.

- **Parameterization** - During sequential reads, a continuous sector may be scanned in next rotation due to it skipping because of IO overhead. Re-placement of sectors on disk - parameterization
> [!tip] 
>  It may take one rotation to read the same sector again - new disks have a buffer cache to store the read data and return a quick response if data is read again.

- Long file names
- Symbolic links
- Atomic rename
