![Tessel 2 and its relay module](https://raw.githubusercontent.com/ebruchez/public/master/Blog%20posts/images/2016-04-23-tessel2-1024.jpg)

### What is Tessel 2?

[Tessel 2](https://tessel.io/) is a Wi-Fi-enabled development board programmable in JavaScript with [Node.js](https://nodejs.org/en/). The first units shipped this month. There is a lot that I like about Tessel 2:

- __It is high-level.__ JavaScript and Node.js greatly lower the barrier of entry since there are so many developers familiar with these technologies.[^jsembedded]
- __It works out of the box.__ Take the device, plug it, push some JavaScript, and it does its magic! There is no need to install your own Linux distribution or additional software.
- __It is autonomous.__ Thanks to its powerful hardware[^specs], built-in Wi-Fi and Node.js, it runs independently from other computers or even cables (except for power).
- __It is open.__ The Tessel 2 software and hardware are open source. In fact, Tessel is not even a company but "just a collection of people who find it worthwhile to spend [their] time building towards the Tessel Project mission."[^open]

It short Tessel 2 seems perfect for playing with [IoT](https://en.wikipedia.org/wiki/Internet_of_Things)!

### From JavaScript to Scala

As soon as I got my Tessel 2, I followed the [tutorial](http://tessel.github.io/t2-start/) to get a basic hang of it, and that went quite smoothly.

But my plan all along had been to use [Scala](http://www.scala-lang.org/) on Tessel 2. You might know Scala primarily as a server-side language running on the Java VM. But Scala also compiles to JavaScript thanks to [Scala.js](https://www.scala-js.org/), and it does it spectacularly well.

So I set to do something simple like [toggling relays](http://tessel.github.io/t2-start/modules/relay.html), but in Scala instead of JavaScript. Here are the rough steps:

- setup a [Scala.js sbt project](https://www.scala-js.org/tutorial/basic/)
- write an app calling the Tessel LED and relay module APIs
- run `sbt fullOptJs` to compile the Scala code to optimized JavaScript
- run `t2 run target/scala-2.11/tessel-scala-opt.js` to deploy the resulting JavaScript to Tessel

After I figured out a couple of tweaks ([`scalaJSOutputWrapper`](https://github.com/ebruchez/tessel-scala/blob/master/build.sbt) and [`.tesselinclude`](https://github.com/ebruchez/tessel-scala/blob/master/.tesselinclude)), it just worked! Here is the code:

```scala
object Demo extends js.JSApp {

  def main(): Unit = {

    println(s"starting `main()` with node version ${g.process.version}")

    val tessel    = g.require("tessel")
    val relayMono = g.require("relay-mono")

    val relay = relayMono.use(tessel.port.A)

    relay.on("ready", () ⇒ {
      println("Relay ready!")

      js.timers.setInterval(2.seconds) {
        relay.toggle(1)
      }

      js.timers.setInterval(1.seconds) {
        relay.toggle(2)
      }
    })

    relay.on("latch", (channel: Int, value: Boolean) ⇒ {
        println(s"Latch on relay channel $channel switched to $value")

        if (value)
          tessel.led(channel + 1).on()
        else
          tessel.led(channel + 1).off()
    })
  }
}
```

Notice how I can call Tessel APIs from Scala without further ado. When used this way, Scala.js works like JavaScript: it's all dynamic.[^dynamic]

### Types and facades

But a major reason to use Scala instead of JavaScript is to get help from *types*. So after that initial attempt I wrote some minimal [facades](https://www.scala-js.org/doc/interoperability/facade-types.html)[^typescript] for the Tessel and Node APIs I needed. Facades expose typed APIs to Scala, which allows the compiler to check that you are calling the APIs properly, and also gives your text editor a chance to provide autocompletion and suggestions. You can see this in action in [IntelliJ](https://www.jetbrains.com/idea/):

![Code completion in IntelliJ](https://raw.githubusercontent.com/ebruchez/public/master/Blog%20posts/images/2016-04-18-suggestion.png)

Here are the minimal facades I have so far:

- [`node.scala`](https://github.com/ebruchez/tessel-scala/blob/master/src/main/scala/org/bruchez/tessel/node.scala)
- [`tessel.scala`](https://github.com/ebruchez/tessel-scala/blob/master/src/main/scala/org/bruchez/tessel/tessel.scala)

Along the way I realized that working on facades is also a great way to learn APIs in depth! This is the resulting code (which you can [find on github](https://github.com/ebruchez/tessel-scala/blob/master/src/main/scala/org/bruchez/tessel/Demo.scala)):

```scala
object Demo extends js.JSApp {

  def main(): Unit = {

    println(s"starting `main()` with node version ${g.process.version}")

    val tessel    = g.require("tessel").asInstanceOf[Tessel]
    val relayMono = g.require("relay-mono").asInstanceOf[RelayMono]

    val relay = relayMono.use(tessel.port.A)

    relay.onReady {
      println("Relay ready!")

      js.timers.setInterval(2.seconds) {
        relay.toggle(1)
      }

      js.timers.setInterval(1.seconds) {
        relay.toggle(2)
      }
    }

    relay.onLatch { (channel, value) ⇒
        println(s"Latch on relay channel $channel switched to $value")

        if (value)
          tessel.led(channel + 1).on()
        else
          tessel.led(channel + 1).off()
    }
  }
}
```

As you can see, it's not very different from the dynamic example, except that I now get help from the editor and compiler.

### Why do this, again?

Now you might argue that in both cases the code looks more or less like JavaScript, so why go through the trouble?

It's true that, superficially, JavaScript and Scala look very similar in these examples. But underneath there is Scala's type system at work, and this is for me the main reason to want to use that language.

This said, there is more, such as:

- __Immutability by default.__ I like this because it helps reduce errors and works great with functional programming idioms.
- __Collections.__ Scala has a very complete collection library, including immutable collections (but you can also use mutable collections).
- __Functional programming.__ Scala was designed for functional from the get go and has some pretty neat functional programming third-party libraries too.

And I could go on with features like case classes, pattern matching and destructuring, for-comprehensions, and more. But I should also mention a few drawbacks of using Scala instead of JavaScript:

- __Harder language.__ Scala is a super interesting language, but no matter how you look at it, it is a bigger beast than JavaScript.
- __Executable size.__ Scala.js has an amazing optimizer which also strips the resulting JavaScript from pretty much any unused bit of code[^dce]. Still, you will likely have resulting files which are larger than what you would get by writing JavaScript by hand. So expect your app to yield uncompressed JavaScript files in the order of a few hundreds of KB (much smaller when compressed). Tessel doesn't seem to have any issues with that so far, so it might not be a problem at all, but it's worth keeping an eye on this as Tessel doesn't have Gigabytes of RAM.
- __Compilation step.__ There is a compilation and optimization step in addition to publishing the software to Tessel. For my very simple demo, this takes a couple of seconds only. For larger projects, the time will increase. Now this is very manageable thanks to sbt's incremental compilation, and if you consider that pushing a project to Tessel can take several seconds anyway, I would say that right now it's not an issue.

So who would want to program Tessel in Scala? Probably not everybody, but it's a great option to have if you already know the language or are interested in learning it, especially if you are going to write large amounts of code.

### What's next?

I plan to continue playing with Tessel 2 and Scala. The next step is to try to do something fun (and maybe even useful) beyond blinking LEDs and relays!

[^jsembedded]: This trend is definitely in the air. Read for example [Why JavaScript is Good for Embedded Systems](https://www.linkedin.com/pulse/why-javascript-good-embedded-systems-shawn-hymel).

[^open]: From [Code of Conduct/About the Tessel Project/How to Get Your Issue Fixed](https://forums.tessel.io/t/code-of-conduct-about-the-tessel-project-how-to-get-your-issue-fixed/2378). It is all the more impressive that they managed to make and ship such cool hardware and software.

[^dce]: Also known as DCE for Dead Code Elimination.

[^specs]: Tessel 2 is fairly beefy compared to an Arduino board, for example: it features a 580 MHz CPU, built-in 802.11 b/g/n Wi-Fi, and 64 MB of RAM and 32 MB of Flash. You can add more storage via USB.

[^typescript]: Scala.js facades are lot like [TypeScript declaration files](https://www.typescriptlang.org/docs/handbook/writing-declaration-files.html).

[^dynamic]: Under the hood, this is thanks to Scala's [`Dynamic`](http://www.scala-lang.org/files/archive/api/2.11.8/index.html#scala.Dynamic) support.