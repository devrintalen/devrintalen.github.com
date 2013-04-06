---
layout: post
title: Introduction to Groff
---

Groff is the GNU implementation of AT&T's `troff` and associated
programs (`pic`, `table`, etc.). It's a typesetting language much like
LaTeX.

But who cares! Here's the canonical `helloworld.ms`:

    .TL
    Hello World
    .AU
    Devrin Talen
    .AI
    Awesome, Inc.
    .AB no
    .AE
    .PP
    Lorem ipsum dolor sit amet, consectetur adipisicing elit, sed do eiusmod
    tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim
    veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea
    commodo consequat. Duis aute irure dolor in reprehenderit in voluptate
    velit esse cillum dolore eu fugiat nulla pariatur. Excepteur sint occaecat
    cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id
    est laborum.

Compile this into something useful with

    % groff -ms -P-pletter helloworld.ms | ps2pdf - helloworld.pdf

Groff's `info` page is chock full of good documentation and should be
your first resource for learning how to use it. But here are the rough
strokes:

- Lines that begin with `.` characters specify macros. For example, the `.TL`
  macro sets up everything that follows as title text. The `.AU` macro
  changes that to a different font for listing author names.
- Unlike LaTeX, which uses `begin` and `end` statements to enclose sections,
  groff uses macros to switch up the formatting until the next macro is
  specified.
- Everything has to be processed in one pass of the source document. This
  means that tables, figures, etc. will all be positioned where you specify
  them in the source, unlike LaTeX which will do its best to find a good
  spot. (Also --- groff won't generate a bunch of temporary files and clutter
  stuff up)

It's a venerable and time-tested typesetting language should be a part
of anyone's typesetting arsenal.
