---
published: true
layout: post
category: blog
tags: [javascript, functional, part]
---

I like reading my newsletters.  A weekly summary like JS weekly allows me to see if my twitter or github
network was on top of the same hot news as the publisher.  The more I click in and read, the worse
I've done staying on top of things.

This week, I read [Calling an Array of Functions in JavaScript](http://zpao.com/posts/calling-an-array-of-functions-in-javascript/).
Paul O’Shannessy is correct, this stuff is not obvious.  Which is why I made (and use) [part](https://github.com/AutoSponge/_part_).
Part allows me to approach problems like this, calling an array of functions, using the native functionality
I know but with the syntax I want.  Paul's blog doesn't allow comments, so I had to post this...

{% highlight js %}
//borrow the functions we need
_part_._augment( this )( "each", Array.prototype.forEach );
_part_._augment( this )( "call", Function.prototype.call );

//create a special combination of native functionality
//which we can reuse anywhere
var invokeAll = each_( call_() );

//setup our scenario
var a, b;
var callbacks = [function () {a = 1;}, function () {b = 2;}];

//use our special method
invokeAll( callbacks );
[a,b];
{% endhighlight %}

[(go play with this example in the repl)](http://bit.ly/1FUKUOl)

There are two main differences between **part** and functional JavaScript libraries.

1.  First, we're just using native methods with a different, predictable signature.
1.  Second, nothing is done _for_ you.

**Part** isn't a library and isn't trying to be one.  It is trying to allow flexibility with common, well-understood
methods and encourage a functional approach.

My guess: this doesn't perform as well as a library version or possibly even Paul's, native version.  However, performance isn't
always what you need.  Sometimes, you need readable syntax and the freedom to move along to the next problem.  If performance _is_
your problem, you should be able to replace the `invokeAll` function with one that does exactly the same thing using
a `for` loop but keep your useage nice and readable.  Remember, be kind to your future self.