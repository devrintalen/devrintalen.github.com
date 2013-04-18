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
handle everything sequentially, as we usually do:

{% highlight c++ %}
    for (;;) {
        char c = read_keypad_input();
        write_lcd_display(c);
    }
{% endhighlight %}

But a lower power way would be to only update the LCD when a key is
pressed, and even then to interrupt on keypresses rather than
polling. The operative word here is *interrupt*: we want the keypad to
cause the processor to do something, rather than the processor
constantly checking the keypad. This is where we asynchronous.

AVR Interrupts
--------------

Without going too far into the [general mechanics of interrupts][1],
we can explore how they work on an AVR processor. The AVR architecture
defines a global interrupt enable, and if we're going to use
interrupts we have to run this:

[1]: yo

{% highlight c++ %}
   #include <avr/interrupt.h>

   int main() {
       sei(); // Enables interrupts
   }
{% endhighlight %}

At the very basic level, an interrupt is some event that causes the
processor to jump from whatever code it's executing over to a
predefined address where it executes an *interrupt handler*. There are
a lot of different kinds of events, but two that we usually care about
are:

- Timer interrupts
- Pin change interrupts

To create an interrupt handler we write a function wrapped in a
special macro[^1]. Before we go over how to set up timer or pin change
interrupts, here is the additional code we'd need to define the
interrupt handlers for them:

[^1]: This syntax is for [avr-gcc][2] and other compilers may handle it differently.

[2]: http://gcc.gnu.org/wiki/avr-gcc

{% highlight c++ %}
    ISR(TIM0_COMPA_vect)
    {
	// Timer 0
	// This code may update the LCD
    }

    ISR(INT0_vect)
    {
	// External interrupt 0
	// This could could read the keypad
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

Writing good asynchronous code means understanding what state[^2] can
get modified and when. In a sequential program it's clear when state
gets modified, but when there are interrupts occuring underneath
sequential code then weird things can happen when they share any
state. For example, consider this simple program:

[^2]: And by state I mean variables, which remember are stored in memory. Any program is just instructions and memory.

{% highlight c++ %}
   #include <avr/interrupt.h>
   
   int i; // global scope

   int main() {
       for(i = 0; i < 100; i++) {
           // does something...
       }
   }

   ISR(TIM0_COMPA_vect) {
       for(i = 0; i < 10; i++) {
           // does something else
       }
   }
{% endhighlight %}

It's possible for timer 0 interrupts to cause the `for(...)` loop in
`main()` to execute more than 100 times. That could happen if:

1. The `for` loop in `main()` begins, initializing `i` to 0.
2. After 20 iterations, we get a timer 0 interrupt. Here `i=20`.
3. The `for` loop in the timer interrupt leaves `i` at 10.
4. The processor now returns to the main `for` loop, now with `i=10`.
