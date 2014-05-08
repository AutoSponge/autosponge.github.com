---
published: true
layout: post
category: blog
tags: [javascript, maintainability, functional]
---

Functional composition leads to code maintainability.  Also, if I can achieve the same process with native code
as with hand-written code, I will always prefer native code (unless I face a performance barrier and hand-written
code can help).  [`part`](https://github.com/AutoSponge/_part_) was the first library I wrote which I felt got me
really close to reusing native code in a pattern which supports functional composition.

"But native functions are too slow!"  Yeah, that part sucks.  But I no longer look for opportunities to
micro-optimize before writing something maintainable.  But that previous life informs me if I actually have
a performance issue to solve: `.bind()`, any iterator, loops vs. function calls, unravelled loops, etc.
Besides, native functions can get better with environment improvements.  My handcrafted code... probably not.

I was missing a simple bridge between those native functions (like `Array.prototype.reduce`) and my
use of them in the `part` pattern.  With [`part-native`](https://github.com/AutoSponge/part-native), I may have
built my bridge.  I'm currently putting it through some exercises in my [`utils`](https://github.com/AutoSponge/utils)
repo.  By far, my favorite usage involves syntax like this:

{% highlight js %}
module.exports = require( 'part-native' )( 'Array.slice_' ).bind( null, 0 );
{% endhighlight %}

If the method exists in the environment, `part-native` will find it, `.borrow()` it (using `part`) and cache it.
This might also be useful for capability detection.  Anyway, look for imports of `part-native` instead of
`part` in future repos.