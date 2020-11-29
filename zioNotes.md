## ZIO

### Pure Function

A pure function has following characteristics(TDP)

*  Total -- The function ALWAYS returns output for ANY input;
* Deterministic - returns the same output for the same input;
* Pure - No side effect(mutating data and interact with external world), only effect is to compute the output.

Pure functions do not interact with external world directly, instead they return a data structure that describes how to interact with external world



### Functinal Programming

Functional programming is to build applications by combining pure functions in a total, deterministic way.



### What is ZIO

ZIO is a library for pure functional programming in scala. At its core, is the ZIO data structure.

Zio[R,E,A] is a **immutable data structure **data structure that **describes** an effectual program, which may fail with a value of type E ,or succeed with a value of type A with the input environmental vaue of type R. A value of type ZIO[R,E,A] is like a effectual function 

```scala
R => Either[E,A]
```

### Return Value Type - A

A is the value type that the effect can succeed with, it is the output of the function

### Error Type - E

The error type represents the potential ways that an effect can fail. The error type is helpful because it allows us to use operators (like flatMap) that work on the success type of the effect, while deferring error handling until higher-levels. This allows us to **concentrate on the “happy path” of the program** and **handle errors at the right place**.

One of the most useful features of the error type is being able to specify that an effect cannot fail at all, perhaps because its errors have already been caught and handled

```scala
ZIO[R,Nothing,A]
```



### Environmental Type -R

It represents the requirements to run the ZIO effect

Envrionmental type models the environment or services that the ZIO value requires to evaluate.

```scala
// to access the environment type

ZIO.access(env => {})
// to privoide
val zio:ZIO[R,E,A] = ???
val zio.privide(r) :ZIO[Any,E,A]
```

ZIO provides 5 essential 5 services which makes testing deterministic by leveraging test version of these services.

* Clock : retry, repetition, schedule

  ```scala
      import zio.clock._
      import zio.duration._
      // delay operation with clock
      def delay[R,E,A](zio:ZIO[R,E,A])(duration: Duration):ZIO [R with Clock, E, A] ={
        clock.sleep(duration) *> zio
      }
  
  ```

  

* Random

* System

* Console

* Blocking (blocking thread pool)

## Ailas

```scala
IO[+E,+A] == ZIO[Any,E,A]
UIO[A]  == ZIO[Any,Nothing,A] infallible effect
RIO[-R,A] == ZIO[R,Throwable,A]
URIO[-R,A] == ZIO[R,Nothing,A]
Task[A] == ZIO[Any,Throable,A]

```

## Construct ZIO

