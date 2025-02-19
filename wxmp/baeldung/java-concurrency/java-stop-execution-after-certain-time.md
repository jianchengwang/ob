
## 1. Overview

In this article, we'll learn how we can end a long-running execution after a certain time. We'll explore the various solutions to this problem. Also, we'll cover some of their pitfalls.

## 2. Using a Loop

Imagine that we're processing a bunch of items in a loop, such as some details of the product items in an e-commerce application, but that it may not be necessary to complete all the items.

In fact, we'd want to process only up to a certain time, and after that, we want to stop the execution and show whatever the list has processed up to that time.

Let's see a quick example:

```java
long start = System.currentTimeMillis();
long end = start + 30 * 1000;
while (System.currentTimeMillis() < end) {
    // Some expensive operation on the item.
}
```

Here, the loop will break if the time has surpassed the limit of 30 seconds. There are some noteworthy points in the above solution:

- Low accuracy: **The loop can run for longer than the imposed time limit**. This will depend on the time each iteration may take. For example, if each iteration may take up to 7 seconds, then the total time can go up to 35 seconds, which is around 17% longer than the desired time limit of 30 seconds
- Blocking: **Such processing in the main thread may not be a good idea as it'll block it for a long time**. Instead, these operations should be decoupled from the main thread

In the next section, we'll discuss how the interrupt-based approach eliminates these limitations.

## 3. Using an Interrupt Mechanism

Here, we'll use a separate thread to perform the long-running operations. The main thread will send an interrupt signal to the worker thread on timeout.

If the worker thread is still alive, it'll catch the signal and stop its execution. If the worker finishes before the timeout, it'll have no impact on the worker thread.

Let's take a look at the worker thread:

```java
class LongRunningTask implements Runnable {
    @Override
    public void run() {
        for (int i = 0; i < Long.MAX_VALUE; i++) {
            if(Thread.interrupted()) {
                return;
            }
        }
    }
}
```

Here, the for loop through _Long.MAX_VALUE_ simulates a long-running operation. Instead of this, there could be any other operation. It's important to **check the interrupt flag because not all the operations are interruptible**. So in those cases, we should manually check the flag.

Also, we should check this flag in every iteration to ensure that the thread stops executing itself within the delay of one iteration at most.

Next, we'll cover three different mechanisms of sending the interrupt signal.

### 3.1. Using a _Timer

