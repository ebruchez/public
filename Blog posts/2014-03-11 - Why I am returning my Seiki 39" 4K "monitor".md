After reading some recent blog posts (such as [4K is for programmers][4k]) about using the [Seiki SE39UY04 39-Inch 4K TV][seiki] as a computer monitor, I figured it might be an interesting experiment and I ordered last week a slightly used one. The price including taxes and free shipping was just under $500.

First, I do want more pixels [^pixels] but it's a bit unclear to me whether a 39" monitor is workable or even desirable on a desk. I currently use an old 30" Dell monitor, so I am used to big screens, but 39" is gigantic! [^ideal]

Second, that particular monitor is sold as a TV and not designed to be used as computer monitor in the first place. This means that it has important limitations, such as the absence of a direct input mode, and an abysmal 30 Hz input refresh rate at the native resolution. (This doesn't mean that you see flickering, as on old CRT monitors. It just means that you only get at most 30 different frames every second.)

So I got the monitor on Friday and immediately set it up. It was easy:

- connect the HDMI cable to the MacBook Pro's built-in HDMI output
- turn on the TV and wait for it to "boot"
- turn down "sharpness" to 0 (important, otherwise you see artifacts)
- play a bit with brightness (possibly too bright even at the minimum) and contrast

With the image adjustments, picture quality is pretty good. I don't think it's possible to get this monitor to have colors that are acceptable for photography or video, but for programming it seems decent.

However the issue that was an immediate turn off is the mouse lag. In practice, this translates to mouse or trackpad input which is *very* imprecise. Keyboard input is also visibly lagging.

It just take a while, it seems, for the output of the computer to actually make it to the screen. I suspect that part of the issue might not be so much the 30 Hz refresh rate (although you do notice that when moving stuff around and scrolling) as image processing done by the TV.

It is a known issue [^latency] that monitors and TVs do crazy (and unneeded) image processing which increases the time between receiving data from the computer and actually showing it on the screen. Some TVs have a "direct" or "game" mode which alleviates the problem, but the Seiki doesn't.

I tried a couple of things to improve the lag:

- I [upgraded the firmware][firmware], and even tried the 50" firmware.
- I tried [SmoothMouse][smooth], which seemed to improve things a tiny bit.

In the end I don't think I can get used to the lag, so the Seiki is going back. I will revisit getting a new monitor once the dust around 4K settles a little bit. There is [upcoming 60 Hz output support in OS X for the MacBook Pro][osx], and there should be many new 4K monitors of various sizes coming out this year. I am not sure yet if I would choose something as big as 39", but we'll see!

[^pixels]: The built-in 15" Retina MacBook Pro display is 2880x1800, my external 30" Dell is 2560x1600 and the Seiki is 3840x2160. Having more pixels is good, whether to get closer to a "retina" resolution, or just to get more content on screen (like a debugger next to the app being debugged).

[^ideal]: Ideally, I would like the whole virtual space around me to be something like a monitor, where I could put virtual items, and use the brain's spatial abilities to make the best use of it. But hardware and software are not there yet.

[^latency]: See [John Siracusa on various podcasts][siracusa] and [John Carmack's post on display latency][carmack].



[4k]: http://tiamat.tsotech.com/4k-is-for-programmers
[seiki]: http://www.amazon.com/gp/product/B00DOPGO2G/
[siracusa]: http://atp.fm/episodes/43-brilliance-enhancer
[carmack]: http://www.altdevblogaday.com/2013/02/22/latency-mitigation-strategies/
[firmware]: http://www.seiki.com/support/downloads.php
[smooth]: http://smoothmouse.com/
[firefox]: http://blog.bruchez.name/2014/01/firefox-vs-chrome.html
[chromium]: http://code.google.com/p/chromium/issues/detail?id=319580
[osx]: http://arstechnica.com/apple/2014/03/first-os-x-10-9-3-beta-comes-with-improved-4k-display-support/