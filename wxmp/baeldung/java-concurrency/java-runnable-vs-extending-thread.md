
## 1. Introduction

“Should I implement a _Runnable_ or extend the _Thread_ class”? is quite a common question.

In this article, we'll see which approach makes more sense in practice and why.

## 2. Using Thread

Let's first define a _SimpleThread_ class that extends _Thread_:

```java
public class SimpleThread extends Thread {

    private String message;

    // standard logger, constructor

    @Override
    public void run() {
        log.info(message);
    }
}
```

Let's also see how we can run a thread of this type:

```java
@Test
public void givenAThread_whenRunIt_thenResult()
  throws Exception {
 
    Thread thread = new SimpleThread(
      "SimpleThread executed using Thread");
    thread.start();
    thread.join();
}
```

We can also use an _ExecutorService_ to execute the thread:

```java
@Test
public void givenAThread_whenSubmitToES_thenResult()
  throws Exception {
    
    executorService.submit(new SimpleThread(
      "SimpleThread executed using ExecutorService")).get();
}
```

That's quite a lot of code for running a single log operation in a separate thread.

Also, note that **SimpleThread cannot extend any other class**, as Java doesn't support multiple inheritance.

## 3. Implementing a Runnable

Now, let's create a simple task which implements the _java.lang.Runnable_ interface:

```java
class SimpleRunnable implements Runnable {
	
    private String message;
	
    // standard logger, constructor
    
    @Override
    public void run() {
        log.info(message);
    }
}
```

The above _SimpleRunnable_ is just a task which we want to run in a separate thread.

There're various approaches we can use for running it; one of them is to use the _Thread_ class:

```java
@Test
public void givenRunnable_whenRunIt_thenResult()
 throws Exception {
    Thread thread = new Thread(new SimpleRunnable(
      "SimpleRunnable executed using Thread"));
    thread.start();
    thread.join();
}
```

We can even use an _ExecutorService_:

```java
@Test
public void givenARunnable_whenSubmitToES_thenResult()
 throws Exception {
    
    executorService.submit(new SimpleRunnable(
      "SimpleRunnable executed using ExecutorService")).get();
}
```

We can read more about _ExecutorService_ in [here](https://www.baeldung.com/java-executor-service-tutorial).

Since we're now implementing an interface, we're free to extend another base class if we need to.

Starting with Java 8, any interface which exposes a single abstract method is treated as a functional interface, which makes it a valid lambda expression target.

**We can rewrite the above Runnable code using a lambda expression**:

```java
@Test
public void givenARunnableLambda_whenSubmitToES_thenResult() 
  throws Exception {
    
    executorService.submit(
      () -> log.info("Lambda runnable executed!"));
}
```

## 4. Runnable or Thread?

Simply put, we generally encourage the use of _Runnable_ over _Thread_:

-   When extending the _Thread_ class, we're not overriding any of its methods. Instead, we override the method of _Runnable (_which _Thread_ happens to implement_)_. This is a clear violation of IS-A _Thread_ principle
-   Creating an implementation of _Runnable_ and passing it to the _Thread_ class utilizes composition and not inheritance – which is more flexible
-   After extending the _Thread_ class, we can't extend any other class
-   From Java 8 onwards, _Runnables_ can be represented as lambda expressions

## 5. Conclusion

In this quick tutorial, we saw how implementing _Runnable_ is typically a better approach than extending the _Thread_ class.