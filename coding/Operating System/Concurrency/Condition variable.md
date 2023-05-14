> [!info] 
> Condition variables are useful when some kind of signaling must take place between threads, if one thread is waiting for another to do something before it can continue. 

> [!tip] 
> Lock is needed in addition to the condition
> 

```
pthread_mutex_t lock = PTHREAD_MUTEX_INITIALIZER;
pthread_cond_t init = PTHREAD_COND_INITIALIZER;
Pthread_mutex_lock(&lock);
while (initialized == 0)
	Pthread_cond_wait(&init, &lock);
Pthread_mutex_unlock(&lock);
```

Pthread_cond_wait ->
- Puts the calling thread to sleep, and thus waits for some other thread to signal it, usually when something in the program has changed that the now-sleeping thread might care about.
- Wait call, in addition to putting the calling thread to sleep, releases the lock when putting said caller to sleep.
- Before returning after being woken, the pthread cond wait() re-acquires the lock, thus ensuring that any time the waiting thread is running between the lock acquire at the beginning of the wait sequence, and the lock release at the end, it holds the lock.

```
Pthread_mutex_lock(&lock);
initialized = 1; //Lock while accessing global variable to avoid race condition
Pthread_cond_signal(&init);
Pthread_mutex_unlock(&lock);
```

> [!caution] 
> **Spinning**
> `while (initialized == 0) ; //Spin` 
> - Wastes CPU cycles
> - Error prone (using flags to synchronize between threads)

