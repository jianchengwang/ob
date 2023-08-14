
## 1. Overview

The [_ExecutorService_](https://www.baeldung.com/java-executor-service-tutorial) framework makes it easy to process tasks in multiple threads. We're going to exemplify some scenarios in which we wait for threads to finish their execution.

Also, we'll show how to gracefully shutdown an _ExecutorService_ and wait for already running threads to finish their execution.

## 2. After Executor's Shutdown

When using an _Executor,_ we can shut it down by calling the _shutdown()_ or _shutdownNow()_ methods. **Although, it won't wait until all threads stop executing.**

**Waiting for existing threads to complete their execution can be achieved by using the awaitTermination() method.**

This blocks the thread until all tasks complete their execution or the specified timeout is reached:

```java
public void awaitTerminationAfterShutdown(ExecutorService threadPool) {
    threadPool.shutdown();
    try {
        if (!threadPool.awaitTermination(60, TimeUnit.SECONDS)) {
            threadPool.shutdownNow();
        }
    } catch (InterruptedException ex) {
        threadPool.shutdownNow();
        Thread.currentThread().interrupt();
    }
}
```

## 3. Using CountDownLatch

Next, let's look at another approach to solving this problem – using a _CountDownLatch_ to signal the completion of a task.

We can initialize it with a value that represents the number of times it can be decremented before all threads, that have called the _await()_ method, are notified.

For example, if we need the current thread to wait for another _N_ threads to finish their execution, we can initialize the latch using _N_:

```java
ExecutorService WORKER_THREAD_POOL 
  = Executors.newFixedThreadPool(10);
CountDownLatch latch = new CountDownLatch(2);
for (int i = 0; i < 2; i++) {
    WORKER_THREAD_POOL.submit(() -> {
        try {
            // ...
            latch.countDown();
        } catch (InterruptedException e) {
            Thread.currentThread().interrupt();
        }
    });
}

// wait for the latch to be decremented by the two remaining threads
latch.await();
```

## 4. Using _invokeAll()

The first approach that we can use to run threads is the _invokeAll()_ method. **The method returns a list of _Future_ objects after all tasks finish or the timeout expires**.

Also, we must note that the order of the returned _Future_ objects is the same as the list of the provided _Callable_ objects:

```java
ExecutorService WORKER_THREAD_POOL = Executors.newFixedThreadPool(10);

List<Callable<String>> callables = Arrays.asList(
  new DelayedCallable("fast thread", 100), 
  new DelayedCallable("slow thread", 3000));

long startProcessingTime = System.currentTimeMillis();
List<Future<String>> futures = WORKER_THREAD_POOL.invokeAll(callables);

awaitTerminationAfterShutdown(WORKER_THREAD_POOL);

long totalProcessingTime = System.currentTimeMillis() - startProcessingTime;
 
assertTrue(totalProcessingTime >= 3000);

String firstThreadResponse = futures.get(0).get();
 
assertTrue("fast thread".equals(firstThreadResponse));

String secondThreadResponse = futures.get(1).get();
assertTrue("slow thread".equals(secondThreadResponse));
```

## 5. Using ExecutorCompletionService

Another approach to running multiple threads is by using _ExecutorCompletionService._ It uses a supplied _ExecutorService_ to execute tasks.

One difference over _invokeAll()_ is the order in which the _Futures,_ representing the executed tasks are returned. **ExecutorCompletionService uses a queue to store the results in the order they are finished**, while _invokeAll()_ returns a list having the same sequential order as produced by the iterator for the given task list:

```java
CompletionService<String> service
  = new ExecutorCompletionService<>(WORKER_THREAD_POOL);

List<Callable<String>> callables = Arrays.asList(
  new DelayedCallable("fast thread", 100), 
  new DelayedCallable("slow thread", 3000));

for (Callable<String> callable : callables) {
    service.submit(callable);
}
```

The results can be accessed using the _take()_ method:

```java
long startProcessingTime = System.currentTimeMillis();

Future<String> future = service.take();
String firstThreadResponse = future.get();
long totalProcessingTime
  = System.currentTimeMillis() - startProcessingTime;

assertTrue("First response should be from the fast thread", 
  "fast thread".equals(firstThreadResponse));
assertTrue(totalProcessingTime >= 100
  && totalProcessingTime < 1000);
LOG.debug("Thread finished after: " + totalProcessingTime
  + " milliseconds");

future = service.take();
String secondThreadResponse = future.get();
totalProcessingTime
  = System.currentTimeMillis() - startProcessingTime;

assertTrue(
  "Last response should be from the slow thread", 
  "slow thread".equals(secondThreadResponse));
assertTrue(
  totalProcessingTime >= 3000
  && totalProcessingTime < 4000);
LOG.debug("Thread finished after: " + totalProcessingTime
  + " milliseconds");

awaitTerminationAfterShutdown(WORKER_THREAD_POOL);
```

## 6. Conclusion

Depending on the use case, we have various options to wait for threads to finish their execution.

**A CountDownLatch is useful when we need a mechanism to notify one or more threads that a set of operations performed by other threads has finished.**

**ExecutorCompletionService is useful when we need to access the task result as soon as possible and other approaches when we want to wait for all of the running tasks to finish.**

The source code for the article is available [over on GitHub](https://github.com/eugenp/tutorials/tree/master/core-java-modules/core-java-concurrency-basic).