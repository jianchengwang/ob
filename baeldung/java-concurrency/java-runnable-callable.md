
## 1. Overview

Since Java's early days, multithreading has been a major aspect of the language. _Runnable_ is the core interface provided for representing multithreaded tasks, and Java 1.5 provided _Callable_ as an improved version of _Runnable_.

In this tutorial, we'll explore the differences and the applications of both interfaces.

## 2. Execution Mechanism

Both interfaces are designed to represent a task that can be run by multiple threads. We can run _Runnable_ tasks using the _Thread_ class or _ExecutorService_, whereas we can only run _Callable_s using the latter.

## 3. Return Values

Let's look deeper at how these interfaces handle return values.

### 3.1. With Runnable

The _Runnable_ interface is a functional interface and has a single _run()_ method that doesn't accept any parameters or return any values.

This works for situations where we aren't looking for a result of the thread execution, such as incoming events logging:

```java
public interface Runnable {
    public void run();
}
```

Let's understand this with an example:

```java
public class EventLoggingTask implements  Runnable{
    private Logger logger
      = LoggerFactory.getLogger(EventLoggingTask.class);

    @Override
    public void run() {
        logger.info("Message");
    }
}
```

In this example, the thread will just read a message from the queue and log it in a log file. There's no value returned from the task.

We can launch the task using _ExecutorService_:

```java
public void executeTask() {
    executorService = Executors.newSingleThreadExecutor();
    Future future = executorService.submit(new EventLoggingTask());
    executorService.shutdown();
}
```

In this case, the _Future_ object will not hold any value.

### 3.2. With Callable

The _Callable_ interface is a generic interface containing a single _call()_ method that returns a generic value _V_:

```java
public interface Callable<V> {
    V call() throws Exception;
}
```

Let's look at calculating the factorial of a number:

```java
public class FactorialTask implements Callable<Integer> {
    int number;

    // standard constructors

    public Integer call() throws InvalidParamaterException {
        int fact = 1;
        // ...
        for(int count = number; count > 1; count--) {
            fact = fact * count;
        }

        return fact;
    }
}
```

The result of _call()_ method is returned within a _Future_ object:

```java
@Test
public void whenTaskSubmitted_ThenFutureResultObtained(){
    FactorialTask task = new FactorialTask(5);
    Future<Integer> future = executorService.submit(task);
 
    assertEquals(120, future.get().intValue());
}
```

## 4. Exception Handling

Let's see how suitable they are for exception handling.

### 4.1. With Runnable

**Since the method signature does not have the “throws” clause specified, we don't have a way to propagate further checked exceptions.**

### 4.2. With Callable

_Callable_‘s _call()_ method contains the “throws _Exception_” clause, so we can easily propagate checked exceptions further:

```java
public class FactorialTask implements Callable<Integer> {
    // ...
    public Integer call() throws InvalidParamaterException {

        if(number < 0) {
            throw new InvalidParamaterException("Number should be positive");
        }
    // ...
    }
}
```

In case of running a _Callable_ using an _ExecutorService_, the exceptions are collected in the _Future_ object. We can check this by making a call to the _Future.get()_ method.

This will throw an _ExecutionException_, which wraps the original exception:

```java
@Test(expected = ExecutionException.class)
public void whenException_ThenCallableThrowsIt() {
 
    FactorialCallableTask task = new FactorialCallableTask(-5);
    Future<Integer> future = executorService.submit(task);
    Integer result = future.get().intValue();
}
```

In the above test, the _ExecutionException_ is thrown since we are passing an invalid number. We can call the _getCause()_ method on this exception object to get the original checked exception.

If we don't make the call to the _get()_ method of _Future_ class, the exception thrown by _call()_ method will not be reported back, and the task will still be marked as completed:

```java
@Test
public void whenException_ThenCallableDoesntThrowsItIfGetIsNotCalled(){
    FactorialCallableTask task = new FactorialCallableTask(-5);
    Future<Integer> future = executorService.submit(task);
 
    assertEquals(false, future.isDone());
}
```

The above test will pass successfully even though we've thrown an exception for the negative values of the parameter to _FactorialCallableTask_.

## 5. Conclusion

In this article, we explored the differences between the _Runnable_ and _Callable_ interfaces.

As always, the complete code for this article is available [over on GitHub](https://github.com/eugenp/tutorials/tree/master/core-java-modules/core-java-concurrency-basic).
