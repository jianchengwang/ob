
## 1. Introduction

_CyclicBarriers_ are synchronization constructs that were introduced with Java 5 as a part of the _java.util.concurrent_ package.

In this article, we'll explore this implementation in a concurrency scenario.

## 2. Java Concurrency – Synchronizers

The _java.util.concurrent_ package contains several classes that help manage a set of threads that collaborate with each other. Some of these include:

- _CyclicBarrier_
- _Phaser_
- _CountDownLatch_
- _Exchanger_
- _Semaphore_
- _SynchronousQueue_

These classes offer out of the box functionality for common interaction patterns between threads.

If we have a set of threads that communicate with each other and resemble one of the common patterns, **we can simply reuse the appropriate library classes (also called Synchronizers) instead of trying to come up with a custom scheme using a set of locks and condition objects** and the _synchronized_ keyword.

Let's focus on the _CyclicBarrier_ going forward.

## 3. CyclicBarrier

A _CyclicBarrier_ is a synchronizer that allows a set of threads to wait for each other to reach a common execution point, also called a _barrier_.

> _CyclicBarriers_ are used in programs in which we have a fixed number of threads that must wait for each other to reach a common point before continuing execution.

**The barrier is called cyclic because it can be re-used after the waiting threads are released.**

## 4. Usage

The constructor for a _CyclicBarrier_ is simple. It takes a single integer that denotes the number of threads that need to call the _await()_ method on the barrier instance to signify reaching the common execution point:

```java
public CyclicBarrier(int parties)
```

The threads that need to synchronize their execution are also called _parties_ and calling the _await()_ method is how we can register that a certain thread has reached the barrier point.

This call is synchronous and the thread calling this method suspends execution till a specified number of threads have called the same method on the barrier. **This situation where the required number of threads have called await(), is called tripping the barrier.**

Optionally, we can pass the second argument to the constructor, which is a _Runnable_ instance. This has logic that would be run by the last thread that trips the barrier:

```java
public CyclicBarrier(int parties, Runnable barrierAction)
```

## 5. Implementation

To see _CyclicBarrier_ in action, let's consider the following scenario:

There's an operation that a fixed number of threads perform and store the corresponding results in a list. When all threads finish performing their action, one of them (typically the last one that trips the barrier) starts processing the data that was fetched by each of these.

Let's implement the main class where all the action happens:

```java
public class CyclicBarrierDemo {

    private CyclicBarrier cyclicBarrier;
    private List<List<Integer>> partialResults
     = Collections.synchronizedList(new ArrayList<>());
    private Random random = new Random();
    private int NUM_PARTIAL_RESULTS;
    private int NUM_WORKERS;

    // ...
}
```

This class is pretty straight forward – _NUM_WORKERS_ is the number of threads that are going to execute and _NUM_PARTIAL_RESULTS_ is the number of results that each of the worker threads is going to produce.

Finally, we have _partialResults_ that are a list that's going to store the results of each of these worker threads. Do note that this list is a _SynchronizedList_ because multiple threads will be writing to it at the same time, and the _add()_ method isn't thread-safe on a plain _ArrayList_.

Now let's implement the logic of each of the worker threads:

```java
public class CyclicBarrierDemo {

    // ...

    class NumberCruncherThread implements Runnable {

        @Override
        public void run() {
            String thisThreadName = Thread.currentThread().getName();
            List<Integer> partialResult = new ArrayList<>();

            // Crunch some numbers and store the partial result
            for (int i = 0; i < NUM_PARTIAL_RESULTS; i++) {    
                Integer num = random.nextInt(10);
                System.out.println(thisThreadName
                  + ": Crunching some numbers! Final result - " + num);
                partialResult.add(num);
            }

            partialResults.add(partialResult);
            try {
                System.out.println(thisThreadName 
                  + " waiting for others to reach barrier.");
                cyclicBarrier.await();
            } catch (InterruptedException e) {
                // ...
            } catch (BrokenBarrierException e) {
                // ...
            }
        }
    }

}
```

We'll now implement the logic that runs when the barrier has been tripped.

