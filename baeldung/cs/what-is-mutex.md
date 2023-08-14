
## 1. Overview

When we start working with parallel or concurrent programming, usually the first thing we come across is the problem when two concurrent (or parallel) executions try to access the same resource (for example a variable). [_Mutex_](https://www.baeldung.com/java-mutex) is one of the simplest solutions to solve this issue. In the following tutorial, we’re going to explain how it works and how to use it.

## 2. Mutex Explained

What is the problem precisely that the _Mutexes_ are the solution for? Let’s imagine a Scrum daily meeting in the morning with five developers and the Scrum Master. One of them is drinking his coffee and he’s unable to speak until fully awaken. The other would like to go back to the desk to finish that SQL query rather than being in a meeting. The rest (all three) understand the goal of the meeting and are excited to talk about what they did so they start to speak simultaneously.

This ends up chaos and no one understands anything. The Scrum Master steps up and gives a ball to one of the developers and says: “Only the one with the ball can talk!”. From that point on, they understood each other and could conclude the meeting rapidly:

![Mutex repr](https://www.baeldung.com/wp-content/uploads/sites/4/2020/08/Mutex-repr.png)

**If we think about it the SM solved the situation by defining the critical section in the system.** We understand the word “critical” because we can see where it leads us if we don’t manage it properly. The developers (actors) used the ball as a tool to signal the others who can speak and who must wait for it. In other words, the ball mutually excluded the developers to speak. This naming is so common that people started to abbreviate: _Mutex_ (**Mutual Exclusion**).

## 3. Where Do We Use?

The concept can be applied in many different situations, the main goal is always to be able to define the critical section:

-   Multiple threads accessing shared memory with write and read access
-   Multiple processes accessing a common resource (printer and camera)

One common implementation of a _Mutex_ is the _Lock_ that can be acquired (entering the critical section) and released (leaving the critical section):

![Mutex flow](https://www.baeldung.com/wp-content/uploads/sites/4/2020/08/Mutex-flow.png)

## 4. Properties

**As we stated above, the critical section is guaranteed to give access to only one of the actors at a time.** If we design our system poorly we can end up with one big critical section where only one actor can work while all the other always have to wait for the _Mutex_ to be resolved.

This doesn’t differ from a sequential execution so we didn’t gain any performance by choosing concurrent programming (if you think about it, our _Scrum_ example was like that). This shows the importance of the definition of the critical section. When we design our system we have to identify and localize the common resources to be able to benefit from parallel execution.

**Another common issue is [_Deadlock_](https://www.baeldung.com/java-deadlock-livelock)**. Let’s assume for the sake of fun, that the Scrum team decides that a developer passes the ball only if he finished completely, but he needs some information from his colleague. What happens?

-   The one who has the ball (“Dev A”) asks a question from “Dev B” and wait for the answer
-   “Dev B” (no ball) knows the answer but he waits for the ball to be able to speak
-   “Dev A” cannot pass the ball until he completely finished and he needs the answer

![mutex deadlock](https://www.baeldung.com/wp-content/uploads/sites/4/2020/08/mutex-deadlock.png)

Is there a resolution to this situation? Unfortunately no. Our system hangs forever or, in other words, is in _Deadlock_.

Deadlocks can happen especially in systems with multiple locks. To ensure that your design is deadlock-free, you shall acquire the locks in the same order in each actor.

A similar phenomenon is expressed in the [catch-22](https://en.wikipedia.org/wiki/Catch-22_(logic)) situation.

## 5. Conclusion

In this article, we summarized the definition and properties of _Mutexes_ and saw what problems we might have using them. It is important to note that there are some design patterns that try to solve interactions between actors differently than a _Mutex_. Understanding the problem is crucial to be able to design the best solution for it.

