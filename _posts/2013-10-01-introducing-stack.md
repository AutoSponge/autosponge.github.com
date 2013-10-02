---
published: true
layout: post
category: blog
tags: [javascript, functional]
---

[Stack.js](https://github.com/AutoSponge/stack) began as a simple, academic exercise to explore the utility of
[linked-lists](http://en.wikipedia.org/wiki/Linked_list) in JavaScript.  I think
[Nicholas C. Zakas's article](http://www.nczonline.net/blog/2009/04/13/computer-science-in-javascript-linked-list/)
made me curious, initially, but the more I learned, the more I appreciated this simple
structure.  For instance, programmers can use linked-lists, like arrays, to implement
other well-known structures like queues and stacks.

<div style="float:right;padding-left:10px;">
<a href="http://xkcd.com/1270/"><img src="http://imgs.xkcd.com/comics/functional.png" alt="functional"></a>
</div>

While implementing a stack using a singly-linked list, friends at work were buzzing about
Reginald Braithwaite's [JavaScript Allongé](https://leanpub.com/javascript-allonge).  To understand
the discussion, I looked up several articles about functional programming, function composition,
and mathematical theory.  I now had a purpose for *Stack*, I could use it to create
complex compositions with smaller units of code.  However, browsers limit the _call stack_ to
manage memory--and they're all different.

In my research, I found another gem by Braithwaite,
[Trampolines in JavaScript](http://raganwald.com/2013/03/28/trampolines-in-javascript.html).  Trampolines
turn a series of nested function calls into an iteration--removing the limits on composition.  The `factorial`
function is a classic problem.  But since JavaScript does not handle large numbers well,
I'll demonstrate this by summing integers less than _n_ instead of multiplying them.

{% highlight js %}
function sumInts(n) {
    return n === 0 ? 0 : n + sumInts(n - 1);
}
{% endhighlight %}

For instance, my build of Chrome can provide an answer for `sumInts(15634)` but throws a `RangeError`
for `15635` while Safari can go as high as `sumInts(34944)`.  When I write the same algorithm as a Stack,
it solves even higher numbers (and is only slightly less elegant, IMO).

{% highlight js %}
var sumInts = Stack(function (n) {
    return n === 0 ? this.val : sumInts;
}).push(function (n) {
    this.val += n;
    return n - 1;
});

sumInts.pipe(500000, {val: 0});
{% endhighlight %}

So, what do you add to a trampolining, singly-linked, stack implementation?  Promises.  Promises represent
one of the most paradigm-shifting concepts I ever learned in this language--I can't leave them out.

Check out [Stack.js](https://github.com/AutoSponge/stack), tell me what you think and what you might
build with it.