---
layout: post
title: Race Conditions
tags:
- ADB
- debugging
- science
status: publish
type: post
published: true
meta:
  _edit_last: '19855582'
  jabber_published: '1329061226'
  tagazine-media: a:7:{s:7:"primary";s:0:"";s:6:"images";a:0:{}s:6:"videos";a:0:{}s:11:"image_count";s:1:"0";s:6:"author";s:8:"19855582";s:7:"blog_id";s:8:"19340635";s:9:"mod_stamp";s:19:"2012-02-12
    15:40:20";}
---

At school we didn't have any classes on debugging. We weren't taught
how to reproduce a bug under a controlled environment, or how to
isolate variables, or create regression suites to prevent bugs in the
first place. Most of us scraped by with brute force and a lot of plain
old good luck. It's a shame, because debugging is a skill that can be
taught and should be.

My [ADBUSB code][1] had a bug recently. The project was at the stage
where I could translate ADB to USB if typed slowly, but if I sped up
the keystrokes on the keyboard the AVR would hang and the USB keyboard
would get stuck on the last key it tried to send, requiring a forced
reset. It's something that felt a lot like a classic race condition
bug, and I started debugging.

Step 1: Reproduce it
--------------------

You can't debug without having something to work on. Finding out how
to reproduce the issue (or reduce the time-to-failure) tells you a lot
about where the bug may be. Does it only happen when a sequence of
events happens? Does it only happen when multiple events happen at
once? Having a readily reproducible bug also means that you can start
to eliminate variables, but we'll talk about that next.

In this case the bug happened when I "typed quickly," but that's not
very specific. What input would cause this hang? I began to
experiment.

Mashing keys was a good way to hit this. I kept three fingers on "asd"
and after about a minute or two I would get it hung. How else could I
get this sped up? Since I suspected a race condition, I thought of
increasing the frequency of the USB interrupt from once every 100ms to
once every 10ms. Bingo! I could now hang the processor after only a
few button mashes. I now had a readily reproducing problem, so it was
time to move on.

<object width="420" height="315"><param name="movie" value="http://www.youtube.com/v/2kimfYvIw24?hl=en_US&amp;version=3"></param><param name="allowFullScreen" value="true"></param><param name="allowscriptaccess" value="always"></param><embed src="http://www.youtube.com/v/2kimfYvIw24?hl=en_US&amp;version=3" type="application/x-shockwave-flash" width="420" height="315" allowscriptaccess="always" allowfullscreen="true"></embed></object>

Step 2: Narrow it down
----------------------
The benefit of having a reproducible failure is being able to change conditions and observe if the time to failure changes. Narrowing down a bug can mean a few different things.

1. Can I remove parts of code and get the same failure?
2. Can I output some breadcrumbs in different parts of code for debug?
3. Can I stop and observe the processor just before the failure?

This was the main loop of ADBUSB:

{% highlight c++ %}
    while(1) {
        /* ADB phase. */
        adb_status = adb_poll(adb_buff, &adb_len);
        if (adb_len &gt; 0) {
            kb_register(adb_buff[0]);
        }
        /* USB phase. */
        usbPoll();
        if (usbInterruptIsReady()) {
            keybReportBuffer.meta = kb_usbhid_modifiers();
            kb_usbhid_keys(keybReportBuffer.b);
            usbSetInterrupt((void *)&keybReportBuffer, \
                            sizeof(keybReportBuffer));
            keybReportBuffer.b[0] = 0;
        }
    }
{% endhighlight %}

There is an ADB phase and a USB phase. During the ADB phase the processor is polling the keyboard for any new data. The USB phase consists of seeing if an interrupt has been received recently and sending a response if necessary. Based on how the failure happened quicker when reducing the time between USB interrupts, I suspected that a USB interrupt was getting delivered during the ADB poll phase. The ADB receive code was very timing sensitive, and missing a transition on the line could have caused it to get stuck.

I instrumented the ADB code to light up different LEDs for each function that gets called (this is one of the benefits of using an STK500). My goal was to find out where in the code the hang was occurring. Here's how I mapped them:

- LED 0: `adb_poll()`
- LED 1: `adb_command()`
- LED 2: `adb_rx()`
- LED 3: spin loop in `adb_rx()` #1
- LED 4: spin loop in `adb_rx()` #2
- LED 5: spin loop in `adb_rx()` #3

And here's what happened:

<object width="420" height="315"><param name="movie" value="http://www.youtube.com/v/1zq8pkxflAQ?hl=en_US&amp;version=3"></param><param name="allowFullScreen" value="true"></param><param name="allowscriptaccess" value="always"></param><embed src="http://www.youtube.com/v/1zq8pkxflAQ?hl=en_US&amp;version=3" type="application/x-shockwave-flash" width="420" height="315" allowscriptaccess="always" allowfullscreen="true"></embed></object>

Why? When I turn on the board LEDs 0-2 light up. Really what's happening is that `adb_poll()` is called continuously and calling `adb_command()` and `adb_rx()` once per iteration. I plug the USB cable in and then start mashing keys. The LEDs freeze, indicating that the code got stuck. Where? LEDs 0 and 2 are lit, meaning that we're in adb_rx(). LED 3 is also lit, meaning that we got stuck in this bit of code:

{% highlight c++ %}
    PORTA &= ~(_BV(3));
    while(bit_is_clear(ADB_PIN, 2)) {};
    while(bit_is_set(ADB_PIN, 2)) {};
    PORTA |= _BV(3);
{% endhighlight %}

Problem found! This code is meant to capture the first bit of a data packet that's received over ADB. The assumption here is that the loop is fast enough to catch the line pulled low then high. A USB interrupt arriving sometime here, however, is enough to break the assumption - the interrupt may take a long time to service, meaning that the device has already finished sending data by the time it returns. The device will leave the line high, meaning that we poll infinitely waiting for the line to get pulled low, which it never will.

Step 3: Profit
--------------
This is classic debug methodology. If you're good at it, you probably go through these steps without even thinking about them. If your bug is easy enough to find, you probably don't have to worry about these either. But for tricky problems, following these steps will save you a lot of frustration. Without an easy way to reproduce the problem you'll never really know if you solved it or not. Without reducing your variables, you may be searching forever trying different solutions.

This bug ended up not being a [race condition][2], at least in the true sense of the phrase. It's actually more of a [deadlock][3] caused by new code violating the timing assumptions of older code. Fixing this is going to require me to either move the USB functionality to a separate processor, or rework the ADB code to be event-driven and use timers and interrupts to change state (rather than busy-delay loops).

[1]: https://github.com/aneviltrend/adbusb
[2]: https://en.wikipedia.org/wiki/Race_condition
[3]: https://en.wikipedia.org/wiki/Deadlock
