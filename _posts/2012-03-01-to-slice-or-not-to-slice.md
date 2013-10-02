---
published: true
layout: post
category: blog
tags: [javascript, performance]
---

You may have noticed that in my last article, I mentioned I would rather loop through copying
arguments than slice them.  That came out of this research: [jsPerf](http://jsperf.com/slice-vs-copy/).

{% highlight js %}
var args = [1,2,3,4], i,
    len = args.length,
    arr = new Array(len);
for (i = 0; i < len; i += 1) {
    arr[i] = args[i];
}
{% endhighlight %}

With relatively short lists, copying out performs slice by 4x or more (your browser mileage may vary).
This is important for low-level frameworky things that get invoked a lot.  Since well-designed methods
often have signatures under 10 (hopefully way under 10), looping is faster in every browser I tested.
Safari had the most interesting results in that copying was always faster.  It makes me wonder how that
native implementation really works.

Part of the technique is pre-allocating the array length you will copy into.  That seems to make a difference
in some browsers so it's worth mentioning that I used that technique in my tests.  I believe this makes a
difference because the looping copy process does not need to change the allocated memory for the target
array as we add new values.  If that were the case, I would expect lower thresholds in the browsers where
this matters, like Chrome, _see [jsPerf](http://jsperf.com/pre-allocationg-vs-literal)_.

Lastly, I found an interesting [jsPerf test](http://jsperf.com/array-copy/3) around the same idea except I
noticed they constructed the test incorrectly, testing the performance of function construction instead
of just the cloning action.  I rewrote the tests with what I learned from the other test into this
one: [jsPerf](http://jsperf.com/array-copy/4).

{% highlight js %}
function clone(arr) {
    var i, len = arr.length,
        arr_clone = new Array(len);
    for (i = 0; i < len; i += 1) {
        arr_clone[i] = arr[i];
    }
    return arr_clone;
}
{% endhighlight %}

In conclusion, even when converted to a method, this technique can outperform native methods on
short lists (although much less so).  Given the smaller differences as a method, I would not
advance the idea into a clone method on the Array prototype that switches techniques based on
the current length--that additional logic would probably bring the overall speed down to native
levels.  Either copy in-line (only with small lists) or use a native method when performance matters.