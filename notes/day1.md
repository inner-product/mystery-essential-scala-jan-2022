# ??? Essential Scala Day 1

## Introductions

- Name, programming background, issues w/ Scala or things you want to learn how to do

- Michael: basic Scala. Would like to know: implicits. Programming background: C, C++, Python, assembly. Metal work, nature, travel.

- Arun: backend engineer, data engineer. Java, a bit of Python. Working with Scala now, but not very experienced. Implicits. What are the patterns for successful programming in Scala?

- Swapnil: data engineer (Data lake team). Python & Java. Little Scala experience. Treating Scala as Java. Let's do Scala as Scala. Tennis & music.

- Chang: Account security. C++ & Python. No Scala experience. Best practices. Some projects involve Scala / Spark. What is more Scala or Spark specific?

- Amy: Mostly Python, R, SASS. Started using Scala about 6 months ago. Scala equivalent of "the Python way". Machine learning engineer.

- Akash: Software developer with Nemesis (?) team. 6-7 months at ???, with a bit of Scala. Years of Node.js. Unlearning functional style (Node) for OO style (Scala). [Noel is confused.]

- Jun: Discovery team, machine learning engineer. Fortran, R, Sass, Python. Last few years of Python. Joining ??? necessitates Scala (legacy [!?!] products in Scala)

- Maggie: recently joined as machine learning engineer. Used a little bit of Scala. Reading / hiking.

- Mehul: Software developer mainly using Java, Python. Recently joined ??? (2 months) and hence the Scala journey starts.

- Dipesh: Scala since joining ??? 4 months ago. Java and C# previously. Learn more about FP.


## Course Overview

### Notes & Learning

Learn, actively


### Admin

Times & breaks


### Curriculum

Setup and Basic Workflow
Expressions, Types, and Values
Algebraic Data Types: sealed trait, final case classes / proof by induction
Sequencing Computations: map, flatMap / monads, functors
Implicits and Type classes: implicits (parameter, values, classes) / type class ad-hoc polymorphism


### How We Teach

Models and processes
- Build mental models to understand how things work
- Learn processes to understand how to do things


### Additional Material

Essential Scala


## Setup

- Essential Exercises: https://github.com/inner-product/essential-exercises 
  - Use the `sbt` project, not the `gradle` one!


## Expressions, Types, and Values

- Compile time: types are a property of compile time
- Run time: types are NOT a property of run time

Types are a property of expressions
- a type is a set of values; the set of values that could be produced when an expression is evaluated (and assuming it evaluates successfully)

`Int`: 2^32
`Future[List[String]]`

- expressions are program text (you can write them in a file, on a piece of paper, etc.) that when evaluated produce values

- values are things in the computer's memory

Analogy:
- Writing == expressions
- Reading == evaluation
- Understanding == values

Sometimes values have *tags* that represent type information (i.e. transfer information from compile time to run time) but they don't always.


## Algebraic Data Types

Algebraic data types are a way of representing data when the data can be described in terms of logical ands and logical ors.

Example:
- A Macbook is an Air, Pro 13", Pro 14", or Pro 16"
- A Pro 14" is M1, SSD, RAM, and GPU

All languages have ands, but most don't have ors.

Logical ands

```scala
// A is a B and C
final case class A(b: B, c: C)
```


Logical ors

```scala
// A is a B or C
sealed trait A
final case class B() extends A
final case class C() extends A
```

Details:
- case classes should be immutable (don't change)
- don't use `new` when constructing them
- they have a useful `copy` method

Algebraic data types cannot be extended
- they are a closed world---we know all the possible cases
- this gives us certain guarantees in, e.g., pattern matching the compiler can check we handle all the cases


### Structural Recursion

How we process algebraic data types. Given an algebraic data type we can transform it into any other type using a pattern called structural recursion.

Implementation in Scala can be:
- pattern matching
- dynamic dispatch

#### Dynamic Dispatch

```scala
// A is a B or C
sealed trait A {
  def someMethod: D
}
final case class B(f: F, g: G) extends A {
  def someMethod: D = f.aMethod(g)
}
final case class C() extends A {
  def someMethod: D = theValueOfTypeD
}

val anA: A = C()
anA.someMethod // calls someMethod on C not A
```

C++ `virtual` ?

#### Pattern Matching

Pattern matching is an expression (produces a value) that checks a value against "patterns" and evaluates the first pattern that matches the value.

```scala
"hello" match {
  case "bye" => false
  case "hello" => true
}
```

```scala
aValue match {
  case pat1 => expr1
  case pat2 => expr2
  ...
}
```

We match `aValue` against the patterns `pat` ... from top to bottom. We evaluate the RHS expression of the first pattern that matches, and the result of the entire pattern matching expression is that result of the RHS expression that matched.

A pattern can be:
- a literal like "hello" or 2, matches exactly that literal
- a wildcard, which is any identifier, matches any value and creates a variable with the name of the wildcard in the scope of the RHS expression

```scala
(2 + 2) match {
  case 0 => "zero"
  case 2 => "two"
  case whatever => s"$whatever is bigger than two"
}

```

- the name of a case class, with patterns for each constructor argument of the case class

```scala
final case class Cat(name: String, purriness: Int)

Cat("Owl", 4) match {
  case Cat(name, 6) => "fairly purry"
  case Cat("Owl", 4) => "getting in early"
  case Cat("Owl", p) => s"Owl has purriness $p"
  case Cat("Owl", 4) => "late to the party"
}
```

```scala
// A is a B and C
anA match {
  case A(b, c) => ???
}
// A is a B or C
anA match {
  case B() => ???
  case C() => ???
}
```

Recursion: if the data is recursive then the method is also recursive in the same place.

```scala
// A List of Int is
// - Empty
// - Pair, which is a head (which is an Int) and a tail which is a List of Int
// tail is recursive
sealed trait ListOfInt
final case class Empty() extends ListOfInt
final case class Pair(head: Int, tail: ListOfInt) extends ListOfInt

// This all comes from the rules above
def sum(list: ListOfInt): Int =
  list match {
    case Empty() => ???
    case Pair(h, t) => sum(t)
  }

def sum(list: ListOfInt): Int =
  list match {
    case Empty() => 0
    case Pair(h, t) => h + sum(t)
  }
  
val aList = Pair(3, Pair(2, Pair(1, Empty())))
sum(aList)
```
