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

