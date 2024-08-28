## Concept

### Name Spaces
Scala has two namespaces:
 * values (fields, methods, packages and singleton objects)
 * types (classes and traits)

Java has four name spaces:
 * package
 * type
 * field
 * method

### Closure
A closure is a function together with a referencing environment for the non-local variables of that function. A closure allows a function to access variables outside its immediate lexical scope.
```scala

class Foo {
      // a method that takes a function and a string, and passes the string into
      // the function, and then executes the function
      def exec(f:(String) => Unit, name: String) {
        f(name)
      }
    }
    var hello = "Hello"
    def sayHello(name: String) { println(s"$hello, $name") }

    // execute sayHello from the exec method foo
    val foo = new Foo
    foo.exec(sayHello, "Al")
     // will print "Hello, Al"

    // change the local variable 'hello', then execute sayHello from
    
    hello = "Hola"
    foo.exec(sayHello, "Lorenzo")
    // print "Hola, Lorenzo",  when the referenced variable is changed, the closure will pick it.
```
### Case Classes
Case classes add some syntactic sugar to a regular class:
* Factory method with same name as classes to create new instance of class without "new";
* Class parameters implicitly get prefixed with val, become field values.
* Compiler adds natural implementations for : equal, hashcode, tostring and copy.

### Pattern Match
Pattern match can be used to match
* Constant  case "Peter" => ....
* Type      case a:Int => ...
* Constructor  case User(firstName, Address(city,street)) =>  Most frequently used
* Sequence     case List(a,_*) => a // a is the first element
* Tuple        case (a,b,c) =>
Type eraser: generic type information is not available at the runtime.


### PAF
Partially applied function is an expression that a function call without all arguments provided.
```
def sum(a:Int,b:Int,c:Int):Int = a + b + c
val add3 = sum(1,2,_:Int)
// add3 is a PAF
```
### Repeated Parameters
```
def echo(a:String*) = for( arg <- a) println(arg)
echo(Array("a","b","c"))
```

### High Order Functions
Functions that take function as parameter or return functions

### Curried Functions
Curried functions are functions with more than one parameter list

## Call-by-name/Call by value
call-by-name A function that accepts an expression /code block that is not evaluated until it's being used within a function
the parameter signature: =>
```
def showLong(t: => Long):Unit = println(t)
def showLongF(t: () => Long) :Unit = println(t)
showLong(123) // code bloc
showTime( {println("It is in a code block");123})
 showLongF( () => 123)  // You must pass in a function
```
### trait
In a trait , you can do anything you can do within a class except:
No class parameters


### Function literal / Anonymous Function
```scala
val add = (a:Int,b:Int) = a + b

```
### Partial Function vs Total Function
A partial function is a function that can not provide all the answer to all the values given in; A partial function has the type of PartialFunction[A, B](isDefinedAt, andThen, orElse)

A total function is a function that provide all the answer to all all values given in, FP typically leverage total function to build applications.

```
def getSecondEle[T]:List[T] =>T = {
      case x :: y :: _ => y
    }
getSecondEle(List(1,2)) shouldBe(2)
assertThrows[MatchError] { getSecondEle(List(1)) }
```
### Class Constructors
* private constructor only accessible within the class auxiliary constructors and companion 
```scala
class AClassWithPrivateConstructor private (a:Int,b:String)
object AClassWithPrivateConstructor{
  def apply(a:Int,b:String) = new AClassWithPrivateConstructor(a,b) 
}
```
* private class
```scala
trait Queue[T] {
def head:T
def enqueue(t:T):Queue[T]
}
object Queue{

private class QueueImpl[T](private val leading:List[T],private val trailing:List[T]){
override def head:T = {
}
}
}
```

### Type Variance
Liskov Substitution Principle : It is safe to say the type T is sub type of U if you can substitute a value of U with a value of T wherever a value of U is required.
* covariance 
* nonvariance
* contravariance

### Make a recursion tail-recursive
1. Create a private internal function with additional accumulator parameter(s);
2. Put the expression(akka last action) to calculate the accumulator in the tail call.
3. The main function call the internal tail-recursive function with the seed value(s) for accumulator(s).

### Type Constructor
A class with type parameter is actually a type constructor, it can construct a family of types.
e.g. List[T] is a type constructor, List[String] is a type

