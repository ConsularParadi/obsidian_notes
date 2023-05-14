> [!note] 
> - **Address Space** - array of n sectors (512 b blocks) (0 to n-1)
> - Multi-sector Operations (4kb or more) by filesystem possible but atomicity of only single sector guaranteed by manufacturer so power loss may result in **torn write**.

> [!tip] 
> Sequential access (accessing contiguous sectors) fastest access mode than other possible random access pattern.

#### Geometry
- <u>Platter</u> - a single disk of a hard drive with 2 sides (each a **surface**). Usually made of hard material like aluminium and coated with magnetic layer that enables the drive to persistently store bits even when the drive is powered off. A drive can have multiple platters.
- <u>Spindle</u> - bounds all platters and spins the disk with help of motor at a fixed rate (RPM) when disk is powered on.
- <u>Tracks</u> - concentric circles on platter surface where data is stored in multiple sectors. Can be as thin as 1/1000th of a human hair width.
- <u>Disk head + arm</u> - mechanism to sense magnetic patterns (read) or induce magnetic changes (write) on a disk. Each head per surface attached to its own arm which helps it move across the surface to position the head over the desired track.

![[hdd-single.png]]

#### Latency
- <u>Seek time</u> - arm moving the head to the correct track where the sector exists (acceleration + coasting + deceleration) + **settling time** (often quite significant, e.g., 0.5 to 2 ms, as the drive must be certain to find the right track)
- <u>Rotational Latency</u> - head waiting for the sector to be read in a track while the disk spins to the correct location.
	- Worst case - full rotation delay to access the previous sector.
- <u>Transfer</u> - where data is either read from or written to the surface.

##### Other methods:
- **Skewed** - Sectors are often skewed because when switching from one track to another, the disk needs time to reposition the head (even to neighboring tracks). Without such skew, the head would be moved to the next track but the desired next block would have already rotated under the head, and thus the drive would have to wait almost the entire rotational delay to access the next block.
![[skewed track sectors.png]]
- **Multi-zoned** - outer tracks tend to have more sectors than inner tracks due to geometry.
	- disk is organized into multiple zones, and where a zone is consecutive set of tracks on a surface. Each zone has the same number of sectors per track, and outer zones have more sectors than inner zones.
- **Cache/Drive Buffer** - around 8 or 16 MB) which the drive can use to hold data read from or written to the disk.
	- For example: reads all sectors on a track when one sector is requested and serves subsequent requests of subsequent tracks using cache.
	- <u>Write back (immediate reporting) vs write through:</u> write back can be dangerous if the file system or applications require that data be written to disk in a certain order for correctness, write-back caching can lead to problem.

#### Disk Scheduling
Greedy disk IO scheduling by OS (principle of SJF(shortest job first))

##### SSTF (Shortest Seek Time First)
-> schedules request according to nearest track

> [!failure] 
> - Drive geometry not available, instead sees a block of array
> 	- Solved using NBF (nearest block first)
> - Starvation of track request completely different from incoming stream of track requests
> 	- Solved by elevator/sweep approach

##### Elevator / SCAN
-> Single pass across the disk (from outer to inner tracks, or inner to outer) is a sweep. Thus, if a request comes for a block on a track that has already been serviced on this sweep of he disk, it is not handled immediately, but rather queued until the next sweep (in the other direction).
- F-SCAN (Freezes queue) - service of new requests is deferred until all of the old requests have been processed
- C-SCAN (Circular) - sweep only occurs in one direction and then resets (otherwise middle tracks have higher preference in original SCAN algo)
Also known as **Elevator** approach

> [!caution] 
> Does not take into account rotational cost 

##### SPTF

> [!info] 
> **Shortest positioning/access time first**
> - It depends on the latency of a query with respect to current head position
> - Usually implemented inside the drive as OS has no idea about track boundaries or relative head sweep position

##### Other scheduling issues
- <u>Modern systems</u> - OS decides on a few requests based on its knowledge and sends it to disk. It then uses its internal scheduler and data from disk controller (head position,track layout) to decide best scheduling of requests using SPTF.
- <u>I/O Merging</u> - Merging of requests that are relatively closer to increase efficiency of scheduling. On OS level, it becomes even more important to reduces number of IO requests sent to disk scheduler and decrease overhead.
- <u>Non-work-conserving</u> - By waiting, a new and “better” request may arrive at the disk, and thus overall efficiency is increased.



