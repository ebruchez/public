If you have done some Scala for a while, you know about pattern matching and `match/case`. Things like:

```scala
value match {
  case Some(value) ⇒ …
  case None ⇒ …
}
```

But there is another use of the `case` keyword, without `match`, as in:

```scala
map foreach { case (k, v) ⇒ println(k + " → " + v) }
```

The first time I saw this kind of things I was a bit puzzled: in which situations could `case` be used without `match`? Well, it turns out [^1] that a block with a bunch of `case` inside is one way of defining an *anonymous function*.

There is nothing new with anonymous functions of course, and Scala has a very compact notation for those that doesn't involve `case`. But this particular way of defining anonymous functions gives you a lot for free, namely all the good things of pattern matching like casting-done-right, guards, and destructuring. The example above, with `foreach`, shows how case can be used for destructuring the tuples of the map into key and value components.

But there is more. Consider:

```scala
scala> List(41, "cat") map { case i: Int ⇒ i + 1 }
scala.MatchError: cat (of class java.lang.String)
```

As expected this crashes, because the pattern match doesn't know what to do when the string "cat" is passed to it.

On the other hand, this example doesn't crash:

```scala
scala> List(41, "cat") collect { case i: Int ⇒ i + 1 }
res1: List[Int] = List(42)
```

So what's the difference? Does `collect` just catch the `MatchError` and proceed? That would be clumsy and inefficient. In fact, the apparent magic lies in the fact that `case` blocks define special functions called *partial functions*. [^2]

