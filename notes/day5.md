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


## Monix

https://monix.io/

### What Problem Does It Solve?

### What Libraries Does It Compete With?
