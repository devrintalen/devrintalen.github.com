---
layout: post
title: Everyday Linux - rsync
---

I have two computers, both of which I like to listen to music on: my
laptop, for when I'm in class (ahem, *studying*), and my desktop when
I'm in my room. I use iTunes on my laptop and Amarok on my
desktop. You can see that I might have a few issues when syncing my
music between the two computers. How I do it is today's Everyday
Linux.

rsync
-----

This is my typical scenario: I'm on campus, and I've just got some
music from a site like [soul sides][soul] or Amazon's sweet new [music
store][store]. I import my music and listen to it on iTunes. Later I
get back to my room, and would like to listen to my new music with the
better speakers hooked up to my desktop. How do I sync up my music?

I'm never typically near nor have access to my desktop whenever I get
new music. So I need a way to copy whatever music I have over to my
desktop. But why not just copy over the music manually? Well, I
*could*, but that's not the cool way to do it.

The more practical reason is that iTunes on my laptop organizes my
music as it sees fit, and I'd rather not have to traverse arcane
directory names in order to get to the folder that I want to copy
over. Amarok, fortunately, is much more forgiving with its
organization, and will put up with the structure that iTunes uses. I
also prune my music collection from time to time, and would rather not
have to track what changes I make to do the same on my desktop. In
short: I need rsync so I have a drop-dead simple syncing system for my
music.

rsync is a utility that synchronizes files and directories. They can
be two local directories, two remote, one local and one remote, it
doesn't matter. All it takes is one terminal command (given, you'll
probably spend some time perfecting this command). The other caveat is
that (if you're using rsync with remote computers) you'll need to set
up the rsync daemon on any remote computers you connect to.

Implementation
--------------

The first step was to get my desktop set up for rsync. I created a
music folder in my home directory to begin, then set up the
`rsyncd.conf` file in my `/etc` directory.

To set up rsync on my Ubuntu system I followed along at the [ubuntu
guide entry][ubuntu] and at another [excellent page][rsync]. Here are
some of the highlights:

The more exciting parts of this file look like this on my system:

    [musicbackup]
    
    path = <home>/music
    comment = the music backup location
    secrets file = /etc/rsyncd.secrets

Not bad at all. The `rsyncd.secrets` file is next:

    <user>:<password>

For me it's just one line: my name and password. 

Now the fun part is on my laptop. This is the rsync command that I use
to do a one-way sync from my laptop to my desktop.

    rsync --verbose --progress --stats --compress --rsh=/usr/bin/ssh \
    --recursive --times --delete \
    --exclude "Apple" \
    --exclude "Movies" \
    ...
    --exclude "Video" \
    <home>/Music/iTunes/iTunes\ Music/* \
    devrin@<desktop ip>:music

The options I specify include:

- Keep me updated with `--verbose` and `--progress`.
- Minimize the bandwidth with `--compress`.
- Prune out removed directories with `--delete`.
- Traverse into each folder with `--recursive`.
- Don't copy a few specific directories (in my case because they have video
  files) by using `--exclude`.

It would be a real pain to have to type out this entire command each
time I wanted to sync, so I copied it into its own bash script that I
called `music_backup.sh`. Now each time I want to back up I just
invoke my little script:

    $ ./music_backup.sh

And my music gets synced up. Not bad! You'll want to read up on the
documentation I linked to above to get a better feel for how to use
rsync to accomplish your goals. There's a few steps there that I
didn't cover but that should be fairly trivial to do.

All in all, rsync is a great system. It works perfectly for what I
need it to do, and I'm sure that a lot of people have some sort of
syncing problem that could be solved elegantly with rsync.

[soul]: http://soul-sides.com/
[store]: http://www.amazon.com/MP3-Music-Download/b?ie=UTF8&node=163856011
[ubuntu]: http://ubuntuguide.org/wiki/Ubuntu:Feisty#How_to_copy_files.2Ffolders_from_local_machine_into_a_remote_Ubuntu_host_.28rsync.29
[rsync]: http://everythinglinux.org/rsync/