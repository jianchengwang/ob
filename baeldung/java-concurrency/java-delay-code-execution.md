
## 1. Introduction

It is relatively common for Java programs to add a delay or pause in their operation. This can be useful for task pacing or to pause execution until another task completes.

This tutorial will describe two ways to implement delays in Java.

## 2. A Thread-Based Approach

**When a Java program runs, it spawns a process** that runs on the host machine. **This process contains at least one thread – the main thread** – in which the program runs. Furthermore, Java enables [multithreading](https://www.baeldung.com/java-thread-lifecycle), which enables applications to create new threads that run in parallel, or asynchronously, to the main thread.

### 2.1. Using Thread.sleep

A quick and dirty way to pause in Java is to tell the current thread to sleep for a specified amount of time. This can be done using _Thread.sleep(milliseconds)_:

```java
try {
    Thread.sleep(secondsToSleep * 1000);
} catch (InterruptedException ie) {
    Thread.currentThread().interrupt();
}
```

**It is good practice to wrap the sleep method in a try/catch block in case another thread interrupts the sleeping thread.** In this case, we catch the _InterruptedException_ and explicitly interrupt the current thread, so it can be caught later and handled. This is more important in a multi-threaded program, but still good practice in a single-threaded program in case we add other threads later.

### 2.2. Using TimeUnit.sleep

**For better readability, we can use TimeUnit.XXX.sleep(y), where XXX is the time unit to sleep for (SECONDS, MINUTES, etc.), and y is the number of that unit to sleep for.** This uses _Thread.sleep_ behind the scenes. Here's an example of the _TimeUnit_ syntax:

```java
try {
    TimeUnit.SECONDS.sleep(secondsToSleep);
} catch (InterruptedException ie) {
    Thread.currentThread().interrupt();
}
```

However, **there are some disadvantages to using these thread-based methods**:

- The sleep times are not exactly precise, especially when using smaller time increments like milliseconds and nanoseconds
- When used inside of loops, sleep will drift slightly between loop iterations due to other code execution so the execution time could get imprecise after many iterations

## 3. An ExecutorService-Based Approach

Java provides the [_ScheduledExecutorService_](https://www.baeldung.com/java-executor-service-tutorial) interface, which is a more robust and precise solution. This interface can schedule code to run once after a specified delay or at fixed time intervals.

To run a piece of code once after a delay, we can use the _schedule_ method:

```java
ScheduledExecutorService executorService = Executors.newSingleThreadScheduledExecutor();

executorService.schedule(Classname::someTask, delayInSeconds, TimeUnit.SECONDS);
```

The _Classname::someTask_ part is where we specify the method that will run after the delay:

- _someTask_ is the name of the method we want to execute
- _Classname_ is the name of the class that contains the _someTask_ method

To run a task at fixed time intervals, we can use the _scheduleAtFixedRate_ method:

```java
ScheduledExecutorService executorService = Executors.newSingleThreadScheduledExecutor();

executorService.scheduleAtFixedRate(Classname::someTask, 0, delayInSeconds, TimeUnit.SECONDS);
```

This will repeatedly call the _someTask_ method, pausing for _delayInSeconds_ between each call.

Besides allowing more timing options, the _ScheduledExecutorService_ method yields more precise time intervals, since it prevents issues with drift.

## 4. Conclusion

In this article, we discussed two methods for creating delays in Java programs.

The full code for this article can be found [over on Github](https://github.com/eugenp/tutorials/tree/master/core-java-modules/core-java-concurrency-basic-2). This is a Maven-based project, so it should be easy to import and run as it is.