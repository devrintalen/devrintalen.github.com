---
layout: post
title: Everyday Linux - subversion
---

"Everyday Linux" is my idea for relating the power (and ease) of the
programs that make up a standard Linux distro that you might never
have tried out, or maybe have never heard of. I'll show you how I use
it on my system, and hopefully you might be able to use it for your
own.  Today's installation covers one of my favorites: subversion.

the dilemma.
------------

Being in school, I found myself creating directories for each of my
classes.  Makes sense, considering that each of them is isolated. But
after a semester, I'd wish that I could regain the disk space that I
was losing (fast) to each of these class folders.

I also found myself creating [subversion][svn] repositories for coding
projects, then checking them out into my existing class folders. This
was useful for classes with group coding projects (of which I take
many). I became a big fan of [source control][1], and I wanted to
extend it to the rest of my files for the class.

So I faced two issues:

- Rapidly decreasing disk space, and
- Placing my existing class directories under version control.

Subversion was the easy answer to these. Not only would everything be
under version control, but I could delete my class folders from my
laptop after the semester ended without losing the data.

the solution.
-------------

Subversion *is not always the solution* to these problems, especially
if:

- You have a large, complex directory structure (you'll want to stay
  away from subversioning directories trees that are really huge
  --- like your entire home folder).

- You work with large binary files (such as movie clips, pictures,
  Word documents, etc.) that are not plain text, and that you modify
  often. Subversion has to completely replace the file each time you
  commit, and this will make your repository balloon in size.

So when does subversion work well? Here's what made my setup ideal:

- I work with plain-text files (for the most part). Rather than
  needing to store an entirely new copy of the file, subversion can
  just write whatever changes you made to the file, saving space in
  the repository.

- Each of my classes is readily fitted into its own repository.

- I have a desktop computer that's always on, on which I could store
  the repositories. This kept the load off of my laptop disk space and
  shifted it to my desktop.

So subversion fit the bill for me. Having decided to take the plunge
and subversion each of my class folders, I'll show you what my setup
is now and how I got there.

the implementation.
-------------------

I'll assume that you know how to use subversion. If you don't, you'll
definitely want to read [the subversion book][book], which should give
you a good introduction. If you want to actually implement what I did,
then make sure you read the section on administering repositories.

creating the repositories.
--------------------------

The first step I took was to create a repository for each of my class
directories. I chose a simple naming scheme: `ece310`, `mae378`,
etc. I also chose to keep all of my repositories in my `~/repos/`
directory.

setting up permissions.
-----------------------

In my ideal world, I'd have complete access to my repositories, and
I'd be able to grant access to my friends and classmates that require
access to the certain parts of the repositories. Well, *welcome to the
world of subversion*.

This is the `svnserve.conf` file in each of my class repositories:

    [general]
    anon-access = none
    auth-access = write
    password-db = ~/repos/passwd
    authz-db = ~/repos/authz
    realm = cornell

Not bad. To give somebody access to my repositories, I just add an
entry for them in the `passwd` file. Then I can control what folders
within the repository they have access to. For instance, I can grant
Bob access to the code folder, but keep him out of the rest of my
files. All I need is to modify my `authz` file:

    [ece574:/]
    devrin = rw
    
    [ece574:/code]
    bob = rw

backing up regularly.
---------------------

I have a weekly cron job that tarballs my `~/repos` directory and
pushes it out to an external hard drive. The script appends the date
to the tarball to keep them from colliding. I know I'll eventually
start to run low on space, but at that point I'll either be out of
school (and won't need these repositories anymore) or I can start
deleting some manually.

everyday use.
-------------

No surprises here. It's been extremely useful to be able to check out
one of my repositories onto a computer that *isn't* my laptop. I might
be in the computer lab, for instance, and need to do some work on my
computer architecture class with the tools on the lab computer. No
problem: I just check out the repository, do my work, and commit my
changes. And later, when I want to type up the report, I update my
laptop, and in a few seconds I'm completely up do date.


conclusion.
-----------

Hopefully you've seen just how useful subversion is and how it can be
a real help if it fits your needs. I'm definitely not saying that
source control is the answer to everyone's problems, but for me it
sure helped out.

[svn]: http://subversion.tigris.org
[book]: http://svnbook.red-bean.com/nightly/en/index.html
[1]: http://en.wikipedia.org/wiki/Revision_control
