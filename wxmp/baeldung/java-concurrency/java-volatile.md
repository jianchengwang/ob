
## 1. Overview

Without necessary synchronizations, the compiler, runtime, or processors may apply all sorts of optimizations. Even though these optimizations are usually beneficial, sometimes they can cause subtle issues.

Caching and reordering are optimizations that may surprise us in concurrent contexts. Java and the JVM provide many ways to control [memory order](https://www.baeldung.com/java-variable-handles#memory-ordering), and the _volatile_ keyword is one of them.

This tutorial focuses on Java's foundational but often misunderstood concept, the _volatile_ keyword. First, we'll start with some background about how the underlying computer architecture works, and then we'll get familiar with memory order in Java. Further, we'll understand the challenges of concurrency in multiprocessor shared architecture and how volatile help fix them.

## 2. Shared Multiprocessor Architecture

Processors are responsible for executing program instructions. Therefore, they must retrieve the program instructions and required data from RAM.

As CPUs can carry many instructions per second, fetching from RAM isn't ideal for them. To improve this situation, processors use tricks like [Out of Order Execution](https://en.wikipedia.org/wiki/Out-of-order_execution), [Branch Prediction](https://en.wikipedia.org/wiki/Branch_predictor), [Speculative Execution](https://en.wikipedia.org/wiki/Speculative_execution), and Caching.

This is where the following memory hierarchy comes into play:

[![cpu](https://www.baeldung.com/wp-content/uploads/2017/08/cpu.png)](https://www.baeldung.com/wp-content/uploads/2017/08/cpu.png)

As different cores execute more instructions and manipulate more data, they fill their caches with more relevant data and instructions. **This will improve the overall performance at the expense of introducing [cache coherence](https://en.wikipedia.org/wiki/Cache_coherence) challenges**.

**We should think twice about what happens when one thread updates a cached value.**

## 3. Cache Coherence Challenges

To expand more on cache coherence, we'll borrow an example from the book [Java Concurrency in Practice](https://www.oreilly.com/library/view/java-concurrency-in/0321349601/):

```java
public class TaskRunner {

    private static int number;
    private static boolean ready;

    private static class Reader extends Thread {

        @Override
        public void run() {
            while (!ready) {
                Thread.yield();
            }

            System.out.println(number);
        }
    }

    public static void main(String[] args) {
        new Reader().start();
        number = 42;
        ready = true;
    }
}
```

The _TaskRunner_ class maintains two simple variables. Its main method creates another thread that spins on the _ready_ variable as long as it's _false._ When the variable becomes _true,_ the thread prints the _number_ variable.

**Many may expect this program to print 42 after a short delay; however, the delay may be much longer. It may even hang forever or print zero.**

**The cause of these anomalies is the lack of proper memory visibility and reordering**. Let's evaluate them in more detail.

### 3.1. Memory Visibility

This simple example has two application threads: the main and the reader threads. Let's imagine a scenario in which the OS schedules those threads on two different CPU cores, where:

-   The main thread has its copy of _ready_ and _number_ variables in its core cache.
-   The reader thread ends up with its copies, too.
-   The main thread updates the cached values.

Most modern processors write requests that won't be applied right after they're issued. Processors **tend to queue those writes in a special write buffer**. After a while, they'll apply those writes to the main memory all at once.

With all that being said, **when the main thread updates the number and ready variables, there's no guarantee about what the reader thread may see. In other words, the reader thread may immediately see the updated value, with some delay, or never at all.**

This memory visibility may cause liveness issues in programs relying on visibility.

### 3.2. Reordering

To make matters even worse, **the reader thread may see those writes in an order other than the actual program order**. For instance, since we first update the _number_ variable:

```java
public static void main(String[] args) { 
    new Reader().start();
    number = 42; 
    ready = true; 
}
```

We may expect the reader thread to print 42. **But it's actually possible to see zero as the printed value.**

Reordering is an optimization technique for performance improvements. Interestingly, different components may apply this optimization:

-   The processor may flush its write buffer in an order other than the program order.
-   The processor may apply an out-of-order execution technique.
-   The JIT compiler may optimize via reordering.

## 4. volatile Memory Order

We can use volatile to tackle the issues with Cache Coherence.

To ensure that updates to variables propagate predictably to other threads, we should apply the _volatile_ modifier to those variables.

This way, we can communicate with runtime and processor to not reorder any instruction involving the volatile variable. Also, processors understand that they should immediately flush any updates to these variables.

```java
public class TaskRunner {

    private volatile static int number;
    private volatile static boolean ready;

    // same as before
}
```

This way, we communicate with runtime and processor to not reorder any instruction involving the _volatile_ variable. Also, processors understand that they should immediately flush any updates to these variables.

## 5. volatile and Thread Synchronization

For multithreaded applications, we need to ensure a couple of rules for consistent behaviour:

-   Mutual Exclusion – only one thread executes a critical section at a time
-   Visibility – changes made by one thread to the shared data are visible to other threads to maintain data consistency

_synchronized_ methods and blocks provide both of the above properties at the cost of application performance.

_volatile_ is quite a useful keyword because it **can help ensure the visibility aspect of the data change without providing mutual exclusion**. Thus, it's useful where we're ok with multiple threads executing a block of code in parallel, but we need to ensure the visibility property.

## 6. Happens-Before Ordering

The memory visibility effects of _volatile_ variables extend beyond the _volatile_ variables themselves.

To make matters more concrete, suppose thread A writes to a _volatile_ variable, and then thread B reads the same _volatile_ variable. In such cases, **the values that were visible to A before writing the volatile variable will be visible to B after reading the volatile variable:**

[![happens before](https://www.baeldung.com/wp-content/uploads/2017/08/happens-before.png)](https://www.baeldung.com/wp-content/uploads/2017/08/happens-before.png)

**Technically, any write to a volatile field happens-before every subsequent read of the same field**. This is the _volatile_ variable rule of the Java Memory Model ([JMM](https://docs.oracle.com/javase/specs/jls/se8/html/jls-17.html)).

### 6.1. Piggybacking

**Because of the strength of the happens-before memory ordering, sometimes we can piggyback on the visibility properties of another volatile variable**. For instance, in our particular example, we just need to mark the _ready_ variable as _volatile_:

```java
public class TaskRunner {

    private static int number; // not volatile
    private volatile static boolean ready;

    // same as before
}
```

Anything prior to writing _true_ to the _ready_ variable is visible to anything after reading the _ready_ variable. Therefore, the _number_ variable piggybacks on the memory visibility enforced by the _ready_ variable. Simply put, **even though it's not a volatile variable, it's exhibiting a volatile behaviour.**

## 7. Conclusion
In this article, we explored the _volatile_ keyword, its capabilities, and the improvements made to it, starting with Java 5.

As always, the code examples can be found [on GitHub](https://github.com/eugenp/tutorials/tree/master/core-java-modules/core-java-concurrency-simple).



