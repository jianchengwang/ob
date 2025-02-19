
## 1. Introduction

This tutorial is a guide to the functionality and use cases of the _CompletableFuture_ class that was introduced as a Java 8 Concurrency API improvement.

## 2. Asynchronous Computation in Java

Asynchronous computation is difficult to reason about. Usually, we want to think of any computation as a series of steps, but in the case of asynchronous computation, **actions represented as callbacks tend to be either scattered across the code or deeply nested inside each other**. Things get even worse when we need to handle errors that might occur during one of the steps.

The _Future_ interface was added in Java 5 to serve as a result of an asynchronous computation, but it did not have any methods to combine these computations or handle possible errors.

**Java 8 introduced the CompletableFuture class.** Along with the _Future_ interface, it also implemented the _CompletionStage_ interface. This interface defines the contract for an asynchronous computation step that we can combine with other steps.

_CompletableFuture_ is at the same time, a building block and a framework, with **about 50 different methods for composing, combining, and executing asynchronous computation steps and handling errors**.

Such a large API can be overwhelming, but these mostly fall into several clear and distinct use cases.

## 3. Using CompletableFuture as a Simple Future

First, the _CompletableFuture_ class implements the _Future_ interface so that we can **use it as a Future implementation but with additional completion logic**.

For example, we can create an instance of this class with a no-arg constructor to represent some future result, hand it out to the consumers, and complete it at some time in the future using the _complete_ method. The consumers may use the _get_ method to block the current thread until this result is provided.

In the example below, we have a method that creates a _CompletableFuture_ instance, then spins off some computation in another thread and returns the _Future_ immediately.

When the computation is done, the method completes the _Future_ by providing the result to the _complete_ method:

```java
public Future<String> calculateAsync() throws InterruptedException {
    CompletableFuture<String> completableFuture = new CompletableFuture<>();

    Executors.newCachedThreadPool().submit(() -> {
        Thread.sleep(500);
        completableFuture.complete("Hello");
        return null;
    });

    return completableFuture;
}
```

To spin off the computation, we use the _Executor_ API. This method of creating and completing a _CompletableFuture_ can be used with any concurrency mechanism or API, including raw threads.

Notice that **the calculateAsync method returns a Future instance**.

We simply call the method, receive the _Future_ instance, and call the _get_ method on it when we're ready to block for the result.

Also, observe that the _get_ method throws some checked exceptions, namely _ExecutionException_ (encapsulating an exception that occurred during a computation) and _InterruptedException_ (an exception signifying that a thread was interrupted either before or during an activity):

```java
Future<String> completableFuture = calculateAsync();

// ... 

String result = completableFuture.get();
assertEquals("Hello", result);
```

**If we already know the result of a computation**, we can use the static _completedFuture_ method with an argument that represents the result of this computation. Consequently, the _get_ method of the _Future_ will never block, immediately returning this result instead:

```java
Future<String> completableFuture = 
  CompletableFuture.completedFuture("Hello");

// ...

String result = completableFuture.get();
assertEquals("Hello", result);
```

