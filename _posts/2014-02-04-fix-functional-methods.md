---
published: true
layout: post
category: blog
tags: [javascript,functional,part]
---

While I don't go in for puzzlers as interview questions anymore (yes, I was that guy), I enjoy a good brain
teaser now and again.  [JavaScript Puzzlers!](http://javascript-puzzlers.herokuapp.com/) reminded me that in my
attempt to tame native functions, I was missing a few.  Namely, `parseInt` (a binary function with no
receiver object and an often forgotten second parameter) and `Math.pow` (a binary function with a more helpful
second parameter) functions.

Like the `leave` example from
[Take or Leave Functional Programming](http://autosponge.github.io/blog/2014/01/29/take-or-leave-functional-programming/),
the following code will have problems with *hidden arguments* passed by map.

{% highlight js %}
["1", "2", "3"].map(parseInt);
//first invocation:  ["1", 0, Array[3]]
//second invocation: ["2", 1, Array[3]]
//third invocation:  ["3", 2, Array[3]]
//result: [1, NaN, NaN]
{% endhighlight %}

What if we change `map` instead of guarding its arguments the way `leave` did.

{% highlight js %}
_part_._augment( this )( "mapUnary", function ( fn, receiver ) {
  return this.map( function ( a ) {
    return fn.call( receiver, a );
  } );
} );

_mapUnary( ["1", "2", "3"] )( parseInt );
//[1, 2, 3]
{% endhighlight %}

While that works for the example code, you should always pass the radix parameter.

So, this begs the question, "Flip or fix?"  Both of these methods have binary signatures, which means I can
tame them with a **flip/curry** approach or a more general **fix**.  Flip appears commonly in functional
libraries.  The general utility of flipping parameters around seems helpful, but I can't imagine using it
very often beyond binary functions.  Fixing a parameter works like a selective version of partial application
wherein a specific argument takes the place of a particular parameter.

While `leave`, like `mapUnary` can help prevent hidden arguments leaking into the predicate,
it will not prevent other errors caused by **not** passing the *radix* argument.  To flip and curry the arguments
we need to do something like this:

{% highlight js %}
var reverse = _part_.create_( Array.prototype.reverse )();

function flip( fn ) {
  return function ( ...args1 ) {
    return function ( ...args2 ) {
	    return fn.apply( this, reverse( [...args1, ...args2] ) );
    };
  };
}

flip(function ( a, b, c, d ) {
  return [a,b,c, d];
} )( 1 )( 2, 3, 4 );
//[4, 3, 2, 1]
{% endhighlight %}

I don't think I would add this to \_part\_ because it needs to create reverse.  Besides, it still breaks:

{% highlight js %}
["1", "2", "3"].map( flip( parseInt )( 10 ) );
//first invocation:  [Array[3], 0, "1", 10] <-- we made it the last argument
//second invocation: [Array[3], 1, "2", 10]
//third invocation:  [Array[3], 2, "3", 10]
//result: [1, NaN, 1]
{% endhighlight %}

Ok, fine.  What if we *flip* the `args` using some ES6 goodness?

{% highlight js %}
function flip( fn ) {
  return function ( ...args1 ) {
    return function ( ...args2 ) {
      return fn.apply( this, [...args2, ...args1]); //tricky bits
    };
  };
}

flip(function ( a, b, c, d ) {
  return [a,b,c, d];
} )( 1 )( 2, 3, 4 );
//[2, 3, 4, 1]
{% endhighlight %}

That makes *some* sense, it really helps having ES6 to make it happen, and it will not create a new method internally.
I could see adding it to \_part\_ **except** it will still not work for the original problem!

{% highlight js %}
["1", "2", "3"].map( flip( parseInt )( 10 ) );
//first invocation:  ["1", 0, Array[3], 10] <-- radix goes at then END
//second invocation: ["2", 1, Array[3], 10]
//third invocation:  ["3", 2, Array[3], 10]
//result: [1, NaN, NaN]
{% endhighlight %}

On the other hand, `fix` could work like this:

{% highlight js %}
var splice_ = _part_.create_( Array.prototype.splice );

function fix( fn, n, arg ) {
  return function () {
    var args = arguments;
    splice_( n, 0, arg )( args );
    return fn.apply( this, args );
  };
}

["1", "2", "3"].map( fix( parseInt, 1, 10 ) ); //fix the radix
//[1, 2, 3]
{% endhighlight %}

While that fixes the problem (pun intended), I will refrain from adding it to the library because it creates
`splice_` without exposing it.  But I think you can see, `fix`, the more general solution, works better
than `flip`.  Performance tests anyone?
