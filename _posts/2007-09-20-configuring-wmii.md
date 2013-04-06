---
layout: post
title: Configuring wmii
---

I've been using [wmii] for a while now after having [discussed the
benefits][1] of tiling window managers. What I've had the most trouble
finding is documentation on how to configure the darn thing. Yesterday
I think I found the last piece of the puzzle, so I'll lay down what
I've found so far and where you might be able to find more info.

[wmii]: http://www.suckless.org/wiki/wmii
[1]: http://aneviltrend.com/articles/1

First, the wmii config file lives at `/etc/X11/wmii-3/wmiirc`. You'll
want to make a local copy of this rc file in your `~/.wmii-3/`
directory. This terminal command should do the trick:

    $ mkdir ~/.wmii-3; cp /etc/X11/wmii-3/wmiirc ~/.wmii-3/wmiirc

If you take a look at the file you should see few lines with variables
like `WMII_NORMCOLORS` and such. These allow you to change the "theme"
of your wmii. Changing these blindly probably won't do you too much
good, so check out [the wmii themes page][2] and pick your favorite
one. You'll notice that the variables on the theme page and the
variables in your wmiirc file don't quite match up. Go ahead and use
the names that already exist. Also, keep all the colors in one quote,
instead of the three quotes that they're broken up into on the themes
page.

[2]: http://www.suckless.org/wiki/wmii/tips/themes

If you wanted to change your theme to the "gray & green", your wmiirc
file would look like this:

    # Default theme
    # WMII_SELCOLORS='#ffffff #285577 #4c7899'
    # WMII_NORMCOLORS='#222222 #eeeeee #666666'
    
    # Gray & green theme
    WMII_SELCOLORS='#A0FF00 #686363 #8c8c8c'
    WMII_NORMCOLORS='#e0e0e0 #444444 #666666'

After you log out and back in your new colors should be used.

You might also want to add/change some of the key combos or such. In
that case, check out this [code snippets][3] page. It has a few bits
of code that you can insert into your wmiirc file to add/change
functionality. I'm not going to lie, though: I haven't gotten this
working yet. I'm planning on checking out the IRC channel to see what
I can come up with. You should, too, if you're having issues or just
looking for some advice: the channel is #wmii on irc.oftc.net.

[3]: http://www.suckless.org/wiki/wmii/tips/code_snippets

The only other change that I've made so far is changing the default
terminal from gnome-terminal to xterm. I'm sure there's a more elegant
way than what I did, but the quick and dirty way is to make an alias
for an x-terminal-emulator.  Here's what I did:

    $ mkdir ~/bin
    $ ln -s /usr/bin/xterm ~/bin/x-terminal-emulator

And in my `.bashrc` file I have the line:

    export PATH=/home/devrin/bin:$PATH

Make sure that the directory where the alias resides comes before your
normal `PATH` variable. That way, when wmii tries to execute the
x-terminal-emulator, it'll find xterm instead of gnome-terminal. To
make sure that your `PATH` is set correctly, do this: log out then
back in, then run this terminal command:

    $ which x-terminal-emulator

If you set up the path correctly, it should report something like
`/home/<user>/bin/x-terminal-emulator` (or wherever you put the
alias).
