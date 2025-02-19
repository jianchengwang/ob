
## 1. Overview

In this short article, we'll have a look at daemon threads in Java and see what can they be used for. We'll also explain the difference between daemon threads and user threads.

## 2. Difference Between Daemon and User Threads

Java offers two types of threads: user threads and daemon threads.

User threads are high-priority threads. **The JVM will wait for any user thread to complete its task before terminating it.**

On the other hand, **daemon threads are low-priority threads whose only role is to provide services to user threads.**

Since daemon threads are meant to serve user threads and are only needed while user threads are running, they won't prevent the JVM from exiting once all user threads have finished their execution.

That's why infinite loops, which typically exist in daemon threads, will not cause problems, because any code, including the _finally_ blocks, won't be executed once all user threads have finished their execution. For this reason, **daemon threads are not recommended for I/O tasks.**

However, there're exceptions to this rule. Poorly designed code in daemon threads can prevent the JVM from exiting. For example, calling Thread.join() on a running daemon thread can block the shutdown of the application.

## 3. Uses of Daemon Threads

Daemon threads are useful for background supporting tasks such as garbage collection, releasing memory of unused objects and removing unwanted entries from the cache. Most of the JVM threads are daemon threads.

## 4. Creating a Daemon Thread

To set a thread to be a daemon thread, all we need to do is to call _Thread.setDaemon()._ In this example, we'll use the _NewThread_ class which extends the _Thread_ class:

```java
NewThread daemonThread = new NewThread();
daemonThread.setDaemon(true);
daemonThread.start();
```

**Any thread inherits the daemon status of the thread that created it.** Since the main thread is a user thread, any thread that is created inside the main method is by default a user thread.

The method _setDaemon()_ can only be called after the _Thread_ object has been created and the thread has not been started. An attempt to call _setDaemon()_ while a thread is running will throw an _IllegalThreadStateException_:

```java
@Test(expected = IllegalThreadStateException.class)
public void whenSetDaemonWhileRunning_thenIllegalThreadStateException() {
    NewThread daemonThread = new NewThread();
    daemonThread.start();
    daemonThread.setDaemon(true);
}
```

## 5. Checking if a Thread Is a Daemon Thread

Finally, to check if a thread is a daemon thread, we can simply call the method _isDaemon()_:

```java
@Test
public void whenCallIsDaemon_thenCorrect() {
    NewThread daemonThread = new NewThread();
    NewThread userThread = new NewThread();
    daemonThread.setDaemon(true);
    daemonThread.start();
    userThread.start();
    
    assertTrue(daemonThread.isDaemon());
    assertFalse(userThread.isDaemon());
}
```

## 6. Conclusion

In this quick tutorial, we've seen what daemon threads are and what they can be used for in a few practical scenarios.

As always, the full version of the code is available [over on GitHub](https://github.com/eugenp/tutorials/tree/master/core-java-modules/core-java-concurrency-advanced-2).