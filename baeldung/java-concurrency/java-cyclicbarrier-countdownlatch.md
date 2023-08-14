
## 1. Introduction

In this tutorial, we'll compare _CyclicBarrier_ and _CountDownLatch_ and try to understand the similarities and differences between the two.

## 2. What Are These?

When it comes to concurrency, it can be challenging to conceptualize what each is intended to accomplish.

First and foremost, both **CountDownLatch and CyclicBarrier are used for managing multi-threaded applications**.

And, **they are both intended to express how a given thread or group of threads should wait.**

### 2.1. _CountDownLatch_[](https://www.baeldung.com/java-cyclicbarrier-countdownlatch#1countdownlatch)

A _CountDownLatch_ is a construct that a thread waits on while other threads count down on the latch until it reaches zero.

We can think of this like a dish at a restaurant that is being prepared. No matter which cook prepares however many of the _n_ items, the waiter must _wait_ until all the items are on the plate. If a plate takes _n_ items, any cook will _count down_ on the latch for each item she puts on the plate.

### 2.2. CyclicBarrier

A _CyclicBarrier_ is a reusable construct where a group of threads _waits_ together until all of the threads _arrive_. At that point, the barrier is broken and an _action_ can optionally be taken.

We can think of this like a group of friends. Every time they plan to eat at a restaurant they decide a common point where they can meet. They _wait_ for each other there, and only when everyone _arrives_ can they go to the restaurant to eat together.

### 2.3. Further Reading

And for a lot more detail on each of these individually, refer to our previous tutorials on _[CountDownLatch](https://www.baeldung.com/java-countdown-latch)_ and _[CyclicBarrier](https://www.baeldung.com/java-cyclic-barrier)_ respectively.

## 3. Tasks vs. Threads

Let's take a deeper dive into some of the semantic differences between these two classes.

As stated in the definitions, _CyclicBarrier_ allows a number of threads to wait on each other, whereas _CountDownLatch_ allows one or more threads to wait for a number of tasks to complete.

In short, **CyclicBarrier maintains a count of threads** whereas **CountDownLatch maintains a count of tasks**.

In the following code, we define a _CountDownLatch_ with a count of two. Next, we call _countDown()_ twice from a single thread:

```java
CountDownLatch countDownLatch = new CountDownLatch(2);
Thread t = new Thread(() -> {
    countDownLatch.countDown();
    countDownLatch.countDown();
});
t.start();
countDownLatch.await();

assertEquals(0, countDownLatch.getCount());
```

Once the latch reaches zero, the call to _await_ returns.

Note that in this case, **we were able to have the same thread decrease the count twice.**

**CyclicBarrier, though, is different on this point.**

Similar to the above example, we create a _CyclicBarrier,_ again with a count of two and call _await()_ on it, this time from the same thread:

```java
CyclicBarrier cyclicBarrier = new CyclicBarrier(2);
Thread t = new Thread(() -> {
    try {
        cyclicBarrier.await();
        cyclicBarrier.await();    
    } catch (InterruptedException | BrokenBarrierException e) {
        // error handling
    }
});
t.start();

assertEquals(1, cyclicBarrier.getNumberWaiting());
assertFalse(cyclicBarrier.isBroken());
```

The first difference here is that the threads that are waiting are themselves the barrier.

Second, and more importantly, **the second _await()_ is useless**. **A single thread can't count down a barrier twice.**

Indeed, because _t_ must _wait_ for another thread to call _await()_ – to bring the count to two – _t_‘s second call to _await()_ won't actually be invoked until the barrier is already broken!

In our test, **the barrier hasn't been crossed because we only have one thread waiting and not the two threads that would be required for the barrier to be tripped.** This is also evident from the _cyclicBarrier.isBroken()_ method, which returns _false_.

## 4. Reusability

The second most evident difference between these two classes is reusability. To elaborate, **when the barrier trips in CyclicBarrier, the count resets to its original value.** **CountDownLatch is different because the count never resets.**

In the given code, we define a _CountDownLatch_ with count 7 and count it through 20 different calls:

```java
CountDownLatch countDownLatch = new CountDownLatch(7);
ExecutorService es = Executors.newFixedThreadPool(20);
for (int i = 0; i < 20; i++) {
    es.execute(() -> {
        long prevValue = countDownLatch.getCount();
        countDownLatch.countDown();
        if (countDownLatch.getCount() != prevValue) {
            outputScraper.add("Count Updated");
        }
    }); 
} 
es.shutdown();

assertTrue(outputScraper.size() <= 7);
```

We observe that even though 20 different threads call _countDown()_, the count doesn't reset once it reaches zero.

Similar to the above example, we define a _CyclicBarrier_ with count 7 and wait on it from 20 different threads:

```java
CyclicBarrier cyclicBarrier = new CyclicBarrier(7);

ExecutorService es = Executors.newFixedThreadPool(20);
for (int i = 0; i < 20; i++) {
    es.execute(() -> {
        try {
            if (cyclicBarrier.getNumberWaiting() <= 0) {
                outputScraper.add("Count Updated");
            }
            cyclicBarrier.await();
        } catch (InterruptedException | BrokenBarrierException e) {
            // error handling
        }
    });
}
es.shutdown();

assertTrue(outputScraper.size() > 7);
```

In this case, we observe that the value decreases every time a new thread runs, by resetting to the original value, once it reaches zero.

## 5. Conclusion

All in all, _CyclicBarrier_ and _CountDownLatch_ are both helpful tools for synchronization between multiple threads. However, they are fundamentally different in terms of the functionality they provide. Consider each carefully when determining which is right for the job.

As usual, all the discussed examples can be accessed [over on Github](https://github.com/eugenp/tutorials/tree/master/core-java-modules/core-java-concurrency-advanced-2).