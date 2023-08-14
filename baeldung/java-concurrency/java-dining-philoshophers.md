
## 1. Introduction

The [Dining Philosophers problem](https://www.baeldung.com/cs/dining-philosophers) is one of the classic problems used to **describe synchronization issues in a multi-threaded environment and illustrate techniques for solving them**. Dijkstra first formulated this problem and presented it regarding computers accessing tape drive peripherals.

The present formulation was given by Tony Hoare, who is also known for inventing the quicksort sorting algorithm. In this article, we analyze this well-known problem and code a popular solution.

## 2. The Problem

[![dp0](https://www.baeldung.com/wp-content/uploads/2017/05/dp0-297x300.png)](https://www.baeldung.com/wp-content/uploads/2017/05/dp0.png)

The diagram above represents the problem. There are five silent philosophers (P1 – P5) sitting around a circular table, spending their lives eating and thinking.

There are five forks for them to share (1 – 5) and to be able to eat, a philosopher needs to have forks in both his hands. After eating, he puts both of them down and then they can be picked by another philosopher who repeats the same cycle.

> The goal is to come up with a scheme/protocol that helps the philosophers achieve their goal of eating and thinking without getting starved to death.

## 3. A Solution

An initial solution would be to make each of the philosophers follow the following protocol:

```c
while(true) { 
    // Initially, thinking about life, universe, and everything
    think();

    // Take a break from thinking, hungry now
    pick_up_left_fork();
    pick_up_right_fork();
    eat();
    put_down_right_fork();
    put_down_left_fork();

    // Not hungry anymore. Back to thinking!
}
```

As the above pseudo code describes, each philosopher is initially thinking. **After a certain amount of time, the philosopher gets hungry and wishes to eat.**

At this point, **he reaches for the forks on his either side and once he's got both of them, proceeds to eat**. Once the eating is done, the philosopher then puts the forks down, so that they're available for his neighbor.

## 4. Implementation

We model each of our philosophers as classes that implement the _Runnable_ interface so that we can run them as separate threads. Each _Philosopher_ has access to two forks on his left and right sides:

```java
public class Philosopher implements Runnable {

    // The forks on either side of this Philosopher 
    private Object leftFork;
    private Object rightFork;

    public Philosopher(Object leftFork, Object rightFork) {
        this.leftFork = leftFork;
        this.rightFork = rightFork;
    }

    @Override
    public void run() {
        // Yet to populate this method
    }

}
```

We also have a method that instructs a _Philosopher_ to perform an action – eat, think, or acquire forks in preparation for eating:

```java
public class Philosopher implements Runnable {

    // Member variables, standard constructor

    private void doAction(String action) throws InterruptedException {
        System.out.println(
          Thread.currentThread().getName() + " " + action);
        Thread.sleep(((int) (Math.random() * 100)));
    }

    // Rest of the methods written earlier
}
```

As shown in the code above, each action is simulated by suspending the invoking thread for a random amount of time, so that the execution order isn't enforced by time alone.

Now, let's implement the core logic of a _Philosopher_.

To simulate acquiring a fork, we need to lock it so that no two _Philosopher_ threads acquire it at the same time.

To achieve this, we use the _synchronized_ keyword to acquire the internal monitor of the fork object and prevent other threads from doing the same. A guide to the _synchronized_ keyword in Java can be found [here](https://www.baeldung.com/java-synchronized). We proceed with implementing the _run()_ method in the _Philosopher_ class now:

```java
public class Philosopher implements Runnable {

   // Member variables, methods defined earlier

    @Override
    public void run() {
        try {
            while (true) {
                
                // thinking
                doAction(System.nanoTime() + ": Thinking");
                synchronized (leftFork) {
                    doAction(
                      System.nanoTime() 
                        + ": Picked up left fork");
                    synchronized (rightFork) {
                        // eating
                        doAction(
                          System.nanoTime() 
                            + ": Picked up right fork - eating"); 
                        
                        doAction(
                          System.nanoTime() 
                            + ": Put down right fork");
                    }
                    
                    // Back to thinking
                    doAction(
                      System.nanoTime() 
                        + ": Put down left fork. Back to thinking");
                }
            }
        } catch (InterruptedException e) {
            Thread.currentThread().interrupt();
            return;
        }
    }
}
```

This scheme exactly implements the one described earlier: a _Philosopher_ thinks for a while and then decides to eat.

After this, he acquires the forks to his left and right and starts eating. When done, he places the forks down. We also add timestamps to each action, which would help us understand the order in which events occur.

To kick start the whole process, we write a client that creates 5 _Philosophers_ as threads and starts all of them:

```java
public class DiningPhilosophers {

    public static void main(String[] args) throws Exception {

        Philosopher[] philosophers = new Philosopher[5];
        Object[] forks = new Object[philosophers.length];

        for (int i = 0; i < forks.length; i++) {
            forks[i] = new Object();
        }

        for (int i = 0; i < philosophers.length; i++) {
            Object leftFork = forks[i];
            Object rightFork = forks[(i + 1) % forks.length];

            philosophers[i] = new Philosopher(leftFork, rightFork);
            
            Thread t 
              = new Thread(philosophers[i], "Philosopher " + (i + 1));
            t.start();
        }
    }
}
```

We model each of the forks as generic Java objects and make as many of them as there are philosophers. We pass each _Philosopher_ his left and right forks that he attempts to lock using the _synchronized_ keyword.

Running this code results in an output similar to the following. Your output will most likely differ from the one given below, mostly because the _sleep()_ method is invoked for a different interval:

```c
Philosopher 1 8038014601251: Thinking
Philosopher 2 8038014828862: Thinking
Philosopher 3 8038015066722: Thinking
Philosopher 4 8038015284511: Thinking
Philosopher 5 8038015468564: Thinking
Philosopher 1 8038016857288: Picked up left fork
Philosopher 1 8038022332758: Picked up right fork - eating
Philosopher 3 8038028886069: Picked up left fork
Philosopher 4 8038063952219: Picked up left fork
Philosopher 1 8038067505168: Put down right fork
Philosopher 2 8038089505264: Picked up left fork
Philosopher 1 8038089505264: Put down left fork. Back to thinking
Philosopher 5 8038111040317: Picked up left fork
```

All the _Philosopher_s initially start off thinking, and we see that _Philosopher 1_ proceeds to pick up the left and right fork, then eats and proceeds to place both of them down, after which `Philosopher 5` picks it up.

## 5. The Problem With the Solution: Deadlock

Though it seems that the above solution is correct, there's an issue of a deadlock arising.

> A deadlock is a situation where the progress of a system is halted as each process is waiting to acquire a resource held by some other process.

We can confirm the same by running the above code a few times and checking that some times, the code just hangs. Here's a sample output that demonstrates the above issue:

```java
Philosopher 1 8487540546530: Thinking
Philosopher 2 8487542012975: Thinking
Philosopher 3 8487543057508: Thinking
Philosopher 4 8487543318428: Thinking
Philosopher 5 8487544590144: Thinking
Philosopher 3 8487589069046: Picked up left fork
Philosopher 1 8487596641267: Picked up left fork
Philosopher 5 8487597646086: Picked up left fork
Philosopher 4 8487617680958: Picked up left fork
Philosopher 2 8487631148853: Picked up left fork
```

In this situation, each of the Philosophers has acquired his left fork, but can't acquire his right fork, because his neighbor has already acquired it. This situation is commonly known as the **circular wait** and is one of the conditions that results in a deadlock and prevents the progress of the system.

## 6. Resolving the Deadlock

As we saw above, the primary reason for a deadlock is the circular wait condition where each process waits upon a resource that's being held by some other process. Hence, to avoid a deadlock situation we need to make sure that the circular wait condition is broken. There are several ways to achieve this, the simplest one being the follows:

> All Philosophers reach for their left fork first, except one who first reaches for his right fork.

We implement this in our existing code by making a relatively minor change in code:

```java
public class DiningPhilosophers {

    public static void main(String[] args) throws Exception {

        final Philosopher[] philosophers = new Philosopher[5];
        Object[] forks = new Object[philosophers.length];

        for (int i = 0; i < forks.length; i++) {
            forks[i] = new Object();
        }

        for (int i = 0; i < philosophers.length; i++) {
            Object leftFork = forks[i];
            Object rightFork = forks[(i + 1) % forks.length];

            if (i == philosophers.length - 1) {
                
                // The last philosopher picks up the right fork first
                philosophers[i] = new Philosopher(rightFork, leftFork); 
            } else {
                philosophers[i] = new Philosopher(leftFork, rightFork);
            }
            
            Thread t 
              = new Thread(philosophers[i], "Philosopher " + (i + 1));
            t.start();
        }
    }
}

```

The change comes in lines 17-19 of the above code, where we introduce the condition that makes the last philosopher reach for his right fork first, instead of the left. This breaks the circular wait condition and we can avert the deadlock.

Following output shows one of the cases where all the _Philosopher_s get their chance to think and eat, without causing a deadlock:

```java
Philosopher 1 88519839556188: Thinking
Philosopher 2 88519840186495: Thinking
Philosopher 3 88519840647695: Thinking
Philosopher 4 88519840870182: Thinking
Philosopher 5 88519840956443: Thinking
Philosopher 3 88519864404195: Picked up left fork
Philosopher 5 88519871990082: Picked up left fork
Philosopher 4 88519874059504: Picked up left fork
Philosopher 5 88519876989405: Picked up right fork - eating
Philosopher 2 88519935045524: Picked up left fork
Philosopher 5 88519951109805: Put down right fork
Philosopher 4 88519997119634: Picked up right fork - eating
Philosopher 5 88519997113229: Put down left fork. Back to thinking
Philosopher 5 88520011135846: Thinking
Philosopher 1 88520011129013: Picked up left fork
Philosopher 4 88520028194269: Put down right fork
Philosopher 4 88520057160194: Put down left fork. Back to thinking
Philosopher 3 88520067162257: Picked up right fork - eating
Philosopher 4 88520067158414: Thinking
Philosopher 3 88520160247801: Put down right fork
Philosopher 4 88520249049308: Picked up left fork
Philosopher 3 88520249119769: Put down left fork. Back to thinking
```

It can be verified by running the code several times, that the system is free from the deadlock situation that occurred before.

## 7. Conclusion

In this article, we explored the famous Dining Philosophers problem and **the concepts of circular wait and deadlock**. We coded a simple solution that caused a deadlock and made a simple change to break the circular wait and avoid a deadlock. This is just a start, and more sophisticated solutions do exist.

The code for this article can be found [over on GitHub](https://github.com/eugenp/tutorials/tree/master/core-java-modules/core-java-concurrency-advanced).