### Setter and Getter
```scala
class User{
var firstName:String
}
// Will be expanded to 
class User{
}

// object-private field
class Stock {
    // a private[this] var is object-private, and can only be seen
    // by the current instance
    private[this] var price: Double = _
   // private var price: Double = _ other Stock instance can see this field.
    def setPrice(p: Double) { price = p }
    // error: this method won't compile because price is now object-private
    def isHigher(that: Stock): Boolean = this.price > that.price
}
```

### Implicit 
Implicit is a technique that 
* a value could be passed to a function call automatically
* a type could be converted to another type automatically

To achieve these two automations, some rules need to follow.
Mark rules: Function, Method, Variable, Class can be marked as implicits
Conversion Rules:
Implicit conversion is being used in three places.
1. Conversion to an expected type;
2. Implicit parameter-- View Bound [T:TargetType]
3. Convert receiver When call obj.doIt, but obj does not 'doit' method, where objA has the method "doit", compiler tries to insert a conversion to convert obj to objA

Resolving rules:
1. Enclosing scope
2. inherited
3. Imported
4. Companion object

* Implicitly
implicit and implicitly are related, but quite distinct. The implicit keyword is part of the language. implicitly is defined in plain Scala code in the standard library

Implicitly is avaliable in Scala 2.8 and is defined in Predef as:
```scala
def implicitly[T](implicit e: T): T = e
```
```scala
implicit val a = "aString"
    val b = implicitly[String]
    b shouldBe(a)
```
It is commonly used to check if an implicit value of type T is available and return it if such is the case.
```

```


```scala
case class Rational(a:Int,b:Int){
def +(that:Rational) = Rational(a + that.a,b + that.b)
}
implicit def convertInt2Rational(a:Int) = Rational(a,0)
1 + Rational(1,2) // will work
```

### For Expression
```scala
// if you replace parentheses with curly braces, semicolons become optional: 
for {  p <- persons // generator
       n = p.name // definition (has the same effect as 'val' definition) 
       if (n startsWith "To") // filter
    } yield n
```
### ClassTag and TypeTag
TypeTag resolves the type eraser problem, 
ClassTag and Typetag is used to provide type information at runtime, ClassTag is a replacement of ClassManifest, and TypeTag is more or less the replacement manifest.

ClassTag provides only the type information at the runtime
TypeTag provides the full type information
```scala
import scala.reflect.runtime.universe._

def meth[A : TypeTag](xs: List[A]) = typeOf[A] match {
  case t if t =:= typeOf[String] => "list of strings"
  case t if t <:< typeOf[Foo] => "list of foos"
}
```

### Extractor 
Extractor is an object with unapply method to extract an object.
```scala
class Email(user:String,domain:String)
object EMail {
  // the injection method (optional)
  def apply(user: String, domain: String) = user + "@" + domain

  // the extraction method (mandatory)
  def unapply(str: String): Option[(String, String)] = {
    // returns option type over pair of strings, since it must handle the case where
    // received param is not an email address
    val parts = str split "@"
    if (parts.length == 2) Some(parts(0), parts(1)) else None
  }

 val s = "user@comain"
    s match {
      case EMail(user,domain)=> println(s"user:$user,domain:$domain")
    }
}
```

### Self Type
Self-types are a way to declare that a trait must be mixed into another trait, even though it doesn’t directly extend it
```scala
trait User{
def userName:String
}
trait Tweet {
this => User // Tweet
def tweet(text:String) = println(s"$userName is tweeting:$text")
}
```


## Self Alias

```scala
trait A
trait B { self => }
// equiveli
trait B {
  private val self = this
}
```



## Scala hierarchy

Any (==,##, toString)
 * AnyVal -- Any primitives plus Unit
 * AnyRef -- Any reference classes ( eq/ne method is for Reference equality check)
   * Null   -- sub type of every reference classes
Nothing -- sub type of every other type

## Scala Future
A future is a **placeholder object** for a value that may be become available at some point in time, asynchronously. It is not the asynchronous computation itself.
A Promise is a function that let client to fulfil the future.

By returning a promise you are giving the right to complete it to somebody else, which allows the client to complete (only once)it at its will, and by returning the future you are giving the right to subscribe to it

## Akka
