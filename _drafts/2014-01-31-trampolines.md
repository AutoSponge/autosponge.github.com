---
published: false
layout: post
category: blog
tags: [javascript,culture]
---


I want to put my idea for [`\_part\_`](https://github.com/AutoSponge/_part_) to the test.
The idea that I can use native methods in a new way to achieve
the same functionality as many popular libraries while having the flexibility to change it and the possibility
of learning from it.

{% highlight js %}
function trampoline( fn, receiver ) {
    return function () {
      var bounce = _apply( fn, receiver || null )( arguments );
      while ( typeof bounce === "function" ) {
          bounce = bounce();
      }
      return bounce;
    };
}
{% endhighlight %}