To keep things simple, let's just add all the numbers in the partial results list:

```java
public class CyclicBarrierDemo {

    // ...
    
    class AggregatorThread implements Runnable {

        @Override
        public void run() {

            String thisThreadName = Thread.currentThread().getName();

            System.out.println(
              thisThreadName + ": Computing sum of " + NUM_WORKERS 
              + " workers, having " + NUM_PARTIAL_RESULTS + " results each.");
            int sum = 0;

            for (List<Integer> threadResult : partialResults) {
                System.out.print("Adding ");
                for (Integer partialResult : threadResult) {
                    System.out.print(partialResult+" ");
                    sum += partialResult;
                }
                System.out.println();
            }
            System.out.println(thisThreadName + ": Final result = " + sum);
        }
    }
}
```

The final step would be to construct the _CyclicBarrier_ and kick things off with a _main()_ method:

```java
public class CyclicBarrierDemo {

    // Previous code
 
    public void runSimulation(int numWorkers, int numberOfPartialResults) {
        NUM_PARTIAL_RESULTS = numberOfPartialResults;
        NUM_WORKERS = numWorkers;

        cyclicBarrier = new CyclicBarrier(NUM_WORKERS, new AggregatorThread());

        System.out.println("Spawning " + NUM_WORKERS
          + " worker threads to compute "
          + NUM_PARTIAL_RESULTS + " partial results each");
 
        for (int i = 0; i < NUM_WORKERS; i++) {
            Thread worker = new Thread(new NumberCruncherThread());
            worker.setName("Thread " + i);
            worker.start();
        }
    }

    public static void main(String[] args) {
        CyclicBarrierDemo demo = new CyclicBarrierDemo();
        demo.runSimulation(5, 3);
    }
}
```

In the above code, we initialized the cyclic barrier with 5 threads that each produce 3 integers as a part of their computation and store the same in the resulting list.

Once the barrier is tripped, the last thread that tripped the barrier executes the logic specified in the AggregatorThread, namely – add all the numbers produced by the threads.

## 6. Results

Here is the output from one execution of the above program – each execution might create different results as the threads can be spawned in a different order:

```bash
Spawning 5 worker threads to compute 3 partial results each
Thread 0: Crunching some numbers! Final result - 6
Thread 0: Crunching some numbers! Final result - 2
Thread 0: Crunching some numbers! Final result - 2
Thread 0 waiting for others to reach barrier.
Thread 1: Crunching some numbers! Final result - 2
Thread 1: Crunching some numbers! Final result - 0
Thread 1: Crunching some numbers! Final result - 5
Thread 1 waiting for others to reach barrier.
Thread 3: Crunching some numbers! Final result - 6
Thread 3: Crunching some numbers! Final result - 4
Thread 3: Crunching some numbers! Final result - 0
Thread 3 waiting for others to reach barrier.
Thread 2: Crunching some numbers! Final result - 1
Thread 2: Crunching some numbers! Final result - 1
Thread 2: Crunching some numbers! Final result - 0
Thread 2 waiting for others to reach barrier.
Thread 4: Crunching some numbers! Final result - 9
Thread 4: Crunching some numbers! Final result - 3
Thread 4: Crunching some numbers! Final result - 5
Thread 4 waiting for others to reach barrier.
Thread 4: Computing final sum of 5 workers, having 3 results each.
Adding 6 2 2 
Adding 2 0 5 
Adding 6 4 0 
Adding 1 1 0 
Adding 9 3 5 
Thread 4: Final result = 46
```

As the above output shows, _Thread 4_ is the one that trips the barrier and also executes the final aggregation logic. It is also not necessary that threads are actually run in the order that they're started as the above example shows.

## 7. Conclusion

In this article, we saw what a _CyclicBarrier_ is, and what kind of situations it is helpful in.

We also implemented a scenario where we needed a fixed number of threads to reach a fixed execution point, before continuing with other program logic.

As always, the code for the tutorial can be found [over on GitHub](https://github.com/eugenp/tutorials/tree/master/core-java-modules/core-java-concurrency-advanced).