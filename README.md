My Website
==========

My site is hosted by the nice folks at Github on their pages platform
with [Jekyll]. I'm not doing anything fancy with Jekyll (yet); no
plugins or modifications. The project wiki is a little light on the
bootstrapping portion, so if you get stuck I recommend remembering the
following:

- Jekyll doesn't care about anything in `_pages`.
- You need to create your own `index.html`, Jekyll doesn't make it for you.
- Don't use `{% include foo %}` to link to media like images, instead put
  them in a folder like `img` and Jekyll will copy that over to your site.

This site's layout:

    _attachments: unused, should be deleted
    _includes: unused currently
    _layouts
        default.html: base layout, <body> is blank
        post.html: individual posts
    _posts: duh.
    css
    img
    js
    index.html
    about.html
    projects.html

Feel free to use my configuration and/or layout as inspiration for
your own site, but blatant copying will cause me to frown and be
mildly irritated (but I'll get over it).

[Jekyll]: https://github.com/mojombo/jekyll