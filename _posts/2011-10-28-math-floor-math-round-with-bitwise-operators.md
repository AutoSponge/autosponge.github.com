---
published: true
layout: post
category: blog
tags: [javascript, performance]
---

[James Padolsey's article](http://james.padolsey.com/javascript/double-bitwise-not/) on the bitwise _NOT_
inspired me to research if the "doesn't work for *negative* numbers" thing would have an easy workaround
and if that workaround would allow us to still calculate faster than the Math.floor function.

I came up with this test to replace *Math.floor*:

{% highlight js %}
//http://jsperf.com/math-floor-vs-bitwise
for (i = -10; i < 10; i += .01) {
  i > 0 ? ~~i : i == ~~i ? i : ~~i -1 === Math.floor(i) ? i : console.log("failed", i);
}
{% endhighlight %}

Then I came up with one for <strong>Math.round</strong> as well:
{% highlight js %}
//http://jsperf.com/math-round-vs-bitwise-decimal
for (i = -5; i < 5; i += .01) {
  i > 0 ? i < ~~i + 0.5 ? ~~i : ~~i + 1 : i < ~~i - 0.5 ? ~~i - 1 : ~~i === Math.round(i) ? i : console.log("failed", i);
}
{% endhighlight %}

Each one will out-perform the peer Math function (your browser's mileage may vary).  See the
*JSPerf* links [here](http://jsperf.com/math-floor-vs-bitwise) and [here](http://jsperf.com/math-round-vs-bitwise-decimal).

As a colleague pointed out, if you make these expressions into a *function* for reuse, the Math functions will out
perform them or, at worst, break even.

So, really the only time you might want to use this is in a *performance-critical* application
(like animation).  But it's still *cool*!


