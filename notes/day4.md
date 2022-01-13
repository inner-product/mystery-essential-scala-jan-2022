# ??? Essential Scala Day 4

## Implicits

Get the compiler to do work for you, based on types.

|----------------------|---------------------------------------------------------------|
| Feature              | Use                                                           |
|----------------------|---------------------------------------------------------------|
| Implicit parameters  | Get the compiler to supply parameters for a method            |
| Implicit values      | Tell the compiler values it can supply to implicit parameters |
| Implicit classes     | Add extension methods to classes                              |
| Implicit conversions | Evil. We will never talk of them again.                       |
|----------------------|---------------------------------------------------------------|


### Implicit Parameters

An *implicit parameter list* is:

- the last parameter list of a method
- starts with the keyword `implicit`

This indicates that all the parameters in the list are implicit parameters.

```scala
// On List
def max[A](implicit ord: math.Ordering[B]): A
def maxBy[B](f: (A) => B)(implicit cmp: math.Ordering[B]): A

// Example with multiple implicit parameters
def maxBoth[A, B](listA: List[A], listB: List[B])(implicit oA: Ordering[A], oB: Ordering[B]): (A, B) =
  (listA.max, listB.max)
```

Semantics:
- If we explicitly pass a parameter, it works as a normal parameter.
- If we don't explicitly pass parameters, the compiler will try to find values to pass as the parameters.

The values:
- must be of same type as the implicit parameter
- must be marked as implicit to indicate to the compiler that they can passed implicitly


### Implicit Values

An *implicit value* is:

- a value that the compiler can pass to an implicit parameter
- it is either:
  - a `val`
  - an `object`
  - or a `def` *with only implicit parameters*
  - that starts the declaration with the keyword `implicit`
  
```scala
implicit val a: Int = 42
implicit object AnImplict {}
implicit def aMethod(implicit ord: Ordering[A]) = ???
```

If there are two or more implicit values with the same type within scope the compiler will raise an error. E.g.:

```
error: ambiguous implicit values:
 both value a of type Int
 and value a2 of type Int
 match expected type Int
```

Implicit scope:
- where the compiler looks for implicit values.

The implicit scope is:
- the usual lexical scope
- companion objects of any type involved in an implicit parameter

```
sealed trait List[A] {
  def max[A](implicit ord: math.Ordering[B]): A
}

val aList: List[Int] = List(1, 2, 3, 4)
aList.max // This will look at the companion objects of List, Int, and Ordering
```


## Type Classes

|---------------------|--------------------|
| Concept             | Scala Feature      |
|---------------------|--------------------|
| Type class          | trait              |
| Type class instance | implicit value     |
| Type class use      | implicit parameter |
|---------------------|--------------------|

Interface that defines a set of operations. This is a type class and in Scala we represent it as a trait.
- Will always have at least one generic type, to represent the type to which we apply the operations for concrete instances.

Implementations of the type class for specific types. These are type class instances, represented in Scala as implicit values.

Places where we want to use a type class. In Scala this is an implicit parameter (or a context bound)

Why?
- Decoupling implementation of the interface from the definition of the class (or type). Interface can be implemented at a later point in time.
- Can have multiple implementations of the interface for one class (or type)

Ad-hoc polymorphism. Fancy FP word for what type classes do.

Parametric polymorphism: type variables / generic types == any type
Ad-hoc polymorphism: type classes == any type that has this interface implemented. Any type that meets this constraint. Don't have to be related by extension in the type hierarchy.

Diverging implicit expansion

Context bound.
