layout: page
title: "Java Concurrency Notes"
permalink: /javaConcurrency/

## Java Concurrency

### Thread

The concurrent tasks that run inside a process are called threads

## Java Memory Model

The Java Memory Model requires fetch and store operations to be atomic, but for nonvolatile long and double variables, **the JVM is permitted to treat a 64-bit(long or double) read or write as two separate 32-bit operations.**

## Volatile Variables

When a field is declared volatile, the compiler and runtime are put on notice that this variable is shared and that operations on it should not be reordered with other memory operations. Volatile variables are not cached in registers or in caches where they are hidden from other processors, so a read of a volatile variable always returns the most recent write by any thread.

 * Volatile variables can only guarantee visibility.
 * Not strong enough to provide atomicity.

## Lock

Locking is not just about mutual exclusion; it is also about memory visibility.

### Thread Safety

A piece of code is called thread safe means the code will always behave correctly when accessed from multi-threads, without additional synchronization or coordination on the part of the calling code, regardless the scheduling or interleaving of he execution of those threads by the runtime environments.
One of technique to achieve thread safety without synchronization is thread confinement, make the variable only accessible to a single thread, e.g. local variable, thread local variables.

Local variables are intrinsically confined to the executing thread; they exist on the executing thread's stack, which is not accessible to other threads.
Thread-Local provides get and set accessor methods that maintain a separate copy of the value for each thread that uses it, so a get returns the most recent value passed to set from the currently executing thread.
Conceptually, you can think of a ThreadLocal<T> as holding a Map<Thread,T>

### Intrinsic Lock

Each object has a built-in intrinsic lock. The intrinsic lock is reentrant. The fact that every object has a built-in lock is just a convenience so that you needn't explicitly create lock objects.

### Thread States