[code](https://github.com/csvsoft/zio-note/blob/develop/src/test/scala/zio/notes/create/CreateSpec.scala)

1. General Effect to capture code that may execute side effect operations

   ```scala
   ZIO.effect(a: => A)
   //
   ZIO.effect(println("Hello world"))
   ```

2. Infalliable

   ```scala
   ZIO.effectTotal(a: => A):ZIO[R,Nothing,A]
   ZIO.succeed(a: => A):ZIO[Any,Nothing,A]
   
   ```

3. From existing effect, e.g. Option,Either,Try,Future

   ```scala
   ZIO.fromOption(Some('A')):ZIO[Any,None.Type,String]
   ZIO.fromEither
   ZIO.fromFuture(make:ExecutionContext => Future)
   ```

4. Convert from asynchronous code

   ```scala
   // existing asynchonous code
   def getUserById[B](id:Integer)(callback:Option[User] => B)
   // Convert it to ZIO
   def getUserByIdZIO[B](id:Integer):ZIO[Any,None.Type,B] ={
   ZIO.effectAsync{ callback =>
     getUserById(id){
       case Some(user) => callback(ZIO.succeed(user))
       case None => callback(ZIO.fail(None))
     }
   }  
   }
   
   ```

5. Force the code to execute on blocking thread pool, in case of JVM, by default, ZIO provides two thread pools, one is for general short-time computation, another is for blocking code.

   ```scala
   
   zio.blocking.effectBlocking{
     Thread.sleep(1000)
   }
   ```

6. Build from Iterable with foreachPar/foreach

   ```scala
   val r:UIO[List[Int]] = ZIO.foreachPar(List(1, 2))(i => ZIO.succeed(i * 2))
    eval(r) shouldBe (List(2, 4))
   ```

   

## ZIO Operations

1. ### Sequential Operations

   * flatmap
   * zip
   * zipwith
   * collectAll
   * reduceAll
   * mergeAll

   #### 2. Parallel Operations

   * CollectAllPar

     ```scala
        val  f1 = ZIO.succeed("a")
        val f2 = ZIO.succeed("b")
        val f1f2 = ZIO.collectAllPar(List(f1,f2))
        eval(f1f2) shouldBe (List("a","b"))
     ```

     

### Schedule[A,B]

Shedule[A,B] is an immutable value that describes a schedule effect, it consumes a vale of type A and produces a B and the decides to continue or halt some delay d;

### Fiber

Fiber is a lightweight thread for concurrency , they are lightweight "green threads" implemented by the ZIO runtime system; All ZIO effect are executed by some finer.

#### 1. Create Fiber By Forking a ZIO effect

The fiber is created by forking the parent fiber; a fiber tree could be created with forking; children fiber are automacially supervised by there parent, if parent is interrupted , then all children fibers will be interrupted.

```scala
val fiber = ZIO.succeed("a").fork
val fiberDaemon = ZIO.succeed("a").forkDaemon // no s

```

#### 2. Joining Fiber

fiber.join will return the original wrapped ZIO effect,

```scala
val p = for {
  fiber <- ZIO.succeed("a").fork
  r <- fiber.join
} yield r
eval(r) shouldBe ("a")
```

#### 3. Await Fiber

fiber.await will return a value of Exit which contains all the information of how the effect is completed,.e.g.

The success Value, Failure Cause, is interrupted, 

```scala
val p = for {
  fiber <- ZIO.succeed("a").fork
  r <- fiber.await
} yield r
eval(r) shouldBe (zio.Exit.Success("a"))
```

#### 4. Interrupt Fiber

Fiber could be interrupted, ZIO runtime will cancel the interrupted fiber **if the fiber is interruptible* , the lowest granularity of excution unit in ZIO is the ZIO effect, interruption only works on effect level; NOT statement level as native thread; fibers check the interrupt on completion of each task in the fiber task queue, once interruption is requested, the remaining unexecuted tasks will be removed, and then run the finalizer

```scala
import zio.blocking._
    import zio.duration._
    val print= effectBlocking(println("hello world")).repeat(Schedule.spaced(1.seconds))
   val p = for {
      fiber <- print.fork
      e <- ZIO.sleep(10.seconds) *> fiber.interrupt
    } yield e
    eval(p)
```





IO[E,A].fork will yield UIO[Fiber[E,A]],Fiber could be join and interrupt.

All effects are executed by some fiber.

Fibers are scheduled by the ZIO runtime and will cooperatively yield to each other. They can even run on a single-threaded system.



### ZIO DataTypes

###  Chunks

* Chunks are immutable

### ZLayer

ZIO[R,E,A] , R represents requirements to run the effect, while R could depend on other Rs, this is is a typical depedency management. Instead of leverage scala's mixins, ZIO introduces ZLayer to manage the class dependencies; the reason is that ZLayer has more power to construct , modify and access the dependencies.

* type safety

  ```scala
  // after get userService, only methods of userservice is accessible
  ZIO.accessM(mix => mix.get[UserService].registerUser(user)) 
  ```

* composibility

  ```scala
  // compose layer horizontally
  val dbLayer:ZLayer[Any,E,HasDBService] = ???
  val loggerLayer: ZLayer[Any,E,HasLoggService] ==???
  val dbLogger = dbLayer ++ loggerLayer
  
  // compose layer vertically
  val configLayer:ZLayer[Any,E,HasConfig] = ???
  val serviceLayer:ZLayer[HasConfig,E,HasService] = ???
  val finalLayer = configLayer >>> serviceLayer
  ```

* 

### ZIO Test

zio-test is a set of utilities to facilitate testing of ZIO effects.

The core idea of zoo-test is to make test suites the first-class objects.

#### Spec[L,T]

The backbone of zoo-test is the Spec[L,T] class. 

A spec of labled with L and a test of type T / a suite that contains other specs.

1. Construction spec

   ```scala
   suite function:
   suite[R, E, T](label: String)(specs: Spec[R, E, T]*): Spec[R, E, T]
   
   testM:
   testM[R, E](label: String)(assertion: => ZIO[R, E, TestResult]): ZSpec[R, E] 
   
   assert: To create TestResult
   assert[A](value: => A)(assertion: Assertion[A]): TestResult 
   
   Assertion Creation:
   equalTo, isFalse, isTrue, contains, throws
   
   suite("test hello world"){
     testM("hello world should be printed"){
       for{
         _ < yourObj.sayHello
         output <- TestConsole.output
       } yield assert(output)(equals(Vector("Hello World\n")))
     }
   }
   ```

   