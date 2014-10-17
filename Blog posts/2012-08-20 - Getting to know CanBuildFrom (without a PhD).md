Recently I needed to write a pretty simple function: given a sequence of `(name, value)` pairs, return
a sequence of `(name, some collection of all the values having that name)` pairs. Here is a simple implementation:

    def combineValues(pairs: Seq[(String, String)]): Seq[(String, Seq[String])] = {
        val result = LinkedHashMap[String, List[String]]()

        for ((name, value) ← pairs)
            result += name → (value :: result.getOrElse(name, Nil))

        result.toList
    }

Then I realized that I not only needed the result as a `Seq[(String, Seq[String])]`, but also as a
`Seq[(String, Array[AnyRef])]`, for compatibility with a Java API. I could of course transform one into the other,
but I wondered whether there might be a way to directly return the desired collection type (without duplicating the function!).

Let's think a little bit about what this entails: the revised `combineValues` needs to create *new collections* of a type
specified by the caller. So first the type needs to be specified, which means that the function needs to take so-called
*type parameters*: one for the resulting collection type, and one for the item type:

    import language.higherKinds // so that Scala 2.10 doesn't warn
    def combineValues[U, T[_]](pairs: Seq[(String, String)]): Seq[(String, T[U])]

The type parameters are specified with the funny `[U, T[_]]` declaration before the usual function parameters. You have
probably seen this syntax on generic classes. But functions can have type parameters too!

Also, the function now says it returns values of type `T[U]`. The idea here is that we will return collections of type `T` where `T`
stands for things like `Seq`, `Array` and `Set` containing elements of type `U`, where `U` stands for `String` or
`AnyRef`.

Do you see what we've done here? We have changed the signature to be more abstract: instead of specifying beforehand
that we are dealing with `Seq` or `Array` or `String`, we use "variables" (`T` and `U`). In case you are wondering, there
is nothing magic about the names `T` and `U`, we could have used `Coll` and `Elem` instead. It's a matter of taste.

A couple of remarks on the syntax:

- It's not possible to just specify `[T[U]]` as a single type parameter: the two parameters must be separate.
- Because `T` is a type which itself takes a parameter, we must say so with `T[_]` (`T[Whatever]` works too).

Here is how you call the function with explicit type parameters (sometimes, type parameters can be inferred by the
compiler, but not here):

    combineValues[String, List](seq)

Now the function needs to make use of these type parameters to create the new collections. How do we do that?

Naively, let's try creating new empty collections of type `T[U]` using a constructor:

    new T[U] // incorrect Scala

or, using a companion object's `apply`:

    T[U]() // incorrect Scala

Unfortunately, neither even compiles. How would something like this work anyway? You could imagine that the
runtime type information is available to `combineValues` and then use reflection to create new instances. But:

- Scala erases parametrized types during compilation, and type parameters are not automatically available at runtime
  (although there are ways to obtain them).
- The collection type parameter could be a trait, like `Seq`, in which case you would have to find a reasonable concrete
  class implementing that trait. How would you go about making that determination?
- This would result in instantiating empty instances of the resulting collection. How would you add elements to that
  collection? `Array` and `List` in particular aren't `Growable` collections. So you would have to find a constructor
  taking all the items of the collection instead. That's possible, but not guaranteed to work at runtime for all types.
- This might not be very efficient due to the use of reflection.

So besides the fact that it does not work out of the box, this approach is not as simple as it first looked. We are missing
something, and that something is a *factory* for the collection to create. And Scala already has
a trait for that kind of factories in the Scala standard library: it is called [`CanBuildFrom`][1].

This trait is a bit of a funny thing: it is part of many Scala collections function signatures, and for that reason
has been the [target][2] of criticism, with the result that Scala 2.8's Scaladoc hides it from signatures by default.
[Some][3] have argued that it's not necessary to understand it to use Scala collections, and I agree with that
(in general, Scala collections just work even if you don't have any idea that `CanBuildFrom` exists).

This said, `CanBuildFrom` itself is a pretty simple thing conceptually: if you have an instance of `CanBuildFrom` for a given collection
type, you can call `apply` on it to get a `Builder` for that collection. Once you have the builder, you just add
elements to it, and finally obtain the resulting collection. The trait itself is parametrized, to specify a source
collection type, an element type, and a resulting collection type: `CanBuildFrom[-From, -Elem, +To]`.

Here, what we want is a `CanBuildFrom[T[U], U, T[U]]`. The first type parameter, the `From` collection, does not seem to
be consequential here.) In short it's a factory able to return a `Builder` for a collection `T[U]`.

