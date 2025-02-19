
## 1. Overview

With the growing demand for writing non-blocking code, we need ways to execute the code asynchronously.

In this tutorial, we'll look at a few ways to achieve [asynchronous programming](https://www.baeldung.com/cs/async-vs-multi-threading) in Java. We'll also explore a few Java libraries that provide out-of-the-box solutions.

## 2. Asynchronous Programming in Java

### 2.1. Thread

We can create a new thread to perform any operation asynchronously. With the release of [lambda expressions](https://www.baeldung.com/java-8-lambda-expressions-tips) in Java 8, it's cleaner and more readable.

Let's create a new thread that computes and prints the factorial of a number:

```java
int number = 20;
Thread newThread = new Thread(() -> {
    System.out.println("Factorial of " + number + " is: " + factorial(number));
});
newThread.start();
```

### 2.2. FutureTask

Since Java 5, the _Future_ interface provides a way to perform asynchronous operations using the _[FutureTask](https://www.baeldung.com/java-future#1-implementing-futures-with-futuretask)_.

We can use the _submit_ method of the _[ExecutorService](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/concurrent/ExecutorService.html)_ to perform the task asynchronously and return the instance of the _FutureTask_.

So let's find the factorial of a number:

```java
ExecutorService threadpool = Executors.newCachedThreadPool();
Future<Long> futureTask = threadpool.submit(() -> factorial(number));

while (!futureTask.isDone()) {
    System.out.println("FutureTask is not finished yet..."); 
} 
long result = futureTask.get(); 

threadpool.shutdown();
```

Here we've used the _isDone_ method provided by the _Future_ interface to check if the task is completed. Once finished, we can retrieve the result using the _get_ method.

### 2.3. CompletableFuture

**Java 8 introduced [_CompletableFuture_](https://www.baeldung.com/java-completablefuture) with a combination of a _Future_ and [_CompletionStage_](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/concurrent/CompletionStage.html).** It provides various methods like _supplyAsync_, _runAsync_, and _thenApplyAsync_ for asynchronous programming.

Now let's use the _CompletableFuture_ in place of the _FutureTask_ to find the factorial of a number:

```java
CompletableFuture<Long> completableFuture = CompletableFuture.supplyAsync(() -> factorial(number));
while (!completableFuture.isDone()) {
    System.out.println("CompletableFuture is not finished yet...");
}
long result = completableFuture.get();
```

We don't need to use the _ExecutorService_ explicitly. The **CompletableFuture internally uses [_ForkJoinPool_](https://www.baeldung.com/java-fork-join) to handle the task asynchronously**. Thus, it makes our code a lot cleaner.

## 3. Guava

[**Guava**](https://www.baeldung.com/guava-21-new) **provides the _[ListenableFuture](https://guava.dev/releases/28.2-jre/api/docs/index.html?com/google/common/util/concurrent/ListenableFuture.html)_ class to perform asynchronous operations.**

First, we'll add the latest [_guava_](https://search.maven.org/search?q=g:com.google.guava%20a:guava) Maven dependency:

```xml
<dependency>
    <groupId>com.google.guava</groupId>
    <artifactId>guava</artifactId>
    <version>31.0.1-jre</version>
</dependency>
```

Then let's find the factorial of a number using the _ListenableFuture_:

```java
ExecutorService threadpool = Executors.newCachedThreadPool();
ListeningExecutorService service = MoreExecutors.listeningDecorator(threadpool);
ListenableFuture<Long> guavaFuture = (ListenableFuture<Long>) service.submit(()-> factorial(number));
long result = guavaFuture.get();
```

Here the _[MoreExecutors](https://guava.dev/releases/28.2-jre/api/docs/index.html?com/google/common/util/concurrent/MoreExecutors.html)_ class provides the instance of the _[ListeningExecutorService](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/concurrent/ExecutorService.html)_ class. Then the _ListeningExecutorService.submit_ method performs the task asynchronously and returns the instance of the _ListenableFuture_.

**Guava also has a [_Futures_](https://guava.dev/releases/28.2-jre/api/docs/com/google/common/util/concurrent/Futures.html) class that provides methods like submitAsync, scheduleAsync, and transformAsync to chain the ListenableFutures, similar to the CompletableFuture.**

For instance, let's see how to use _Futures.submitAsync_ in place of the _ListeningExecutorService.submit_ method:

```java
ListeningExecutorService service = MoreExecutors.listeningDecorator(threadpool);
AsyncCallable<Long> asyncCallable = Callables.asAsyncCallable(new Callable<Long>() {
    public Long call() {
        return factorial(number);
    }
}, service);
ListenableFuture<Long> guavaFuture = Futures.submitAsync(asyncCallable, service);
```

Here the _submitAsync_ method requires an argument of [_AsyncCallable_](https://guava.dev/releases/28.2-jre/api/docs/com/google/common/util/concurrent/AsyncCallable.html), which is created using the [_Callables_](https://guava.dev/releases/28.2-jre/api/docs/com/google/common/util/concurrent/Callables.html) class.

Additionally, the _Futures_ class provides the _addCallback_ method to register the success and failure callbacks:

```java
Futures.addCallback(
  factorialFuture,
  new FutureCallback<Long>() {
      public void onSuccess(Long factorial) {
          System.out.println(factorial);
      }
      public void onFailure(Throwable thrown) {
          thrown.getCause();
      }
  }, 
  service);
```

## 4. EA Async

**Electronic Arts brought the async-await feature from .NET to the Java ecosystem through the [_ea-async_ library](https://github.com/electronicarts/ea-async).**

This library allows writing asynchronous (non-blocking) code sequentially. Therefore, it makes asynchronous programming easier and scales naturally.

First, we'll add the latest [_ea-async_](https://search.maven.org/search?q=g:com.ea.async%20a:ea-async) Maven dependency to the _pom.xml_:

```xml
<dependency>
    <groupId>com.ea.async</groupId>
    <artifactId>ea-async</artifactId>
    <version>1.2.3</version>
</dependency>
```

Then we'll transform the previously discussed _CompletableFuture_ code by using the _await_ method provided by EA's _[Async](https://javadoc.io/doc/com.ea.async/ea-async/latest/com/ea/async/Async.html)_ class:

```java
static { 
    Async.init(); 
}

public long factorialUsingEAAsync(int number) {
    CompletableFuture<Long> completableFuture = CompletableFuture.supplyAsync(() -> factorial(number));
    long result = Async.await(completableFuture);
}
```

Here we make a call to the _Async.init_ method in the _static_ block to initialize the _Async_ runtime instrumentation.

_Async_ instrumentation transforms the code at runtime, and rewrites the call to the _await_ method to behave similarly to using the chain of _CompletableFuture_.

Therefore, **the call to the await method is similar to calling Future.join.**

We can use the – _javaagent_ JVM parameter for compile-time instrumentation. This is an alternative to the _Async.init_ method:

```shell
java -javaagent:ea-async-1.2.3.jar -cp <claspath> <MainClass>
```

Now let's look at another example of writing asynchronous code sequentially.

First, we'll perform a few chain operations asynchronously using the composition methods like _thenComposeAsync_ and _thenAcceptAsync_ of the _CompletableFuture_ class:

```java
CompletableFuture<Void> completableFuture = hello()
  .thenComposeAsync(hello -> mergeWorld(hello))
  .thenAcceptAsync(helloWorld -> print(helloWorld))
  .exceptionally(throwable -> {
      System.out.println(throwable.getCause()); 
      return null;
  });
completableFuture.get();
```

Then we can transform the code using EA's _Async.await()_:

```java
try {
    String hello = await(hello());
    String helloWorld = await(mergeWorld(hello));
    await(CompletableFuture.runAsync(() -> print(helloWorld)));
} catch (Exception e) {
    e.printStackTrace();
}
```

**The implementation resembles the sequential blocking code; however, the await method doesn't block the code.**

As discussed, all calls to the _await_ method will be rewritten by the _Async_ instrumentation to work similarly to the _Future.join_ method.

So once the asynchronous execution of the _hello_ method is finished, the _Future_ result is passed to the _mergeWorld_ method. Then the result is passed to the last execution using the _CompletableFuture.runAsync_ method.

## 5. Cactoos

**Cactoos is a Java library based on object-oriented principles.**

It's an alternative to Google Guava and Apache Commons that provides common objects for performing various operations.

First, let's add the latest _[cactoos](https://search.maven.org/search?q=g:org.cactoos%20a:cactoos)_ Maven dependency:

```xml
<dependency>
    <groupId>org.cactoos</groupId>
    <artifactId>cactoos</artifactId>
    <version>0.43</version>
</dependency>
```

This library provides an _[Async](https://javadoc.io/doc/org.cactoos/cactoos/latest/org/cactoos/func/Async.html)_ class for asynchronous operations.

So we can find the factorial of a number using the instance of Cactoos's _Async_ class:

```java
Async<Integer, Long> asyncFunction = new Async<Integer, Long>(input -> factorial(input));
Future<Long> asyncFuture = asyncFunction.apply(number);
long result = asyncFuture.get();
```

Here **the _apply_ method executes the operation using the _ExecutorService.submit_ method, and returns an instance of the _Future_ interface**.

Similarly, the _Async_ class has the _exec_ method that provides the same feature without a return value.

Note: the Cactoos library is in the initial stages of development and may not be appropriate for production use yet.

## 6. Jcabi-Aspects

Jcabi-Aspects provides the _[@Async](https://aspects.jcabi.com/apidocs-0.22.6/com/jcabi/aspects/Async.html)_ annotation for asynchronous programming through [AspectJ](https://www.baeldung.com/aspectj) AOP aspects.

First, let's add the latest [_jcabi-aspects_](https://search.maven.org/search?q=g:com.jcabi%20a:jcabi-aspects) Maven dependency:

```xml
<dependency>
    <groupId>com.jcabi</groupId>
    <artifactId>jcabi-aspects</artifactId>
    <version>0.22.6</version>
</dependency>
```

The _jcabi-aspects_ library requires AspectJ runtime support, so we'll add the [_aspectjrt_](https://search.maven.org/search?q=g:org.aspectj%20a:aspectjrt) Maven dependency:

```xml
<dependency>
    <groupId>org.aspectj</groupId>
    <artifactId>aspectjrt</artifactId>
    <version>1.9.5</version>
</dependency>
```

Next, we'll add the [_jcabi-maven-plugin_](https://search.maven.org/search?q=g:com.jcabi%20a:jcabi-maven-plugin) plugin that weaves the binaries with AspectJ aspects. The plugin provides the _ajc_ goal that does all the work for us:

```xml
<plugin>
    <groupId>com.jcabi</groupId>
    <artifactId>jcabi-maven-plugin</artifactId>
    <version>0.14.1</version>
    <executions>
        <execution>
            <goals>
                <goal>ajc</goal>
            </goals>
        </execution>
    </executions>
    <dependencies>
        <dependency>
            <groupId>org.aspectj</groupId>
            <artifactId>aspectjtools</artifactId>
            <version>1.9.1</version>
        </dependency>
        <dependency>
            <groupId>org.aspectj</groupId>
            <artifactId>aspectjweaver</artifactId>
            <version>1.9.1</version>
        </dependency>
    </dependencies>
</plugin>
```

Now we're all set to use the AOP aspects for asynchronous programming:

```java
@Async
@Loggable
public Future<Long> factorialUsingJcabiAspect(int number) {
    Future<Long> factorialFuture = CompletableFuture.supplyAsync(() -> factorial(number));
    return factorialFuture;
}
```

When we compile the code, **the library will inject AOP advice in place of the _@Async_ annotation through AspectJ weaving,** for the asynchronous execution of the _factorialUsingJcabiAspect_ method.

Let's compile the class using the Maven command:

```shell
mvn install
```

The output from the _jcabi-maven-plugin_ may look like:

```shell
 --- jcabi-maven-plugin:0.14.1:ajc (default) @ java-async ---
[INFO] jcabi-aspects 0.18/55a5c13 started new daemon thread jcabi-loggable for watching of @Loggable annotated methods
[INFO] Unwoven classes will be copied to /tutorials/java-async/target/unwoven
[INFO] jcabi-aspects 0.18/55a5c13 started new daemon thread jcabi-cacheable for automated cleaning of expired @Cacheable values
[INFO] ajc result: 10 file(s) processed, 0 pointcut(s) woven, 0 error(s), 0 warning(s)
```

We can verify if our class is woven correctly by checking the logs in the _jcabi-ajc.log_ file generated by the Maven plugin:

```plaintext
Join point 'method-execution(java.util.concurrent.Future 
com.baeldung.async.JavaAsync.factorialUsingJcabiAspect(int))' 
in Type 'com.baeldung.async.JavaAsync' (JavaAsync.java:158) 
advised by around advice from 'com.jcabi.aspects.aj.MethodAsyncRunner' 
(jcabi-aspects-0.22.6.jar!MethodAsyncRunner.class(from MethodAsyncRunner.java))
```

Then we'll run the class as a simple Java application, and the output will look like:

```shell
17:46:58.245 [main] INFO com.jcabi.aspects.aj.NamedThreads - 
jcabi-aspects 0.22.6/3f0a1f7 started new daemon thread jcabi-loggable for watching of @Loggable annotated methods
17:46:58.355 [main] INFO com.jcabi.aspects.aj.NamedThreads - 
jcabi-aspects 0.22.6/3f0a1f7 started new daemon thread jcabi-async for Asynchronous method execution
17:46:58.358 [jcabi-async] INFO com.baeldung.async.JavaAsync - 
#factorialUsingJcabiAspect(20): 'java.util.concurrent.CompletableFuture@14e2d7c1[Completed normally]' in 44.64µs
```

As we can see, a new daemon thread, _jcabi-async,_ is created by the library that performed the task asynchronously.

Similarly, the logging is enabled by the [_@Loggable_](https://aspects.jcabi.com/apidocs-0.22.6/com/jcabi/aspects/Loggable.html) annotation provided by the library.

## 7. Conclusion

In this article, we learned a few ways of asynchronous programming in Java.

To begin with, we explored Java's in-built features like _FutureTask_ and _CompletableFuture_ for asynchronous programming. Then we examined a few libraries, like EA Async and Cactoos, with out-of-the-box solutions.

We also discussed the support of performing tasks asynchronously using Guava's _ListenableFuture_ and _Futures_ classes. Finally, we touched on the jcabi-AspectJ library that provides AOP features through its _@Async_ annotation for asynchronous method calls.

As usual, all the code implementations are available [over on GitHub](https://github.com/eugenp/tutorials/tree/master/core-java-modules/core-java-concurrency-advanced-3).