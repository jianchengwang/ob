
## 1. Overview

In this tutorial, we'll learn about _[Future](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/concurrent/Future.html)_. An interface that's been around since Java 1.5, it can be quite useful when working with asynchronous calls and concurrent processing.

## 2. Creating Futures

Simply put, the _Future_ class represents a future result of an asynchronous computation. This result will eventually appear in the _Future_ after the processing is complete.

Let's see how to write methods that create and return a _Future_ instance.

Long running methods are good candidates for asynchronous processing and the _Future_ interface because we can execute other processes while we're waiting for the task encapsulated in the _Future_ to complete.

Some examples of operations that would leverage the async nature of _Future_ are:

-   computational intensive processes (mathematical and scientific calculations)
-   manipulating large data structures (big data)
-   remote method calls (downloading files, HTML scrapping, web services)

### 2.1. Implementing Futures With FutureTask

For our example, we're going to create a very simple class that calculates the square of an _Integer_. This definitely doesn't fit the long-running methods category, but we're going to put a _Thread.sleep()_ call to it so that it lasts 1 second before completing:

```java
public class SquareCalculator {    
    
    private ExecutorService executor 
      = Executors.newSingleThreadExecutor();
    
    public Future<Integer> calculate(Integer input) {        
        return executor.submit(() -> {
            Thread.sleep(1000);
            return input * input;
        });
    }
}
```

The bit of code that actually performs the calculation is contained within the _call()_ method, and supplied as a lambda expression. As we can see, there's nothing special about it, except for the _sleep()_ call mentioned earlier.

It gets more interesting when we direct our attention to the use of _[Callable](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/concurrent/Callable.html)_ and _[ExecutorService](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/concurrent/ExecutorService.html)_.

_Callable_ is an interface representing a task that returns a result, and has a single _call()_ method. Here we've created an instance of it using a lambda expression.

Creating an instance of _Callable_ doesn't take us anywhere; we still have to pass this instance to an executor that will take care of starting the task in a new thread, and give us back the valuable _Future_ object. That's where _ExecutorService_ comes in.

There are a few ways we can access an _ExecutorService_ instance, and most of them are provided by the utility class _[Executors](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/concurrent/Executors.html)‘_ static factory methods. In this example, we used the basic _newSingleThreadExecutor()_, which gives us an _ExecutorService_ capable of handling a single thread at a time.

Once we have an _ExecutorService_ object, we just need to call _submit(),_ passing our _Callable_ as an argument. Then _submit()_ will start the task and return a _[FutureTask](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/concurrent/Future.html)_ object, which is an implementation of the _Future_ interface.

## 3. Consuming Futures

Up to this point, we've learned how to create an instance of _Future_.

In this section, we'll learn how to work with this instance by exploring all the methods that are part of _Future_‘s API.

### 3.1. Using isDone() and get() to Obtain Results

Now we need to call _calculate(),_ and use the returned _Future_ to get the resulting _Integer_. Two methods from the _Future_ API will help us with this task.

_[Future.isDone()](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/concurrent/Future.html)_ tells us if the executor has finished processing the task. If the task is complete, it will return _true;_ otherwise, it returns _false_.

The method that returns the actual result from the calculation is _[Future.get()](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/concurrent/Future.html)_. We can see that this method blocks the execution until the task is complete. However, this won't be an issue in our example because we'll check if the task is complete by calling _isDone()_.

By using these two methods, we can run other code while we wait for the main task to finish:

```java
Future<Integer> future = new SquareCalculator().calculate(10);

while(!future.isDone()) {
    System.out.println("Calculating...");
    Thread.sleep(300);
}

Integer result = future.get();
```

In this example, we'll write a simple message on the output to let the user know the program is performing the calculation.

The method _get()_ will block the execution until the task is complete. Again, this won't be an issue because in our example, _get()_ will only be called after making sure that the task is finished. So in this scenario, _future.get()_ will always return immediately.

It's worth mentioning that _get()_ has an overloaded version that takes a timeout and a [_TimeUnit_](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/concurrent/TimeUnit.html) as arguments:

```java
Integer result = future.get(500, TimeUnit.MILLISECONDS);
```

