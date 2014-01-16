---
published: true
layout: post
category: blog
tags: [tools,javascript,functional]
---

I published my library, [`_part_`](https://github.com/AutoSponge/_part_), to [NPM](https://npmjs.org/package/part) today.
As my first attempt to publish anything to NPM, I expected problems but not the ones I encountered.

I named `_part_` to demonstrate the pattern within the name.  You can use `_part_` as a tool to
bind the context/receiver of a native function or to "curry" the argument(s).
Javascript allows leading underscore for variable names, so I thought I made a clever name.

I followed the [publishing documentation](https://npmjs.org/doc/developers.html)
without much trouble and I tested the installation process locally,
as NPM suggests.  Everything seemed fine.

However, NPM barfed when I tried to publish.

{% highlight console %}
npm ERR! publish Failed PUT response 400
npm ERR! Error: bad_request Only reserved document ids may start with underscore.: _part_
{% endhighlight %}

While devastated, I felt the publishing process needed to continue, so I willingly:

1. changed the name to `-part-`.
1. changed all of the references in the documentation to `-part-`.
1. changed references in the build process to `-part-`.

{% highlight console %}
npm ERR! publish Failed PUT response 403
npm ERR! Error: forbidden name invalid: -part-: -part-
{% endhighlight %}

Ouch!

So, I gave up being clever and just called the damned thing `part`.  I will keep the documentation using the
`_part_` variable name and I will keep the same github repo.  But I wished I would have known about this
earlier.