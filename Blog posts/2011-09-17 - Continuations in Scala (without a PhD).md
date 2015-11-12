With [@avernet](https://twitter.com/avernet) we have been thinking lately about [continuations](https://en.wikipedia.org/wiki/Continuation), for a few reasons:

- Continuations pop up on the web as a concept that could help with event-based programming
- Scala has a continuations plugin, and we're wondering what the deal is with that.
- It just seems like fun to try to understand this (alongside things like monads).

The main idea of continuations is the ability to interrupt a program, save its control state, and resume it at a later point in time.

One thing to realize is that there are many ways to implement this idea and variations around it. Google a bit and you will find a lot of material on continuations, some of which goes deep into computer science. Here we don't care about the big picture: we just want to get at least some insight into Scala continuations.

The main source of information on Scala continuations is the [EPFL paper](http://infoscience.epfl.ch/record/149136/files/icfp113-rompf.pdf) describing how continuations were designed in the Scala compiler. But if you google "scala continuations" and hope to find right away a clear explanation, you might be disappointed. You will find the following example (I am not kidding):

```scala
reset {
  shift { k: (Int ⇒ Int) ⇒
    k(k(k(7)))
  } + 1
} * 2
```

This proudly produces the flamboyant result: 20. As a [commenter says](http://www.scala-lang.org/old/node/2096), "these are convoluted ways of adding numbers and I have no idea what is being gained or accomplished". [@djspiewak](https://twitter.com/djspiewak) echoes this when [he says](http://assets.en.oreilly.com/1/event/45/High%20Wizardry%20in%20the%20Land%20of%20Scala%20Presentation.pdf) that continuations in Scala are "powerful ...but useless". It's a bit like explaining how a combustion engine works, but not that it could be used to, say, move your car from home to work.

So let's try to look at something concrete. Imagine a `read()` function which returns a byte from the network:

```scala
def read: Byte
```

This is typically the signature of a synchronous (blocking) function. After all, it has a return value and in normal programming languages, that means waiting for that value to be available. A program that reads two bytes in a row and prints them looks like this:

```scala
val byte1 = read
println("byte1 = " + byte1)
val byte2 = read
println("byte2 = " + byte2)
```

The issue is that in a web browser or node.js or any other single-threaded, event-driven environment, this is not acceptable: you simply cannot block for a long time, otherwise nothing else can happen in the system. So instead, the `read()` function is made to take a callback, something like:

```scala
def read(callback: Byte => Unit): Unit
```

You must now write your program like this:

```scala
read { byte1 ⇒
  println("byte1 = " + byte1)
  read { byte2 ⇒
    println("byte2 = " + byte2)
  }
}
```

The issue here is that you must write in a funny style, even with Scala's lightweight syntax for closures. Note also how each callback typically causes a new level of indentation. Some programmers manage to get used to this style, but it does not represent the control flow in a very natural way, and the issue grows with the size of the program.

Enter Scala continuations:

```scala
import scala.util.continuations._
reset {
  val byte1 = shift(read)
  println("byte1 = " + byte1)
  val byte2 = shift(read)
  println("byte2 = " + byte2)
}
```

And voilà: you can write the program again in imperative style without callbacks and closures.

You notice the `reset` and `shift` constructs. These terms don't make any sense to a newcomer, but they were introduced a long time ago in an academic paper so are reused in Scala. Basically, `reset` *delimits* the continuation. With full continuations, the entire rest of the program would be under control of the continuation, but here, whatever is before and after the `reset` block has nothing to do with continuations at all. (Also, `reset` can return a value, although here we don't care about it.)

`shift` is the construct that does the real magic. Mainly, it smartly hacks around to pass the *continuation*, that is a closure containing whatever-code-follow-shift-until-the-end-of-the-reset-block, to its body. If you run that closure, you actually run that code after the `shift`. If you store that closure somewhere, you gain the ability to decide when to run that code at a later point. This is the general idea of continuations: interrupt, then resume a program. Here it's all done with functions and closures behind the scene.

To see how our example really works, let's look a the control flow. First, how would you go about implementing the non-blocking `read()` function? Obviously it would have to work hand in hand with an asynchronous framework of some sort. Let's say it's roughly equivalent to something like this:

```scala
var myCallback: Byte ⇒ Unit = null
def read(callback: Byte ⇒ Unit): Unit = myCallback = callback
```

The key here is that `read()` is passed a callback function. `read()` just stores the callback in a variable and then returns immediately. There is just no waiting. This simulates what a real async framework would do.

In our example, `shift` calls `read()` with the continuation as a callback, and as we have seen  `read()` returns right away. But then what does `shift` do? Does it just hang around? No: `shift` returns right away as well, and then control continues right after the `reset` block, and control should then return to the async framework. So it's as if the user program had paused just in the middle of calling `shift(read)`.

Now say that 5 minutes later, a byte (say `42`) is available from the network. The async framework figures this out, notices `myCallback` is registered, and so calls it with the value `42`. The result of calling the callback is to run the continuation, that is the code that follows the first `shift` runs, with `byte1` set to the value `42`. Did you see what happened there? It's as if the user program had resumed. And in effect it has.

What happens next? There is a another `shift`, so the scenario repeats: a new continuation is stored into `myCallback`. This time, it contains the code after the second `shift`. `read()` returns, `shift` returns, and control returns to the async framework, this time via the call to the initial callback. When the framework receives another byte from the network, the user program runs up to the end of the `reset` block and has in effect terminated. We are happy because:

1. We never blocked our single thread.
1. We wrote the program in a clear, understandable style.
1. We actually did something (read and processed bytes from the network)

Obviously to make this real you want a framework and a function library with a set of useful asynchronous functions besides `read()`. Also, note that you can hide the use of `shift` from the programmer, and expose the read function like:

```scala
def aRead = shift(read)
```

And the program becomes:

```scala
reset {
  val byte1 = aRead
  println("byte1 = " + byte1)
  val byte2 = aRead
  println("byte2 = " + byte2)
}
```

By the way, it also works within while loops. With this specific use of continuations where `shift` never calls the continuation directly, control unwinds the stack back to the top, and there is no stack explosion. This is good news:

```scala
reset {
  var value = -1
  while (value != 42) {
    value = aRead
    println(value)
  }
  println("done")
}

```
So I would say that this at least appears to be a very useful (if not mainstream at this point) use of continuations in Scala. They will become even more useful when used as part of a [reactive programming DSL](http://infoscience.epfl.ch/record/148043/files/DeprecatingObserversTR2010.pdf).
