Following-up on [the previous post about iterators](http://ebruchez.blogspot.com/2012/07/scala-iterators-and-iteratoriterate.html),
say we have a clean Scala API with `Option` instead:

    class Foo { def parent: Option[Foo] =  … }
    def findBar(foo: Foo) = Option[Bar] = …

It turns out that `Iterator.iterate` falls apart with `Option`, because it requires a function returning a plain `T`.
But we can write a better `Option`-aware `iterate` function:

    def iterate[T](start: T)(f: T ⇒ Option[T]): Iterator[T] = new Iterator[T] {
        private[this] var acc = Option(start)
        def hasNext = acc.isDefined
        def next() = {
            val result = acc.get
            acc = f(result)
            result
        }
    }

Note that this `iterate` does not depend at all on application-specific code. In fact, it could be part of the standard
Scala library!

With this new tool, the new `ancestorOrSelf` iterator simply looks like this:

    def ancestorOrSelf(foo: Foo) =
        iterate(foo)(_.parent)

And one way to write `findInAncestorOrSelf` becomes:

    def findInAncestorOrSelf(foo: Foo) =
        ancestorOrSelf(foo) map findBar find (_.isDefined) flatten

Now to be honest this last function is not fully satisfying, with its use of `isDefined` and its hanging `flatten` in
the end! There are other ways to write it, including with `collectFirst`, but still it's not quite right.

The root issue is that `Scala.Iterator` doesn't have a `headOption` (or a `nextOption`) method. Until that's the case we
can write our own extension method like this:

    class MyIteratorOps[T](i: Iterator[T]) { def nextOption = if (i.hasNext) Option(i.next) else None }
    implicit def toIteratorOps[T](i: Iterator[T]) = new MyIteratorOps(i)

or, with Scala 2.10:

    implicit class MyIteratorOps(i: Iterator[T]) {
        def nextOption = if (i.hasNext) Option(i.next) else None
    }

Again, this is something which could be in the standard Scala library. With this the final code becomes the much cleaner:

    def findInAncestorOrSelf(foo: Foo) =
        ancestorOrSelf(foo) flatMap findBar nextOption

In the end it's probably a matter of taste, but I would almost bet that as a programmer familiar with Scala collections,
you would find this function immediately comprehensible. That's a huge win in addition to the increased modularity gained
through the use of iterators.
