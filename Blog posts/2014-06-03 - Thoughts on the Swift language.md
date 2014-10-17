## What it is

I am not a language designer but I love programming languages, so I can't resist putting down a few rough thought on [Swift](https://developer.apple.com/swift/), the new programming language announced on Monday by Apple. It is designed to make Objective-C, the main language used to build apps on iOS and OS X, a thing of the past. I think it's fair to say that this was, for developers, the highlight of Monday's [WWDC keynote](http://www.apple.com/apple-events/june-2014/).

Objective-C is a dinosaur language, [invented in the early 1980s](http://en.wikipedia.org/wiki/Objective-C#History). If you know any relatively more modern higher-level language (pick one, including [C#](http://en.wikipedia.org/wiki/C_Sharp_(programming_language)), [Scala](http://www.scala-lang.org/), even [Hack](http://hacklang.org/)), it is clear that it has too much historical baggage and not enough of the features programmers expect.

John Siracusa captured the general idea in his 2005 [Avoiding Copland 2010](http://arstechnica.com/staff/2005/09/1372/) article and its revision, [Copland 2010 revisited: Apple’s language and API future](http://arstechnica.com/apple/2010/06/copland-2010-revisited/), and has kept building a really good case since, in various podcasts, that Apple had to get their act together. Something, *anything*, had to be done. [^mytweet]

There was a possibility that Apple would keep patching Objective-C, moving toward a superset of a safe subset of it. But I don't think that anybody not working at Apple saw Swift coming that, well, swiftly. [^secret]

## Why this is good for programmers

Reactions to Swift so far seem mostly positive. (I don't tend to take the negative reactions I have seen seriously as they are not argumented.) As Jeff Atwood [tweeted](https://twitter.com/codinghorror/status/473567506662035456): "TIL nobody actually liked Objective-C.". I share the positive feeling for three reasons:

First, I believe that programming languages matter:

- they can make developers more or less productive,
- they can encourage or instead discourage entire classes of errors,
- they can help or hinder reuse of code,
- they can make developers more or less happy.

With brute force and billions of dollars, you can overcome many programming languages deficiencies. But it remains a waste of valuable resources to write code in an inferior language. Apple has now shown that it understands that and has acted on it, and they should be commended for it.

Second, concepts which many Objective-C developers might not have been familiar with, like closures, immutable variables, functional programming, generics, pattern matching, and probably more, will now be absorbed and understood. This will lead to better, more maintainable programs. This will also make these developers interested in other languages, like Scala, which push some of these concepts further. The bar will be generally raised.

Finally, arguments over the heavy, ugly syntax of Objective-C, and its lack of modern features can be put to rest: Apple has decided the future path for iOS and OS X developers. That ship has sailed.

## Where it fits

What kind of language is Swift? I noticed on Twitter that many had a bit of trouble positioning the language. Did Apple reinvent JavaScript? Or Go? Is Swift functional first? Is it even like Scala? What about C#? Or Clojure or XQuery?

I haven't seen anything in Swift that is not in other programming languages. In fact, Swift features can be found in dozens of other languages (in Lattner's [own words](http://www.nondot.org/sabre/), "drawing ideas from Objective-C, Rust, Haskell, Ruby, Python, C#, CLU, and far too many others to list"), and that's why many have found similarities with their language of choice. So Swift is not "innovative". Instead it is a reasonable mix and match of features which make sense for the Apple ecosystem.

Here are a few essential aspects of Swift which are not language features but which put it in context. These all appear to be essential to Apple:

1. __Owned by Apple:__ Swift is fully owned by Apple. It does not depend on Oracle (Java/JVM), Microsoft (.NET), or Google.

1. __Objective-C integration:__ Swift is designed to integrate really well with Objective-C. In fact, this is likely the second most important reason Apple felt they had to create their own language (in addition to ownership). There are precedents: Groovy, Scala, Clojure, Kotlin, Ceylon and others are designed to interoperate well with Java; CoffeeScript with JavaScript; Hack with PHP; Microsoft's CLR was designed from the get go as a multi-language VM. This is important for initial adoption so that existing code can be reused and the new language progressively introduced. It would have been possible, but much harder, for Apple to pick an existing language.

1. __Static typing:__ Swift is a statically-typed language. There is type inference, which means you don't have to actually write down the types everywhere, in particular within functions. But types are there nonetheless. So it looks more like dynamic languages, but is not one. [^types]

1. __A dynamic feel:__ This is part of the "modern" aspect of Swift: a move toward concision which appeals to programmers used to dynamic languages, but with the presence of static typing under the hood. This combination of terseness and static typing is something Swift shares with Scala.

    Swift has a REPL and Playgrounds (the interactive demo by Chris Lattner looked impressive), which includes what some other environments call ["worksheets"](http://blog.jetbrains.com/scala/2014/02/17/scala-worksheet-instant-evaluation/) and a bit more. Clearly that's the direction development tools are taking. All of this is becoming mainstream, which again raises the bar.

1. __Native compilation:__ Swift is compiled down to native code, like C, C++, Objective-C, Go, and Rust. There is no interpreter or VM, as in Java, JavaScript, C#, Ruby, PHP, or all dynamic languages, besides the small Objective-C runtime. Also, it doesn't have a real garbage collector: it uses automatic reference counting (ARC).

    Swift is a bit odd in that native compilation and lack of full garbage collection make it closer to systems language, yet it is clearly designed to build applications. I wish the balance had moved more toward the higher level rather than the lower level, but it's an interesting middle ground.

## What's disappointing

Here are a few aspects of Swift which, at first glance, disappoint me a bit. Keeping in mind that this is a first version of Swift which has room to grow:

1. __Openness:__ So far Apple has not announced that the Swift compiler would be open source, like the Objective-C compiler. This is a big question mark. It would be the right thing for them to do to open the compiler, and I am hopeful that they will.

1. __Garbage collection:__ It's likely that Apple considered that ARC was good enough in most situations, and it makes interoperability with Objective-C (compatibility in terms of memory management) much easier to handle. Still, this would give me trouble. Lack of proper garbage collection means more memory bugs to hunt down.

1. __Concurrency support:__ Swift doesn't have async/await, like C#, Scala, and soon JavaScript, or futures and promises. Async support is important in client apps as much as in server apps.

1. __Type system:__ The type system appears very simple. This might be seen as good or bad. The reference book doesn't even mention the word "variance". (I suppose Swift picks a default, but doesn't allow programmers to control that.)

1. __Persistent data structures__: There doesn't seem to be [persistent data structures](http://en.wikipedia.org/wiki/Persistent_data_structure) (which are truly immutable yet can be updated efficiently thanks to structural sharing), as in Clojure and Scala. These are incredible tools which many programmers have now found to be essential. Immutability, in general, gives you much increased confidence about the correctness of your code. I would miss them in Swift.

1. __Well, innovation__: Dart, Go, Hack, and Swift show that it is very hard for big companies to come up with something really unique in their programming languages. Academia remains the place where new ideas are born and grow. Still, it would have been nice if there was one or two new things in Swift that would make it special, like for example [Scala's implicits](http://programmers.stackexchange.com/questions/150953/historical-origins-of-scala-implicits) which have turned out to have far-reaching consequences (several of which I really like).

## Browser and server

I am curious to see if Swift will see adoption on the server, for services. It might make sense for Apple to use Swift internally for their services, although having a language is not enough: you need  proper infrastructure for concurrent and distributed computing. Swift is not there yet. But it could be in the future. This is a bit less important to Apple than the client at this time.

What about the browser? Could one conceivably create a Swift-to-JavaScript compiler? I don't see why not. JVM languages, from Java to Clojure to Scala, now compile to JavaScript. Swift currently uses ARC, but in a browser environment it could probably work with the JavaScript VM's garbage collector.

So there might be room, in years to come, for Swift to conquer more environments.

## Google!

Where does Google stand with regards to this? It's curious, but I think now that it's Google which might have a programming language problem! Android uses Java but, as famous programming languages guy [Erik Meijer tweeted](https://twitter.com/headinthebox/status/473552760520994816), "Swift makes Java look tired." (To be fair, most languages make Java look tired.)

Google also has Dart, which so far hasn't been positioned as a language to develop Android or server apps. But maybe that will come. Go is liked by some for certain types of server applications, but is even more of a "systems language" than Swift, and  again Google hasn't committed to bringing it as a language to write Android apps.

Will Google come up with yet another programming language, targeted at Android? The future will tell. If it was me, which of course it isn't, Scala or a [successor](https://github.com/lampepfl/dotty) would be my choice as a great, forward-looking language for Android. And Google could point their Android developers to Scala and say "Look, it looks very much like Swift which you already know!" ;)


*Did I miss anything? Let me know in the comments or on [Twitter](https://twitter.com/ebruchez).*

[^secret]: Good job by Apple, by the way, to have managed to keep it under covers so well [since July 2010](http://www.nondot.org/sabre/)!

[^types]: There is a difference with with languages that have *optional types*, like Dart and Hack. Dynamic, optionally typed, and statically typed languages can, from a syntax perspective, look very similar. But under the hood some pretty different things take place.

[^mytweet]: Back in 2009 I even [tweeted](https://twitter.com/ebruchez/statuses/3692077704?tw_i=3692077704&tw_e=details&tw_p=archive):

    > MS has Anders Hejlsberg (C#). The JVM world has Martin Odersky (Scala). Apple should work with Odersky on the next language for OS X.

    Obviously it wasn't Odersky, but Chris Lattner, who got to be the mastermind of Swift.

