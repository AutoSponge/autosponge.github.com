---
published: true
layout: post
category: blog
tags: "javascript, performance"
---

While writing a handler for key events, I realized I was having to convert the event’s key data to test for valid inputs every time. I wondered if using a list of valid inputs would be faster than checking a regular expression.

Here’s a quick test:
[http://jsperf.com/regexp-vs-indexof2]()

Not surprisingly, Chrome was fast enough in both cases and `RegExp` was slightly faster. Surprisingly, IE and FF were much faster for `indexOf` with FF running the index test as fast as Chrome’s `RegExp` results.

So I went further in the test, creating actual handlers with jQuery:
[http://jsperf.com/handle-keypress]()

{% highlight js%}
$("#indexof").on("keypress", (function () {
    var str = ",";
    var i;
    for (i = 65; i &lt; 91; i += 1) {
        str += i + ",";
    }
    return function (evt) {
        if (str.indexOf("," + evt.charCode + ",") &lt; 0) {
            evt.preventDefault();
        }
    };
}()));
 
$("#regexp").on("keypress", function (evt) {
    if (/[A-Z]/.test(String.fromCharCode(evt.charCode)) === false) {
        evt.preventDefault();
    }
});
{% endhighlight %}

In this test, far too much time is spent dealing with the event and not enough time just working out the keypress so there appears to be no winner. Based on syntactic brevity, the `RegExp` is the clear choice. However, if you need to control characters other than those easily expressed in `RegExp` (like control characters or unicode), you might try the `indexOf` method with no loss in performance.