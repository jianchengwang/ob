
## 1. Overview

Java supports multithreading out of the box. This means that by running bytecode concurrently in separate worker threads, the [JVM](https://www.baeldung.com/jvm-vs-jre-vs-jdk) is capable of improving application performance.

Although multithreading is a powerful feature, it comes at a price. In multithreaded environments, we need to write implementations in a thread-safe way. This means that different threads can access the same resources without exposing erroneous behavior or producing unpredictable results. **This programming methodology is known as “thread-safety.”**

In this tutorial, we'll look at different approaches to achieve it.

## 2. Stateless Implementations

In most cases, errors in multithreaded applications are the result of incorrectly sharing state between several threads.

So, the first approach that we'll look at is to achieve thread-safety **using stateless implementations.**

To better understand this approach, let's consider a simple utility class with a static method that calculates the factorial of a number:

```java
public class MathUtils {
    
    public static BigInteger factorial(int number) {
        BigInteger f = new BigInteger("1");
        for (int i = 2; i <= number; i++) {
            f = f.multiply(BigInteger.valueOf(i));
        }
        return f;
    }
}
```

**The factorial() method is a stateless deterministic function.** Given a specific input, it always produces the same output.

The method **neither relies on external state nor maintains state at all.** So, it's considered to be thread-safe and can be safely called by multiple threads at the same time.

All threads can safely call the _factorial()_ method and will get the expected result without interfering with each other and without altering the output that the method generates for other threads.

Therefore, **stateless implementations are the simplest way to achieve thread-safety.**

## 3. Immutable Implementations

**If we need to share state between different threads, we can create thread-safe classes by making them immutable.**

Immutability is a powerful, language-agnostic concept, and it's fairly easy to achieve in Java.

To put it simply, **a class instance is immutable when its internal state can't be modified after it has been constructed.**

The easiest way to create an immutable class in Java is by declaring all the fields _private_ and _final_ and not providing setters:

```java
public class MessageService {
    
    private final String message;

    public MessageService(String message) {
        this.message = message;
    }
    
    // standard getter
    
}
```

A _MessageService_ object is effectively immutable since its state can't change after its construction. So, it's thread-safe.

Moreover, if _MessageService_ were actually mutable, but multiple threads only have read-only access to it, it's thread-safe as well.

As we can see, **immutability is just another way to achieve thread-safety.**

## 4. Thread-Local Fields

In Object-Oriented Programming (OOP), objects actually need to maintain state through fields and implement behavior through one or more methods.

If we actually need to maintain state, **we can create thread-safe classes that don't share state between threads by making their fields thread-local.**

We can easily create classes whose fields are thread-local by simply defining private fields in _[Thread](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/lang/Thread.html)_ classes.

We could define, for instance, a _Thread_ class that stores an _array_ of _integers_:

```java
public class ThreadA extends Thread {
    
    private final List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5, 6);
    
    @Override
    public void run() {
        numbers.forEach(System.out::println);
    }
}
```

Meanwhile, another one might hold an _array_ of _strings_:

```java
public class ThreadB extends Thread {
    
    private final List<String> letters = Arrays.asList("a", "b", "c", "d", "e", "f");
    
    @Override
    public void run() {
        letters.forEach(System.out::println);
    }
}
```

**In both implementations, the classes have their own state, but it's not shared with other threads. So, the classes are thread-safe.**

Similarly, we can create thread-local fields by assigning _[ThreadLocal](https://www.baeldung.com/java-threadlocal)_ instances to a field.

Let's consider the following _StateHolder_ class:

```java
public class StateHolder {
    
    private final String state;

    // standard constructors / getter
}
```

We can easily make it a thread-local variable:

```java
public class ThreadState {
    
    public static final ThreadLocal<StateHolder> statePerThread = new ThreadLocal<StateHolder>() {
        
        @Override
        protected StateHolder initialValue() {
            return new StateHolder("active");  
        }
    };

    public static StateHolder getState() {
        return statePerThread.get();
    }
}
```

Thread-local fields are pretty much like normal class fields, except that each thread that accesses them via a setter/getter gets an independently initialized copy of the field so that each thread has its own state.

## 5. Synchronized Collections

We can easily create thread-safe collections by using the set of synchronization wrappers included within the [collections framework](https://docs.oracle.com/javase/8/docs/technotes/guides/collections/overview.html).

We can use, for instance, one of these [synchronization wrappers](https://www.baeldung.com/java-synchronized-collections) to create a thread-safe collection:

```java
Collection<Integer> syncCollection = Collections.synchronizedCollection(new ArrayList<>());
Thread thread1 = new Thread(() -> syncCollection.addAll(Arrays.asList(1, 2, 3, 4, 5, 6)));
Thread thread2 = new Thread(() -> syncCollection.addAll(Arrays.asList(7, 8, 9, 10, 11, 12)));
thread1.start();
thread2.start();
```

Let's keep in mind that synchronized collections use intrinsic locking in each method (we'll look at intrinsic locking later).

**This means that the methods can be accessed by only one thread at a time, while other threads will be blocked until the method is unlocked by the first thread.**

Thus, synchronization has a penalty in performance, due to the underlying logic of synchronized access.

## 6. Concurrent Collections

Alternatively to synchronized collections, we can use concurrent collections to create thread-safe collections.

Java provides the _[java.util.concurrent](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/concurrent/package-summary.html)_ package, which contains several concurrent collections, such as _[ConcurrentHashMap](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/concurrent/ConcurrentHashMap.html)_:

```java
Map<String,String> concurrentMap = new ConcurrentHashMap<>();
concurrentMap.put("1", "one");
concurrentMap.put("2", "two");
concurrentMap.put("3", "three");
```

Unlike their synchronized counterparts, **concurrent collections achieve thread-safety by dividing their data into segments.** In a _ConcurrentHashMap_, for example, several threads can acquire locks on different map segments, so multiple threads can access the _Map_ at the same time.

**Concurrent collections are** **much more performant than synchronized collections**, due to the inherent advantages of concurrent thread access.

It's worth mentioning that **synchronized and concurrent collections only make the collection itself thread-safe and not the contents.**

## 7. Atomic Objects

It's also possible to achieve thread-safety using the set of [atomic classes](https://www.baeldung.com/java-atomic-variables) that Java provides, including _[AtomicInteger](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/concurrent/atomic/AtomicInteger.html)_, _[AtomicLong](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/concurrent/atomic/AtomicLong.html)_, _[AtomicBoolean](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/concurrent/atomic/AtomicBoolean.html)_ and _[AtomicReference](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/concurrent/atomic/AtomicReference.html)_.

**Atomic classes allow us to perform atomic operations, which are thread-safe, without using synchronization.** An atomic operation is executed in one single machine-level operation.

To understand the problem this solves, let's look at the following _Counter_ class:

```java
public class Counter {
    
    private int counter = 0;
    
    public void incrementCounter() {
        counter += 1;
    }
    
    public int getCounter() {
        return counter;
    }
}
```

**Let's suppose that in a [race condition](https://www.baeldung.com/cs/race-conditions), two threads access the incrementCounter() method at the same time.**

In theory, the final value of the _counter_ field will be 2. But we just can't be sure about the result because the threads are executing the same code block at the same time and incrementation is not atomic.

Let's create a thread-safe implementation of the _Counter_ class by using an _AtomicInteger_ object:

```java
public class AtomicCounter {
    
    private final AtomicInteger counter = new AtomicInteger();
    
    public void incrementCounter() {
        counter.incrementAndGet();
    }
    
    public int getCounter() {
        return counter.get();
    }
}
```

**This is thread-safe because while incrementation, ++, takes more than one operation, incrementAndGet is atomic.**

## 8. Synchronized Methods

The earlier approaches are very good for collections and primitives, but we'll sometimes need greater control than that.

So, another common approach that we can use for achieving thread-safety is implementing synchronized methods.

Simply put, **only one thread can access a synchronized method at a time, while blocking access to this method from other threads.** Other threads will remain blocked until the first thread finishes or the method throws an exception.

We can create a thread-safe version of _incrementCounter()_ in another way by making it a synchronized method:

```java
public synchronized void incrementCounter() {
    counter += 1;
}
```

We've created a synchronized method by prefixing the method signature with the [_synchronized_](https://www.baeldung.com/java-synchronized) keyword.

Since one thread at a time can access a synchronized method, one thread will execute the _incrementCounter()_ method, and in turn, others will do the same. No overlapping execution will occur whatsoever.

**Synchronized methods rely on the use of “intrinsic locks” or “monitor locks.”** An intrinsic lock is an implicit internal entity associated with a particular class instance.

In a multithreaded context, the term _monitor_ is just a reference to the role that the lock performs on the associated object, as it enforces exclusive access to a set of specified methods or statements.

**When a thread calls a synchronized method, it acquires the intrinsic lock.** After the thread finishes executing the method, it releases the lock, which allows other threads to acquire the lock and get access to the method.

We can implement synchronization in instance methods, static methods and statements (synchronized statements).

## 9. Synchronized Statements

Sometimes, synchronizing an entire method might be overkill if we just need to make a segment of the method thread-safe.

To exemplify this use case, let's refactor the _incrementCounter()_ method:

```java
public void incrementCounter() {
    // additional unsynced operations
    synchronized(this) {
        counter += 1; 
    }
}
```

The example is trivial, but it shows how to create a synchronized statement. Assuming that the method now performs a few additional operations, which don't require synchronization, we only synchronized the relevant state-modifying section by wrapping it within a _synchronized_ block.

Unlike synchronized methods, synchronized statements must specify the object that provides the intrinsic lock, usually the [_this_](https://www.baeldung.com/java-this) reference.

**Synchronization is expensive, so with this option, we are able to only synchronize the relevant parts of a method.**

### 9.1. Other Objects as a Lock

We can slightly improve the thread-safe implementation of the _Counter_ class by exploiting another object as a monitor lock, instead of _this_.

Not only does this provide coordinated access to a shared resource in a multithreaded environment, **but it also uses an external entity to enforce exclusive access to the resource**:

```java
public class ObjectLockCounter {

    private int counter = 0;
    private final Object lock = new Object();
    
    public void incrementCounter() {
        synchronized(lock) {
            counter += 1;
        }
    }
    
    // standard getter
}
```

We use a plain [_Object_](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/lang/Object.html) instance to enforce mutual exclusion. This implementation is slightly better, as it promotes security at the lock level.

When using _this_ for intrinsic locking, **an attacker could cause a [deadlock](https://www.baeldung.com/cs/deadlock-livelock-starvation) by acquiring the intrinsic lock and triggering a denial of service (DoS) condition.**

On the contrary, when using other objects, **that private entity is not accessible from the outside.** This makes it harder for an attacker to acquire the lock and cause a deadlock.

### 9.2. Caveats

Even though we can use any Java object as an intrinsic lock, we should avoid using _Strings_ for locking purposes:

```java
public class Class1 {
    private static final String LOCK  = "Lock";

    // uses the LOCK as the intrinsic lock
}

public class Class2 {
    private static final String LOCK  = "Lock";

    // uses the LOCK as the intrinsic lock
}
```

At first glance, it seems that these two classes are using two different objects as their lock. However, **because of [string interning](https://www.baeldung.com/string/intern), these two “Lock” values may actually refer to the same object on the [string pool](https://www.baeldung.com/java-string-pool).** That is, the _Class1_ and _Class2_ are sharing the same lock!

This, in turn, may cause some unexpected behaviors in concurrent contexts.

In addition to _Strings_, **we should avoid using any [cacheable or reusable](https://mail.openjdk.java.net/pipermail/valhalla-spec-observers/2020-February/001199.html) objects as intrinsic locks.** For example, the [_Integer.valueOf()_](https://github.com/openjdk/jdk/blob/8c647801fce4d6efcb3780570192973d16e4e6dc/src/java.base/share/classes/java/lang/Integer.java#L1062) method caches small numbers. Therefore, calling _Integer.valueOf(1)_ returns the same object even in different classes.

## 10. Volatile Fields

Synchronized methods and blocks are handy for addressing variable visibility problems among threads. Even so, the values of regular class fields might be cached by the CPU. Hence, consequent updates to a particular field, even if they're synchronized, might not be visible to other threads.

To prevent this situation, we can use [_volatile_](https://www.baeldung.com/java-volatile) class fields:

```java
public class Counter {

    private volatile int counter;

    // standard constructors / getter
    
}
```

**With the volatile keyword, we instruct the JVM and the compiler to store the counter variable in the main memory.** That way, we make sure that every time the JVM reads the value of the _counter_ variable, it will actually read it from the main memory, instead of from the CPU cache. Likewise, every time the JVM writes to the _counter_ variable, the value will be written to the main memory.

Moreover, **the use of a volatile variable ensures that all variables that are visible to a given thread will be read from the main memory as well.**

Let's consider the following example:

```java
public class User {

    private String name;
    private volatile int age;

    // standard constructors / getters
    
}
```

In this case, each time the JVM writes the _age_ _volatile_ variable to the main memory, it will write the non-volatile _name_ variable to the main memory as well. This assures that the latest values of both variables are stored in the main memory, so consequent updates to the variables will automatically be visible to other threads.

Similarly, if a thread reads the value of a _volatile_ variable, all the variables visible to the thread will be read from the main memory too.

**This extended guarantee that volatile variables provide is known as the [full volatile visibility guarantee](http://tutorials.jenkov.com/java-concurrency/volatile.html).**

## 11. Reentrant Locks

Java provides an improved set of _[Lock](https://www.baeldung.com/java-concurrent-locks)_ implementations whose behavior is slightly more sophisticated than the intrinsic locks discussed above.

**With intrinsic locks, the lock acquisition model is rather rigid**: One thread acquires the lock, then executes a method or code block, and finally releases the lock so other threads can acquire it and access the method.

There's no underlying mechanism that checks the queued threads and gives priority access to the longest waiting threads.

_[ReentrantLock](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/concurrent/locks/ReentrantLock.html)_ instances allow us to do exactly that, **preventing queued threads from suffering some types of [resource starvation](https://en.wikipedia.org/wiki/Starvation_(computer_science))**:

```java
public class ReentrantLockCounter {

    private int counter;
    private final ReentrantLock reLock = new ReentrantLock(true);
    
    public void incrementCounter() {
        reLock.lock();
        try {
            counter += 1;
        } finally {
            reLock.unlock();
        }
    }
    
    // standard constructors / getter
    
}
```

The _ReentrantLock_ constructor takes an optional _fairness_ _boolean_ parameter. When set to _true_, and multiple threads are trying to acquire a lock, **the JVM will give priority to the longest waiting thread and grant access to the lock.**

## 12. Read/Write Locks

Another powerful mechanism that we can use for achieving thread-safety is the use of _[ReadWriteLock](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/concurrent/locks/ReadWriteLock.html)_ implementations.

A _ReadWriteLock_ lock actually uses a pair of associated locks, one for read-only operations and the other for writing operations.

As a result, **it's possible to have many threads reading a resource, as long as there's no thread writing to it. Moreover, the thread writing to the resource will prevent other threads from reading it.**

Here's how we can use a _ReadWriteLock_ lock:

```java
public class ReentrantReadWriteLockCounter {
    
    private int counter;
    private final ReentrantReadWriteLock rwLock = new ReentrantReadWriteLock();
    private final Lock readLock = rwLock.readLock();
    private final Lock writeLock = rwLock.writeLock();
    
    public void incrementCounter() {
        writeLock.lock();
        try {
            counter += 1;
        } finally {
            writeLock.unlock();
        }
    }
    
    public int getCounter() {
        readLock.lock();
        try {
            return counter;
        } finally {
            readLock.unlock();
        }
    }

   // standard constructors
   
}
```

## 13. Conclusion

In this article, **we learned what thread-safety is in Java, and we took an in-depth look at different approaches for achieving it.**

As usual, all the code samples shown in this article are available [over on GitHub](https://github.com/eugenp/tutorials/tree/master/core-java-modules/core-java-concurrency-basic).