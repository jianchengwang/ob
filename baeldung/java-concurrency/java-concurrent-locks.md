
## 1. Overview

Simply put, a lock is a more flexible and sophisticated thread synchronization mechanism than the standard _synchronized_ block.

The _Lock_ interface has been around since Java 1.5. It's defined inside the _java.util.concurrent.lock_ package, and it provides extensive operations for locking.

In this tutorial, we'll explore different implementations of the _Lock_ interface and their applications.

## 2. Differences Between Lock and Synchronized Block

There are a few differences between the use of synchronized _block_ and using _Lock_ APIs:

- **A synchronized block is fully contained within a method.** We can have _Lock_ APIs _lock()_ and _unlock()_ operation in separate methods.
- A synchronized block doesn't support the fairness. Any thread can acquire the lock once released, and no preference can be specified. **We can achieve fairness within the Lock APIs by specifying the fairness property.** It makes sure that the longest waiting thread is given access to the lock.
- A thread gets blocked if it can't get an access to the synchronized _block_. **The Lock API provides tryLock() method. The thread acquires lock only if it's available and not held by any other thread.** This reduces blocking time of thread waiting for the lock.
- A thread that is in “waiting” state to acquire the access to _synchronized block_ can't be interrupted. **The Lock API provides a method lockInterruptibly() that can be used to interrupt the thread when it's waiting for the lock.**

## 3. Lock API

Let's take a look at the methods in the _Lock_ interface:

- **void lock()** – Acquire the lock if it's available. If the lock isn't available, a thread gets blocked until the lock is released.
- **void lockInterruptibly()** – This is similar to the _lock()_, but it allows the blocked thread to be interrupted and resume the execution through a thrown _java.lang.InterruptedException_.
- **boolean tryLock()** – This is a nonblocking version of _lock()_ method. It attempts to acquire the lock immediately, return true if locking succeeds.
- **boolean tryLock(long timeout, TimeUnit timeUnit)** – This is similar to _tryLock()_, except it waits up the given timeout before giving up trying to acquire the _Lock_.
- **void unlock()** unlocks the _Lock_ instance.

A locked instance should always be unlocked to avoid deadlock condition.

A recommended code block to use the lock should contain a _try/catch_ and _finally_ block:

```java
Lock lock = ...; 
lock.lock();
try {
    // access to the shared resource
} finally {
    lock.unlock();
}
```

In addition to the _Lock_ interface, we have a _ReadWriteLock_ interface that maintains a pair of locks, one for read-only operations and one for the write operation. The read lock may be simultaneously held by multiple threads as long as there is no write.

_ReadWriteLock_ declares methods to acquire read or write locks:

- **Lock readLock()** returns the lock that's used for reading.
- **Lock writeLock()** returns the lock that's used for writing.

## 4. Lock Implementations

### 4.1. ReentrantLock

_ReentrantLock_ class implements the _Lock_ interface. It offers the same concurrency and memory semantics as the implicit monitor lock accessed using _synchronized_ methods and statements, with extended capabilities.

Let's see how we can use _ReentrantLock_ for synchronization:

```java
public class SharedObject {
    //...
    ReentrantLock lock = new ReentrantLock();
    int counter = 0;

    public void perform() {
        lock.lock();
        try {
            // Critical section here
            count++;
        } finally {
            lock.unlock();
        }
    }
    //...
}
```

We need to make sure that we are wrapping the _lock()_ and the _unlock()_ calls in the _try-finally_ block to avoid the deadlock situations.

Let's see how the _tryLock()_ works:

```java
public void performTryLock(){
    //...
    boolean isLockAcquired = lock.tryLock(1, TimeUnit.SECONDS);
    
    if(isLockAcquired) {
        try {
            //Critical section here
        } finally {
            lock.unlock();
        }
    }
    //...
}
```

In this case, the thread calling _tryLock()_ will wait for one second and will give up waiting if the lock isn't available.

### 4.2. ReentrantReadWriteLock

_ReentrantReadWriteLock_ class implements the _ReadWriteLock_ interface.

Let's see the rules for acquiring the _ReadLock_ or _WriteLock_ by a thread:

