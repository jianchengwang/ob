
## 1. Overview

In this tutorial, we'll see **different ways to implement a [mutex](https://www.baeldung.com/cs/what-is-mutex) in Java**.

## 2. Mutex

In a multithreaded application, two or more threads may need to access a shared resource at the same time, resulting in unexpected behavior. Examples of such shared resources are data-structures, input-output devices, files, and network connections.

We call this scenario a _race condition_. And, the part of the program which accesses the shared resource is known as the _critical section_. **So, to avoid a race condition, we need to synchronize access to the critical section.**

A mutex (or mutual exclusion) is the simplest type of _synchronizer –_ it **ensures that only one thread can execute the critical section of a computer program at a time**.

To access a critical section, a thread acquires the mutex, then accesses the critical section, and finally releases the mutex. In the meantime, **all other threads block till the mutex releases.** As soon as a thread exits the critical section, another thread can enter the critical section.

## 3. Why Mutex?

First, let's take an example of a _SequenceGeneraror_ class, which generates the next sequence by incrementing the _currentValue_ by one each time:

```java
public class SequenceGenerator {
    
    private int currentValue = 0;

    public int getNextSequence() {
        currentValue = currentValue + 1;
        return currentValue;
    }

}
```

Now, let's create a test case to see how this method behaves when multiple threads try to access it concurrently:

```java
@Test
public void givenUnsafeSequenceGenerator_whenRaceCondition_thenUnexpectedBehavior() throws Exception {
    int count = 1000;
    Set<Integer> uniqueSequences = getUniqueSequences(new SequenceGenerator(), count);
    Assert.assertEquals(count, uniqueSequences.size());
}

private Set<Integer> getUniqueSequences(SequenceGenerator generator, int count) throws Exception {
    ExecutorService executor = Executors.newFixedThreadPool(3);
    Set<Integer> uniqueSequences = new LinkedHashSet<>();
    List<Future<Integer>> futures = new ArrayList<>();

    for (int i = 0; i < count; i++) {
        futures.add(executor.submit(generator::getNextSequence));
    }

    for (Future<Integer> future : futures) {
        uniqueSequences.add(future.get());
    }

    executor.awaitTermination(1, TimeUnit.SECONDS);
    executor.shutdown();

    return uniqueSequences;
}
```

Once we execute this test case, we can see that it fails most of the time with the reason similar to:

```java
java.lang.AssertionError: expected:<1000> but was:<989>
  at org.junit.Assert.fail(Assert.java:88)
  at org.junit.Assert.failNotEquals(Assert.java:834)
  at org.junit.Assert.assertEquals(Assert.java:645)
```

The _uniqueSequences_ is supposed to have the size equal to the number of times we've executed the _getNextSequence_ method in our test case. However, this is not the case because of the race condition. Obviously, we don't want this behavior.

So, to avoid such race conditions, we need to **make sure that only one thread can execute the getNextSequence method at a time**. In such scenarios, we can use a mutex to synchronize the threads.

There are various ways, we can implement a mutex in Java. So, next, we'll see the different ways to implement a mutex for our _SequenceGenerator_ class.

## 4. Using synchronized Keyword

First, we'll discuss the [_synchronized_ keyword](https://www.baeldung.com/java-synchronized), which is the simplest way to implement a mutex in Java.

Every object in Java has an intrinsic lock associated with it. **The** **synchronized method and** **the synchronized block use this intrinsic lock** to restrict the access of the critical section to only one thread at a time.

Therefore, when a thread invokes a _synchronized_ method or enters a _synchronized_ block, it automatically acquires the lock. The lock releases when the method or block completes or an exception is thrown from them.

Let's change _getNextSequence_ to have a mutex, simply by adding the _synchronized_ keyword:

```java
public class SequenceGeneratorUsingSynchronizedMethod extends SequenceGenerator {
    
    @Override
    public synchronized int getNextSequence() {
        return super.getNextSequence();
    }

}
```

The _synchronized_ block is similar to the _synchronized_ method, with more control over the critical section and the object we can use for locking.

So, let's now see how we can **use the synchronized block to synchronize on a custom mutex object**:

```java
public class SequenceGeneratorUsingSynchronizedBlock extends SequenceGenerator {
    
    private Object mutex = new Object();

    @Override
    public int getNextSequence() {
        synchronized (mutex) {
            return super.getNextSequence();
        }
    }

}
```

## 5. Using ReentrantLock

The _[ReentrantLock](https://www.baeldung.com/java-concurrent-locks)_ class was introduced in Java 1.5. It provides more flexibility and control than the _synchronized_ keyword approach.

Let's see how we can use the _ReentrantLock_ to achieve mutual exclusion:

```java
public class SequenceGeneratorUsingReentrantLock extends SequenceGenerator {
    
    private ReentrantLock mutex = new ReentrantLock();

    @Override
    public int getNextSequence() {
        try {
            mutex.lock();
            return super.getNextSequence();
        } finally {
            mutex.unlock();
        }
    }
}
```

## 6. Using Semaphore

Like _ReentrantLock_, the _[Semaphore](https://www.baeldung.com/java-semaphore)_ class was also introduced in Java 1.5.

While in case of a mutex only one thread can access a critical section, _Semaphore_ allows **a fixed number of threads to access a critical section**. Therefore, **we can also implement a mutex by setting the number of allowed threads in a_Semaphore to one**.

Let's now create another thread-safe version of _SequenceGenerator_ using _Semaphore_:

```java
public class SequenceGeneratorUsingSemaphore extends SequenceGenerator {
    
    private Semaphore mutex = new Semaphore(1);

    @Override
    public int getNextSequence() {
        try {
            mutex.acquire();
            return super.getNextSequence();
        } catch (InterruptedException e) {
            // exception handling code
        } finally {
            mutex.release();
        }
    }
}
```

## 7. Using Guava's Monitor Class

So far, we've seen the options to implement mutex using features provided by Java.

However, the _Monitor_ class of Google's Guava library is a better alternative to the _ReentrantLock_ class. As per its [documentation](https://guava.dev/releases/19.0/api/docs/com/google/common/util/concurrent/Monitor.html), code using _Monitor_ is more readable and less error-prone than the code using _ReentrantLock_.

First, we'll add the Maven dependency for [Guava](https://search.maven.org/search?q=g:com.google.guava%20AND%20a:guava):

```xml
<dependency>
    <groupId>com.google.guava</groupId>
    <artifactId>guava</artifactId>
    <version>31.0.1-jre</version>
</dependency>
```

Now, we'll write another subclass of _SequenceGenerator_ using the _Monitor_ class:

```java
public class SequenceGeneratorUsingMonitor extends SequenceGenerator {
    
    private Monitor mutex = new Monitor();

    @Override
    public int getNextSequence() {
        mutex.enter();
        try {
            return super.getNextSequence();
        } finally {
            mutex.leave();
        }
    }

}
```

## 8. Conclusion

In this tutorial, we've looked into the concept of a mutex. Also, we've seen the different ways to implement it in Java.

As always, the complete source code of the code examples used in this tutorial is available [over on GitHub](https://github.com/eugenp/tutorials/tree/master/core-java-modules/core-java-concurrency-2).