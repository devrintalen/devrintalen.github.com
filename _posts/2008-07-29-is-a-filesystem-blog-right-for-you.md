---
layout: post
title: Is a Filesystem-Based Blog Right for You?
---

[Chris][1] pointed me to an insightful post by [Chris Siebenmann][2]
on the shortcomings of filesystem-based blogs. I'll summarize his
points:

[1]: http://cdleary.tumblr.com/post/37562612/why-file-as-blog-entry-blog-engines-have-problems
[2]: http://utcc.utoronto.ca/~cks/space/blog/web/EntryAsFileProblems

- Editing a post changes the modification time; this gets annoying when all you
  want to do is fix a typo and not republish.
- Embedding metadata is akward.
- Storing metadata in a static location defeats the entire purpose of
  abandoning a database-driven engine.

The best defense I can come up with is something like this: if you're
having these problems with your file-based blog, you probably
shouldn't be using one.  I don't think file-based blogs are superior
to anything driven by a database; in fact they're pretty much dumber
overall. If you need the metadata that database-driven blogs provide
you're probably better off just using one rather than trying to turn a
file-based blog into something that it's not.

I think file-based blogs shine when your blog can be better described
as a loose coupling of essays. I make no claims that my posts are
worthy of being labeled as such, but they are infrequent and
permanent. My [tumblr] page is what I reserve for musings and
link-posting; this site is meant for posts that I'd like everyone to
be able to see for a while.

Nevertheless, I do believe there are a few simple tricks that you can
pull to adequately address some of Siebenmann's points.

Modification Times
------------------

The solution I have is to include the post date along with the title
at the top of my posts. The first two lines of this post are:

    Is a Filesystem-Based Blog Right for You?
    6/8/2008

[Can] interprets the first line as the title --- and formats a slug
accordingly --- and the second line as the publication date. The post
gets published at noon of the day given and isn't published if the
date is in the future. The modification time of the file has nothing
to do with the publication time.

Reminds me of how we used to title our homework assignments in grade
school.

Metadata
--------

Siebenmann is concerned about a lot more than publication times:
metadata can include tags, categories, revisions, modification times,
and so on. I don't think file-based blogs are cut out to handle gobs
of metadata; if you find yourself needing that data it's probably time
to move to something like [Wordpress].

[Wordpress]: http://wordpress.org

I feel that Siebenmann's last point --- that having local storage for
metadata --- is addressed by what I just said above.

Bonus Problem: Post-specific Media
----------------------------------

File-based engines have no real way to store media in connection with
a post. I considered a few options:

1. Just don't. Have a `/media` directory and hard-code in links in posts.
   Can't name two pieces of media the same thing (i.e. no `foo.jpg` in two
   posts).
2. Turn each post into a folder with the post text file and any associated
   media files inside. Just one problem: it's a real pain in the butt. Kind of
   defeats the purpose of being able to just drop posts into a directory and
   publish them.
3. Create a folder for each post in a `/media` directory and have can
   modify links in the post to point at this new location. I'll explain this
   one below.
4. Embrace the possibility of a blog without any media. Would do that, but I
   already have posts with screenshots and such.

My initial post on can outlined what I was planning: basically to
search and replace links in the post source. To quote myself:

> But I the approach I'd like is something like this:
> 
> - Publishing script creates a directory per post in a specified media base
>   directory.
> - Drop any media corresponding to a particular post into said directory.
> - The post source uses a flag --- something like `class='media'` --- in links
>   that reference local media. The publishing script looks for these in posts
>   and prepends the post's media directory to the link.

The first two are right. The third point is crap. What I want is
simplicity: can uses Markdown, and including a class for a link means
writing out the link by hand. The solution I use is to just have links
that look like:

    <a href='/media/is_a_filesystem-based_blog_right_for_you.html/foo.jpg'>...</a>

Simple. During publishing can goes through and replaces that link with
this:

    <a href='/media/is_a_filesystem-based_blog_right_for_you.html/post_slug/foo.jpg'>...</a>

And I drop `foo.jpg` into that folder to complete the process. Lets me
use identical names and, more importantly, it keeps the media folder
nice and organized. Still not as easy as database-based blogs, but
thankfully I'm text-heavy and tend not to have any media.

(Disclaimer: can actually doesn't do any of this. But it will. Soon.)

Pick Your Poison
----------------

File-based blogs aren't popular. For many they're going to be a square
peg in a round hole.  But if your blog isn't complicated or updated
often then they offer a simplicity that something like Wordpress
can't.

[tumblr]: http://aneviltrend.tumblr.com
[Can]: http://launchpad.net/can/
