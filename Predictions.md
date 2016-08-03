# Predictions

Predictions are fun! Here I am keeping track of my predictions and a few of my friends', trying to be reasonably specific about what exactly is being predicted and when it will happen. I take this as a game and won't feel too disappointed if my own predictions don't happen.

Content:

- [Considerations](#considerations)
    - [General considerations on self-driving vehicles](#2015-07-04-general-considerations-on-self-driving-vehicles)
- [My pending predictions](#my-pending-predictions)
- [My completed predictions](#my-completed-predictions)
- [Other people's predictions](#other-peoples-predictions)

## Considerations

### 2015-07-04: General considerations on self-driving vehicles

Self-driving cars are a great area for predictions!

The technology of the [Google driverless car](https://en.wikipedia.org/wiki/Google_driverless_car) is pretty impressive already, based on this [June 2015 TED Talk by Chris Urmson](http://www.ted.com/talks/chris_urmson_how_a_driverless_car_sees_the_road). I am pretty sure that self-driving cars will happen, although some legal, social, and technological obstacles remain. The big question is *when* they will be overcome, and what kind of predictions make sense.

Here are some examples of milestones that come to mind:

- Demonstration of end-to-end automated vehicles picking up and dropping off people in realistic scenarios (such as home to work). This must include the ability for the vehicle to park or wait in a safe location, and to drive safely and without unreasonable delays. This must include local driving, and may include highway driving. For demonstration purposes, a human supervisor may be present in the car.
- First US state to allow (under conditions to be met) certain types of fully automated vehicles on at least some public roads. Such vehicles do not require a driver at all, and might not even have a driving wheel (like the Google car). No driver license is needed to enter the car. The car can circulate on its own without any human aboard.
- First lawsuit related to to a self-driving vehicle having allegedly caused harm.
- First company operating a fleet of fully automated taxis (it could be Uber).
- Self-driving vehicles (owned or not) are an option for some people (in the same way electric vehicles such as the Nissan Leaf are as of 2015) as a replacement for a regular car. Such vehicles might be operated by a separate company. They are an option to take people to and from work on a daily basis, as well as perform other trips.
- Self-driving vehicles are mainstream.
- Rate of car ownership in the US is reduced by 50% compared to 2015 levels.
- Number of cars on the road in the US is reduced by 50% compared to 2015 levels.
- Merger/bankruptcy of large car manufacturers due to the reduced market for vehicles.
- Cities start reclaiming and repurposing roads and unused parking lots.
- Most (more than 50%) people don't own a car.
- It is reasonable to most young people (at least in a certain area) to completely avoid getting a driver license and never learn how to drive.
- Some public roads are closed to vehicles with human drivers.

Trucks are also an obvious target for self-driving technology as they drive long distances. Here are a few milestones:

- Demonstration of self-driving 18-wheeler for long highway drives (such as CA highway 5). Daimler has just revealed a [limited concept in May 2015](http://www.huffingtonpost.com/2015/05/07/self-driving-truck_n_7225490.html). See also [Self-Driving Trucks Are Going to Hit Us Like a Human-Driven Truck](https://medium.com/basic-income/self-driving-trucks-are-going-to-hit-us-like-a-human-driven-truck-b8507d9c5961).
- Commercial availability of self-driving 18-wheeler for long highway drives (such as CA highway 5), still requiring a driver in the cab for supervision, entering and existing highways, and local driving.
- First law against self-driving trucks passed in a US state, under the pressure of truckers.
- Repeal of such laws after legal battles.
- Fully automated self-driving 18-wheeler able to do end-to-end trips.

## My pending predictions

### PREDICTION-0016: 2016-01-07: Timeline for summoning a self-driving car around the Bay Area

- From: @ebruchez
- Text: "In 10 years, we will be able to summon an autonomous car with a mobile app to go to random addresses around the San Francisco Bay Area."
- Comments:
    - This implies some companies will provide that service (a la Uber).
    - There is no requirement to be able to go outside of the Bay Area (say Tahoe, etc.).
    - The system must be useful: that is, there have to be reasonable uses for people like us. For example, if there are only a couple of available destination addresses, the system will not be usable. The expectation is that most, but not necessarily all, destinations should be available. For example, taking you from your home to a friend's home, or to a train station, etc.
     - The system must be usable also in that it must take a reasonable amount of time. It cannot be, for example, more than 50% slower than driving on your own in the same circumstances.
- Resolution:
    - WAIT until 2026-01-07

### PREDICTION-0013: 2015-12-03: Swift in the browser

- From: @ebruchez
- Text: "Now that Swift is actually open source, developers, whether at Apple or third-party, will produce a usable and efficient Swift-to-JavaScript compiler similar to Scala.js before the end of 2016. It will be called Swift.js."
- Comments:
    - I will consider this prediction successful even if the compiler is not officially called Swift.js!
    - The intent of this prediction is that Swift.js will reuse much of the open source Swift toolchain, in the same way that [Scala.js](http://www.scala-js.org/) reuses much of the Scala toolchain. (As a counter example, there is at least one project attempting to do this on GitHub, but it implements a subset of Swift and appears very immature so doesn't qualify.)
    - Swift already has the [Swift Intermediate Language](http://llvm.org/devmtg/2015-10/slides/GroffLattner-SILHighLevelIR.pdf) which could be the basis for a JavaScript backend.
    - Swift.js as described here doesn't compile to asm.js/WebAssembly, but maps to JavaScript in a way very similar to what [Scala.js](http://www.scala-js.org/) does.
    - One important difference is that Swift.js will be garbage-collected, while its LLVM version is reference-counted. Developers will have to be made aware of that difference.
- Resolution:
	- WAIT until 2016-12-31

### PREDICTION-0012: 2015-07-04: Swift overtaking Objective-C

- From: @ebruchez
- Text: "In June 2016, two years after Apple's announcement, Swift will have overtaken Objective-C on GitHub based on metrics by [RedMonk](http://redmonk.com/sogrady/category/programming-languages/)."
- Comments:
    - This is a renewal of [PREDICTION-0005](#prediction-0005-2014-06-11-swift-overtaking-objective-c) below set for one year later.
    - I think the momentum of Swift will accelerate this year due to Swift 2, open sourcing, and the fact that a rise in Swift means a decline of Objective-C.
    - Last year's PREDICTION-0005 was too aggressive. I still think the updated prediction might not be realized in one year, but this is my bet!
- Resolution:
	- WAIT until 2016-07-01

### PREDICTION-0010: 2015-06-11: A Node.js for Swift

- From: @ebruchez
- Text: "Now that Apple has announced the open sourcing of Swift, I predict that by the end of 2016 there will at least one popular server-side, async/event-driven platform a la Node.js but based on Swift. It will run on Linux and OS X."
- Updates
    - 2016-03-01: [Swift Express](https://github.com/crossroadlabs/Express), a Play- and Express.js-inspired framework.
- Resolution:
	- WAIT until 2016-12-31

### PREDICTION-0009: 2015-01-21: Marijuana in California

- From: @ebruchez
- Text: "California will legalize marijuana before year 2020."
- Resolution:
    - WAIT until 2019-12-31

### PREDICTION-0003: 2013-09-26: Smart glasses technology

- From: @ebruchez
- Text: "In 10 years, glasses with features identical to 2013 Google Glass (or better) will be indistinguishable from regular prescription eye glasses (except if social or legal requirements impose some identifying aspects)"
- Comments:
    - The main idea is that the technology for this will be there and that products will be on the market with that technology.
- Resolution:
    - WAIT until 2023-09-26

## My completed predictions

### PREDICTION-0008: 2015-01-09: iPhone with 2 GB of RAM

- From: @ebruchez
- Text: "At least one new fall 2015 iPhone model will have 2 GB of RAM or more."
- Resolution:
    - __SUCCESS__
    - Both the iPhone 6S and iPhone 6S Plus have 2 GB of RAM.

### PREDICTION-0006: 2014-09-30: Share of smartphones with large screen sizes

- From: @ebruchez
- Text: "In 1 year, 50% of smartphones sold in the US in the previous quarter will have screen sizes around 5.5 to 6 inches."
- Comments:
    - In other words, I am predicting that the larger form factors will gain a lot of traction quickly.
    - "Smartphone" means the device has a connection to a traditional cellular network (3G, LTE).
- Resolution:
    - __FAIL__
    - According to [this](http://www.nielsen.com/us/en/insights/news/2015/super-size-me-large-screen-mobile-sees-growth-in-the-midst-of-a-small-screen-surge.html), on the Android side, the share of "phablets" is only 13.7% as of June 2015, although it doubled since early 2014.

### PREDICTION-0005: 2014-06-11: Swift overtaking Objective-C

- From: @ebruchez, @avernet
- Text: "In June 2015, one year after Apple's announcement, Swift will have overtaken Objective-C on GitHub based on metrics by either of [RedMonk](http://redmonk.com/sogrady/category/programming-languages/) or [Ohloh](https://www.ohloh.net/languages/compare)."
- Comments:
    - following [@gruber](http://daringfireball.net/linked/2014/06/11/hillegass-objc)
- Updates:
    - 2015-01-16: "Swift just jumped from number 68 to number 22 in […] RedMonk" ([Wired](http://www.wired.com/2015/01/redmonk-swift/), [RedMonk](http://redmonk.com/sogrady/2015/01/14/language-rankings-1-15/)). Ojbective-C is still in position 10 though, so I don't know if the prediction will come true.
- Resolution:
    - __FAIL__
    - [The 2015-06 RedMonk survey](http://redmonk.com/sogrady/2015/07/01/language-rankings-6-15/) places Swift at position 18, and Objective-C still at position 10, and GitHub reflects this. So my prediction has obviously failed. But RedMonk says of Swift:
        > "Even if you assign little importance to the actual ranking, then, there is no debate that Swift is growing faster than anything else we track. The forthcoming release of Swift as open source and availability of builds for Linux, as well, should theoretically provide even more momentum going forward."
    - One interesting aspect of the respective ranking of Objective-C/Swift is that the languages are linked and the rise of Swift will happen at the detriment of Objective-C. So as Swift becomes more popular, Objective-C will become less so.
    - I think the momentum of Swift 2 will be big, causing many iOS programmers to consider a switch to Swift. I am renewing my prediction for next year as PREDICTION-0012.

### PREDICTION-0007: 2014-12-29: Apple open sourcing Swift

- From: @ebruchez
- Text: "During 2015, Apple will either open source or unequivocally announce that it will open source Swift."
- Resolution:
    - __SUCCESS__
    - 2015-06-08: Correct prediction as announced by Apple at WWDC 2015!
    - 2015-12-03: Open-sourcing is effective on [GitHub](https://github.com/apple/).

## Other people's predictions

### PREDICTION-0018: 2014-10-15: Driverless cars by 2023

- From: @elonmusk
- Text: "We’ll be able to achieve true autonomous driving, where you could literally get in the car, go to sleep and wake up at your destination."
- Comments:
    - See [Elon Musk: We’ll Have Driverless Cars By 2023](http://www.huffingtonpost.com/2014/10/15/tesla-driverless-cars_n_5990136.html) and [The High-Stakes Race to Rid the World of Human Drivers](http://www.theatlantic.com/technology/archive/2015/12/driverless-cars-are-this-centurys-space-race/417672/).
    - The technical ability must be there, with solid demos and pilots, but not necessarily the availability to all the public due to regulations. 
    - Says Musk: "it would take regulators another two to three years to approve the autonomous cars for use in public".
    - It doesn't have to be Tesla doing it, it could be another company.
- Resolution:
    - WAIT until 2024-01-01

### PREDICTION-0017: 2016-01-07: Timeline for summoning a self-driving car around the Bay Area

- From: @avernet
- Text: "In 15 years, we will be able to summon an autonomous car with a mobile app to go to random addresses around the San Francisco Bay Area."
- Comments:
    - Same conditions as PREDICTION-0016 but with a timeline of 15 years instead of 10.  
- Resolution:
    - WAIT until 2031-01-07

### PREDICTION-0014: 2016-08-02: 2016 election will not be close 

- From: @avernet
- Text: "The 2016 US presidential election will not be close: Trump will lose by a significant margin."
- Resolution:
    - WAIT until 2016-11-09

### PREDICTION-0015: 2016-01-06: Timeline for summoning a self-driving car for a trip out of town on a rainy day

- From: @avernet
- Text: "In 10 years, we still won't be able to summon an autonomous car, on a rainy day, to go from San Carlos to Healdsburg, with the trip taking a reasonable amount of time."
- Comments:
    - The "rainy day" is designed to make it clear that with a private car, the technical ability to do the trip would not be dependent on whether it is raining or not.
    - The "reasonable amount of time" qualifier is there to insist on the usability of the system: if the car takes the whole day to go there, it won't be usable and therefore probably not used. As of 2016-08-03, that trip takes, by car just over 2 hours. We could say that if the trip takes, say, more than 50% of the time for the same trip by private car, the solution would be unacceptable. We can debate the exact percentage (50%, 100%, etc.).
- Resolution:
    - WAIT until 2026-01-05

### PREDICTION-0011: 2015-07-02: Timeline of real-life test of Google self-driving cars

- From: @avernet
- Text: "Google will not manage to start a real-life test of their self-driving car in the US before the end of Q1 2016. By real-life, we mean that such a test would not rely on Google employees. The test doesn't have to be in California, but it must be in the US and on public roads."
- Comments:
    - This is in response to a rumor that Google might start such a test in 2015.
- Resolution:
    - __SUCCESS__
    - 2016-08-03: As far as we can tell, Google hasn't done such a real-life test by 2016-03-31.

### PREDICTION-0004: 2013-12-03: Amazon Prime Air timeline

- From: @avernet
- Text: "Amazon Prime Air won't be usable in 5 years at Thomas's place (for technological or cost reasons, including but not limited to FAA requirements or high initial cost)."
- Resolution:
    - WAIT until 2018-12-03

### PREDICTION-0002: 2013-09-16: iPhone CPU performance

- From: @avernet
- Text: "No 40x CPU perf improvement between iPhone 5S to 6 years after its release."
- Comments:
    - 2007-06-29: Original iPhone release.
    - 2013-09-20: iPhone 5S release (just over 6 years later).
    - On 2013-09-10, Apple [announced the iPhone 5S](http://www.macrumors.com/2013/09/10/live-coverage-of-apples-2013-iphone-media-event/) and and claims "CPU is 40x faster than original iPhone, with half coming with jump between A6 and A7".
    - [Geekbench](http://browser.primatelabs.com/ios-benchmarks): 11.8x (1619 for the iPhone 5, 137 for the original iPhone)
    - NOTE: This is *not* about overall performance, although that is interesting, but about *CPU* performance.
    - It's almost certain that there will be devices called "iPhone" in 6 years.
    - The question is: how do you measure performance improvements? On a per-core basis seems unfair. So some kind of reasonable benchmark, possibly using all the cores, should show a 40x performance improvement.
- Resolution:
    - WAIT until 2019-09-16

### PREDICTION-0001: 2013-07-20: Death of Apple

- From: @otazi:
- Text: "Apple will be dead in 10 years."
- Comments:
    - What will "dead" mean? I will interpret it not as "out of business" as that seems impossible for a company of this size, but as "largely irrelevant". This means significantly less relevant than in 2013. How does this translate in terms of market share or number of devices or services? Maybe we can set the bar at 5% market share, a suggested by @avernet.
- Resolution:
    - WAIT until 2023-07-20
