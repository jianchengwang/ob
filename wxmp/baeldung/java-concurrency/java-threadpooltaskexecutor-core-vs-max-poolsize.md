
## 1. Overview

The Spring [_ThreadPoolTaskExecutor_](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/scheduling/concurrent/ThreadPoolTaskExecutor.html) is a JavaBean that provides an abstraction around a [_java.util.concurrent.ThreadPoolExecutor_](https://www.baeldung.com/java-executor-service-tutorial) instance and exposes it as a Spring [_org.springframework.core.task.TaskExecutor_](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/core/task/TaskExecutor.html). Further, it is highly configurable through the properties of _corePoolSize, maxPoolSize, queueCapacity, allowCoreThreadTimeOut_ and _keepAliveSeconds._ In this tutorial, we'll look at the _corePoolSize_ and _maxPoolSize_ properties.

## 2. corePoolSize vs. maxPoolSize

Users new to this abstraction may easily get confused about the difference in the two configuration properties. Therefore, let's look at each independently.

### 2.1. corePoolSize

The **corePoolSize is the minimum number of workers to keep alive** without timing out. It is a configurable property of _ThreadPoolTaskExecutor_. However, the _ThreadPoolTaskExecutor_ abstraction delegates setting this value to the underlying _java.util.concurrent.ThreadPoolExecutor__._ To clarify, all threads may time out — effectively setting the value of _corePoolSize_ to zero if we've set _allowCoreThreadTimeOut_ to _true_.

### 2.2. maxPoolSize

In contrast, the **maxPoolSize defines the maximum number of threads that can ever be created**. Similarly, the _maxPoolSize_ property of _ThreadPoolTaskExecutor_ also delegates its value to the underlying _java.util.concurrent.ThreadPoolExecutor_. To clarify, **maxPoolSize depends on queueCapacity** in that _ThreadPoolTaskExecutor_ will only create a new thread if the number of items in its queue exceeds _queueCapacity_.

## 3. So What's the Difference?

The difference between _corePoolSize_ and _maxPoolSize_ may seem evident. However, there are some subtleties regarding their behavior.

When we submit a new task to the _ThreadPoolTaskExecutor,_ it creates a new thread if fewer than _corePoolSize_ threads are running, even if there are idle threads in the pool, or if fewer than _maxPoolSize_ threads are running and the queue defined by _queueCapacity_ is full.

Next, let's look at some code to see examples of when each property springs into action.

## 4. Examples

Firstly, let's say we have a method that executes new threads, from the _ThreadPoolTaskExecutor_, named _startThreads_:

```java
public void startThreads(ThreadPoolTaskExecutor taskExecutor, CountDownLatch countDownLatch, 
  int numThreads) {
    for (int i = 0; i < numThreads; i++) {
        taskExecutor.execute(() -> {
            try {
                Thread.sleep(100L * ThreadLocalRandom.current().nextLong(1, 10));
                countDownLatch.countDown();
            } catch (InterruptedException e) {
                Thread.currentThread().interrupt();
            }
        });
    }
}
```

Let's test the default configuration of _ThreadPoolTaskExecutor_, which defines a _corePoolSize_ of one thread, an unbounded _maxPoolSize,_ and an unbounded _queueCapacity_. As a result, we expect that no matter how many tasks we start, we'll only have one thread running:

```java
@Test
public void whenUsingDefaults_thenSingleThread() {
    ThreadPoolTaskExecutor taskExecutor = new ThreadPoolTaskExecutor();
    taskExecutor.afterPropertiesSet();

    CountDownLatch countDownLatch = new CountDownLatch(10);
    this.startThreads(taskExecutor, countDownLatch, 10);

    while (countDownLatch.getCount() > 0) {
        Assert.assertEquals(1, taskExecutor.getPoolSize());
    }
}
```

Now, let's alter the _corePoolSize_ to a max of five threads and ensure it behaves as advertised. As a result, we expect five threads to be started no matter the number of tasks submitted to the _ThreadPoolTaskExecutor_:

```java
@Test
public void whenCorePoolSizeFive_thenFiveThreads() {
    ThreadPoolTaskExecutor taskExecutor = new ThreadPoolTaskExecutor();
    taskExecutor.setCorePoolSize(5);
    taskExecutor.afterPropertiesSet();

    CountDownLatch countDownLatch = new CountDownLatch(10);
    this.startThreads(taskExecutor, countDownLatch, 10);

    while (countDownLatch.getCount() > 0) {
        Assert.assertEquals(5, taskExecutor.getPoolSize());
    }
}
```

Similarly, we can increment the _maxPoolSize_ to ten while leaving the _corePoolSize_ at five. As a result, we expect to start only five threads. To clarify, only five threads start because the _queueCapacity_ is still unbounded:

```java
@Test
public void whenCorePoolSizeFiveAndMaxPoolSizeTen_thenFiveThreads() {
    ThreadPoolTaskExecutor taskExecutor = new ThreadPoolTaskExecutor();
    taskExecutor.setCorePoolSize(5);
    taskExecutor.setMaxPoolSize(10);
    taskExecutor.afterPropertiesSet();

    CountDownLatch countDownLatch = new CountDownLatch(10);
    this.startThreads(taskExecutor, countDownLatch, 10);

    while (countDownLatch.getCount() > 0) {
        Assert.assertEquals(5, taskExecutor.getPoolSize());
    }
}
```

Further, we'll now repeat the previous test but increment the _queueCapacity_ to ten and start twenty threads. Therefore, we now expect to start ten threads in total:

```java
@Test
public void whenCorePoolSizeFiveAndMaxPoolSizeTenAndQueueCapacityTen_thenTenThreads() {
    ThreadPoolTaskExecutor taskExecutor = new ThreadPoolTaskExecutor();
    taskExecutor.setCorePoolSize(5);
    taskExecutor.setMaxPoolSize(10);
    taskExecutor.setQueueCapacity(10);
    taskExecutor.afterPropertiesSet();

    CountDownLatch countDownLatch = new CountDownLatch(20);
    this.startThreads(taskExecutor, countDownLatch, 20);

    while (countDownLatch.getCount() > 0) {
        Assert.assertEquals(10, taskExecutor.getPoolSize());
    }
}
```

Likewise, if we had set the _queueCapactity_ to zero and only started ten tasks, we'd also have ten threads in our _ThreadPoolTaskExecutor_.

## 5. Conclusion

_ThreadPoolTaskExecutor_ is a powerful abstraction around a _java.util.concurrent.ThreadPoolExecutor_, providing options for configuring the _corePoolSize_, _maxPoolSize_, and _queueCapacity_. In this tutorial, we looked at the _corePoolSize_ and _maxPoolSize_ properties, as well as how _maxPoolSize_ works in tandem with _queueCapacity_, allowing us to easily create thread pools for any use case.

As always, you can find the code available [over on Github](https://github.com/eugenp/tutorials/tree/master/spring-threads).

