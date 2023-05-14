> [!info] 
> Problems faced by FFS:
> - system memories are growing and most disk IO traffic is dependent on write performance.
> - large gap between random & sequential write performance.
> - work poorly for common workloads (6 writes to disk for creating new file)
> - fs are not RAID-aware

##### Writing sequentially and periodically

> [!info]
> **Write Buffering** - Before writing to the disk, LFS keeps track of updates in memory; when it has received a sufficient number of updates, it writes them (<u>segment</u>) to disk all at once, thus ensuring efficient use of the disk. 

> [!tip] 
> **How much to buffer** - Amortization
> ![[how much to buffer 2.png]]

![[how much to buffer.png]]







 


