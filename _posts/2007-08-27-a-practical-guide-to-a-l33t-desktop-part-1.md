---
layout: post
title: A Practical Guide to a l33t Desktop (part 1)
---

So you're new to Linux, or maybe you've been using it for a while and
are finally starting to get comfortable with it. Either way, you're
itching for one of those cool desktop setups that all the cool kids
have --- complete with CPU monitors and cool graphs and charts strewn
about, along with a bunch of terminals and logs scrolling. Sound like
what you want? Well, with this *patented, fool-proof system* I'll have
you up and running in no time.

I'll assume that you're running Ubuntu or some other Debian-based
distro, but this should work for just about any. Also, KDE might need
a little more persuasion to get some of these to run, but all this is
pretty standard stuff.

conky
-----

[conky][1] is a slick light-weight system monitor that sits on your
desktop. It's customizable beyond your imagination, so whatever you
have in mind, conky is up to the challenge. Let's go ahead and
download it:

[1]: http://conky.sourceforge.net/

    $ sudo apt-get install conky

Or, if you're not running from a Debian-based distro, or you just want
the latest, bleeding-edge version, you can compile it from
source. Whatever suits you.

![conky screenshot](/img/conky_desktop.png)

Once you have it, let's make a `.conkyrc` file in your home directory
to customize it. Otherwise, conky will run with some pretty horrendous
default settings. Follow this (note: if you installed conky from
source, the sample rc file should be located at your prefix. The
example below is on Ubuntu):

    $ cp /usr/share/doc/conky/examples/conkyrc.sample.gz ~
    $ cd ~
    $ gunzip conkyrc.sample.gz
    $ mv conkyrc.sample .conkyrc

If you're running Gnome (and I believe this applies on KDE also)
you'll have to make a few changes to get conky to display
correctly. The first is to uncomment the `own_window_hints`
line. Also, comment out the `own_window_color` line (unless you're a
huge fan of hot pink). This should be the bare minimum to get conky to
display and start drawing some neat stuff.

Now, this wasn't enough when I was trying to get conky to run. I had
this maddening flicker on every update. No combination of
double-buffering and window settings seemed to fix it. If you're in
the same bind, I suggest that you check out your `/etc/X11/xorg.conf`
and add this if it doesn't exist already:

    Section "Module"
    Load "dbe"

Reboot your computer (or, if you know what you're doing, just
X11). That fixed the problem for me.

Now that conky is up and running, let's tweak some stuff to get it the
way you want it. For reference, I've uploaded my [.conkyrc file][2],
which has some small tweaks, mainly to the colors and stuff. For
starters, let's change some basic appearance options. Change

[2]: http://aneviltrend.com/images/conkyrc.txt

    draw_shades yes
    draw_borders yes
    draw_graph_borders yes
    stippled_borders 8

to

    draw_shades no
    draw_borders no
    draw_graph_borders no
    #stippled_borders 8

as they appear in the `.conkyrc`. You'll notice that a lot of the
customization options and such are very self-explanatory, and the file
itself is very well commented. If you're not such a fan of my setup,
don't hesitate to check out [other screenshots][3] and their
accompanying `.conkyrc` files.

[3]: http://conky.sourceforge.net/screenshots.html

Launching conky is as easy as:

    $ conky &

And, if you're running Ubuntu, you might want to make conky launch at
login. You can do this by going to System > Preferences >
Sessions. Add a new startup program, and make the command "`conky -d`"
(the `-d` option puts conky in "daemon mode" From now, conky will
launch whenever you log into Gnome.

desktop logs
------------

Onwards and upwards. Let's move on to the next big game: desktop logs.

If you use a lightweight window manager such as [fluxbox][4] or
[wmii][5] then you'll want to check out [root-tail][6]. Recent
versions of Gnome and KDE don't allow drawing to the root window, and
so this doesn't work with Ubuntu. For Ubuntu, we'll be using conky
(again) to show us logs. Yes, conky is that awesome.

[4]: http://fluxbox.sourceforge.net
[5]: http://www.suckless.org/wiki/wmii
[6]: http://www.goof.com/pcg/marc/root-tail.html

If you've been following along, then you're already using conky to
provide CPU info and whatnot. We can use a second instance of conky to
show logs. Let's create a second conky rc file. Copy the sample rc
file again and this time call it something like `.conky_logrc` (or
whatever you want). Now let's do a little surgery --- these are the
edits that you'll probably want to make:

1. Uncomment the `own_window_hints` line.

2. Turn off `draw_shades`, `draw_borders`, `stippled_borders`,
   `border_margin`, and `border_width` either by commenting them out
   or changing `yes` to `no` (whatever's appropriate).

3. Change the `alignment` to whatever you like --- I put mine in the
   top right.

4. Delete every line after "TEXT" and replace it with the following:

       user.log:
       ${tail /var/log/user.log 10}
       $stippled_hr
       kern.log:
       ${tail /var/log/kern.log 10}

Now let's run conky with this config file to see what we get. Use the
`-c` option:

    $ conky -c .conky_logrc

Replace "conky_logrc" with whatever you named this new config file. If
all goes well, you should see a new conky open up with the two log
files. You might want to put this into your login programs as we did
before.

![conky screenshot](/img/conky_log.png)

Sweet! You can replace the logs with whichever ones you want, or more,
or less.  Check out `/var/log` for log files.

xterm (and others)
------------------

Now, if you don't know your way around the terminal, then this section
might not be for you (and I don't know how you made it this far into
the article). But for those of you that do, you have a few options
that you may not know about.

In addition to the stock gnome terminal that you might be using there
are a whole slew of terminal emulators. Here's a few of the good ones:

- **xterm:** where it all started. This is my personal favorite, and
  it looks way cooler than gnome-terminal.

- **Eterm:** meant to be like xterm, but have more eye candy. Has
  support for themes and transparency.  Check out the [Eterm
  homepage][7] if you'd like to get it.

- **aterm:** tries to be faithful to the afterstep terminal.  Provides
  more eye candy than xterm as well.  Again, here is the [aterm
  homepage][8].

[7]: http://www.eterm.org
[8]: http://www.afterstep.org/aterm.php

I'm not going to write much about this, other than to check out the
man page for whatever one you get. I'm not a huge fan of having a
terminal glued to the desktop, just because I use one too much and
it's pretty inconvenient having to minimize all your windows to be
able to see your terminal.  Nevertheless, if that's your cup of tea
you'll definitely be able to pull it off. You'll want to check out
turning on transparency and setting the geometry of the terminal when
it's launched.

kick-butt wallpaper
-------------------

Finding a good wallpaper can be a challenge. Often I'll see a really
cool wallpaper on somebody's desktop screenshot, but have no way of
knowing where it came from. If that happens to you, try asking in the
comments (if there are any), or checking out any one of these
sites. All have a lot of wallpapers up for grabs. I suggest sorting by
rating or popularity.

- [deviantART Wallpapers](http://browse.deviantart.com/customization/wallpaper/?order=9&startts=1188201600&endts=1188288000)

- [art.gnome.org](http://art.gnome.org/backgrounds)

- [gnome-look.org wallpapers](http://www.gnome-look.org/index.php?xcontentmode=170x171x172x173x174x175x176x177x178x179)

- [kde-look.org wallpapers](http://www.kde-look.org/index.php?xcontentmode=1x2x3x4x5x6x7x29x71x72x73)
