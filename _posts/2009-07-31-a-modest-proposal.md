---
layout: post
title: And I Thought HTML Was Supposed to Be a Real Markup Language
---

Every time I compile some C code I write I get --- and I counted to
make sure --- approximately 43 million errors, all of which are
nagging me about unbalanced parentheses and forgotten semicolons. And
every time I gesticulate violently and sternly address the screen, "If
you're so smart *you* fix it!"

Which is, to say, that the compiler doesn't allow room for error. And
it shouldn't, either. As soon as the compiler tries to be smarter than
you are --- mind you, it *is* --- and starts fixing your mistakes,
it'll inject some really mind-blowingly stupid code that'll leave you
scratching your head and wondering why you didn't just fix it in the
first place.

What I'm left wondering is why browsers accept bad code. They're
parsing a language with syntax and specifications and all the
paperwork to be a legitimate language, yet they willfully drop into
"quirks" mode to handle malformed HTML. And the result is predictable:
ordinary people don't give a hoot if their pages are valid because who
the heck cares? It renders just fine, doesn't it?

Why, if my compiler doesn't, should my browser bend over backward to
render pages that are invalid? No programmer would expect invalid code
to compile, and yet here we are, something like a decade after HTML
was introduced, still treating it as a baby. Can I say something? HTML
is *dead simple* to write.  There are like two rules: every opening
tag needs a closing tag, and some tags --- such as `<a>` --- need
specific attributes. Compare that to C, Python, Java, etc. This isn't
rocket science.

The History of Quirks
---------------------

"Quirks" mode harkens back the dark days of the internet. Fledgling
web developers (read: pubescent teenagers fiddling on Angelfire) were
crafting Web 1.0, replete with Tomb Raider walkthroughs and Real
Ultimate Power (which, as an aside, is still *hilarious*). And these
pioneers had no time for "syntax" or "rules". How could they? Fueled
only by *raw vision* and Tang the internet was born.

Corporations were taking notice. "Why," they said, "we could use this
newfangledness for intranets." And they promptly tasked the most
capable employees: the aging site admins whose jobs were slowly being
replaced by computers. Well, if you can't beat them --- you know.

And there were the visionaries. We all know them. They were the
darlings of Wall Street: [eBay][ebay], etc. These men and women were
going to change the world. On their Herman Miller aerochairs they
gazed into their crystal balls and revealed to the world its fate,
largely a concoction of digital money, internet grocery stores, and
beanie babies. The world had enough by 2000, but the bubble boys had
left their mark(up. Ha!).

The browsers of the time --- Netscape and Internet Explorer --- were
playing second fiddle to the internet. Success was black and white:
either you render the web, or the other guy does. If Joe here sees a
jumbled mess at `joeisawesome.com` with Netscape he'll do what's
rational: curse loudly and open up Internet Explorer.

And in that dark race quirks mode came to light.

What Quirks Mode Means Today
----------------------------

Quirks mode means that people don't care. Validating your site is like
extra credit on a test. Only that kid with the huge glasses is going
to care if he gets it right. Yeah, we'll try it, but we're too cool to
care.

There's already a lot of discussion on the subject and it'd be
redundant to bring it up. Suffice it to say that the way browsers
handle code today is *not good*.

A Modest Proposal
-----------------

Kill quirks mode.

But seriously.

We can't leave out half the web, can we? There's a lot --- *a lot* ---
of content out there that's not valid HTML. And never will be. This is
content that people rely on: popular websites, corporate intranets,
your website works-in-progress. And cutting out the quirks mode of
every browser would mean alienating a lot of people and making life
much harder for others. We can't realistically say that cutting out
quirks mode is a good thing (though it's what I'm personally rooting
for). Not to mention that it'll *never happen*.

But what if Google didn't index invalid HTML?

(Giving higher priority to valid over invalid HTML would have a
similar effect.)

Google has a very large stick with which to brandish: if you're not on
Google's search results, you're not on the internet. Plain and
simple. Companies know this and already invest heavily in search
engine optimization. Turn web standards into an SEO strategy and
you'll have even the most remote corners of the web evangelized.

How could we ever get Google to drink the Kool-Aid and actually pull
this off?  I haven't the slightest clue. It's a pie-in-the-sky
dream. But it could work.

[ebay]: http://ebay.com/