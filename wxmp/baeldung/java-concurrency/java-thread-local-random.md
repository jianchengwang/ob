
## 1. Overview

Generating random values is a very common task. This is why Java provides the _java.util.Random_ class.

**However, this class doesn't perform well in a multi-threaded environment.**

In a simplified way, the reason for the poor performance of _Random_ in a multi-threaded environment is due to contention – given that multiple threads share the same _Random_ instance.

To address that limitation, **Java introduced the java.util.concurrent.ThreadLocalRandom class in JDK 7 – for generating random numbers in a multi-threaded environment**.

Let's see how _ThreadLocalRandom_ performs and how to use it in real-world applications.

## 2. ThreadLocalRandom Over Random

**ThreadLocalRandom is a combination of the _[ThreadLocal](https://www.baeldung.com/java-threadlocal)_ and _Random_ classes (more on this later) and is isolated to the current thread.** Thus, it achieves better performance in a multithreaded environment by simply avoiding any concurrent access to instances of _Random_.

The random number obtained by one thread is not affected by the other thread, whereas _java.util.Random_ provides random numbers globally.

Also, unlike _Random,_ _ThreadLocalRandom_ doesn't support setting the seed explicitly. Instead, it overrides the _setSeed(long seed)_ method inherited from _Random_ to always throw an _UnsupportedOperationException_ if called.

### 2.1. Thread Contention

So far, we've established that the _Random_ class performs poorly in highly concurrent environments. To better understand this, let's see how one of its primary operations, [_next(int)_](https://github.com/openjdk/jdk/blob/a8a2246158bc53414394b007cbf47413e62d942e/src/java.base/share/classes/java/util/Random.java#L198), is implemented:

```java
private final AtomicLong seed;

protected int next(int bits) {
    long oldseed, nextseed;
    AtomicLong seed = this.seed;
    do {
        oldseed = seed.get();
        nextseed = (oldseed * multiplier + addend) & mask;
    } while (!seed.compareAndSet(oldseed, nextseed));

    return (int)(nextseed >>> (48 - bits));
}
```

This is a Java implementation for the [Linear Congruential Generator](https://en.wikipedia.org/wiki/Linear_congruential_generator) algorithm. It's obvious that all threads are sharing the same _seed_ instance variable.

To generate the next random set of bits, it first tries to change the shared _seed_ value atomically via _compareAndSet_ or _CAS_ for short.

**When multiple threads attempt to update the seed concurrently using CAS, one thread wins and updates the seed, and the rest lose. Losing threads will try the same process over and over again until they get a chance to update the value and** **ultimately generate the random number.**

This algorithm is lock-free, and different threads can progress concurrently. However, **when the contention is high, the number of CAS failures and retries will hurt the overall performance significantly.**

On the other hand, the _ThreadLocalRandom_ completely removes this contention, as each thread has its own instance of _Random_ and, consequently, its own confined _seed._

Let's now take a look at some of the ways to generate random _int, long_ and _double_ values.

## 3. Generating Random Values Using ThreadLocalRandom

As per the Oracle documentation, **we just need to call ThreadLocalRandom.current() method, and it will return the instance of ThreadLocalRandom for the current thread**. We can then generate random values by invoking available instance methods of the class.

Let's generate a random _int_ value without any bounds:

```java
int unboundedRandomValue = ThreadLocalRandom.current().nextInt());
```

Next, let's see how we can generate a random bounded _int_ value, meaning a value between a given lower and upper limit.

Here's an example of generating a random _int_ value between 0 and 100:

```java
int boundedRandomValue = ThreadLocalRandom.current().nextInt(0, 100);
```

Please note, 0 is the inclusive lower limit and 100 is the exclusive upper limit.

We can generate random values for _long_ and _double_ by invoking _nextLong()_ and _nextDouble()_ methods in a similar way as shown in the examples above.

Java 8 also adds the _nextGaussian()_ method to generate the next normally-distributed value with a 0.0 mean and 1.0 standard deviation from the generator's sequence.

As with the _Random_ class, we can also use the _doubles(), ints()_ and _longs()_ methods to generate streams of random values.

## 4. Comparing ThreadLocalRandom and Random Using JMH

Let's see how we can generate random values in a multi-threaded environment, by using the two classes, then compare their performance using JMH.

First, let's create an example where all the threads are sharing a single instance of _Random._ Here, we're submitting the task of generating a random value using the _Random_ instance to an _ExecutorService:_

```java
ExecutorService executor = Executors.newWorkStealingPool();
List<Callable<Integer>> callables = new ArrayList<>();
Random random = new Random();
for (int i = 0; i < 1000; i++) {
    callables.add(() -> {
         return random.nextInt();
    });
}
executor.invokeAll(callables);
```

Let's check the performance of the code above using JMH benchmarking:

```java
# Run complete. Total time: 00:00:36
Benchmark                                            Mode Cnt Score    Error    Units
ThreadLocalRandomBenchMarker.randomValuesUsingRandom avgt 20  771.613 ± 222.220 us/op
```

Similarly, let's now use _ThreadLocalRandom_ instead of the _Random_ instance, which uses one instance of _ThreadLocalRandom_ for each thread in the pool:

```java
ExecutorService executor = Executors.newWorkStealingPool();
List<Callable<Integer>> callables = new ArrayList<>();
for (int i = 0; i < 1000; i++) {
    callables.add(() -> {
        return ThreadLocalRandom.current().nextInt();
    });
}
executor.invokeAll(callables);
```

Here's the result of using _ThreadLocalRandom:_

```java
# Run complete. Total time: 00:00:36
Benchmark                                                       Mode Cnt Score    Error   Units
ThreadLocalRandomBenchMarker.randomValuesUsingThreadLocalRandom avgt 20  624.911 ± 113.268 us/op
```

Finally, by comparing the JMH results above for both _Random_ and _ThreadLocalRandom_, we can clearly see that the average time taken to generate 1000 random values using _Random_ is 772 microseconds, whereas using _ThreadLocalRandom_ it's around 625 microseconds.

Thus, we can conclude that **ThreadLocalRandom is more efficient in a highly concurrent environment**.

To learn more about **JMH**, check out our previous [article here](https://www.baeldung.com/java-microbenchmark-harness).

## 5. Implementation Details

It's a good mental model to think of a _ThreadLocalRandom_ as a combination of _ThreadLocal_ and _Random_ classes. As a matter of fact, this mental model was aligned with the actual implementation before Java 8.

**As of Java 8, however, this alignment broke down completely as the ThreadLocalRandom became a singleton**. Here's how the [_current()_](https://github.com/openjdk/jdk14u/blob/89deef4dd8b7aac7c3cea6e13c494a438d34d4c4/src/java.base/share/classes/java/util/concurrent/ThreadLocalRandom.java#L176) method looks in Java 8+:

```java
static final ThreadLocalRandom instance = new ThreadLocalRandom();

public static ThreadLocalRandom current() {
    if (U.getInt(Thread.currentThread(), PROBE) == 0)
        localInit();

    return instance;
}
```

It's true that sharing one global _Random_ instance leads to sub-optimal performance in high contention. However, using one dedicated instance per thread is also overkill.

**Instead of a dedicated instance of Random per thread, each thread only needs to maintain its own seed value**. As of Java 8, the _[Thread](https://github.com/openjdk/jdk14u/blob/d48548f5b7713e0d51b107a5e2dfd60383edbd88/src/java.base/share/classes/java/lang/Thread.java#L2059)_ class itself has been retrofitted to maintain the _seed_ value:

```java
public class Thread implements Runnable {
    // omitted

    @jdk.internal.vm.annotation.Contended("tlr")
    long threadLocalRandomSeed;

    @jdk.internal.vm.annotation.Contended("tlr")
    int threadLocalRandomProbe;

    @jdk.internal.vm.annotation.Contended("tlr")
    int threadLocalRandomSecondarySeed;
}
```

The _threadLocalRandomSeed_ variable is responsible for maintaining the current seed value for _ThreadLocalRandom._ Moreover, the secondary seed, _threadLocalRandomSecondarySeed_, is usually used internally by the likes of _ForkJoinPool._

This implementation incorporates a few optimizations to make _ThreadLocalRandom_ even more performant:

- Avoiding [false sharing](https://alidg.me/blog/2020/4/24/thread-local-random#false-sharing) by using the _@Contented_ annotation, which basically adds enough padding to isolate the contended variables in their own cache lines
- Using _sun.misc.Unsafe_ to update these three variables instead of using the Reflection API
- Avoiding extra hashtable lookups associated with the _ThreadLocal_ implementation

## 6. Conclusion

This article illustrated the difference between _java.util.Random_ and _java.util.concurrent.ThreadLocalRandom_.

We also saw the advantage of _ThreadLocalRandom_ over _Random_ in a multithreaded environment, as well as performance and how we can generate random values using the class.

_ThreadLocalRandom_ is a simple addition to the JDK, but it can create a notable impact in highly concurrent applications.

And, as always, the implementation of all of these examples can be found [over on GitHub](https://github.com/eugenp/tutorials/tree/master/core-java-modules/core-java-concurrency-advanced-2).