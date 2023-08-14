
## 1. Introduction

In this brief article, **we'll cover stopping a _Thread_ in Java – which is not that simple since the _Thread.stop()_ method is deprecated.**

As explained in [this update from Oracle,](https://docs.oracle.com/javase/8/docs/technotes/guides/concurrency/threadPrimitiveDeprecation.html) _stop()_ can lead to monitored objects being corrupted.

## 2. Using a Flag

Let's start with a class that creates and starts a thread. This task won't end on its own, so we need some way of stopping that thread.

We'll use an atomic flag for that:

```java
public class ControlSubThread implements Runnable {

    private Thread worker;
    private final AtomicBoolean running = new AtomicBoolean(false);
    private int interval;

    public ControlSubThread(int sleepInterval) {
        interval = sleepInterval;
    }
 
    public void start() {
        worker = new Thread(this);
        worker.start();
    }
 
    public void stop() {
        running.set(false);
    }

    public void run() { 
        running.set(true);
        while (running.get()) {
            try { 
                Thread.sleep(interval); 
            } catch (InterruptedException e){ 
                Thread.currentThread().interrupt();
                System.out.println(
                  "Thread was interrupted, Failed to complete operation");
            }
            // do something here 
         } 
    } 
}
```

Rather than having a _while_ loop evaluating a constant _true_, we’re using an _AtomicBoolean_ and now we can start/stop execution by setting it to _true/false._

As explained in our [introduction to Atomic Variables](https://www.baeldung.com/java-atomic-variables), using an _AtomicBoolean_ prevents conflicts in setting and checking the variable from different threads.

## 3. Interrupting a Thread

What happens when _sleep()_ is set to a long interval, or if we're waiting for a _[lock](https://www.baeldung.com/java-util-concurrent)_ that might never be released?

**We face the risk of blocking for a long period or never terminating cleanly.**

We can create the _interrupt()_ for these situations, let's add a few methods and a new flag to the class:

```java
public class ControlSubThread implements Runnable {

    private Thread worker;
    private AtomicBoolean running = new AtomicBoolean(false);
    private int interval;

    // ...

    public void interrupt() {
        running.set(false);
        worker.interrupt();
    }

    boolean isRunning() {
        return running.get();
    }

    boolean isStopped() {
        return stopped.get();
    }

    public void run() {
        running.set(true);
        stopped.set(false);
        while (running.get()) {
            try {
                Thread.sleep(interval);
            } catch (InterruptedException e){
                Thread.currentThread().interrupt();
                System.out.println(
                  "Thread was interrupted, Failed to complete operation");
            }
            // do something
        }
        stopped.set(true);
    }
}
```

We've added an _interrupt()_ method that sets our _running_ flag to false and calls the worker thread's _interrupt()_ method.

If the thread is sleeping when this is called, _sleep()_ will exit with an _InterruptedException,_ as would any other blocking call.

This returns the thread to the loop, and it will exit since _running_ is false.

## 4. Conclusion

In this quick tutorial, we looked at how to use an atomic variable, optionally combined with a call to _interrupt(),_ to cleanly shut down a thread. This is definitely preferable to calling the deprecated _stop()_ method and risking locking forever and memory corruption.