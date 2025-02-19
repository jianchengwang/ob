
## 1. Overview

In this short article, we'll have a look at the standard _sleep()_ and _wait()_ methods in core Java, and understand the differences and similarities between them.

## 2. General Differences Between Wait and Sleep

Simply put, **wait() is an instance method that's used for thread synchronization.**

It can be called on any object, as it's defined right on _java.lang.Object,_ but it can **only be called from a synchronized block**. It releases the lock on the object so that another thread can jump in and acquire a lock.

On the other hand, _Thread.sleep()_ is a static method that can be called from any context. **Thread.sleep() pauses the current thread and does not release any locks.**

Here's a very simplistic initial look at these two core APIs in action:

```java
private static Object LOCK = new Object();

private static void sleepWaitExamples() 
  throws InterruptedException {
 
    Thread.sleep(1000);
    System.out.println(
      "Thread '" + Thread.currentThread().getName() +
      "' is woken after sleeping for 1 second");
 
    synchronized (LOCK) {
        LOCK.wait(1000);
        System.out.println("Object '" + LOCK + "' is woken after" +
          " waiting for 1 second");
    }
}

```

Running this example will produce the following output:

_Thread ‘main' is woken after sleeping for 1 second_  
_Object ‘java.lang.Object@31befd9f' is woken after waiting for 1 second_

## 3. Waking up Wait and Sleep

When we use the _sleep()_ method, a thread gets started after a specified time interval, unless it is interrupted.

For _wait()_, the waking up process is a bit more complicated. We can wake the thread by calling either the _notify()_ or _notifyAll()_ methods on the monitor that is being waited on.

Use _notifyAll()_ instead of _notify()_ when you want to wake all threads that are in the waiting state. Similarly to the _wait()_ method itself, _notify()_, and _notifyAll()_ have to be called from the synchronized context.

For example, here's how you can _wait_:

```java
synchronized (b) {
    while (b.sum == 0) {
        System.out.println("Waiting for ThreadB to complete...");
        b.wait();
    }

    System.out.println("ThreadB has completed. " + 
      "Sum from that thread is: " + b.sum);
}
```

And then, here's how another thread can then **wake up the waiting thread – by calling notify() on the monitor**:

```java
int sum;
 
@Override 
public void run() {
    synchronized (this) {
        int i = 0;
        while (i < 100000) {
            sum += i;
            i++; 
        }
        notify(); 
    } 
}
```

Running this example will produce the following output:

_Waiting for ThreadB to complete…_  
_ThreadB has completed._ Sum _from that thread is: 704982704_

## 4. Conclusion

This is a quick primer to the semantics of _wait_ and _sleep_ in Java.

In general, we should use _sleep()_ for controlling execution time of one thread and _wait()_ for multi-thread-synchronization. Naturally, there's a lot more to explore – after understanding the basics well.

As always, you can check out the examples provided in this article [over on GitHub](https://github.com/eugenp/tutorials/tree/master/core-java-modules/core-java-concurrency-basic-2).