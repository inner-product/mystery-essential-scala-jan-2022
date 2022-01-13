# ??? Essential Scala Day 3

## Type Parameters and Functions Continued...

Finish the exercises.

### Talking points
- Programming strategies: following the types
- Scala API documentation
- Companion objects
- Multiple parameter lists
- Variance: covariance, contravariance, invariance
- apply

### Scala API Documentation

https://www.scala-lang.org/api/current/

### Companion Objects

An object that:
- has the same name as a type (class or trait) AND
- is in the same file as that type

Conventionally stores constructors and utilities. Scala equivalent of Java's static methods.

Packaging of algebraic data type members within companion objects


## Apply method

If an object has a method named `apply` you don't have to write the name `apply` when you call the method; you can call it "as a function"

```scala
anObject.apply(1)
// Can instead write
anObject(1)
```

## Collections

- map
- flatMap
- foldLeft / foldRight


### Partial Functions

If we have a function with a single parameter and we want to pattern match on that parameter we can write `{ case pat => expr }` this is called a partial function in Scala


### Irrefutable Pattern in val

If a pattern match cannot fail we can use it in a `val`