A version of our function receiving a `CanBuildFrom` looks like this:

    def combineValues[U, T[_]](pairs: Seq[(String, String)],
            cbf: CanBuildFrom[T[U], U, T[U]]): Seq[(String, T[U])] = {
        val result = LinkedHashMap[String, Builder[String, T[U]]]()

        for ((name, value) ← pairs)
            result.getOrElseUpdate(name, cbf()) += value

        result map { case (k, v) ⇒ k → v.result } toList
    }

Note where the factory is called: `cbf()`, and where the resulting collection is obtained: `v.result`.

But how does the caller even *obtain* a `CanBuildFrom`? You have not only to pass `T` and `U`
(the type parameters) but also somehow figure out where to locate that factory.

Of course, and this is why the Scala collections and `CanBuildFrom` are designed this way in the first place, there is a twist: *implicit
parameters*. The idea is that instead of finding and passing the factory explicitly to the function, the compiler does
that for you. So we change the signature as follows:

    def combineValues[U, T[_]](pairs: Seq[(String, String)])
            (implicit cbf: CanBuildFrom[T[U], U, T[U]]): Seq[(String, T[U])]

Notice how we changed `cbf` from a regular parameter to an implicit parameter with the `implicit` keyword, and moved it to
a second parameter list (yes, Scala supports more than one parameter list).

When the compiler sees a call to such a function, it does something called an *implicit search*. This is the process
whereby it locates an appropriate value for the implicit parameter, based on the function scope and parameters
(see [more on implicit search][4]). Importantly, it looks at the *type* of the implicit parameter. Here this means that the value must be of type
`CanBuildFrom`, with the proper type parameters too.

The Scala collections have such factories already, and they are nicely made available to implicit search when you use
collection types such as `Seq` and `Array`. So the compiler will find them without trouble.

So say we call the function like this:

    combineValues[AnyRef, Array](seq)
    combineValues[String, List](seq)

The compiler will respectively search for the following `CanBuildFrom` instances, and pass them to the function:

    CanBuildFrom[Array[AnyRef], AnyRef, Array[AnyRef]]
    CanBuildFrom[List[String], String, List[String]]

And it will just work!

The result? We have a achieved more than what we were looking for: we have a function able to return a result made of
any existing collection available in the standard library, or even outside of the standard library, for that matter.
The resulting collection can be anything you want as long as there is a `CanBuildFrom` for the resulting type. Most notably, `Array` is a plain
Java array, and it works because Scala provides a `CanBuildFrom` for it. It's a powerful approach.

From the caller side, things remain easy: you call your function as usual except that you specify the type of the result
you want.

Is there magic here? No: it is all done thanks to the clever design of implicit parameters and their use within the standard Scala
collections. And the great thing is that the mechanics of it are available to anybody, not just to the compiler
or Scala standard library maintainers.

What to think of the type parameters and the implicit `CanBuildFrom` factory? They certainly lead to more abstract
programming, but when put in simple terms, like "here is a function which can return a result based on any
collection type you want, as long as the compiler finds a factory for that type", I think it sounds quite reasonable.

[1]: http://www.scala-lang.org/archives/downloads/distrib/files/nightly/docs/library/index.html#scala.collection.generic.CanBuildFrom
[2]: http://stackoverflow.com/questions/1722726/is-the-scala-2-8-collections-library-a-case-of-the-longest-suicide-note-in-hist
[3]: http://grahamhackingscala.blogspot.com/2011/01/scala-pragmatism-ignore-canbuildfrom.html
[4]: http://stackoverflow.com/questions/5598085/where-does-scala-look-for-implicits