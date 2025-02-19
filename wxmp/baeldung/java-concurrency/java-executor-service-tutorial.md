
## 1. Overview

_[ExecutorService](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/concurrent/ExecutorService.html)_ is a JDK API that simplifies running tasks in asynchronous mode. Generally speaking, _ExecutorService_ automatically provides a pool of threads and an API for assigning tasks to it.

## 2. Instantiating ExecutorService

### 2.1. Factory Methods of the _Executors_ Class

The easiest way to create _ExecutorService_ is to use one of the factory methods of the _Executors_ class.

For example, the following line of code will create a thread pool with 10 threads:

```java
ExecutorService executor = Executors.newFixedThreadPool(10);
```

There are several other factory methods to create a predefined _ExecutorService_ that meets specific use cases. To find the best method for your needs, consult [Oracle's official documentation](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/concurrent/Executors.html).

### 2.2. Directly Create an ExecutorService

Because _ExecutorService_ is an interface, an instance of any its implementations can be used. There are several implementations to choose from in the _[java.util.concurrent](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/concurrent/Executors.html)_ package, or you can create your own.

For example, the _ThreadPoolExecutor_ class has a few constructors that we can use to configure an executor service and its internal pool:

```java
ExecutorService executorService = 
  new ThreadPoolExecutor(1, 1, 0L, TimeUnit.MILLISECONDS,   
  new LinkedBlockingQueue<Runnable>());
```

You may notice that the code above is very similar to the [source code](https://github.com/openjdk-mirror/jdk7u-jdk/blob/master/src/share/classes/java/util/concurrent/Executors.java#L133) of the factory method _newSingleThreadExecutor()._ For most cases, a detailed manual configuration isn't necessary.

## 3. Assigning Tasks to the ExecutorService

_ExecutorService_ can execute _Runnable_ and _Callable_ tasks. To keep things simple in this article, two primitive tasks will be used. Notice that we use lambda expressions here instead of anonymous inner classes:

```java
Runnable runnableTask = () -> {
    try {
        TimeUnit.MILLISECONDS.sleep(300);
    } catch (InterruptedException e) {
        e.printStackTrace();
    }
};

Callable<String> callableTask = () -> {
    TimeUnit.MILLISECONDS.sleep(300);
    return "Task's execution";
};

List<Callable<String>> callableTasks = new ArrayList<>();
callableTasks.add(callableTask);
callableTasks.add(callableTask);
callableTasks.add(callableTask);
```

We can assign tasks to the _ExecutorService_ using several methods including _execute()_, which is inherited from the _Executor_ interface, and also _submit()_, _invokeAny()_ and _invokeAll()_.

The **_execute()_** method is _void_ and doesn't give any possibility to get the result of a task's execution or to check the task's status (is it running):

```java
executorService.execute(runnableTask);
```

**_submit()_** submits a _Callable_ or a _Runnable_ task to an _ExecutorService_ and returns a result of type _Future_:

```java
Future<String> future = 
  executorService.submit(callableTask);
```

**_invokeAny()_** assigns a collection of tasks to an _ExecutorService_, causing each to run, and returns the result of a successful execution of one task (if there was a successful execution):

```java
String result = executorService.invokeAny(callableTasks);
```

_**invokeAll()**_ assigns a collection of tasks to an _ExecutorService_, causing each to run, and returns the result of all task executions in the form of a list of objects of type _Future_:

```java
List<Future<String>> futures = executorService.invokeAll(callableTasks);
```

Before going further, we need to discuss two more items: shutting down an _ExecutorService_ and dealing with _Future_ return types.

## 4. Shutting Down an ExecutorService

In general, the _ExecutorService_ will not be automatically destroyed when there is no task to process. It will stay alive and wait for new work to do.

In some cases this is very helpful, such as when an app needs to process tasks that appear on an irregular basis or the task quantity is not known at compile time.

On the other hand, an app could reach its end but not be stopped because a waiting _ExecutorService_ will cause the JVM to keep running.

To properly shut down an _ExecutorService_, we have the _shutdown()_ and _shutdownNow()_ APIs.

The **shutdown()** method doesn't cause immediate destruction of the _ExecutorService_. It will make the _ExecutorService_ stop accepting new tasks and shut down after all running threads finish their current work:

```java
executorService.shutdown();
```

The **shutdownNow()** method tries to destroy the _ExecutorService_ immediately, but it doesn't guarantee that all the running threads will be stopped at the same time:

```java
List<Runnable> notExecutedTasks = executorService.shutDownNow();
```

This method returns a list of tasks that are waiting to be processed. It is up to the developer to decide what to do with these tasks.

One good way to shut down the _ExecutorService_ (which is also [recommended by Oracle](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/concurrent/ExecutorService.html)) is to use both of these methods combined with the **awaitTermination()** method:

```java
executorService.shutdown();
try {
    if (!executorService.awaitTermination(800, TimeUnit.MILLISECONDS)) {
        executorService.shutdownNow();
    } 
} catch (InterruptedException e) {
    executorService.shutdownNow();
}
```

With this approach, the _ExecutorService_ will first stop taking new tasks and then wait up to a specified period of time for all tasks to be completed. If that time expires, the execution is stopped immediately.

## 5. The Future Interface

The _submit()_ and _invokeAll()_ methods return an object or a collection of objects of type _Future_, which allows us to get the result of a task's execution or to check the task's status (is it running).

The _Future_ interface provides a special blocking method _get()_, which returns an actual result of the _Callable_ task's execution or _null_ in the case of a _Runnable_ task:

```java
Future<String> future = executorService.submit(callableTask);
String result = null;
try {
    result = future.get();
} catch (InterruptedException | ExecutionException e) {
    e.printStackTrace();
}
```

Calling the _get()_ method while the task is still running will cause execution to block until the task properly executes and the result is available.

With very long blocking caused by the _get()_ method, an application's performance can degrade. If the resulting data is not crucial, it is possible to avoid such a problem by using timeouts:

```java
String result = future.get(200, TimeUnit.MILLISECONDS);
```

If the execution period is longer than specified (in this case, 200 milliseconds), a _TimeoutException_ will be thrown.

We can use the _isDone()_ method to check if the assigned task already processed or not.

The _Future_ interface also provides for canceling task execution with the _cancel()_ method and checking the cancellation with the _isCancelled()_ method:

```java
boolean canceled = future.cancel(true);
boolean isCancelled = future.isCancelled();
```

## 6. The ScheduledExecutorService Interface

The _ScheduledExecutorService_ runs tasks after some predefined delay and/or periodically.

Once again, the best way to instantiate a _ScheduledExecutorService_ is to use the factory methods of the _Executors_ class.

For this section, we use a _ScheduledExecutorService_ with one thread:

```java
ScheduledExecutorService executorService = Executors
  .newSingleThreadScheduledExecutor();
```

To schedule a single task's execution after a fixed delay, use the _scheduled()_ method of the _ScheduledExecutorService_.

Two _scheduled()_ methods allow you to execute _Runnable_ or _Callable_ tasks:

```java
Future<String> resultFuture = 
  executorService.schedule(callableTask, 1, TimeUnit.SECONDS);
```

The _scheduleAtFixedRate()_ method lets us run a task periodically after a fixed delay. The code above delays for one second before executing _callableTask_.

The following block of code will run a task after an initial delay of 100 milliseconds. And after that, it will run the same task every 450 milliseconds:

```java
executorService.scheduleAtFixedRate(runnableTask, 100, 450, TimeUnit.MILLISECONDS);
```

If the processor needs more time to run an assigned task than the _period_ parameter of the _scheduleAtFixedRate()_ method, the _ScheduledExecutorService_ will wait until the current task is completed before starting the next.

If it is necessary to have a fixed length delay between iterations of the task, _scheduleWithFixedDelay()_ should be used.

For example, the following code will guarantee a 150-millisecond pause between the end of the current execution and the start of another one:

```java
executorService.scheduleWithFixedDelay(task, 100, 150, TimeUnit.MILLISECONDS);
```

According to the _scheduleAtFixedRate()_ and _scheduleWithFixedDelay()_ method contracts, period execution of the task will end at the termination of the _ExecutorService_ or if an exception is thrown during task execution.

## 7. ExecutorService vs Fork/Join

After the release of Java 7, many developers decided to replace the _ExecutorService_ framework with the fork/join framework.

This is not always the right decision, however. Despite the simplicity and frequent performance gains associated with fork/join, it reduces developer control over concurrent execution.

_ExecutorService_ gives the developer the ability to control the number of generated threads and the granularity of tasks that should be run by separate threads. The best use case for _ExecutorService_ is the processing of independent tasks, such as transactions or requests according to the scheme “one thread for one task.”

In contrast, [according to Oracle's documentation](https://docs.oracle.com/javase/tutorial/essential/concurrency/forkjoin.html), fork/join was designed to speed up work that can be broken into smaller pieces recursively.

## 8. Conclusion

Despite the relative simplicity of _ExecutorService_, there are a few common pitfalls.

Let's summarize them:

**Keeping an unused ExecutorService alive**: See the detailed explanation in Section 4 on how to shut down an _ExecutorService_.

**Wrong thread-pool capacity while using fixed length thread pool**: It is very important to determine how many threads the application will need to run tasks efficiently. A too-large thread pool will cause unnecessary overhead just to create threads that will mostly be in the waiting mode. Too few can make an application seem unresponsive because of long waiting periods for tasks in the queue.

**Calling a Future‘s get() method after task cancellation**: Attempting to get the result of an already canceled task triggers a _CancellationException_.

**Unexpectedly long blocking with Future‘s get() method**: We should use timeouts to avoid unexpected waits.

As always, the code for this article is available in [the GitHub repository](https://github.com/eugenp/tutorials/tree/master/core-java-modules/core-java-concurrency-simple).