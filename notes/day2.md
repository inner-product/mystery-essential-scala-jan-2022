# ??? Essential Scala Day 2

## Scala Style

CamelCase

Types start with a capital
Values and methods with a lower case

```scala
trait SomeType {
  def someMethod
}
```


## Exhaustivity Checking

If you add a new case to an algebraic data type the compiler will tell you all the pattern matches that need to be updated. Nice!


## Dynamic Dispatch vs Pattern Matching

Dynamic dispatch (OO)
- familiar to OO programmers
- sometimes works around type inference bugs in Scala 2
- might be faster

Pattern matching
- puts all the code of the method in one place
- we can perform pattern matching outside of the types that we match on. I.e. we can add new methods without changing the algebraic data type's code
- familiar to FP programmers (does this matter?)


## Extensibility in FP and OO Style

- Algebraic data types:
  - we *can* add new methods without changing existing code (e.g. using pattern matching)
  - we *cannot* add new types of data without changing existing code
  
- OO style (programming to interfaces)
  - we *cannot* add new methods without changing existing code (adding a new method to a super type requires we can change all implementations)
  - we *can* new implementations of a super type or interface without changing existing code
  
|-------------|----|----|
| Add new ... | FP | OO |
|-------------|----|----|
| methods     | :) | :( |
| data        | :( | :) |
|-------------|----|----|

- Open-Closed Principle
  - software entities (classes, modules, functions, etc.) should be open for extension, but closed for modification
  - I (Noel) disagree, because extension makes it harder to reason about code. We shouldn't always strive for extensibility. It incurs a cost that is not always justified.


## Sum and Product Types

- Product type: logical and
- Sum type: logical or


## Generic Types and Functions

Scala syntax:
- introducing (declaring) a generic type / type parameter
- using a generic type / type parameter

Introduction:
- can only be declared on a class or trait, or on a method
- within square brackets
- by convention use single upper case character (but more descriptive names are ok)
- has the scope of the class, trait, or method on which it is declared

```scala
final case class Box[A](a: A) {
  def map[B](f: A => B): Box[B] =
    Box(f(a))
}
```

Using them:
- just refer the name as you would any other type
- if you extend a class or trait that has a generic type you must pass in a type for that generic type (which is analogous to method parameters, hence the name type parameters)

```scala
trait SuperType[A]

class SubType[B]() extends SuperType[B]
```

Functions are essential when we use generic types.

A generic type means "any particular type" so we can't call any[^1] methods on it. At the point of declaration we don't know what specific type the user will use for the type parameter.

Functions provide a way for users to pass in functionality that works on a generic type.

Syntax:

- Function literal `(param1, param2, param3) => expr` or `param => expr`
- Function type `(Int, String, Event) => Result` or `Int => String`

E.g.

```scala
val f: Int => String = x => x.toString
```

[^1]: On the JVM we can call equals, toString, or hashCode on any type
