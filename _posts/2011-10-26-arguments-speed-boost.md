---
published: true
layout: post
category: blog
tags: [javascript, performance]
---

When I look at code like the [memoizer](https://github.com/AutoSponge/memoize.js),
I find things that others gloss over because of my *training*.  My most recent project involves a 
*huge* application with brutal *SLA's*.  We must look at every function call or loop 
*critically* in order to achieve the required *speed*.  That often means A/B testing 
in multiple browsers to see where we can shave a few _milliseconds_.

Here's another example of what I'm talking about.  How often do you see developers manipulate
arguments in a function the way the memoizer did?

{% highlight js %}
var args = Array.prototype.slice.call(arguments);
{% endhighlight %}

It's pretty standard.  Well, it may seem trivial, but nothing's free.  If you don't have to do it, don't--that's
the easiest way to boost performance.  I wrote two example tests for the most common uses of arguments:
[iterating](http://jsperf.com/handle-args) and [prepending](http://jsperf.com/prepend-args).

{% highlight js %}
(function () {
    var i, len;
    //just iterate, you don't need an Array method (forEach is slow!)
    for (i = 0, len = arguments.length; i < len; i += 1) {
       noop(arguments[i], i);
    }
}(3, 2, 1));


var list = [3];
(function () {
    var i, len, llen;
    //we know getting length once is fast, so do it here then add them
    for (i = 0, llen = list.length, len = llen + arguments.length; i < len; i += 1) {
        //determine which set to use based on i and the length of the prepended set
        //just use property access to get arguments out, no slice, no concat
       console.log(llen > i ? list[i] : arguments[i - llen], i);
    }
}(2, 1));
{% endhighlight %}

get the [Gist](https://gist.github.com/1318517)