Now you might wonder, coming from a "normal" programming language background, what it means, for a function to be "partial". Well, it [comes from mathematics](http://en.wikipedia.org/wiki/Partial_function), where it's opposed to "total" functions.

But even though it comes from math it's actually simple. Take for example this function:

```scala
def inc(i: Int) = i + 1
```

It is *defined* for any `Int` input value. That means for that any `Int` argument, it produces a resulting `Int` result. [^3]

A partial function on the other hand is defined only for a *subset* of the possible values of its arguments:

```scala
def fraction(d: Int) = 42 / d
```

is not defined for `d == 0` and `fraction(0)` will throw an exception. Think also of the square root function, which is not defined for negative real numbers. Examples abound. And it's true also for the `collect` example above, where the anonymous function is only defined for an `Int` argument but not for a `String` (or any other) argument.

So you get  the idea about some values not "making sense" as the argument of a function because they can't yield a significant result.

Now if you think about it you will notice lots of situations like this in your programs, where functions are expected to work properly only for some input values. If the function is called with a disallowed value, it will typically crash, yield a special return value, or throw an exception (and this should better be documented). In short, partial function are very common in real-life programs even if you don't know about it.

So here `fraction` is defined as a regular function, but conceptually it is a partial function. The good thing is that Scala has built-in support for partial functions thanks to the `PartialFunction` trait. And here is one way of defining such a partial function:

```scala
val fraction = new PartialFunction[Int, Int] {
    def apply(d: Int) = 42 / d
    def isDefinedAt(d: Int) = d != 0
}
```

A `PartialFunction` must provides a method `isDefinedAt`, which allows the caller of the partial function to know, beforehand, whether the function can return a result for a given input value:

```scala
scala> fraction.isDefinedAt(42)
res2: Boolean = true
scala> fraction.isDefinedAt(0)
res3: Boolean = false
```

And if you call the function:

```scala
scala> fraction(42)
res4: Int = 1
scala> fraction(0)
java.lang.ArithmeticException: / by zero
```

This takes us back to the use of `case` to define partial functions. The exact same function can be written:

```scala
val fraction: PartialFunction[Int, Int] =
    { case d: Int if d != 0 ⇒ 42 / d }
```

(Notice that you must specify that the `PartialFunction[Int, Int]` type. It would be great if Scala had a syntax to make this even more compact but it doesn't as of Scala 2.11.)

And if you call the function:

```scala
scala> fraction(42)
res5: Int = 1
scala> fraction(0)
scala.MatchError: 0 (of class java.lang.Integer)
```

(Note that there is one visible difference from the outside when you use the case way: you get a `MatchError` as you usually do with pattern matching.)

The idea doesn't apply only to numbers. In our `collect` example above, the partial function implicitly defined looks like this:

```scala
val incAny: PartialFunction[Any, Int] =
    { case i: Int ⇒ i + 1 }
```

The function takes an `Any` as parameter because `List(41, "cat")` is a `List[Any]`. But it is only defined for inputs that are of type `Int`:

```scala
scala> incAny(41)
res6: Int = 42
scala> incAny("cat")
scala.MatchError: cat (of class java.lang.String)
```

Passing a `String` didn't go too well, as expected. But now you can check this before calling the function with:

```scala
scala> incAny.isDefinedAt(41)
res7: Boolean = true
scala> incAny.isDefinedAt("cat")
res8: Boolean = false
```

So we now have the explanation for the difference in behavior between `collect` and `map`, which is that `collect` expects a partial function. It asks `incAny` whether it is defined for `41` and then `"cat"`, and so automatically filters out `"cat"`. Another cool thing here is that the Scala compiler can even infer a clean resulting collection type: `List[Int]`!

```scala
scala> List(41, "cat") collect incAny
res9: List[Int] = List(42)
```

Also, as you notice, if you define the partial function inline, the compiler knows that it's a partial function and you avoid the explicit `PartialFunction` trait.

Notice that partial functions can lie:

```scala
scala> val liar: PartialFunction[Any, Int] =
    { case i: Int ⇒ i; case s: String ⇒ s.toInt }
liar: PartialFunction[Any,Int] = <function1>
scala> liar.isDefinedAt(42)
res10: Boolean = true
scala> liar.isDefinedAt("cat")
res11: Boolean = true
scala> liar("cat")
java.lang.NumberFormatException: For input string: "cat"
```

Here `liar` says incorrectly that it's defined for `"cat"`. It would probably be better to write:

```scala
scala> val honest: PartialFunction[Any, Int] =
    { case i: Int ⇒ i; case s: String if isParsableAsInt(s) ⇒ s.toInt }
honest: PartialFunction[Any,Int] = <function1>
scala> honest.isDefinedAt("cat")
res12: Boolean = false
```

So now you see how partial functions defined with `case` can be used for things like `collect` with a super compact  notation. You will see them in other places, including catch expressions.

There is another situation in Scala where partial functions are "just there" and you might not know it. Take the following List:

```scala
val pets = List("cat", "dog", "frog")
```

In Scala, any instance of `Seq`, `Set` or `Map` is also a function. So you can write:

```scala
scala> pets(0)
res13: java.lang.String = cat
```

But:

```scala
scala> pets(3)
java.lang.IndexOutOfBoundsException: 3
```

Wouldn't that mean that the `pets` function is, hum, only *defined* for values `0`, `1`, and `2`? Sounds familiar? Wouldn't it be cool to look at pets as a partial function then? Well you can because in Scala any instance of `Seq`, `Set` or `Map` is actually a partial function. So you can write:

```scala
scala> pets.isDefinedAt(0)
res14: Boolean = true
scala> pets.isDefinedAt(3)
res15: Boolean = false
```

And if you had a list of indexes and wanted to safely collect values for these indexes in a new list, you could write:

```scala
scala> Seq(1, 2, 42) collect pets
res16: Seq[java.lang.String] = List(dog, frog)
```

Here it works well because `collect` handles everything for us. But it can be a pain to check `isDefinedAt` all over the place. If anything, it feels a bit like a `null` check, and we hate those in Scala. The good news is that in Scala the `PartialFunction` trait supports the `lift` method, which converts the partial function to a normal function that doesn't crash:

```scala
scala> pets.lift(0)
res17: Option[java.lang.String] = Some(cat)
scala> pets.lift(42)
res18: Option[java.lang.String] = None
```

As you see the `lift` returns a function that returns an `Option` of the value. This allows you to safely process values without `null` checks and without calling `isDefinedAt` yourself:

```scala
scala> pets.lift(0) map ("I love my " + _) getOrElse ""
res19: java.lang.String = I love my cat
scala> pets.lift(42) map ("I love my " + _) getOrElse ""
res20: java.lang.String = ""
```

I hope this helps make some sense of partial functions in Scala.

*NOTE: This post was updated on 2014-12-27 to fix the use of `def` where `val` was called for, based on user comments. A few typos have been corrected as well.*

[^1]: From The Scala Language Specification: "An anonymous function can be defined by a sequence of cases […] which appear as an expression without a prior match."

[^2]: This is not to be confused with *partially applied functions*, which are a completely different topic.

[^3]: In Scala, it is defined even for `Int.MaxValue`, as `Int.MaxValue + 1 == Int.MinValue`. The result is just plain wrong but it's defined!