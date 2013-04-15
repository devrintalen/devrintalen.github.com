---
layout: post
title: Writing Bug-free Asynchronous AVR Code
---

Writing code that doesn't have any bugs is a pretty impossible goal,
especially when we're talking about asynchronous code to boot. But, by
sticking to a set of design methods we can mostly avoid some of the
common pitfalls that trip up newcomers to parallel code.

Let me back up. What do I mean by parallel code, and why would I want
to run it on a single-core, 8b microcontroller? Let's suppose that
your project included both user input and some sort of output. Maybe
we have a numeric keypad and an LCD. In that case, we could simply
handle everything sequentially as we normally do:

{% highlight c %}
    for (;;) {
        char c = read_keypad_input();
        write_lcd_display(c);
    }
{% endhighlight %}

But a lower power way would be to only update the LCD when a key is
pressed, and even then to interrupt on keypresses rather than
polling. The operative word here is *interrupt*: we want the keypad to
cause the processor to do something, rather than the processor
constantly checking the keypad. The power savings are obvious.

Processor Interrupts
--------------------

Without going too far into the [general mechanics of interrupts][1],
we can explore how they work on an AVR processor. At the very basic
level, an interrupt is some event that causes the processor to jump
from whatever code it's executing over to a predefined address where
it executes an *interrupt handler*. There are a lot of different kinds
of events, but two that we care about are:

- Timer interrupts
- Pin change interrupts

Once the interrupt handler is completed, the processor returns to
where it left off, and, aside from any state that was modified, the
code that was executing has no way of knowing that something just
happened.

GCC uses macros to create interrupt handlers. Our example application, which will need to 

{% highlight c++ %}
    #include <avr/interrupt.h>

    ISR(ADC_vect)
    {
      // user code here
    }
{% endhighlight %}

These look like normal functions, but as we went over above they will
get called based on hardware events, and in general shouldn't be
called from normal code.

Specifics: Timer Interrupts
---------------------------

How to set these up on an AVR.

Specifics: External Interrupts
------------------------------

How to set these up on an AVR.

Code Design
-----------

How to write code that can handle multiple interrupts occuring.