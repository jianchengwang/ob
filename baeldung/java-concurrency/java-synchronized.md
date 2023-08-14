
## 1. Overview

In this article, we'll learn using the _synchronized_ block in Java.

Simply put, in a multi-threaded environment, a [race condition](https://www.baeldung.com/cs/race-conditions) occurs when two or more threads attempt to update mutable shared data at the same time. Java offers a mechanism to avoid race conditions by synchronizing thread access to shared data.

A piece of logic marked with _synchronized_ becomes a synchronized block, **allowing only one thread to execute at any given time**.

## 2. Why Synchronization?

Let's consider a typical race condition where we calculate the sum, and multiple threads execute the _calculate()_ method:

```java
public class SynchronizedMethods {

    private int sum = 0;

    public void calculate() {
        setSum(getSum() + 1);
    }

    // standard setters and getters
}
```

Then let's write a simple test:

```java
@Test
public void givenMultiThread_whenNonSyncMethod() {
    ExecutorService service = Executors.newFixedThreadPool(3);
    SynchronizedMethods summation = new SynchronizedMethods();

    IntStream.range(0, 1000)
      .forEach(count -> service.submit(summation::calculate));
    service.awaitTermination(1000, TimeUnit.MILLISECONDS);

    assertEquals(1000, summation.getSum());
}
```

We're using an _ExecutorService_ with a 3-threads pool to execute the _calculate()_ 1000 times.

If we executed this serially, the expected output would be 1000, but **our multi-threaded execution fails almost every time** with an inconsistent actual output:

```shell
java.lang.AssertionError: expected:<1000> but was:<965>
at org.junit.Assert.fail(Assert.java:88)
at org.junit.Assert.failNotEquals(Assert.java:834)
...
```

Of course, we don't find this result unexpected.

A simple way to avoid the race condition is to make the operation thread-safe using the _synchronized_ keyword.

## 3. The Synchronized Keyword

We can use the _synchronized_ keyword on different levels:

-   Instance methods
-   Static methods
-   Code blocks

When we use a _synchronized_ block, Java internally uses a [monitor](https://www.baeldung.com/cs/monitor), also known as a monitor lock or intrinsic lock, to provide synchronization. These monitors are bound to an object; therefore, all synchronized blocks of the same object can have only one thread executing them at the same time.

### 3.1. Synchronized Instance Methods

We can add the _synchronized_ keyword in the method declaration to make the method synchronized:

```java
public synchronized void synchronisedCalculate() {
    setSum(getSum() + 1);
}
```

Notice that once we synchronize the method, the test case passes with the actual output as 1000:

```java
@Test
public void givenMultiThread_whenMethodSync() {
    ExecutorService service = Executors.newFixedThreadPool(3);
    SynchronizedMethods method = new SynchronizedMethods();

    IntStream.range(0, 1000)
      .forEach(count -> service.submit(method::synchronisedCalculate));
    service.awaitTermination(1000, TimeUnit.MILLISECONDS);

    assertEquals(1000, method.getSum());
}
```

Instance methods are _synchronized_ over the instance of the class owning the method, which means only one thread per instance of the class can execute this method.

### 3.2. Synchronized Static Methods

Static methods are _synchronized_ just like instance methods:

```java
 public static synchronized void syncStaticCalculate() {
     staticSum = staticSum + 1;
 }
```

These methods are _synchronized_ on the _Class_ object associated with the class. Since only one _Class_ object exists per JVM per class, only one thread can execute inside a _static_ _synchronized_ method per class, irrespective of the number of instances it has.

Let's test it:

```java
@Test
public void givenMultiThread_whenStaticSyncMethod() {
    ExecutorService service = Executors.newCachedThreadPool();

    IntStream.range(0, 1000)
      .forEach(count -> 
        service.submit(SynchronizedMethods::syncStaticCalculate));
    service.awaitTermination(100, TimeUnit.MILLISECONDS);

    assertEquals(1000, SynchronizedMethods.staticSum);
}
```

### 3.3. Synchronized Blocks Within Methods

Sometimes we don't want to synchronize the entire method, only some instructions within it. We can achieve this by applying synchronized to a block:

```java
public void performSynchronisedTask() {
    synchronized (this) {
        setCount(getCount()+1);
    }
}
```

Then we can test the change:

```java
@Test
public void givenMultiThread_whenBlockSync() {
    ExecutorService service = Executors.newFixedThreadPool(3);
    SynchronizedBlocks synchronizedBlocks = new SynchronizedBlocks();

    IntStream.range(0, 1000)
      .forEach(count -> 
        service.submit(synchronizedBlocks::performSynchronisedTask));
    service.awaitTermination(100, TimeUnit.MILLISECONDS);

    assertEquals(1000, synchronizedBlocks.getCount());
}
```

Notice that we passed a parameter _this_ to the _synchronized_ block. This is the monitor object. The code inside the block gets synchronized on the monitor object. Simply put, only one thread per monitor object can execute inside that code block.

If the method was _static_, we would pass the class name in place of the object reference, and the class would be a monitor for synchronization of the block:

```Java
public static void performStaticSyncTask(){
    synchronized (SynchronisedBlocks.class) {
        setStaticCount(getStaticCount() + 1);
    }
}
```

Let's test the block inside the _static_ method:

```java
@Test
public void givenMultiThread_whenStaticSyncBlock() {
    ExecutorService service = Executors.newCachedThreadPool();

    IntStream.range(0, 1000)
      .forEach(count -> 
        service.submit(SynchronizedBlocks::performStaticSyncTask));
    service.awaitTermination(100, TimeUnit.MILLISECONDS);

    assertEquals(1000, SynchronizedBlocks.getStaticCount());
}
```

### 3.4. Reentrancy

**The lock behind the _synchronized_ methods and blocks is a reentrant.** This means the current thread can acquire the same _synchronized_ lock over and over again while holding it:

```java
Object lock = new Object();
synchronized (lock) {
    System.out.println("First time acquiring it");

    synchronized (lock) {
        System.out.println("Entering again");

         synchronized (lock) {
             System.out.println("And again");
         }
    }
}
```

As shown above, while in a _synchronized_ block, we can repeatedly acquire the same monitor lock.

## 4. Conclusion

In this brief article, we explored different ways of using the _synchronized_ keyword to achieve thread synchronization.

We also learned how a race condition can impact our application and how synchronization helps us avoid that. For more about thread safety using locks in Java, refer to our _java.util.concurrent.Locks_ [article](https://www.baeldung.com/java-concurrent-locks).

The complete code for this article is available [over on GitHub](https://github.com/eugenp/tutorials/tree/master/core-java-modules/core-java-concurrency-simple).
