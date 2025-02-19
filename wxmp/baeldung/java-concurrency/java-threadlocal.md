
## 1. Overview

In this tutorial, we'll be looking at the _[ThreadLocal](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/lang/ThreadLocal.html)_ construct from the _java.lang_ package. This gives us the ability to store data individually for the current thread and simply wrap it within a special type of object.

## 2. ThreadLocal API

The _TheadLocal_ construct allows us to store data that will be **accessible only** by **a specific thread**.

Let's say that we want to have an _Integer_ value that will be bundled with the specific thread:

```java
ThreadLocal<Integer> threadLocalValue = new ThreadLocal<>();
```

Next, when we want to use this value from a thread, we only need to call a _get()_ or _set()_ method. Simply put, we can imagine that _ThreadLocal_ stores data inside of a map with the thread as the key.

As a result, when we call a _get()_ method on the _threadLocalValue_, we'll get an _Integer_ value for the requesting thread:

```java
threadLocalValue.set(1);
Integer result = threadLocalValue.get();
```

We can construct an instance of the _ThreadLocal_ by using the _withInitial()_ static method and passing a supplier to it:

```java
ThreadLocal<Integer> threadLocal = ThreadLocal.withInitial(() -> 1);
```

To remove the value from the _ThreadLocal_, we can call the _remove()_ method:

```java
threadLocal.remove();
```

To see how to use the _ThreadLocal_ properly, we'll first look at an example that doesn't use a _ThreadLocal_, and then we'll rewrite our example to leverage that construct.

## 3. Storing User Data in a Map

Let's consider a program that needs to store the user-specific _Context_ data per given user id:

```java
public class Context {
    private String userName;

    public Context(String userName) {
        this.userName = userName;
    }
}
```

We want to have one thread per user id. We'll create a _SharedMapWithUserContext_ class that implements the _Runnable_ interface. The implementation in the _run()_ method calls some database through the _UserRepository_ class that returns a _Context_ object for a given _userId_.

Next, we store that context in the _ConcurentHashMap_ keyed by _userId_:

```java
public class SharedMapWithUserContext implements Runnable {
 
    public static Map<Integer, Context> userContextPerUserId
      = new ConcurrentHashMap<>();
    private Integer userId;
    private UserRepository userRepository = new UserRepository();

    @Override
    public void run() {
        String userName = userRepository.getUserNameForUserId(userId);
        userContextPerUserId.put(userId, new Context(userName));
    }

    // standard constructor
}
```

We can easily test our code by creating and starting two threads for two different _userIds,_ and asserting that we have two entries in the _userContextPerUserId_ map:

```java
SharedMapWithUserContext firstUser = new SharedMapWithUserContext(1);
SharedMapWithUserContext secondUser = new SharedMapWithUserContext(2);
new Thread(firstUser).start();
new Thread(secondUser).start();

assertEquals(SharedMapWithUserContext.userContextPerUserId.size(), 2);
```

## 4. Storing User Data in ThreadLocal

We can rewrite our example to store the user _Context_ instance using a _ThreadLocal_. Each thread will have its own _ThreadLocal_ instance.

When using _ThreadLocal_, we need to be very careful because every _ThreadLocal_ instance is associated with a particular thread. In our example, we have a dedicated thread for each particular _userId_, and this thread is created by us, so we have full control over it.

The _run()_ method will fetch the user context and store it into the _ThreadLocal_ variable using the _set()_ method:

```java
public class ThreadLocalWithUserContext implements Runnable {
 
    private static ThreadLocal<Context> userContext 
      = new ThreadLocal<>();
    private Integer userId;
    private UserRepository userRepository = new UserRepository();

    @Override
    public void run() {
        String userName = userRepository.getUserNameForUserId(userId);
        userContext.set(new Context(userName));
        System.out.println("thread context for given userId: " 
          + userId + " is: " + userContext.get());
    }
    
    // standard constructor
}
```

We can test it by starting two threads that will execute the action for a given _userId_:

```java
ThreadLocalWithUserContext firstUser 
  = new ThreadLocalWithUserContext(1);
ThreadLocalWithUserContext secondUser 
  = new ThreadLocalWithUserContext(2);
new Thread(firstUser).start();
new Thread(secondUser).start();
```

After running this code, we'll see on the standard output that _ThreadLocal_ was set per given thread:

```java
thread context for given userId: 1 is: Context{userNameSecret='18a78f8e-24d2-4abf-91d6-79eaa198123f'}
thread context for given userId: 2 is: Context{userNameSecret='e19f6a0a-253e-423e-8b2b-bca1f471ae5c'}
```

We can see that each of the users has its own _Context_.

## 5. ThreadLocals and Thread Pools

_ThreadLocal_ provides an easy-to-use API to confine some values to each thread. This is a reasonable way of achieving [thread-safety](https://www.baeldung.com/java-thread-safety) in Java. However, **we should be extra careful when we're using ThreadLocals and [thread pools](https://www.baeldung.com/thread-pool-java-and-guava) together**.

In order to better understand this possible caveat, let's consider the following scenario:

1.  First, the application borrows a thread from the pool.
2.  Then it stores some thread-confined values into the current thread's _ThreadLocal_.
3.  Once the current execution finishes, the application returns the borrowed thread to the pool.
4.  After a while, the application borrows the same thread to process another request.
5.  **Since the application didn't perform the necessary cleanups last time, it may re-use the same _ThreadLocal_ data for the new request.**

This may cause surprising consequences in highly concurrent applications.

One way to solve this problem is to manually remove each _ThreadLocal_ once we're done using it. Because this approach needs rigorous code reviews, it can be error-prone.

### 5.1. Extending the ThreadPoolExecutor

As it turns out, **it's possible to extend the _ThreadPoolExecutor_ class and provide a custom hook implementation for the [_beforeExecute()_](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/concurrent/ThreadPoolExecutor.html#beforeExecute(java.lang.Thread,java.lang.Runnable)) and [_afterExecute()_](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/concurrent/ThreadPoolExecutor.html#afterExecute(java.lang.Runnable,java.lang.Throwable)) methods.** The thread pool will call the _beforeExecute()_ method before running anything using the borrowed thread. On the other hand, it'll call the _afterExecute()_ method after executing our logic.

Therefore, we can extend the _ThreadPoolExecutor_ class and remove the _ThreadLocal_ data in the _afterExecute()_ method:

```java
public class ThreadLocalAwareThreadPool extends ThreadPoolExecutor {

    @Override
    protected void afterExecute(Runnable r, Throwable t) {
        // Call remove on each ThreadLocal
    }
}
```

If we submit our requests to this implementation of _[ExecutorService](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/concurrent/ExecutorService.html)_, then we can be sure that using _ThreadLocal_ and thread pools won't introduce safety hazards for our application.

## 6. Conclusion

In this brief article, we examined the _ThreadLocal_ construct. We implemented the logic that uses _ConcurrentHashMap_ that was shared between threads to store the context associated with a particular _userId._ Then we rewrote our example to leverage _ThreadLocal_ to store data associated with a particular _userId_ and a particular thread.

The implementation of all these examples and code snippets can be found [over on GitHub](https://github.com/eugenp/tutorials/tree/master/core-java-modules/core-java-concurrency-advanced).