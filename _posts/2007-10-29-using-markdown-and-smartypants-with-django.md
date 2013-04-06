---
layout: post
title: Using markdown and smartypants with Django
---

[Markdown][1] and [smartypants][2] are two tools created to markup
"standard" text into XHTML-compliant code. In English, they make it
way easier to write HTML, i.e. for a blog.

[1]: http://daringfireball.net/projects/markdown
[2]: http://daringfireball.net/projects/smartypants

An Evil Trend is powered by [Django][3] (which is [no secret][4]), and
recently I've integrated both markdown and smartypants to make it
easier for me to post both articles and blog entries. I'll document
the process that I went through, which is basically from a sterile
Django installation to one with markdown and smartypants.

[3]: http://www.djangoproject.com
[4]: http://aneviltrend.com/about

step 1: getting the necessary packages
--------------------------------------

To begin you'll need to install [python-markdown][5], usually
available via `apt-get` or your distro's package repository. You'll
also need to grab [smartypants.py][6], which should be copied into
your django project's main directory (usually where your `settings.py`
and `urls.py` files are).

[5]: http://www.freewisdom.org/projects/python-markdown
[6]: http://web.chad.org/projects/smartypants.py

step 2: making changes to your settings
---------------------------------------

The next step is to inform django that you'll be using markdown in
your project.  While you might at some point want to check out [the
official documentation][7], you can just follow along here.

[7]: http://www.djangoproject.com/documentation/add_ons/#markup

Add the following line to your `settings.py` file:

    INSTALLED_APPS = (
        ...
        'django.contrib.markup',
        ...
    )

step 3: using markdown
----------------------

At this point you're ready to go with markdown but *not*
smartypants. I use markdown as a template filter: I upload my blog
posts in markdown format, then rely on my server to process the text
on each page load. This may or not be an acceptable performance hit to
you. In that case, follow this [additional documentation][8] on how to
have the processing happen only once.

[8]: http://code.djangoproject.com/wiki/UsingMarkup

Within each template file that will be rendering markdown text include
the following line somewhere near the top:

{% raw %}
    {% load markup %}
{% endraw %}

This will give you access to the `markdown` filter, which you can now
use like any other filter in a template. I use it like this:

    <div class='postcontent'>
        {{ post.content|markdown }}
    </div>

That's it for markdown. Smartypants is a bit different.

step 4: getting smartypants ready
---------------------------------

While using markdown is basically a matter of adding two or three
lines, smartypants is a bit more involved. That's not to say it's
hard. Django doesn't have a smartypants tag built in, so we need to
create that tag ourselves. The django documentation outlines the
[general process][9].

[9]: http://www.djangoproject.com/documentation/templates_python/#extending-the-template-system

The next step will differ for you depending on your setup. I have two
different django apps for my blog and articles, so I'll be taking a
longer road here. If you really only have one app that needs
smartypants, then you can do something a bit more intuitive. Let me
explain the latter approach first.

A django app supports user-defined template tags. To create tags for
an app you simply add a `templatetags` folder to the app folder. Go
ahead and create a folder like this for whatever app you need
smartypants for. Your app directory should look like this:

    ../some_django_app/
        models.py
        templatetags/
            __init__.py
            smartypants.py
        views.py

Where `__init__.py` and `smartypants.py` are two empty files. Going
back to above: if you have two or more apps that need the same
smartypants filter, it probably makes more sense to do what I did and
create a new django app for your custom tags. If this is what you
want, go ahead and create a new app:

    $ python manage.py startapp custom_tags

And within this new application replicate the directory structure
outlined above by adding a `templatetags` directory with two empty
files: `__init__.py` and `smartypants.py`. You'll also need to add
this new app to your `INSTALLED_APPS` setting in `settings.py`.

Note that the `smartypants.py` within the `templatetags` directory
**is not** the same one you downloaded earlier and put in your project
directory. This new file will define our custom tag.

step 5: creating the smartypants filter
---------------------------------------

It's now time to edit the empty `smartypants.py` file you created
above. This is the code you'll want to fill it with:

    from django import template
    
    register = template.Library()
    
    @register.filter
    def smartypants(value):
        try:
            import your_project.smartypants
            return your_project.smartypants.smartyPants(value)
        except:
            return value

This filter will attempt to load smartypants from the project
directory. If it doesn't find it, or has an error, it'll pass through
the value given to it without modification, otherwise it'll pass it
through smartypants.

At this point you're ready to use the smartypants filter.

step 6: using the smartypants filter
------------------------------------

You get to use the smartypants filter in pretty much exactly the same
way you used the markdown filter. Near the top of any template that
will use the filter you need to include the line:

{% raw %}
    {% load smartypants %}
{% endraw %}

This loads in the custom filter you just wrote above. Now use the
filter exactly like markdown. I use it like this:

    <div class='postcontent'>
        {{ post.content|markdown|smartypants }}
    </div>

Be sure to send your content through markdown *before* you send it
through smartypants.

That's it! Not exactly three simple steps, but this guide should help
you get the job done. It's not that hard to add these useful apps to
your django website, and they've certainly made my life a lot easier.
