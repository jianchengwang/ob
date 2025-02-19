
## 1. Overview

In this tutorial, we'll discuss the different _join()_ methods in the _Thread_ class. We'll go into the details of these methods and some example codes.

Like the _wait()_ and _notify() methods_, _join()_ is another mechanism of inter-thread synchronization.

You can have a quick look at [this tutorial](https://www.baeldung.com/java-wait-notify) to read more about _wait()_ and _notify()_.

## 2. The Thread.join() Method

The join method is defined in the _[Thread](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/lang/Thread.html#join())_ class:

> _public final void join() throws InterruptedException_  
> _Waits for this thread to die._

**When we invoke the join() method on a thread, the calling thread goes into a waiting state. It remains in a waiting state until the referenced thread terminates.**

We can see this behaviour in the following code:

```java
class SampleThread extends Thread {
    public int processingCount = 0;

    SampleThread(int processingCount) {
        this.processingCount = processingCount;
        LOGGER.info("Thread Created");
    }

    @Override
    public void run() {
        LOGGER.info("Thread " + this.getName() + " started");
        while (processingCount > 0) {
            try {
                Thread.sleep(1000);
            } catch (InterruptedException e) {
                LOGGER.info("Thread " + this.getName() + " interrupted");
            }
            processingCount--;
            LOGGER.info("Inside Thread " + this.getName() + ", processingCount = " + processingCount);
        }
        LOGGER.info("Thread " + this.getName() + " exiting");
    }
}

@Test
public void givenStartedThread_whenJoinCalled_waitsTillCompletion() 
  throws InterruptedException {
    Thread t2 = new SampleThread(1);
    t2.start();
    LOGGER.info("Invoking join");
    t2.join();
    LOGGER.info("Returned from join");
    assertFalse(t2.isAlive());
}
```

We should expect results similar to the following when executing the code:

```plaintext
[main] INFO: Thread Thread-1 Created
[main] INFO: Invoking join
[Thread-1] INFO: Thread Thread-1 started
[Thread-1] INFO: Inside Thread Thread-1, processingCount = 0
[Thread-1] INFO: Thread Thread-1 exiting
[main] INFO: Returned from join
```

**The join() method may also return if the referenced thread is interrupted**.  In this case, the method throws an _InterruptedException_.

Finally, **if the referenced thread was already terminated or hasn't been started, the call to join() method returns immediately**.

```java
Thread t1 = new SampleThread(0);
t1.join();  //returns immediately
```

## 3. Thread.join() Methods with Timeout

The _join()_ method will keep waiting if the referenced thread is blocked or takes too long to process. This can become an issue as the calling thread will become non-responsive. To handle these situations, we use overloaded versions of the _join()_ method that allow us to specify a timeout period.

**There are two [timed versions](https://docs.oracle.com/en/java/javase/19/docs/api/java.base/java/lang/Thread.html#join(long)) that overload the join() method:**

> _“public final void join(long_ millis_) throws InterruptedException_  
> _Waits at most_ millis _milliseconds for this thread to die. A timeout of 0 means to wait forever.”_
> 
> _“public final void join(long_ millis,intnanos_) throws InterruptedException_  
> _Waits at most_ millis _milliseconds plus_ nanos _nanoseconds for this thread to die.”_

We can use the timed _join()_ as below:

```java
@Test
public void givenStartedThread_whenTimedJoinCalled_waitsUntilTimedout()
  throws InterruptedException {
    Thread t3 = new SampleThread(10);
    t3.start();
    t3.join(1000);
    assertTrue(t3.isAlive());
}
```

In this case, the calling thread waits for roughly 1 second for the thread t3 to finish. If the thread t3 does not finish in this time period, the _join()_ method returns control to the calling method.

**Timed join() is dependent on the OS for timing. So, we cannot assume that join() will wait exactly as long as specified.**

## 4. Thread.join() Methods and Synchronization

In addition to waiting until termination, calling the _join()_ method has a synchronization effect. **join() creates a [happens-before](https://docs.oracle.com/javase/specs/jls/se8/html/jls-17.html#jls-17.4.5) relationship:**

> _“All actions in a thread happen-before any other thread successfully returns from a join() on that thread.”_

This means that when a thread t1 calls t2.join(), all changes done by t2 are visible in t1 on return. However, if we do not invoke _join()_ or use other synchronization mechanisms, we do not have any guarantee that changes in the other thread will be visible to the current thread even if the other thread has been completed.

Hence, even though the _join()_ method call to a thread in the terminated state returns immediately, we still need to call it in some situations.

We can see an example of improperly synchronized code below:

```java
SampleThread t4 = new SampleThread(10);
t4.start();
// not guaranteed to stop even if t4 finishes.
do {
       
} while (t4.processingCount > 0);
```

To properly synchronize the above code, we can add timed _t4.join()_ inside the loop or use another synchronization mechanism.

## 5. Conclusion

_join()_ method is quite useful for inter-thread synchronization. In this article, we discussed the _join()_ methods and their behaviour. We also reviewed the code using the _join()_ method.

As always, the full source code can be found [on GitHub](https://github.com/eugenp/tutorials/tree/master/core-java-modules/core-java-concurrency-simple).