
## 1. Introduction

In this article, we'll discuss in detail a core concept in Java – the lifecycle of a thread.

We'll use a quick illustrated diagram and, of course, practical code snippets to better understand these states during the thread execution.

To get started understanding Threads in Java, [this article](https://www.baeldung.com/java-runnable-vs-extending-thread) on creating a thread is a good place to start.

## 2. Multithreading in Java

**In the Java language, multithreading is driven by the core concept of a Thread**. During their lifecycle, threads go through various states:

[![Life Cycle of a Thread](https://www.baeldung.com/wp-content/uploads/2018/02/Life_cycle_of_a_Thread_in_Java.jpg)](https://www.baeldung.com/wp-content/uploads/2018/02/Life_cycle_of_a_Thread_in_Java.jpg)

## 3. Life Cycle of a Thread in Java

The _java.lang.Thread_ class contains a _static State enum –_ which defines its potential states. During any given point of time, the thread can only be in one of these states:

1.  **NEW –** a newly created thread that has not yet started the execution
2.  **RUNNABLE –** either running or ready for execution but it's waiting for resource allocation
3.  **BLOCKED –** waiting to acquire a monitor lock to enter or re-enter a synchronized block/method
4.  **WAITING –** waiting for some other thread to perform a particular action without any time limit
5.  **TIMED_WAITING –** waiting for some other thread to perform a specific action for a specified period
6.  **TERMINATED –** has completed its execution

All these states are covered in the diagram above; let's now discuss each of these in detail.

### 3.1. New

**A NEW Thread (or a Born Thread) is a thread that's been created but not yet started.** It remains in this state until we start it using the _start()_ method.

The following code snippet shows a newly created thread that's in the _NEW_ state:

```java
Runnable runnable = new NewState();
Thread t = new Thread(runnable);
System.out.println(t.getState());
```

Since we've not started the mentioned thread, the method _t.getState()_ prints:

```plaintext
NEW
```

### 3.2. Runnable

When we've created a new thread and called the _start()_ method on that, it's moved from _NEW_ to _RUNNABLE_ state. **Threads in this state are either running or ready to run, but they're waiting for resource allocation from the system.**

In a multi-threaded environment, the Thread-Scheduler (which is part of JVM) allocates a fixed amount of time to each thread. So it runs for a particular amount of time, then relinquishes the control to other _RUNNABLE_ threads.

For example, let's add _t.start()_ method to our previous code and try to access its current state:

```java
Runnable runnable = new NewState();
Thread t = new Thread(runnable);
t.start();
System.out.println(t.getState());
```

This code is **most likely** to return the output as:

```plaintext
RUNNABLE
```

Note that in this example, it's not always guaranteed that by the time our control reaches _t.getState()_, it will be still in the _RUNNABLE_ state.

It may happen that it was immediately scheduled by the _Thread-Scheduler_ and may finish execution. In such cases, we may get a different output.

### 3.3. Blocked

A thread is in the _BLOCKED_ state when it's currently not eligible to run. **It enters this state when it is waiting for a monitor lock and is trying to access a section of code that is locked by some other thread.**

Let's try to reproduce this state:

```java
public class BlockedState {
    public static void main(String[] args) throws InterruptedException {
        Thread t1 = new Thread(new DemoBlockedRunnable());
        Thread t2 = new Thread(new DemoBlockedRunnable());
        
        t1.start();
        t2.start();
        
        Thread.sleep(1000);
        
        System.out.println(t2.getState());
        System.exit(0);
    }
}

class DemoBlockedRunnable implements Runnable {
    @Override
    public void run() {
        commonResource();
    }
    
    public static synchronized void commonResource() {
        while(true) {
            // Infinite loop to mimic heavy processing
            // 't1' won't leave this method
            // when 't2' try to enter this
        }
    }
}
```

In this code:

1.  We've created two different threads – _t1_ and _t2_
2.  _t1_ starts and enters the synchronized _commonResource()_ method; this means that only one thread can access it; all other subsequent threads that try to access this method will be blocked from the further execution until the current one will finish the processing
3.  When _t1_ enters this method, it is kept in an infinite while loop; this is just to imitate heavy processing so that all other threads cannot enter this method
4.  Now when we start _t2_, it tries to enter the _commonResource()_ method, which is already being accessed by _t1,_ thus, _t2_ will be kept in the _BLOCKED_ state

Being in this state, we call _t2.getState()_ and get the output as:

```plaintext
BLOCKED
```

### 3.4. Waiting

**A thread is in WAITING state when it's waiting for some other thread to perform a particular action.** [According to JavaDocs](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/lang/Thread.State.html#WAITING), any thread can enter this state by calling any one of the following three methods:

1.  _object.wait()_
2.  _thread.join()_ or
3.  _LockSupport.park()_

Note that in _wait()_ and _join()_ – we do not define any timeout period as that scenario is covered in the next section.

We have [a separate tutorial](https://www.baeldung.com/java-wait-notify) that discusses in detail the use of _wait()_, _notify()_ and _notifyAll()_.

For now, let's try to reproduce this state:

```java
public class WaitingState implements Runnable {
    public static Thread t1;

    public static void main(String[] args) {
        t1 = new Thread(new WaitingState());
        t1.start();
    }

    public void run() {
        Thread t2 = new Thread(new DemoWaitingStateRunnable());
        t2.start();

        try {
            t2.join();
        } catch (InterruptedException e) {
            Thread.currentThread().interrupt();
            e.printStackTrace();
        }
    }
}

class DemoWaitingStateRunnable implements Runnable {
    public void run() {
        try {
            Thread.sleep(1000);
        } catch (InterruptedException e) {
            Thread.currentThread().interrupt();
            e.printStackTrace();
        }
        
        System.out.println(WaitingState.t1.getState());
    }
}
```

Let's discuss what we're doing here:

1.  We've created and started the _t1_
2.  _t1_ creates a _t2_ and starts it
3.  While the processing of _t2_ continues, we call _t2.join()_, this puts _t1_ in _WAITING_ state until _t2_ has finished execution
4.  Since _t1_ is waiting for _t2_ to complete, we're calling _t1.getState()_ from _t2_

The output here is, as you'd expect:

```plaintext
WAITING
```

### 3.5. Timed Waiting

**A thread is in TIMED_WAITING state when it's waiting for another thread to perform a particular action within a stipulated amount of time.**

[According to JavaDocs](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/lang/Thread.State.html#TIMED_WAITING), there are five ways to put a thread on _TIMED_WAITING_ state:

1.  _thread.sleep(long millis)_
2.  _wait(int timeout)_ or _wait(int timeout, int nanos)_
3.  _thread.join(long_ millis_)_
4.  _LockSupport.parkNanos_
5.  _LockSupport.parkUntil_

To read more about the differences between _wait()_ and _sleep()_ in Java, have a look at [this dedicated article here](https://www.baeldung.com/java-wait-and-sleep).

For now, let's try to quickly reproduce this state:

```java
public class TimedWaitingState {
    public static void main(String[] args) throws InterruptedException {
        DemoTimeWaitingRunnable runnable= new DemoTimeWaitingRunnable();
        Thread t1 = new Thread(runnable);
        t1.start();
        
        // The following sleep will give enough time for ThreadScheduler
        // to start processing of thread t1
        Thread.sleep(1000);
        System.out.println(t1.getState());
    }
}

class DemoTimeWaitingRunnable implements Runnable {
    @Override
    public void run() {
        try {
            Thread.sleep(5000);
        } catch (InterruptedException e) {
            Thread.currentThread().interrupt();
            e.printStackTrace();
        }
    }
}
```

Here, we've created and started a thread _t1_ which is entered into the sleep state with a timeout period of 5 seconds; the output will be:

```plaintext
TIMED_WAITING
```

### 3.6. Terminated

This is the state of a dead thread. **It's in the TERMINATED state when it has either finished execution or was terminated abnormally.**

We have [a dedicated article](https://www.baeldung.com/java-thread-stop) that discusses different ways of stopping the thread.

Let's try to achieve this state in the following example:

```java
public class TerminatedState implements Runnable {
    public static void main(String[] args) throws InterruptedException {
        Thread t1 = new Thread(new TerminatedState());
        t1.start();
        // The following sleep method will give enough time for 
        // thread t1 to complete
        Thread.sleep(1000);
        System.out.println(t1.getState());
    }
    
    @Override
    public void run() {
        // No processing in this block
    }
}
```

Here, while we've started thread _t1_, the very next statement _Thread.sleep(1000)_ gives enough time for _t1_ to complete and so this program gives us the output as:

```plaintext
TERMINATED
```

In addition to the thread state, we can check the _[isAlive()](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/lang/Thread.html#isAlive())_ method to determine if the thread is alive or not. For instance, if we call the _isAlive()_ method on this thread:

```java
Assert.assertFalse(t1.isAlive());
```

It returns _false._ Put simply, **a thread is alive if and only if it has** **been started and has not yet died.**

## 4. Conclusion

In this tutorial, we learned about the life-cycle of a thread in Java. We looked at all six states defined by _Thread.State_ enum and reproduced them with quick examples.

Although the code snippets will give the same output in almost every machine, in some exceptional cases, we may get some different outputs as the exact behavior of Thread Scheduler cannot be determined.