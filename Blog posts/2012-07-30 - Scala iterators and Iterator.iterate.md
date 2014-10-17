There is a common programming pattern where you navigate a hierarchy in order to find an element satisfying a certain
condition. I have written code like this in Java probably a million times:

    class Foo { public Foo getParent() { … } }
    public Bar findBar(Foo foo) { … }

    Bar findInAncestorOrSelf(Foo foo) {
        Foo currentFoo = foo;
        while (currentFoo != null) {
            Bar possibleResult = findBar(currentFoo);
            if (possibleResult != null)
                return possibleResult;
            currentFoo = currentFoo.getParent();
        }
        return null;
    }

It is probably efficient but it's not easy to read, it screams "boilerplate", and it mixes two concerns:

- navigating the hierarchy
- doing something with elements in the hierarchy

So while you can write in the same style in Scala, there is a better way which starts with *iterators*. You probably
know about the Java `Iterator` interface, which allows you to iterate over collections and also underlies the "for-each"
construct. Scala also has an `Iterator` trait, which looks a lot like Java's construct but supports [lots of useful functions]
(http://www.scala-lang.org/archives/downloads/distrib/files/nightly/docs/library/index.html#scala.collection.Iterator).
In fact it supports most of the functions found in `Traversable` (which is the base trait of every Scala
collection).

This makes Scala iterators immensely more useful than Java's: implementing an iterator takes a few lines of code and
instantly you have access to dozens of functions including `foreach`, `find`, `map`, `filter`, `++`.

So what if you use an iterator to implement `findInAncestorOrSelf` above? You can implement your own iterator, but it just
happens that here the built-in `Iterator.iterate` helps us  bit: this function takes a starting object and a function to
obtain the next object, and returns an iterator. Here is one which navigates all ancestors forever:

    def ancestorOrSelf(foo: Foo) =
        Iterator.iterate(foo)(_.getParent)

And here is one which actually stops when there are no more ancestors:

    def ancestorOrSelf(foo: Foo) =
        Iterator.iterate(foo)(_.getParent) takeWhile (_ ne null)

You can use that iterator to achieve the same as the original Java code (except it returns an `Option[Bar]`):

    def findInAncestorOrSelf(foo: Foo) =
        ancestorOrSelf(foo) map findBar find (_ ne null)

It's short and to the point, and the great thing is that you can use the iterator to do any kind of search or
transformation of elements in the hierarchy: navigation is completely independent from the rest.

See also ["More iterators goodness"](http://ebruchez.blogspot.com/2012/07/more-iterators-goodness.html).