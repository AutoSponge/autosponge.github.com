---
published: true
layout: post
category: blog
tags: [javascript, functional]
---

I keep seeing [lodash](http://lodash.com/) and [UnderscoreJS](http://underscorejs.org/)
fighting for popularity, but every time I see either library mentioned, I can't help but think
back to Brian Lonsdorf's great talk [Hey Underscore, You're Doing It Wrong!](http://youtu.be/m3svKOdZijA).

Brian focused on a particular signature choice in UnderscoreJS that places the receiver, or
context object, at the first position of the "functional-style" method call:

{% highlight js %}
_.map([1, 2, 3], function(num){ return num * 3; });
{% endhighlight %}

Without saying which way is _'right'_, I have found both patterns helpful at
different times.  So, I wrote this little utility to create several functions from
native methods with both signatures available.

Because [currying](http://en.wikipedia.org/wiki/Currying) is awesome, these functions curry.
But they curry the _'right'_ way, not the _'optional'_ way. (Ok, now I'm starting to have an opinion.)
This produces "function butts." Although you can deal with function butts for this dose of awesomeness.

In addition to currying and working within a functional style,
[Cody Lindley](http://tech.pro/tutorial/1611/functional-javascript) points out another great benefit
of these functions is their ability to operate on __array-like__ objects as well as native arrays.

"But wait, there's more!"  As Nathal Wall pointed out, in his talk on
[High Integrity JavaScript](http://youtu.be/FrFUI591WhI), when you cache copies of native functions,
your code becomes resilient to disruption by anarchists and heathens who feel it necessary to
modify the built-ins (you might sense my strong feelings on _this_ subject).

Lastly, I struggled with what to call the "flipped" functions.  Since, I believe, like Lonsdorf
and [other functional languages](http://en.wikipedia.org/wiki/Map_\(higher-order_function\)),
that function-first is _'right'_, I settled on the "To" suffix.  This assumes the process to be read
as "apply __to__ X" or "map function __to__ list."

Naturally, if you have your own, strong, feelings on the subject, you're welcome to fork or copy the
gist and make it your own.

{% gist 6993297 array.js %}

This produces:

1. each - each(fn)(arr)
1. eachTo - eachTo(arr)(fn)
1. map - map(fn)(arr)
1. mapTo - mapTo(arr)(fn)
1. filter - filter(fn)(arr)
1. filterTo - filterTo(arr)(fn)
1. some - some(fn)(arr)
1. someTo - someTo(arr)(fn)
1. every - every(fn)(arr)
1. everyTo - everyTo(arr)(fn)
1. reduce - reduce(fn)(arr)
1. reduceTo - reduceTo(arr)(fn)
1. reduceRigth - reduceRight(fn)(arr)
1. reduceRigthTo - reduceRightTo(arr)(fn)

Here are some tests you can copy/paste into the console.

{% gist 6993297 tests.js %}