---
layout: post
title: A Cursory Look at Makefiles
---

New to Makefiles? At best they're confusing, and at worst completely
incomprehensible. Here's a dissection of a *simple* Makefile.

structure
---------

The basic format of a Makefile follows this:

    <targets> : <dependencies>
        <commands>

Where multiple entries will make up a larger Makefile. A simple
Makefile to compile a `helloworld.c` program might look something like
this:

    helloworld.o : helloworld.c
        gcc -o helloworld.o helloworld.c

The target is `helloworld.o`, and it depends on having
`helloworld.c`. Running the following command:

    $ make helloworld.o

Will cause `gcc` to be run as specified in the Makefile.

practical makefiles
-------------------

Developing a Makefile that might actually be used in a smallish
software project involves a bit more work. Generally speaking, the
project will consist of several --- in this case --- .c files, which
will need to be linked in interesting ways against each other.

`%`, `$@`, and `$^` are all special variables. Here's how they might be used:

    %.o : %.c
        gcc -o $@ $^

The `%` grabs the matching string from the target and applies it to
the dependency. If the target is `foo.h`, the Makefile will search for
`foo.c`. The next variable, `$@`, grabs whatever file matched the
target. Likewise, `%^` grabs the file(s) that matched the dependency.

Thus running

    $ make helloworld.o

will, as above, run `gcc -o helloworld.o helloworld.c`. The `%`
operator will match &#8220;helloworld&#8221;, the `$@` grabs
`helloworld.o`, and the `$^` grabs `helloworld.c`.

Most Makefiles will also define some standard targets, such as
`clean`:

    clean :
        rm -f *.o

That covers the basics. For additional resources on Makefiles check
out:

- The [GNU make page][1].
- [GNU make overview][2].
- The [Wikipedia entry][3].

[1]: http://www.gnu.org/software/make/manual/make.html
[2]: http://www.gnu.org/software/make
[3]: http://en.wikipedia.org/wiki/Make_(software)