- **Read Lock** – If no thread acquired the write lock or requested for it, multiple threads can acquire the read lock.
- **Write Lock** – If no threads are reading or writing, only one thread can acquire the write lock.

Let's look at how to make use of the _ReadWriteLock_:

```java
public class SynchronizedHashMapWithReadWriteLock {

    Map<String,String> syncHashMap = new HashMap<>();
    ReadWriteLock lock = new ReentrantReadWriteLock();
    // ...
    Lock writeLock = lock.writeLock();

    public void put(String key, String value) {
        try {
            writeLock.lock();
            syncHashMap.put(key, value);
        } finally {
            writeLock.unlock();
        }
    }
    ...
    public String remove(String key){
        try {
            writeLock.lock();
            return syncHashMap.remove(key);
        } finally {
            writeLock.unlock();
        }
    }
    //...
}
```

For both write methods, we need to surround the critical section with the write lock — only one thread can get access to it:

```java
Lock readLock = lock.readLock();
//...
public String get(String key){
    try {
        readLock.lock();
        return syncHashMap.get(key);
    } finally {
        readLock.unlock();
    }
}

public boolean containsKey(String key) {
    try {
        readLock.lock();
        return syncHashMap.containsKey(key);
    } finally {
        readLock.unlock();
    }
}
```

For both read methods, we need to surround the critical section with the read lock. Multiple threads can get access to this section if no write operation is in progress.

### 4.3. StampedLock

_StampedLock_ is introduced in Java 8. It also supports both read and write locks.

However, lock acquisition methods return a stamp that is used to release a lock or to check if the lock is still valid:

```java
public class StampedLockDemo {
    Map<String,String> map = new HashMap<>();
    private StampedLock lock = new StampedLock();

    public void put(String key, String value){
        long stamp = lock.writeLock();
        try {
            map.put(key, value);
        } finally {
            lock.unlockWrite(stamp);
        }
    }

    public String get(String key) throws InterruptedException {
        long stamp = lock.readLock();
        try {
            return map.get(key);
        } finally {
            lock.unlockRead(stamp);
        }
    }
}
```

Another feature provided by _StampedLock_ is optimistic locking. Most of the time, read operations don't need to wait for write operation completion, and as a result of this, the full-fledged read lock isn't required.

Instead, we can upgrade to read lock:

```java
public String readWithOptimisticLock(String key) {
    long stamp = lock.tryOptimisticRead();
    String value = map.get(key);

    if(!lock.validate(stamp)) {
        stamp = lock.readLock();
        try {
            return map.get(key);
        } finally {
            lock.unlock(stamp);               
        }
    }
    return value;
}
```

## 5. Working With Condition

The _Condition_ class provides the ability for a thread to wait for some condition to occur while executing the critical section.

This can occur when a thread acquires the access to the critical section but doesn't have the necessary condition to perform its operation. For example, a reader thread can get access to the lock of a shared queue that still doesn't have any data to consume.

Traditionally Java provides _wait()_, _notify()_ and _notifyAll()_ methods for thread intercommunication.

Conditions have similar mechanisms, but we can also specify multiple conditions:

```java
public class ReentrantLockWithCondition {

    Stack<String> stack = new Stack<>();
    int CAPACITY = 5;

    ReentrantLock lock = new ReentrantLock();
    Condition stackEmptyCondition = lock.newCondition();
    Condition stackFullCondition = lock.newCondition();

    public void pushToStack(String item){
        try {
            lock.lock();
            while(stack.size() == CAPACITY) {
                stackFullCondition.await();
            }
            stack.push(item);
            stackEmptyCondition.signalAll();
        } finally {
            lock.unlock();
        }
    }

    public String popFromStack() {
        try {
            lock.lock();
            while(stack.size() == 0) {
                stackEmptyCondition.await();
            }
            return stack.pop();
        } finally {
            stackFullCondition.signalAll();
            lock.unlock();
        }
    }
}
```

## 6. Conclusion

In this article, we saw different implementations of the _Lock_ interface and the newly introduced _StampedLock_ class.

We also explored how we can make use of the _Condition_ class to work with multiple conditions.

The complete code for this article is available [over on GitHub](https://github.com/eugenp/tutorials/tree/master/core-java-modules/core-java-concurrency-advanced).