
## 1. Introduction

In this tutorial, we’ll learn what a monitor is and how we use it in Java.

## 2. What Is a Monitor?

A monitor is a synchronization mechanism that allows threads to have:

-   _mutual exclusion_ – only one thread can execute the method at a certain point in time, using _locks_
-   _cooperation_ – the ability to make threads wait for certain conditions to be met, using _wait-set_

Why is this feature called “monitor”? Because **it monitors how threads access some resources.**

Monitors formally became subject of interest in the early ’70s in the paper written by P.B. Hansen named [Shared Classes](http://citeseerx.ist.psu.edu/viewdoc/download?doi=10.1.1.112.6400&rep=rep1&type=pdf). After that, C.A.R. Hoare wrote the paper [Monitors – an Operating System Structuring Concept](http://citeseerx.ist.psu.edu/viewdoc/download?doi=10.1.1.91.3720&rep=rep1&type=pdf) where he developed the concept of Monitors even further. The paper introduced a form of synchronization and proposed a model of implementation using semaphores.

## 3. Monitor Features

Monitors provide three main features to the concurrent programming:

-   only one thread at a time has mutually exclusive access to a critical code section
-   threads running in a monitor could be blocked while they’re waiting for certain conditions to be met
-   one thread can notify other threads when conditions they’re waiting on are met

## 4. How Does Java Implement Monitors?

A _critical section_ is a part of the code that accesses the same data through different threads.

In Java, we use the [_synchronized_](https://www.baeldung.com/java-synchronized) keyword to mark critical sections. We can use it to mark methods (also called synchronized methods) or even smaller portions of code (synchronized statements).

There are opposed opinions about which approach to favor – method synchronization is usually the [recommended simpler approach](http://journals.ecs.soton.ac.uk/java/tutorial/java/threads/monitors.html), while the synchronized statements could be a better choice from the [security point of view](https://wiki.sei.cmu.edu/confluence/display/java/LCK00-J.+Use+private+final+lock+objects+to+synchronize+classes+that+may+interact+with+untrusted+code?focusedCommentId=88498302#comment-88498302).

In Java, there’s a logical connection between the monitor and every object or class. Hence, they cover instance and also static methods. Mutual exclusion is accomplished with a lock associated with every object and class. This lock is a binary semaphore called a _mutex_.

### 4.1. Building and Exclusive Room Analogy

Java’s implementation of a monitor mechanism relies on two concepts – the _entry set_ and the _wait set_. In literature, authors use a building and exclusive room analogy to represent the monitor mechanism. In this analogy, only one person can be present in an exclusive room at a time.

So, in this analogy:

-   the monitor is a building that contains two rooms and a hallway
-   the synchronized resource is the “exclusive room”
-   _wait set_ is a “waiting room”
-   _entry set_ is a “hallway”
-   threads are people who want to get to the exclusive room

![MonitorsAnalogy](https://www.baeldung.com/wp-content/uploads/sites/4/2020/05/MonitorsAnalogy.png)

When the person wants to enter the exclusive room, he first goes to the hallway (the _entry set_) where he waits for a scheduler. Therefore, the scheduler will pick the person and send him to the exclusive room.

Schedulers in JVMs use a priority-based scheduling algorithm. In case two threads have the same priority, the JVM uses the FIFO approach.

Hence, when the scheduler picks the person, he enters the exclusive room. It could be that some specific situation is happening in this room, so that person needs to go out and wait for the exclusive room to become available again. Therefore, that person will end up in the waiting room (the _wait set_). Consequently, the scheduler will schedule this person to enter an exclusive room later.

Also, it’s important to mention the steps that threads go through during this process, using the same analogy:

-   entering the building – entering the monitor
-   entering the exclusive room – acquiring the monitor
-   being in the exclusive room – owning the monitor
-   leaving the exclusive room – releasing the monitor
-   leaving the building – exiting the monitor.

Luckily, Java does most of the work in the background, and we don’t need to write semaphores when we’re dealing with multi-threaded applications. Therefore, the only thing we need to do is wrap our critical section with the _synchronized_ keywords and it momentarily becomes a monitor region

### 4.2. wait() and notify()

[_wait()_ and _notify()_](https://www.baeldung.com/java-wait-notify) are key methods in Java used in synchronized blocks that enable collaboration between threads.

**_wait()_ orders the calling thread to release the monitor and go to sleep until some other thread enters this monitor and calls _notify()_**.  Also, _notify()_ wakes up the first thread that called _wait()_ on the specific object.

## 5. Conclusion

In this article, we discussed the concept of a monitor and then explained its implementation in Java.

As we learned, it’s the core synchronization mechanism that’s hidden behind the _synchronized_ keyword. It relies on the wait set and entry set, and we explained how this works through a simple analogy.

We also mentioned the usage of wait and notify methods.