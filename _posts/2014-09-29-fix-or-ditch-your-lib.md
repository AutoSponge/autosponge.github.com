---
published: true
layout: post
category: blog
tags: [javascript, es6, performance, array comprehension, rest operator, spread operator, generator comprehension, for-of, functional]
---

It's time to start the conversation about ES6 [iterable](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/iterable)
data structures and functional programming.  I, for one, refuse to get caught using an outdated library the first time 
I want to pass an iterable instead of an array.

The iterables include [String, Array, Map, Set and Generator](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/iterable#Builtin_iterables).
In addition, you can craft your own iterable using the `Symbol.iterator`:

{% highlight js %}
const iterable = {
    *[Symbol.iterator](){
        yield 1;
        yield 2;
        yield 3;
    }
}
{% endhighlight %}

Since Arrays are iterable, we should rewrite our functional utilities, like `map` and `reduce`, to assume an iterable
instead of an Array.  I [tested](https://github.com/AutoSponge/iterable) three simple variations on `map` and 
another three on `reduce`.
 
{% highlight js %}
var map = ( fn, iterable ) => [for (i of iterable) i].map( fn );
var map = ( fn, iterable ) => [...iterable].map( fn );
var map = ( fn, iterable ) => [for (i of iterable) fn( i )];

var reduce = ( fn, a, iterable ) => [for (i of iterable) i].reduce( fn, a );
var reduce = ( fn, a, iterable ) => [...iterable].reduce( fn, a );
var reduce = ( fn, a, iterable ) => {
    var b = a;
    for ( let i of iterable ) {
        b = fn( b, i );
    }
    return b;
};
{% endhighlight %}

If you want to make curried or partially applied versions, the ES6 
[spread operator](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Spread_operator) makes it very easy:

{% highlight js %}
var curry = fn => a => b => fn( a, b );
var map = curry2( ( fn, iterable ) => [...iterable].map( fn ) );

var curry2 = fn => a => b => c => fn( a, b, c );
var reduce = curry2( ( fn, a, iterable ) => [...iterable].reduce( fn, a ) );
{% endhighlight %}

Partial application makes use of ES6 [rest parameters](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/rest_parameters).

{% highlight js %}
var papply = ( fn, ...a ) => ( ...b ) => fn( ...a, ...b );
var map = papply( ( fn, iterable ) => [...iterable].map( fn ) );
var reduce = papply( ( fn, a, iterable ) => [...iterable].reduce( fn, a ) );
{% endhighlight %}

Folds and maps come to mind quickly, but you might be surprised to find your favored lib uses `Array.prototype` methods
or hyper-optimized `for` or `while` loops for variadic functions like `compose`. 
 
{% highlight js %}
var send = ( a, fn ) => fn( a );
var compose = ( fns ) => ( a ) => [...fns].reduceRight( send, a );
{% endhighlight %}

Combining the flexibility of these new data types with the power of functional concepts becomes so easy, you may not
even want a lib.  For now, I'm running this under [traceur's runtime](https://github.com/google/traceur-compiler). But 
seeing as most of the iterable data structures [already appear in evergreen browsers](http://kangax.github.io/compat-table/es6/),
`for-of` and spread can't be far behind.  It's time to fix or ditch your functional lib. 