As an alternative scenario, we may want to [**cancel the execution of a _Future_**](https://www.baeldung.com/java-future#2-canceling-a-future-with-cancel).

## 4. CompletableFuture with Encapsulated Computation Logic

The code above allows us to pick any mechanism of concurrent execution, but what if we want to skip this boilerplate and execute some code asynchronously?

Static methods _runAsync_ and _supplyAsync_ allow us to create a _CompletableFuture_ instance out of _Runnable_ and _Supplier_ functional types correspondingly.

_Runnable_ and _Supplier_ are functional interfaces that allow passing their instances as lambda expressions thanks to the new Java 8 feature.

The _Runnable_ interface is the same old interface used in threads and does not allow to return a value.

The _Supplier_ interface is a generic functional interface with a single method that has no arguments and returns a value of a parameterized type.

This allows us to **provide an instance of the Supplier as a lambda expression that does the calculation and returns the result**. It is as simple as:

```java
CompletableFuture<String> future
  = CompletableFuture.supplyAsync(() -> "Hello");

// ...

assertEquals("Hello", future.get());
```

## 5. Processing Results of Asynchronous Computations

The most generic way to process the result of a computation is to feed it to a function. The _thenApply_ method does exactly that; it accepts a _Function_ instance, uses it to process the result, and returns a _Future_ that holds a value returned by a function:

```java
CompletableFuture<String> completableFuture
  = CompletableFuture.supplyAsync(() -> "Hello");

CompletableFuture<String> future = completableFuture
  .thenApply(s -> s + " World");

assertEquals("Hello World", future.get());
```

If we don't need to return a value down the _Future_ chain, we can use an instance of the _Consumer_ functional interface. Its single method takes a parameter and returns _void_.

There's a method for this use case in the _CompletableFuture._ The _thenAccept_ method receives a _Consumer_ and passes it the result of the computation. Then the final _future.get()_ call returns an instance of the _Void_ type:

```java
CompletableFuture<String> completableFuture
  = CompletableFuture.supplyAsync(() -> "Hello");

CompletableFuture<Void> future = completableFuture
  .thenAccept(s -> System.out.println("Computation returned: " + s));

future.get();
```

Finally, if we neither need the value of the computation nor want to return some value at the end of the chain, then we can pass a _Runnable_ lambda to the _thenRun_ method. In the following example, we simply print a line in the console after calling the _future.get():_

```java
CompletableFuture<String> completableFuture 
  = CompletableFuture.supplyAsync(() -> "Hello");

CompletableFuture<Void> future = completableFuture
  .thenRun(() -> System.out.println("Computation finished."));

future.get();
```

## 6. Combining Futures

The best part of the _CompletableFuture_ API is the **ability to combine CompletableFuture instances in a chain of computation steps**.

The result of this chaining is itself a _CompletableFuture_ that allows further chaining and combining. This approach is ubiquitous in functional languages and is often referred to as a monadic design pattern.

**In the following example, we use the _thenCompose_ method to chain two _Futures_ sequentially.**

Notice that this method takes a function that returns a _CompletableFuture_ instance. The argument of this function is the result of the previous computation step. This allows us to use this value inside the next _CompletableFuture_‘s lambda:

```java
CompletableFuture<String> completableFuture 
  = CompletableFuture.supplyAsync(() -> "Hello")
    .thenCompose(s -> CompletableFuture.supplyAsync(() -> s + " World"));

assertEquals("Hello World", completableFuture.get());
```

The _thenCompose_ method, together with _thenApply,_ implements the basic building blocks of the monadic pattern. They closely relate to the _map_ and _flatMap_ methods of _Stream_ and _Optional_ classes, also available in Java 8.

Both methods receive a function and apply it to the computation result, but the _thenCompose_ (_flatMap_) method **receives a function that returns another object of the same type**. This functional structure allows composing the instances of these classes as building blocks.

If we want to execute two independent _Futures_ and do something with their results, we can use the _thenCombine_ method that accepts a _Future_ and a _Function_ with two arguments to process both results:

```java
CompletableFuture<String> completableFuture 
  = CompletableFuture.supplyAsync(() -> "Hello")
    .thenCombine(CompletableFuture.supplyAsync(
      () -> " World"), (s1, s2) -> s1 + s2));

assertEquals("Hello World", completableFuture.get());
```

A simpler case is when we want to do something with two _Futures_‘ results but don't need to pass any resulting value down a _Future_ chain. The _thenAcceptBoth_ method is there to help:

```java
CompletableFuture future = CompletableFuture.supplyAsync(() -> "Hello")
  .thenAcceptBoth(CompletableFuture.supplyAsync(() -> " World"),
    (s1, s2) -> System.out.println(s1 + s2));
```

## 7. Difference Between thenApply() and thenCompose()

In our previous sections, we've shown examples regarding _thenApply()_ and _thenCompose()_. Both APIs help chain different _CompletableFuture_ calls, but the usage of these two functions are different.

### 7.1. thenApply()

**We can use this method to work with the result of the previous call.** However, a key point to remember is that the return type will be combined of all calls.

So this method is useful when we want to transform the result of a _CompletableFuture_ call:

```java
CompletableFuture<Integer> finalResult = compute().thenApply(s-> s + 1);
```

### 7.2. thenCompose()

The _thenCompose()_ is similar to _thenApply()_ in that both return a new CompletionStage. However, **thenCompose() uses the previous stage as the argument**. It will flatten and return a _Future_ with the result directly, rather than a nested future as we observed in _thenApply():_

```java
CompletableFuture<Integer> computeAnother(Integer i){
    return CompletableFuture.supplyAsync(() -> 10 + i);
}
CompletableFuture<Integer> finalResult = compute().thenCompose(this::computeAnother);
```

So if the idea is to chain _CompletableFuture_ methods, then it’s better to use _thenCompose()_.

Also, note that the difference between these two methods is analogous to [the difference between _map()_ and _flatMap()_](https://www.baeldung.com/java-difference-map-and-flatmap)_._

## 8. Running Multiple Futures in Parallel

When we need to execute multiple _Futures_ in parallel, we usually want to wait for all of them to execute and then process their combined results.

The _CompletableFuture.allOf_ static method allows to wait for the completion of all of the _Futures_ provided as a var-arg:

```java
CompletableFuture<String> future1  
  = CompletableFuture.supplyAsync(() -> "Hello");
CompletableFuture<String> future2  
  = CompletableFuture.supplyAsync(() -> "Beautiful");
CompletableFuture<String> future3  
  = CompletableFuture.supplyAsync(() -> "World");

CompletableFuture<Void> combinedFuture 
  = CompletableFuture.allOf(future1, future2, future3);

// ...

combinedFuture.get();

assertTrue(future1.isDone());
assertTrue(future2.isDone());
assertTrue(future3.isDone());
```

Notice that the return type of the _CompletableFuture.allOf()_ is a _CompletableFuture<>_. The limitation of this method is that it does not return the combined results of all _Futures_. Instead, we have to get results from _Futures_ manually. Fortunately, _CompletableFuture.join()_ method and Java 8 Streams API makes it simple:

```java
String combined = Stream.of(future1, future2, future3)
  .map(CompletableFuture::join)
  .collect(Collectors.joining(" "));

assertEquals("Hello Beautiful World", combined);
```

The _CompletableFuture.join()_ method is similar to the _get_ method, but it throws an unchecked exception in case the _Future_ does not complete normally. This makes it possible to use it as a method reference in the _Stream.map()_ method.

## 9. Handling Errors

For error handling in a chain of asynchronous computation steps, we have to adapt the _throw/catch_ idiom in a similar fashion.

Instead of catching an exception in a syntactic block, the _CompletableFuture_ class allows us to handle it in a special _handle_ method. This method receives two parameters: a result of a computation (if it finished successfully) and the exception thrown (if some computation step did not complete normally).

In the following example, we use the _handle_ method to provide a default value when the asynchronous computation of a greeting was finished with an error because no name was provided:

```java
String name = null;

// ...

CompletableFuture<String> completableFuture  
  =  CompletableFuture.supplyAsync(() -> {
      if (name == null) {
          throw new RuntimeException("Computation error!");
      }
      return "Hello, " + name;
  }).handle((s, t) -> s != null ? s : "Hello, Stranger!");

assertEquals("Hello, Stranger!", completableFuture.get());
```

As an alternative scenario, suppose we want to manually complete the _Future_ with a value, as in the first example, but also have the ability to complete it with an exception. The _completeExceptionally_ method is intended for just that. The _completableFuture.get()_ method in the following example throws an _ExecutionException_ with a _RuntimeException_ as its cause:

```java
CompletableFuture<String> completableFuture = new CompletableFuture<>();

// ...

completableFuture.completeExceptionally(
  new RuntimeException("Calculation failed!"));

// ...

completableFuture.get(); // ExecutionException
```

In the example above, we could have handled the exception with the _handle_ method asynchronously, but with the _get_ method, we can use the more typical approach of synchronous exception processing.

## 10. Async Methods

Most methods of the fluent API in the _CompletableFuture_ class have two additional variants with the _Async_ postfix. These methods are usually intended for **running a corresponding execution step in another thread**.

The methods without the _Async_ postfix run the next execution stage using a calling thread. In contrast, the _Async_ method without the _Executor_ argument runs a step using the common _fork/join_ pool implementation of _Executor_ that is accessed with the _ForkJoinPool.commonPool()_, as long as [parallelism > 1](https://www.baeldung.com/java-when-to-use-parallel-stream#2-common-thread-pool). Finally, the _Async_ method with an _Executor_ argument runs a step using the passed _Executor_.

Here's a modified example that processes the result of a computation with a _Function_ instance. The only visible difference is the _thenApplyAsync_ method, but under the hood, the application of a function is wrapped into a _ForkJoinTask_ instance (for more information on the _fork/join_ framework, see the article [“Guide to the Fork/Join Framework in Java”](https://www.baeldung.com/java-fork-join)). This allows us to parallelize our computation even more and use system resources more efficiently:

```java
CompletableFuture<String> completableFuture  
  = CompletableFuture.supplyAsync(() -> "Hello");

CompletableFuture<String> future = completableFuture
  .thenApplyAsync(s -> s + " World");

assertEquals("Hello World", future.get());
```

## 11. JDK 9 CompletableFuture API

Java 9 enhances the _CompletableFuture_ API with the following changes:

- New factory methods added
- Support for delays and timeouts
- Improved support for subclassing

and new instance APIs:

- _Executor defaultExecutor()_
- _CompletableFuture<> newIncompleteFuture()_
- _CompletableFuture<> copy()_
- _CompletionStage<> minimalCompletionStage()_
- _CompletableFuture<> completeAsync(Supplier<? extends T> supplier, Executor executor)_
- _CompletableFuture<T> completeAsync(Supplier<? extends T> supplier)_
- _CompletableFuture<T> orTimeout(long timeout, TimeUnit unit)_
- _CompletableFuture<T> completeOnTimeout(T value, long timeout, TimeUnit unit)_

We also now have a few static utility methods:

- _Executor delayedExecutor(long delay, TimeUnit unit, Executor executor)_
- _Executor delayedExecutor(long delay, TimeUnit unit)_
- _<U> CompletionStage<U> completedStage(U value)_
- _<U> CompletionStage<U> failedStage(Throwable ex)_
- _<U> CompletableFuture<U> failedFuture(Throwable ex)_

Finally, to address timeout, Java 9 has introduced two more new functions:

- _orTimeout()_
- _completeOnTimeout()_

Here's the detailed article for further reading: [Java 9 CompletableFuture API Improvements](https://www.baeldung.com/java-9-completablefuture).

## 12. Conclusion

In this article, we've described the methods and typical use cases of the _CompletableFuture_ class.

The source code for the article is available [over on GitHub](https://github.com/eugenp/tutorials/tree/master/core-java-modules/core-java-concurrency-simple).