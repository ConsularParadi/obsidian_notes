> [!info] 
> How to use locks to make a data structure thread safe for multi-threaded operations
> 

#### Concurrent Counters

> [!info] 
> Follows design pattern similar to most concurrent DS -> it simply adds a single lock, which is acquired when calling a routine that **manipulates the data structure**, and is released when returning from the call.

Similar to DS with monitors -> locks are acquired and released automatically as you call and return from object methods.

![[concurrent counter performance.png]]

<summary>Performance of traditional vs sloppy counters</summary>
- shows total time taken to update counter 1 million times, with 1-4 threads active.
- Precise indicates performance of synchronized counters scales poorly while each thread performs the task in a tiny amount of time (0.03 sec).
- 2 threads concurrently -> 5 seconds

> [!caution] 
> Not a scalable solution multi-threaded counter.

> [!hint] 
> Threads complete just as quickly on multiple processors as the single thread does on one -> called perfect scaling.


##### Scalable Counting (sloppy counter)

> [!info] 
> The sloppy counter works by representing a single logical counter via numerous local physical counters, one per CPU core, as well as a single global counter. There are also locks: one for each local counter, and one for the global counter. 

Because each CPU has its own counter, threads can update local counter parallelly without contention. These local values are periodically (**S -> threshold for updation/sloppiness**) incremented to global counter.

> [!tip] 
> The smaller S is, the more the counter behaves like the non-scalable counter above; the bigger S is, the more scalable the counter, but the further off the global value might be from the actual count.

![[Sloppiness vs Performance.png]]

> [!tip] 
> If S is low, performance is poor (but the global count is always quite accurate), if S is high, performance is excellent, but the global count lags (by the number of CPUs multiplied by S). This accuracy/performance trade-off is what sloppy counters enables. 


