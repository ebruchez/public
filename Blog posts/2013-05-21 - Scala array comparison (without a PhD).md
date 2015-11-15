### Scala collections and equality

Scala collections follow a simple set of general equality rules[^1]. Two collections are equal if:

- they are in the same overall category (`Seq`, `Set`, or `Map`)
- they contain the same elements (as defined by `==`)
- for sequences, the elements are in the same order

This means the following:

```scala
scala> List(1, 2, 3) == Vector(1, 2, 3)
res0: Boolean = true

scala> List(1, 2, 3) == Set(1, 2, 3)
res1: Boolean = false

scala> Set(1, 2, 3) == collection.mutable.LinkedHashSet(3, 1, 2)
res2: Boolean = true
```

### Arrays don't behave

The gotcha is that these rules break down with arrays:

```scala
scala> List(1, 2, 3) == Array(1, 2, 3)
res3: Boolean = false
```

And even:

```scala
scala> Array(1, 2, 3) == Array(1, 2, 3)
res4: Boolean = false
```

Why is this? The answer is that arrays in Java are treated specially all the way down to the JVM, and Scala arrays are just plain Java arrays. It is not possible to extend a JVM array, and this means in particular that it is not possible to override `equals` on arrays. Since native array eqality via `equals` does *not* compare array content, arrays are left to behave differently from Scala collections.

### Solutions

So how do you go about comparing arrays in a way compatible with other Scala collections? First, there is an implicit conversion from `Array` to `collection.mutable.WrappedArray`, which is a `Seq`, so you can write:

```scala
scala> (Array(1, 2, 3): WrappedArray[Int]) == (Array(1, 2, 3): WrappedArray[Int])
res5: Boolean = true
```

Or even shorter:

```scala
scala> (Array(1, 2, 3): Seq[Int]) == (Array(1, 2, 3): Seq[Int])
res6: Boolean = true
```

Of course this also works if you pass an array to anything which expects a `Seq`:

```scala
scala> def sameStuff(s1: Seq[Int], s2: Seq[Int]) = s1 == s2
foo: (s1: Seq[Int], s2: Seq[Int])Boolean

scala> sameStuff(Array(1, 2, 3), Array(1, 2, 3))
res7: Boolean = true
```

From a memory perspective, this is not too bad because `WrappedArray` doesn't make an expensive copy of the collection and instead just wraps the array.[^2] But there is *boxing* going on with `WrappedArray` because it's not  a *value class* [^6] (or rather concrete implementations of it are not value classes). Value classes cannot override equality[^5], and a `WrappedArray` couldn't be a proper `Seq` if that were the case.[^4]

There is another way to compare array content with `sameElements`:

```scala
scala> Array(1, 2, 3) sameElements Array(1, 2, 3)
res8: Boolean = true
```

The benefit is that this more explicitly states the intent, and you don't need to convert the arrays to `Seq` via type declarations.

### Nested arrays

Both solutions above only work if arrays are not nested. Consider:

```scala
scala> Array(Array(11), Array(21, 22)) sameElements Array(Array(11), Array(21, 22))
res9: Boolean = false
```

The reason is that `sameElements` (or `==` on `WrappedArray`) just calls `==` on each array element. If those elements are arrays, Java array equality kicks in again (and we know that array content is not compared in that case).

Instead you can use the `deep` method:

```scala
scala> Array(Array(11), Array(21, 22)).deep == Array(Array(11), Array(21, 22)).deep
res10: Boolean = true
```

The `deep` method wraps arrays so that each access to an array element is first checked: if the element itself is an array, it is wrapped with `WrappedArray` first.[^7] This way equality recursively works (but only if each array is *directly* nested in an array!).

It is generally safe to use `deep` instead of `sameElements` or a conversion to `WrappedArray`, but the implementation of `deep` requires a number of pattern matches, which are not known to be the fastest. If the arrays are known to be flat, the other approaches might be more efficient.

### Case classes

Consider this case class:

```scala
scala> case class Foo(a: Array[Int])
defined class Foo

scala> Foo(Array(1)) == Foo(Array(1))
res11: Boolean = false
```

By now you know why this happens: the case class provides an implementation of `==` for you, but doesn't treat arrays specially. If equality matters to you (and it probably should if you use case classes), it is better to write instead something like this:

```scala
scala> case class Foo(a: Seq[Int])
defined class Foo

scala> Foo(Array(1)) == Foo(Array(1))
res12: Boolean = true
```

Here the case class actually refers to a `WrappedArray`, obtained via implicit conversion from the original array.

### Words of wisdom

The equality issue suggests that it is wise to avoid arrays when possible. If you cannot avoid them, beware of the semantic of equality on them! But since arrays offer interoperability with Java and are compact, native data structures that offers performance benefits, it's often hard to live without them entirely.

There are a number of StackOverflow questions that cover this topic as well.[^3] Please let me know if I omitted anything important!

[^1]: See [The Scala 2.8 Collections API - Equality](http://www.scala-lang.org/docu/files/collections-api/collections_0.html) for details.

[^2]: For an `Array[Int]`, the Scala implementation looks like:

    ```scala
    final class ofInt(val array: Array[Int]) extends WrappedArray[Int] { ... }
    ```

    On the other hand it doesn't seem like `WrappedArray` uses `java.util.Arrays.equals()` so the actual comparison performance might not be absolutely optimal (but I don't have numbers).

[^4]: On the other hand `ArrayOps` are value classes:

    ```scala
    trait ArrayOps[T] extends Any with ...
    ```

    This is ok because the purpose of `ArrayOps` is to provide extension methods on native arrays, and equality cannot be implemented as an extension method:

    ```scala
    scala> (Array(1, 2, 3): ArrayOps[Int]) == (Array(1, 2, 3): ArrayOps[Int])
    res12: Boolean = false
    ```

[^5]: ["A value class… may not define a equals or hashCode method"](http://docs.scala-lang.org/overviews/core/value-classes.html)

[^6]: Value classes are new in Scala 2.10.

[^7]: I find `deep` a bit funny: it returns an `IndexedSeq[Any]` instead of being generic. I am not sure why that is, but for the purpose of comparing array content it doesn't matter.

[^3]: See:

    - [How do I compare two arrays in scala?](http://stackoverflow.com/questions/5393243/how-do-i-compare-two-arrays-in-scala)
    - [Why does `Array(0,1,2) == Array(0,1,2)` not return the expected result?](http://stackoverflow.com/questions/2481149/why-does-array0-1-2-array0-1-2-not-return-the-expected-result)
    - [Why doesn't the Array Equality Function Work as Expected?](http://stackoverflow.com/questions/3737711/why-doesnt-the-array-equality-function-work-as-expected)

