---
layout: post
title: Defining LaTeX Macros
---

After having used LaTeX as my primary document creation tool for a few
years now, I've amassed some tricks into my veritable *arsenal* of
computer geekery.  I thought I'd spill the beans on one of them today.

Creating Your Own Macros
------------------------

Once upon a time, you had a document for which you needed some
mathematical expression that repeated way too many times. You hated
typing it, and you felt the carpal tunnel settling in[^0]. Well, LaTeX
feels your pain, and wants to help out.

In one such homework last semester, I found myself typing something
like this same sequence over and over again:

    \[ \left( \begin{array}{c} n \ p \end{array} \right) \]

Not that bad? Try typing it out 10 times[^1]. It was tedious, and made
the code entirely unreadable.

How can LaTeX help out? With the ability to **define your own
commands**. We can take that code above, and put the following
definition into the preamble (before the `\begin{document}`) of your
LaTeX document:
    
    \newcommand{
    \npchoose}[2]{
        \left( \begin{array}{c} #1 \ #2 \end{array} \right)
    }

What's going on here? Well, the `\newcommand` command tells LaTeX that
you're going to be defining a new macro. The first argument is the
name that you're going to use for this macro (include the prefix
slash, and it can't be an existing keyword, such as `\begin`). The
second argument, in brackets, is the number of arguments to your new
macro. In the example above I have two arguments: one for the top
variable and one for the bottom one. Lastly, you tell LaTeX what the
code for the macro is. Notice that I use the variables `#1` and `#2`
where I want the macro to put whatever variables are passed to it[^2].

Now you can change that entire math expression above to simply:

    \[ \npchoose{n}{p} \]

How does this break down? Notice that we use the command `\npchoose`
because that's the name that we gave to the macro. Then we simply list
the arguments in order. Note that each argument gets its own curly
brackets. LaTeX will replace the variables `#1` and `#2` with these
arguments that we pass it. It will replace them in order &#8211; `n`
gets passed to `#1`, etc.

Dead simple. You can do this with any expression. It doesn't need to
be in math mode.

A Step Further: Your Own Package
--------------------------------

This is all good, but what if you have a command that you want to use
on two different LaTeX files? In the example above, I wanted to use
that command for *every* homework file I created in that class. Sure,
I could have just copied the command into every file I made, but
that's hack. What if I had an entire set of macros that I wanted to
use? There's an elegant solution, as always. And it involves creating
your own package.

At its most basic level, a LaTeX package is just a file with a bunch
of macros defined in it. Well, we already know how to make a macro
(see above), and hopefully we already know how to make a file. We
should be set.

To make a package called `mymacros`, create a file called
`mymacros.sty`, and keep it in the directory of the LaTeX file that
uses it. In my class example, I would have created a file called
`ece310.sty` that looked like this:

    % New command definition
    
    \newcommand{
    \npchoose}[2]{
        \left( \begin{array}{c} #1 \ #2 \end{array} \right)
    }

and at the top of my homework files I would have had the line:

    \usepackage{ece310}

Done. That's LaTeX macros in a nutshell. Nothing confusing about them,
and they can really help out and make your documents easier all
around.

[^0]: Switch to Dvorak and save your wrists. Bask in your glowing aura
      of l33t.

[^1]: I can already hear the vim/emacs users deriding my lack of
      copy/paste macro skills. Well, the entire point of this post is
      to showcase LaTeX macros.

[^2]: For bonus points check out [this page on macros][1],
      specifically the part about default parameters. Now, that's
      *neat*.

[1]: http://www.math.tamu.edu/~boas/courses/math696/math-macros.html
