
## 1. Overview

In this tutorial, we'll look at one of the most fundamental mechanisms in Java — thread synchronization.

We'll first discuss some essential concurrency-related terms and methodologies.

And we'll develop a simple application where we'll deal with concurrency issues, with the goal of better understanding _wait()_ and _notify()_.

## 2. Thread Synchronization in Java

In a multithreaded environment, multiple threads might try to modify the same resource. Not managing threads properly will of course lead to consistency issues.

### 2.1. Guarded Blocks in Java

One tool we can use to coordinate actions of multiple threads in Java is guarded blocks. Such blocks keep a check for a particular condition before resuming the execution.

With that in mind, we'll make use of the following:

-   _[Object.wait()](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/lang/Object.html#wait())_ to suspend a thread
-   _[Object.notify()](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/lang/Object.html#notify())_ to wake a thread up

We can better understand this from the following diagram depicting the life cycle of a _Thread_:

[![Java - Wait and Notify](https://www.baeldung.com/wp-content/uploads/2018/02/Java_-_Wait_and_Notify.png)](https://www.baeldung.com/wp-content/uploads/2018/02/Java_-_Wait_and_Notify.png)

Please note that there are many ways of controlling this life cycle. However, in this article, we're going to focus only on _wait()_ and _notify()_.

## 3. The wait() Method

Simply put, calling _wait()_ forces the current thread to wait until some other thread invokes _notify()_ or _notifyAll()_ on the same object.

For this, the current thread must own the object's [monitor](https://www.baeldung.com/cs/monitor). According to [Javadocs](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/lang/Object.html#notify()), this can happen in the following ways:

-   when we've executed _synchronized_ instance method for the given object
-   when we've executed the body of a _synchronized_ block on the given object
-   by executing _synchronized static_ methods for objects of type _Class_

**Note that only one active thread can own an object's monitor at a time.**

This _wait()_ method comes with three overloaded signatures. Let's have a look at these.

### 3.1. wait()

The _wait()_ method causes the current thread to wait indefinitely until another thread either invokes _notify()_ for this object or _notifyAll()_.

### 3.2. wait(long timeout)

Using this method, we can specify a timeout after which a thread will be woken up automatically. A thread can be woken up before reaching the timeout using _notify()_ or _notifyAll()_.

Note that calling _wait(0)_ is the same as calling _wait()_.

### 3.3. wait(long timeout, int nanos)

This is yet another signature providing the same functionality. The only difference here is that we can provide higher precision.

The total timeout period (in nanoseconds) is calculated as _1_000_000*timeout + nanos_.

## 4. notify() and notifyAll()

We use the _notify()_ method for waking up threads that are waiting for access to this object's monitor.

There are two ways of notifying waiting threads.

### 4.1. notify()

For all threads waiting on this object's monitor (by using any one of the _wait()_ methods), the method _notify()_ notifies any one of them to wake up arbitrarily. The choice of exactly which thread to wake is nondeterministic and depends upon the implementation.

Since _notify()_ wakes up a single random thread, we can use it to implement mutually exclusive locking where threads are doing similar tasks. But in most cases, it would be more viable to implement _notifyAll()_.

### 4.2. notifyAll()

This method simply wakes all threads that are waiting on this object's monitor.

The awakened threads will compete in the usual manner, like any other thread that is trying to synchronize on this object.

But before we allow their execution to continue, always **define a quick check for the condition required to proceed with the thread.** This is because there may be some situations where the thread got woken up without receiving a notification (this scenario is discussed later in an example).

## 5. Sender-Receiver Synchronization Problem

Now that we understand the basics, let's go through a simple _Sender_–_Receiver_ application that will make use of the _wait()_ and _notify()_ methods to set up synchronization between them:

-   The _Sender_ is supposed to send a data packet to the _Receiver_.
-   The _Receiver_ cannot process the data packet until the _Sender_ finishes sending it.
-   Similarly, the _Sender_ shouldn't attempt to send another packet unless the _Receiver_ has already processed the previous packet.

Let's first create a _Data_ class that consists of the data _packet_ that will be sent from _Sender_ to _Receiver_. We'll use _wait()_ and _notifyAll()_ to set up synchronization between them:

```java
public class Data {
    private String packet;
    
    // True if receiver should wait
    // False if sender should wait
    private boolean transfer = true;
 
    public synchronized String receive() {
        while (transfer) {
            try {
                wait();
            } catch (InterruptedException e) {
                Thread.currentThread().interrupt(); 
                System.err.println("Thread Interrupted");
            }
        }
        transfer = true;
        
        String returnPacket = packet;
        notifyAll();
        return returnPacket;
    }
 
    public synchronized void send(String packet) {
        while (!transfer) {
            try { 
                wait();
            } catch (InterruptedException e) {
                Thread.currentThread().interrupt(); 
                System.err.println("Thread Interrupted");
            }
        }
        transfer = false;
        
        this.packet = packet;
        notifyAll();
    }
}
```

Let's break down what's going on here:

-   The _packet_ variable denotes the data that is being transferred over the network.
-   We have a _boolean_ variable _transfer_, which the _Sender_ and _Receiver_ will use for synchronization:
    -   If this variable is _true_, the _Receiver_ should wait for _Sender_ to send the message.
    -   If it's _false_, _Sender_ should wait for _Receiver_ to receive the message.
-   The _Sender_ uses the _send()_ method to send data to the _Receiver_:
    -   If _transfer_ is _false_, we'll wait by calling _wait()_ on this thread.
    -   But when it is _true_, we toggle the status, set our message, and call _notifyAll()_ to wake up other threads to specify that a significant event has occurred and they can check if they can continue execution.
-   Similarly, the _Receiver_ will use the _receive()_ method:
    -   If the _transfer_ was set to _false_ by _Sender_, only then will it proceed, otherwise we'll call _wait()_ on this thread.
    -   When the condition is met, we toggle the status, notify all waiting threads to wake up, and return the data packet that was received.

### 5.1. Why Enclose wait() in a while Loop?

Since _notify()_ and _notifyAll()_ randomly wake up threads that are waiting on this object's monitor, it's not always important that the condition is met. Sometimes the thread is woken up, but the condition isn't actually satisfied yet.

We can also define a check to save us from spurious wakeups — where a thread can wake up from waiting without ever having received a notification.

### 5.2. Why Do We Need to Synchronize send() and receive() Methods?

We placed these methods inside _synchronized_ methods to provide intrinsic locks. If a thread calling _wait()_ method does not own the inherent lock, an error will be thrown.

We'll now create _Sender_ and _Receiver_ and implement the _Runnable_ interface on both so that their instances can be executed by a thread.

First, we'll see how _Sender_ will work:

```java
public class Sender implements Runnable {
    private Data data;
 
    // standard constructors
 
    public void run() {
        String packets[] = {
          "First packet",
          "Second packet",
          "Third packet",
          "Fourth packet",
          "End"
        };
 
        for (String packet : packets) {
            data.send(packet);

            // Thread.sleep() to mimic heavy server-side processing
            try {
                Thread.sleep(ThreadLocalRandom.current().nextInt(1000, 5000));
            } catch (InterruptedException e) {
                Thread.currentThread().interrupt(); 
                System.err.println("Thread Interrupted"); 
            }
        }
    }
}
```

Let's take a closer look at this _Sender_:

-   We're creating some random data packets that will be sent across the network in _packets[]_ array.
-   For each packet, we're merely calling send().
-   Then we're calling _Thread.sleep()_ with random interval to mimic heavy server-side processing.

Finally, let's implement our _Receiver_:

```java
public class Receiver implements Runnable {
    private Data load;
 
    // standard constructors
 
    public void run() {
        for(String receivedMessage = load.receive();
          !"End".equals(receivedMessage);
          receivedMessage = load.receive()) {
            
            System.out.println(receivedMessage);

            //Thread.sleep() to mimic heavy server-side processing
            try {
                Thread.sleep(ThreadLocalRandom.current().nextInt(1000, 5000));
            } catch (InterruptedException e) {
                Thread.currentThread().interrupt(); 
                System.err.println("Thread Interrupted"); 
            }
        }
    }
}
```

Here, we're simply calling _load.receive()_ in the loop until we get the last _“End”_ data packet.

Let's now see this application in action:

```java
public static void main(String[] args) {
    Data data = new Data();
    Thread sender = new Thread(new Sender(data));
    Thread receiver = new Thread(new Receiver(data));
    
    sender.start();
    receiver.start();
}
```

We'll receive the following output:

```plaintext
First packet
Second packet
Third packet
Fourth packet
```

And here we are. **We've received all data packets in the right, sequential order** and successfully established the correct communication between our sender and receiver.

## 6. Conclusion

In this article, we discussed some core synchronization concepts in Java. More specifically, we focused on how we can use _wait()_ and _notify()_ to solve interesting synchronization problems. Finally, we went through a code sample where we applied these concepts in practice.

Before we close, it's worth mentioning that all these low-level APIs, such as _wait()_, _notify()_ and _notifyAll()_, are traditional methods that work well, but higher-level mechanisms are often simpler and better — such as Java's native _Lock_ and _Condition_ interfaces (available in _java.util.concurrent.locks_ package).

For more information on the _java.util.concurrent_ package, visit our [overview of the java.util.concurrent](https://www.baeldung.com/java-util-concurrent) article. And _Lock_ and _Condition_ are covered in the [guide to java.util.concurrent.Locks](https://www.baeldung.com/java-concurrent-locks).

As always, the complete code snippets used in this article are available [over on GitHub.](https://github.com/eugenp/tutorials/tree/master/core-java-modules/core-java-concurrency-simple)