A [thread](http://www.geeksforgeeks.org/multithreading-in-java/) in Java at any point of time exists in any one of the following states. A thread lies only in one of the shown states at any instant:

1. New
2. Runnable
3. Blocked
4. Waiting
5. Timed Waiting
6. Terminated

![State Diagram](https://www.geeksforgeeks.org/lifecycle-and-states-of-a-thread-in-java/contribute.geeksforgeeks.org/wp-content/uploads/threadLifeCycle.jpg)

### Thread Methods

**yield:()** indicates that the thread is not doing anything particularly important and if any other threads or processes need to be run, they can. Otherwise, the **current thread will continue to run.** (Thread.yield())

**sleep()**: causes the thread to definitely stop executing for a given amount of time; if no other thread or process needs to be run, **the CPU will be idle** (and probably enter a power saving mode)

**[join():](https://www.geeksforgeeks.org/joining-threads-in-java/)**  wait the thread at most much of milliseconds to die. It will put the current thread on wait until the thread on which it is called is dead. If thread is interrupted then it will throw InterruptedException.

The join() method of a Thread instance is used to join the start of a thread’s execution to end of other thread’s execution such that a thread does not start running until another thread ends. If join() is called on a Thread instance, the currently running thread will block until the Thread instance has finished executing.

getPriority(): Each thread has a priority (1-10), default to 5



### Interrupted Exception

[Official Reference](https://www.ibm.com/developerworks/library/j-jtp05236/)

#### Blocking Method

When a method throws `InterruptedException`,  It is telling you that it is a *blocking* method and that it will make an attempt to unblock and return early -- if you ask nicely.

The completion of a blocking method differs from ordinary method , 

* Ordinary method depends on only the work being asked to do and computing resources availability;
* Blocking methods also depends on external events , such as timer expiration, I/O completion, or the action of other threads( releasing locks, setting flags etc.), **LESS PREDICTABLE**

 



### Object methods 

All these methods belong to object class as final so that all classes have them. They must be used within a synchronized block only.

- **wait()-**It tells the calling thread to give up the lock and go to sleep until some other thread enters the same monitor and calls notify().

  Firstly it releases the lock it holds on the object.

   Secondly it makes the produce thread to go on a waiting state until all other threads have terminated, that is it can again acquire a lock on PC object and some other method wakes it up by invoking notify or notifyAll on the same object.

- **notify()-**It wakes up one single thread that called wait() on the same object. It should be noted that calling notify() does not actually give up a lock on a resource. It also does 2 things- Firstly, unlike wait(), it does not releases the lock on shared resource therefore for getting the desired result, **. ** Secondly, **it notifies the waiting threads that now they can wake up but only after the current method terminates. So it is advised to use notify only at the end of your method**

- **notifyAll()-**It wakes up all the threads that called wait() on the same object.

### ReentrantLock

The ReentrantLock class implements the Lock interface and provides synchronization to methods while accessing shared resources. The code which manipulates the shared resource is surrounded by calls to lock and unlock method. This gives a lock to the current working thread and blocks all other threads which are trying to take a lock on the shared resource.

**ReentrantLock allow threads to enter into lock on a resource more than once**. When the thread first enters into lock, a hold count is set to one. Before unlocking the thread can re-enter into lock again and every time hold count is incremented by one. For every unlock request, hold count is decremented by one and when hold count is 0, the resource is unlocked.

### Executor
It includes 3 interfaces:
 * Executor
 *  ExecutorService
 *  ScheduledExecutorService
 
 
### Future

Future serves as the result of an aysynchronous compution, It is created by submitting a callable/runnable to a executor service. The get method will block to get the result.

### CompletableFuture

CompletableFuture implements CompletionStage and Future Interface , the name comes from that it is shipped with a method that the future can be explicitly completed.

By default, the task is submit to the thread pool: **ForkJoinPool.commonPool**, but custom executor service could be provided in the *Async APIs.

* A completionStage represents a stage of a certain computation which can be done either synchronously or asynchronously. You can think of it as just a single unit of a pipeline of computations that ultimately generates a final result of interest

*  To execute tasks without returning result: runSync, runAsync, thenRun, thenRunAsync

* To execute task with returning result: supplyAsync

* Chaining methods: thenApply, thenAccept, thenCompose, thenCombine,thenAcceptBoth,thenApplyToEither

  

### Concurrent Collections

### ConcurrenyHashMap

* Introduced in 1.5
* It allows concurrent access to a hash map, it only gets locked when multiple threads tries to modify the same **segment**
* Parameters: initial capacity, load factor ( for resizing),concurrencyLevel(default to 16 power of 2)

#### CopyOnWriteArrayList 

The copyonwrite collections derive their thread safety from the fact that as long as an effectively immutable object is properly published, no further synchronization is required when accessing it.

#### Blocking Queue

put , take will be blocking there is no space or not element in the queue and it throws InterruptedException, any method that throws InterruptedException means it is blocking methods.



offer, poll will reuturn error if there is space of no element in the queue



#### Synchronizers

A latch is a synchronizer that can delay the progress of threads until it reaches its terminal state [CPJ 3.4.2]. A latch acts as a gate: until the latch reaches the terminal state the gate is closed and no thread can pass, and in the terminal state the gate opens, allowing all threads to pass. Once the latch reaches the terminal state, it cannot change state again, so it remains open forever. Latches can be used to ensure that certain activities do not proceed until other onetime activities complete

T

### FutureTask

A futureTask represents a result-bearing computation that implements Future and Runnable. It has the following states:

* Waiting to run

* Runing

* Completed

  

  FutureTask is used by the Executor framework to represent asynchronous tasks

### Semaphore

Counting semaphores are used to **control the number of activities** that can access a certain resource or perform a given action at the same time, It  is widely used in the resource pool implementations or bounded collections.



### Barrier

A synchronization aid that **allows a set of threads to all wait for each other to reach a common barrier point.**

 CyclicBarriers are useful in programs involving a fixed sized party of threads that must occasionally wait for each other. 

The barrier is called *cyclic* because it can be re-used after the waiting threads are released(barrier.reset()) .

A `CyclicBarrier` supports an optional [`Runnable`](https://docs.oracle.com/javase/7/docs/api/java/lang/Runnable.html) command that is run once per barrier point, after the last thread in the party arrives, but before any threads are released. This *barrier action* is useful for updating shared-state before any of the parties continue.

CyclicBarrier is used to make threads wait for each other. It is used when different threads process a part of computation and when all threads have completed the execution, the result needs to be combined in the parent thread. 

A barrier breaks when any of the waiting thread leaves the barrier. This happens when one or more waiting thread is interrupted or when the waiting time is completed because the thread called the await() methods with a timeout as follows:

In the worker thread,the await method need to be invoked after the computation, the core method is await(int time, TimeUnit unit) --Waits until all [parties](https://docs.oracle.com/javase/7/docs/api/java/util/concurrent/CyclicBarrier.html#getParties()) have invoked `await` on this barrier.

The difference between a Barrier and CountDountLatch

- A CountDownLatch can be used only once in a program(until it’s count reaches 0).
- A CyclicBarrier can be used again and again once all the threads in a barriers is released.

Example: [BarrierTest](https://github.com/csvsoft/javaconcurrency/blob/master/src/test/java/com/test/BarrierTest.java)

