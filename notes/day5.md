# ??? Essential Scala Day 5 

## Type Classes Continued

Solutions to exercise

### Type Class Composition

```scala
implicit def listWriter[A](implicit w: Writer[A]): Writer[List[A]] =
  new Writer[List[A]] {
    def write(list: List[A]): Json =
      JArray(list.map(a => w.write(a)))
  }
```

Get the compiler to do work for us.

Specify:
- the atoms
- the rules of combination

and the compiler will apply the rules to construct type class instances.

### Diverging Implicit Expansion

```scala
trait Writer[A] {
  def write(a: A): String
}

implicit def writerWriter[A](implicit w: Writer[A]): Writer[A] = w

implicitly[Writer[Int]]
// def implicitly[A](implicit i: A): A = i
//
// <console>:14: error: diverging implicit expansion for type Writer[Int]
// starting with method writerWriter
//     implicitly[Writer[Int]]
```

### Context bound

Context bound is a shorthand for an implicit parameter

```scala
def aMethod[A: TypeClass] = ???
// equivalent to
def aMethod[A](implicit t: TypeClass[A]) = ???
```

Two downsides:
1. You can't mix implicit parameters and context bounds
2. You don't have a name for the implicit parameter in the scope of the method body

### Implicit Classes

Implicit classes are about adding extension methods. Sometimes called syntax (but it isn't really syntax.)

```scala
implicit class StringOps(s: String) {
  def hello: String = s"Hello $s"
}
implicit class MoreStringOps(s: String) {
  def hi: String = s"Hi $s"
}
implicit class YetMoreStringOps(s: String) {
  def hi: String = s"Howdy $s"
}

"Noel".hello
// String = Hello Noel
"Noel".hi
// <console>:16: error: type mismatch;
//  found   : String("Noel")
//  required: ?{def hi: ?}
// Note that implicit conversions are not applicable because they are ambiguous:
//  both method MoreStringOps of type (s: String)MoreStringOps
//  and method YetMoreStringOps of type (s: String)YetMoreStringOps
//  are possible conversion functions from String("Noel") to ?{def hi: ?}
//        "Noel".hi
//        ^
// <console>:16: error: value hi is not a member of String
//        "Noel".hi
       
implicit class FurtherStringOps(s: String) {
  def toLowerCase: String = "Just joking"
}
```

Constructor argument is the type we add methods to. (Can use a type variable here.)

Methods are added to the type. Normal method rules apply. For example methods can have implicit parameters.

Usual scoping rules apply---implicit class must be in scope.

If a type already has a method implicit classes are not considered at all.

Implicit classes cannot be defined at the top level---must be wrapped in a class or object. Usually in a companion object or a "syntax" object.


## Monix

https://monix.io/

### What Problem Does It Solve?

Write concurrent and asynchronous code that is:

- reasonably compact; and
- reasonably easy to reason about

while still be reasonably performant.


### What Libraries Does It Compete With?

- Future (Scala standard library)
- Cats effect (Noel's preferred choice)
- ZIO

Monix, Cats effect, and ZIO are all "effect monads". Future is a bit different.

F[A].flatMap(A => F[B]) = F[B]

F could be List, Future, Option, etc.

List[A].flatMap(A => List[B]) = List[B]
flatMap transforms every element to a list of new elements
- Can change the number of elements in the List.

List[User]
User => List[Event]

List[User] => List[Event]


List[A].map(A => B)           = List[B]
map transforms every element to a new element
- Cannot change the number of elements in the list


Future[A].flatMap(A => Future[B]) = Future[B]
- sequence of operations:
  - when A is available, calculate Future[B] and start that process running

Either[Error, A].flatMap(A => Either[Error, B]) = Either[Error, B]

Sequencing: Do one thing and then another

ExecutionContext: essentially a thread pool.
- hard to reason about when passed implicitly

Future start running as soon as they are created.

```scala
val f = Future { 5 }
val g = Future { 3 }
val h = for {
  x: Int <- f // returns Future(5)
  y: Int <- g // returns Future(3)
} yield x + y


val h = for {
  x: Int <- Future(5)
  y: Int <- Future(3)
} yield x + y

f.flatMap{ (x: Int) => g.map{ (y: Int) => x + y } }

Future(5).flatMap{ (x: Int) => Future(3).map{ (y: Int) => x + y } }
```

Straightforward refactorings can actually break expected semantics.

Task[A] and other effect monads
- lazy: nothing happens until we ask it to.
- the code doesn't run until we call a method that explicitly runs it

Separation between description and action.

```scala
lazy val x = 1
```

## Error Handling

Two users of errors:
1. End user "I have done something wrong and I want to know what so I can fix it."
2. Programmer "The code has gone wrong and I want to know what so I can fix it."

Two type of errors:
1. Recoverable. Often end user errors. Often part of the UX.
2. Unrecoverable. Often programmer errors. Stack traces and so forth, that shouldn't be seen by end users.

Unrecoverable: exceptions. One global exception handler, print the stack trace and you're done.

Recoverable: typed errors. Structure that you can render for the end user. Maybe use an `Either` and an algebraic data type (logical or) to represent errors.

Typed errors are a bit of a pain in Scala 2. Scala 3's union types make this easier.


## Underscore

Converting a method to a function

```scala
def add(x: Int, y: Int): Int = x + y
val f = add _
```

Import everything

```scala
import scala.concurrent._ // import scala.concurrent.* in Scala 3
```

Existential type

```scala
def doSomething(list: List[_]): Int = list.size
// Simpler to write
def doSomething[A](list: List[A]): Int = list.size
```

Pattern match wildcard

```scala
100 match {
  case 1 => "one"
  case 2 => "two"
  case _ => "big"
}
```


## Existential Types

- A means for all A
- _ (existential type)  means for some type, which we don't happen to know

Like information hiding for types. 

Usually use a type member

```scala
trait SomeTrait {
  type SomeType
}
```

If you can use a generic it is much simpler to work with!
