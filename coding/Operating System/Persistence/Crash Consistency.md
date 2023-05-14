> [!info] 
> **3 operations while appending a data block to a file**
> 	1. Update data bitmap
> 	2. Update inode pointer
> 	3. Write data to data block

> [!fail] 
> **6 crash scenarios for this operation (due to power loss/ bug in OS)**
> 	1. only bitmap -> space leak
> 	2. only inode -> garbage data + file system inconsistency
> 	3. only dblock -> not a problem as per crash consistency
> 	4. I & B -> file system metadata consistent but garbage data
> 	5. I & D -> file system inconsistency
> 	6. B & D -> file system inconsistency and no idea to which file the data belongs


#### File System Checker (fsck)

> [!info] 
>Run before the file system is mounted and made available  and only ensures the filesystem metadata is internally consistent

- <u>Superblock</u>: sanity checks, making sure file system size is greater than no. of allocated blocks.
- <u>Free Blocks</u>: creates own bitmap by checking allocated block through inode direct and indirect pointers and then compares it with original bitmap.
- <u>Inode state</u>: checks inode is checked for corruption like if valid file type is present (ln, dir, rf)
- <u>Inode links</u>: verifies link count by scanning entire directory for references
- <u>Duplicates</u>: checks for duplicate pointer where 2 inode pointers point to the same block 
- <u>Bad blocks</u>: to check if a pointer refers to a block outside the valid range
- <u>Directory checks</u>: integrity checks of directory, see if '.' and '..' entries are present

> [!caution] 
> This process is very slow, especially when our disk size continues to grow very large
> 

####  Journaling (Write-ahead Logging)

> [!info] 
> Adds a log data structure where everything is described ahead of updating the on-disk data structures themselves.

-  Used by ext3, ext4, NTFS, IBM JFS, XFS etc.

![[log in ext3.png]]

Each group contains bitmap, inodes and data blocks.

##### Data Journaling

![[data journaling.png]]

- TxB - the final addresses of the blocks Iv2, Bv2, and Db), and some kind of transaction identifier (TID)
- Mid 3 blocks
	- <u>physical logging</u>- contain the exact contents of the blocks themselves
	- <u>logical logging</u> - “this update wishes to append data block Db to file X” -> more complex but space saving and increased performance
- TxE - marker of the end of this trx, and will also contain the TID.

> [!tip] 
> What if crash occurs while journaling:
> - Each block written atomically (very slow)
> - All five block to be written at once, as this would turn five writes into a single sequential write and thus be faster (disk internal scheduling may end transaction before all 3 blocks are logged inside)

![[journaling to disk.png]]
![[journaling steps.png]]

##### Recovery

> [!info] 
> <u>Redo logging</u> - reenforcing existing log if crash happens before checkpoint.
> 

##### Batching log updates

> [!info] 
> Lot of overhead for writing each transaction to disk, OS marks the in-mem bitmap and inode dirty when transaction occurs  and after a few transactions sends a global trx to be logged onto disk. (buffer updates)

> [!tip] 
> The log is circular and after a transaction has been checkpointed, its log is freed from the journal. A superblock inside the journal maintains which logs have been checkpointed. 
> ![[finite log.png]]

##### Metadata Journaling
![[metadata journaling.png]]
> [!important] 
> Reduces IO cost as data is being written twice in data journaling
> 

##### Block Reuse

> [!info] 
>- Never reuse blocks until the delete of said blocks is checkpointed out of the journal.
>- What Linux ext3 does instead is to add a new type of record to the journal, known as a revoke record. In the case above, deleting the directory would cause a revoke record to be written to the journal. When replaying the journal, the system first scans for such revoke records; any such revoked data is never replayed, thus avoiding the problem.




