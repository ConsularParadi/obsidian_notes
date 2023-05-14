> [!info] 
> POSIX (OS) way of providing mutual exclusion to critical sections of code in multi-threaded programs.


```
pthread_mutex_t lock; //holds the state of lock at any instant
Pthread_mutex_lock(&lock); //wrapper for pthread_mutex_lock (checks errors upon lock and unlock)
x = x + 1; // or whatever your critical section is
Pthread_mutex_unlock(&lock);
```

2 ways to properly initialize locks:
1. `pthread_mutex_t lock = PTHREAD_MUTEX_INITIALIZER;`
2. Dynamic way ```int rc = pthread_mutex_init(&lock, NULL(lock_attr));
	assert(rc == 0); // always check success!
	pthread_cond_destroy(); // destroy lock after no longer needed```

> [!caution] 
> - Lock and unlock routines can also fail! If your code doesn’t properly check error codes, the failure will happen silently, which in this case could allow multiple threads into a critical section.
> 1. Minimally, use wrappers, which assert that the routine succeeded.
> 2. More sophisticated (non-toy) programs, which can’t simply exit when something goes wrong, should check for failure and do something appropriate when the lock or unlock does not succeed.

**Other routines:**
`int pthread_mutex_trylock(pthread_mutex_t *mutex);` - returns failure if the lock is already held
`int pthread_mutex_timedlock(pthread_mutex_t *mutex, struct timespec *abs_timeout);`
	- returns after a timeout or after acquiring the lock, whichever happens first.

- Course grained vs fine grained locking strategy (allows more threads to be in locked code at once -> may increase concurrency)


#### Building a Lock

3 performance measures:
1. Correctness (mutual exclusion)
2. Fairness (No resource contention by single thread)
3. Performance (Computation cost of lock)

###### Controlling Interrupts
```
void lock() {
DisableInterrupts();
}
void unlock() {
EnableInterrupts();
}
```

Cons:
1. **Privileged operation**: greedy program can monopolize processor, malicious/errant program goes into endless loop, trust issue.
2. **Multiprocessor**: turning off interrupt on one processor does not disable threads on other CPUs from acquiring the lock.
3. **Inefficient**: Turning off interrupts executes more slowly than normal instructions

###### Spin Lock (test & set)
> [!info] 
> By making both the **test** (of the old lock value) and **set** (of the new value) a single atomic operation, we ensure that only one thread acquires the lock

> [!tip] 
> Technique requires preemptive scheduler on a single CPU.
> 

Correctness , X Fairness, X Performance (for single CPU)

###### Other similar methods
1. Compare and Swap
```
int CompareAndSwap(int *ptr, int expected, int new) {
	int actual = *ptr;
	if (actual == expected)
		*ptr = new;
	return actual;
}
```

- Hardware primitive used on SPARC and x86

2. Load Linked and Store Conditional
```
int LoadLinked(int *ptr) {
	return *ptr;
}

int StoreConditional(int *ptr, int value) {
	if (no one has updated *ptr since the LoadLinked to this address) {
		*ptr = value;
		return 1; // success!
	} else {
		return 0; // failed to update
	}
}
```

```
void lock(lock_t *lock) {
	while (LoadLinked(&lock->flag)||!StoreConditional(&lock->flag, 1))
		; // spin
}
```

Failure of the store-conditional might arise. One thread calls lock() and executes the load-linked, returning 0 as the lock is not held. Before it can attempt the store-conditional, it is interrupted and another thread enters the lock code, also executing the load-linked instruction, and also getting a 0 and continuing. At this point, two threads have each executed the load-linked and each are about to attempt the store-conditional.
The key feature of these instructions is that only one of these threads will succeed in updating the flag to 1 and thus acquire the lock.

###### Ticket Lock (fetch & add)
This solution uses a ticket and turn variable in combination to build a lock. The globally shared lock->turn is then used to determine
which thread’s turn it is; when (myturn == turn) for a given thread, it is that thread’s turn to enter the critical section. Unlock is accomplished
simply by incrementing the turn such that the next waiting thread (if
there is one) can now enter the critical section.

> [!hint] 
> Progress is ensured for all threads unlike previous methods 

> [!attention] 
> **Problem with spinning** 
> One thread (thread 0) is in a critical section and thus has a lock held, and unfortunately gets interrupted. The second thread (thread 1) now tries to acquire the lock, but finds that it is held. Thus, it begins to spin. And spin. Then it spins some more. And finally, a timer interrupt goes off, thread 0 is run again, which releases the lock, and finally (the next time it runs, say), thread 1 won’t have to spin so much and will be able to acquire the lock.

> [!tip] 
> Hardware support alone cannot solve the problem, we'll need OS support.
> 

> [!important] 
> Thread states -> ready, running, blocked
> 

###### Yield method

> [!info] 
>An OS system call that moves the caller from the running state to the ready state, and thus promotes another thread to running.

If a thread happens to call lock() and find a lock held, it will simply yield the CPU, and thus the other thread will run and finish its critical section. Thread gives up the CPU to another thread instead of spinning.

```
void lock() {
	while (TestAndSet(&flag, 1) == 1)
		yield(); // give up the CPU
}
```

> [!failure] 
> Assuming 100 threads are waiting for a lock, an available thread (or a thread with the lock) will only be able to run (assuming a round-robin scheduler) after other 99 threads have gone through the run-&-yield pattern.
> - Still better than 99 time slices spinning but context switches are still very costly.
> - May also lead to starvation problem. A thread may be caught in endless yield loop while other threads repeatedly enter and exit critical section.


