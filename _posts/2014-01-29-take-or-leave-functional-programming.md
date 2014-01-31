---
published: true
layout: post
category: blog
tags: [javascript,functional,part]
---

I appreciate orthogonality in programming.  If you name a method in your app
"out," I might expect its pair-opposite, "in" to appear.  Even more interesting,
I enjoy seeing the implementation of "in" referencing "out" in some way (or
*visa versa*).

While reading docs for various "functional" JavaScript libraries, I noticed
most have a `take` method.  But I can't find a `leave` method anywhere.
So, here's an introduction to `leave`, what it does, why you need it, and
why it balances `take`.

In a classic JavaScript scenario, the n00b functional programmer reuses the
uber-useful function `sum` in a `map` instead of `add` because he wants to
bind a couple of other parameters.

{% highlight js %}
function add( a, b ) {
    return a + b;
}

function sum() { //variadic
    return Array.prototype.slice.call( arguments, 0 ).reduce( add, 0 );
}

[1, 2].map( sum.bind( null, 1, 2 ) );
//["41,2", "61,2"]   (╯°□°)╯︵ dɐɯ
{% endhighlight %}

The poor n00b expected an array `[4, 5]` but instead created all kinds of errors
with `["41,2", "61,2"]` and can't figure out why.

`take`, in JavaScript, works like `Array.prototype.slice` but always starting at
the `0` index of an array.  So, `list.take( x )` should have the same output as
`list.slice( 0, x )`.

We can use `leave` to "guard" the parameters passed from `map`.  `leave` will only
*leave* one spot open to the signature of `sum`.

{% highlight js %}
//n00b uses _part_, it's super effective!
[
    "reduce", "slice",
].forEach( _part_._borrow( this, Array.prototype ) );

var take_ = slice_.bind( null, 0 );

function add( a, b ) {
    return a + b;
}

function sum() {
    return _reduce( arguments )( add, 0 );
}

function leave( n ) {
    return function ( fn, args ) {
        return function () {
            //leave refers to take, that's cool!
            return fn.apply( this, ( args || [] ).concat( take_( n )( arguments ) ) );
        };
    };
}

[1, 2].map( leave( 1 )( sum, [1, 2] ) );
//[4, 5]
{% endhighlight %}

*For fun, click the "repl" link below and change the code to `...leave( 2 )...`.  Why did that happen?*

Try it in the [repl](http://bit.ly/1gnzsDS)

## EDIT:

I thought of an alternative version where `leave` does not deal with arguments at all using `papply`:

{% highlight js %}
//replaces leave in the above example
function leave( n ) {
    return function ( fn ) {
        return function () {
            return fn.apply( this, take_( n )( arguments ) );
        };
    };
}

//slight change to the usage, allows 1,2 to be passed as separate parameters
[1, 2].map( leave( 1 )( _part_.papply( sum )( 1, 2 ) ) );
{% endhighlight %}