The difference between _[get(long, TimeUnit)](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/concurrent/Future.html#get(long,java.util.concurrent.TimeUnit))_ and _[get()](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/concurrent/Future.html#get())_ is that the former will throw a _[TimeoutException](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/concurrent/TimeoutException.html)_ if the task doesn't return before the specified timeout period.

### 3.2. Canceling a Future With cancel()

Suppose we triggered a task, but for some reason, we don't care about the result anymore. We can use _[Future.cancel(boolean)](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/concurrent/Future.html)_ to tell the executor to stop the operation and interrupt its underlying thread:

```java
Future<Integer> future = new SquareCalculator().calculate(4);

boolean canceled = future.cancel(true);
```

Our instance of _Future,_ from the code above, will never complete its operation. In fact, if we try to call _get()_ from that instance, after the call to _cancel()_, the outcome will be a _[CancellationException](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/concurrent/CancellationException.html)_. _[Future.isCancelled()](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/concurrent/Future.html)_ will tell us if a _Future_ was already cancelled. This can be quite useful to avoid getting a _CancellationException_.

It's also possible that a call to _cancel()_ fails. In that case, the returned value will be _false_. It's important to note that _cancel()_ takes a _boolean_ value as an argument. This controls whether the thread executing the task should be interrupted or not.

## 4. More Multithreading With Thread Pools

Our current _ExecutorService_ is single threaded, since it was obtained with the [Executors.newSingleThreadExecutor](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/concurrent/Executors.html#newSingleThreadExecutor()). To highlight this single thread, let's trigger two calculations simultaneously:

```java
SquareCalculator squareCalculator = new SquareCalculator();

Future<Integer> future1 = squareCalculator.calculate(10);
Future<Integer> future2 = squareCalculator.calculate(100);

while (!(future1.isDone() && future2.isDone())) {
    System.out.println(
      String.format(
        "future1 is %s and future2 is %s", 
        future1.isDone() ? "done" : "not done", 
        future2.isDone() ? "done" : "not done"
      )
    );
    Thread.sleep(300);
}

Integer result1 = future1.get();
Integer result2 = future2.get();

System.out.println(result1 + " and " + result2);

squareCalculator.shutdown();
```

Now let's analyze the output for this code:

```java
calculating square for: 10
future1 is not done and future2 is not done
future1 is not done and future2 is not done
future1 is not done and future2 is not done
future1 is not done and future2 is not done
calculating square for: 100
future1 is done and future2 is not done
future1 is done and future2 is not done
future1 is done and future2 is not done
100 and 10000
```

It's clear that the process isn't parallel. We can see that the second task only starts once the first task is complete, making the whole process take around 2 seconds to finish.

To make our program really multi-threaded, we should use a different flavor of _ExecutorService_. Let's see how the behavior of our example changes if we use a thread pool provided by the factory method _[Executors.newFixedThreadPool()](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/concurrent/Executors.html#newFixedThreadPool(int))_:

```java
public class SquareCalculator {
 
    private ExecutorService executor = Executors.newFixedThreadPool(2);
    
    //...
}
```

With a simple change in our _SquareCalculator_ class, we now have an executor which is able to use 2 simultaneous threads.

If we run the exact same client code again, we'll get the following output:

```java
calculating square for: 10
calculating square for: 100
future1 is not done and future2 is not done
future1 is not done and future2 is not done
future1 is not done and future2 is not done
future1 is not done and future2 is not done
100 and 10000
```

This is looking much better now. We can see that the 2 tasks start and finish running simultaneously, and the whole process takes around 1 second to complete.

There are other factory methods that can be used to create thread pools, like _[Executors.newCachedThreadPool()](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/concurrent/Executors.html#newCachedThreadPool()),_ which reuses previously used _Thread_s when they're available, and _[Executors.newScheduledThreadPool()](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/concurrent/Executors.html#newScheduledThreadPool(int)),_ which schedules commands to run after a given delay_._

For more information about _ExecutorService_, read our [article](https://www.baeldung.com/java-executor-service-tutorial) dedicated to the subject.

## 5. Overview of ForkJoinTask

_[ForkJoinTask](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/concurrent/ForkJoinTask.html)_ is an abstract class which implements _Future,_ and is capable of running a large number of tasks hosted by a small number of actual threads in _[ForkJoinPool](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/concurrent/ForkJoinPool.html)_.

In this section, we'll quickly cover the main characteristics of _ForkJoinPool_. For a comprehensive guide about the topic, check out our [Guide to the Fork/Join Framework in Java](https://www.baeldung.com/java-fork-join).

The main characteristic of a _ForkJoinTask_ is that it will usually spawn new subtasks as part of the work required to complete its main task. It generates new tasks by calling _[fork()](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/concurrent/ForkJoinTask.html#fork()),_ and it gathers all results with _[join()](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/concurrent/ForkJoinTask.html#join()),_ thus the name of the class.

There are two abstract classes that implement _ForkJoinTask_: _[RecursiveTask](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/concurrent/RecursiveTask.html),_ which returns a value upon completion, and _[RecursiveAction](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/concurrent/RecursiveAction.html),_ which doesn't return anything. As their names imply, these classes are to be used for recursive tasks, such as file-system navigation or complex mathematical computation.

Let's expand our previous example to create a class that, given an _Integer_, will calculate the sum squares for all of its factorial elements. So for instance, if we pass the number 4 to our calculator, we should get the result from the sum of 4² + 3² + 2² + 1², which is 30.

First, we need to create a concrete implementation of _RecursiveTask_ and implement its _compute()_ method. This is where we'll write our business logic:

```java
public class FactorialSquareCalculator extends RecursiveTask<Integer> {
 
    private Integer n;

    public FactorialSquareCalculator(Integer n) {
        this.n = n;
    }

    @Override
    protected Integer compute() {
        if (n <= 1) {
            return n;
        }

        FactorialSquareCalculator calculator 
          = new FactorialSquareCalculator(n - 1);

        calculator.fork();

        return n * n + calculator.join();
    }
}
```

Notice how we achieve recursiveness by creating a new instance of _FactorialSquareCalculator_ within _compute()_. By calling _fork()_, a non-blocking method, we ask _ForkJoinPool_ to initiate the execution of this subtask.

The _join()_ method will return the result from that calculation, to which we'll add the square of the number we're currently visiting.

Now we just need to create a _ForkJoinPool_ to handle the execution and thread management:

```java
ForkJoinPool forkJoinPool = new ForkJoinPool();

FactorialSquareCalculator calculator = new FactorialSquareCalculator(10);

forkJoinPool.execute(calculator);
```

## 6. Conclusion

In this article, we comprehensively explored the _Future_ interface, touching on all of its methods. We also learned how to leverage the power of thread pools to trigger multiple parallel operations. The main methods from the _ForkJoinTask_ class, _fork()_ and _join(),_ were briefly covered as well.