Alternatively, we can create a [_TimerTask_](https://www.baeldung.com/java-timer-and-timertask) to interrupt the worker thread upon timeout:

```java
class TimeOutTask extends TimerTask {
    private Thread thread;
    private Timer timer;

    public TimeOutTask(Thread thread, Timer timer) {
        this.thread = thread;
        this.timer = timer;
    }

    @Override
    public void run() {
        if(thread != null && thread.isAlive()) {
            thread.interrupt();
            timer.cancel();
        }
    }
}
```

Here, we've defined a _TimerTask_ that takes a worker thread at the time of its creation. It'll **interrupt the worker thread upon the invocation of its run method**. The [_Timer_](https://www.baeldung.com/java-timer-and-timertask) will trigger the _TimerTask_ after a three seconds delay:

```java
Thread thread = new Thread(new LongRunningTask());
thread.start();

Timer timer = new Timer();
TimeOutTask timeOutTask = new TimeOutTask(thread, timer);
timer.schedule(timeOutTask, 3000);
```

### 3.2. Using the Method Future.get

We can also use the _get_ method of a [_Future_](https://www.baeldung.com/java-future) instead of using a _Timer_:

```java
ExecutorService executor = Executors.newSingleThreadExecutor();
Future future = executor.submit(new LongRunningTask());
try {
    future.get(7, TimeUnit.SECONDS);
} catch (TimeoutException e) {
    future.cancel(true);
} catch (Exception e) {
    // handle other exceptions
} finally {
    executor.shutdownNow();
}
```

Here, we used the _ExecutorService_ to submit the worker thread that returns an instance of _Future_, whose _get_ method will block the main thread until the specified time. It'll raise a _TimeoutException_ after the specified timeout. In the _catch_ block, we are interrupting the worker thread by calling the _cancel_ method on the Future object.

The main benefit of this approach over the previous one is that it **uses a pool to manage the thread, while the Timer uses only a single thread (no pool)**.

### 3.3. Using a _ScheduledExcecutorSercvice

We can also use [_ScheduledExecutorService_](https://www.baeldung.com/java-executor-service-tutorial#ScheduledExecutorService) to interrupt the task. This class is an extension of an _ExecutorService_ and provides the same functionality with the addition of several methods that deal with the scheduling of execution. This can execute the given task after a certain delay of set time units:

```java
ScheduledExecutorService executor = Executors.newScheduledThreadPool(2);
Future future = executor.submit(new LongRunningTask());
Runnable cancelTask = () -> future.cancel(true);

executor.schedule(cancelTask, 3000, TimeUnit.MILLISECONDS);
executor.shutdown();
```

Here, we created a scheduled thread pool of size two with the method _newScheduledThreadPool_. The _ScheduledExecutorService#__schedule_ method takes a [_Runnable_](https://www.baeldung.com/java-runnable-vs-extending-thread), a delay value, and the unit of the delay.

The above program schedules the task to execute after three seconds from the time of submission. This task will cancel the original long-running task.

Note that, unlike the previous approach, we are not blocking the main thread by calling the _Future#get_ method. Therefore, **it's the most preferred approach among all the above-mentioned approaches**.

## 4. Is There a Guarantee?

**There's no guarantee that the execution is stopped after a certain time**. The main reason is that not all blocking methods are interruptible. In fact, there are only a few well-defined methods that are interruptible. So, **if a thread is interrupted and a flag is set, nothing else will happen until it reaches one of these interruptible methods**.

For example, _read_ and _write_ methods are interruptible only if they're invoked on streams created with an [_InterruptibleChannel_](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/nio/channels/InterruptibleChannel.html). [_BufferedReader_](https://www.baeldung.com/java-buffered-reader) is not an _InterruptibleChannel_. So, if the thread uses it to read a file, calling _interrupt()_ on this thread blocked in the _read_ method has no effect.

However, we can explicitly check for the interrupt flag after every read in a loop. This will give a reasonable surety to stop the thread with some delay. But, this doesn't guarantee to stop the thread after a strict time, because we don't know how much time a read operation can take.

On the other hand, the _wait_ method of the _Object_ class is interruptible. Thus, the thread blocked in the _wait_ method will immediately throw an _InterruptedException_ after the interrupt flag is set.

We can identify the blocking methods by looking for a _throws_ _InterruptedException_ in their method signatures.

One important piece of advice is to **avoid using the deprecated [_Thread.stop()_](https://docs.oracle.com/javase/7/docs/technotes/guides/concurrency/threadPrimitiveDeprecation.html) method.** Stopping the thread causes it to unlock all of the monitors that it has locked. This happens because of the [_ThreadDeath_](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/lang/ThreadDeath.html) exception that propagates up the stack.

If any of the objects previously protected by these monitors were in an inconsistent state, the inconsistent objects become visible to other threads. This can lead to arbitrary behavior that is very hard to detect and reason about.

## 5. Design for Interruption

In the previous section, we highlighted the importance of having interruptible methods to stop the execution as soon as possible. Therefore, our code needs to consider this expectation from a design perspective.

Imagine we have a long-running task to execute, and we need to make sure it doesn't take more time than the specified. Also, suppose the task can be split into individual steps.

Let's create a class for the task steps:

```java
class Step {
    private static int MAX = Integer.MAX_VALUE/2;
    int number;

    public Step(int number) {
        this.number = number;
    }

    public void perform() throws InterruptedException {
        Random rnd = new Random();
        int target = rnd.nextInt(MAX);
        while (rnd.nextInt(MAX) != target) {
            if (Thread.interrupted()) {
                throw new InterruptedException();
            }
        }
    }
}
```

Here, the _Step#perform_ method tries to find a target random integer while asking for the flag on each iteration. The method throws an _InterruptedException_ when the flag is activated.

Now, let's define the task which will be performing all the steps:

```java
public class SteppedTask implements Runnable {
    private List<Step> steps;

    public SteppedTask(List<Step> steps) {
        this.steps = steps;
    }

    @Override
    public void run() {
        for (Step step : steps) {
            try {
                step.perform();
            } catch (InterruptedException e) {
                // handle interruption exception
                return;
            }
        }
    }
}
```

Here, the _SteppedTask_ has a list of steps to execute. A for loop performs each step and handles the _InterruptedException_ for stopping the task when it occurs.

Finally, let's see an example of using our interruptible task:

```java
List<Step> steps = Stream.of(
  new Step(1),
  new Step(2),
  new Step(3),
  new Step(4))
.collect(Collectors.toList());

Thread thread = new Thread(new SteppedTask(steps));
thread.start();

Timer timer = new Timer();
TimeOutTask timeOutTask = new TimeOutTask(thread, timer);
timer.schedule(timeOutTask, 10000);
```

First, we create a _SteppedTask_ with four steps. Second, we run the task using a thread. Last, we interrupt the thread after ten seconds using a timer and a timeout task.

With this design, we can ensure our long-running task can be interrupted while executing any step. As we have seen before, the downside is there is no guarantee it will stop at the exact time specified, but surely better than a non-interruptible task.

## 6. Conclusion

In this tutorial, we've learned various techniques for stopping the execution after a given time, along with the pros and cons of each. The complete source code can be found [over on GitHub](https://github.com/eugenp/tutorials/tree/master/core-java-modules/core-java-concurrency-basic-2).