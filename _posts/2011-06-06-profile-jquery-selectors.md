---
published: true
layout: post
category: blog
tags: [javascript, performance]
---

For performance reasons, I wanted to quickly audit what selectors a site used with jQuery.
Using the script below, I evaluated the $profile object to get an idea of how often Sizzle
was engaged and for what types of elements.  It really helps to add this before `document.ready`
(when all the Sizzle craziness starts) but after jQuery and your plugins load.

{% highlight js %}
//this object holds the results
$profile = {};
old$ = jQuery;
$ = function () {
    var args = Array.prototype.slice.apply(arguments);
    if (args && args[0] && typeof args[0] === "string") {
        $profile[args[0]] = typeof $profile[args[0]] === "undefined" ? 1 : $profile[args[0]] += 1;
    }
    return old$.apply(this, arguments);
};
old$.extend(true, $, old$);
jQuery = $;
{% endhighlight %}