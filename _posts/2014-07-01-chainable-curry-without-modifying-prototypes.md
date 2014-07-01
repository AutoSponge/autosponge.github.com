---
published: true
layout: post
category: blog
tags: [javascript, functional, es6]
---

"OMG another _curry_ article!?"
  
Got it out of your system?  Cool.  Let's continue.

Functional programming makes heavy use of partial application and its simpler cousin, _curry_.
I'm not here to rehash the debate over conflating terms, just offer an alternative pattern for
adding _curry_ to your application in es5 or es6 without modifying the native prototypes.

{% highlight js %}
function curry( arg ) {
    var fn = this;
    function curried() {
        var args = [arg];
        args.push.apply( args, arguments );
        return fn.apply( this, args );
    }
    curried.curry = curry;
    return curried;
}
{% endhighlight %}

To use this method, you need to add it to a function, like this:

{% highlight js %}
//start with a normal fn
function add( a, b ) {
    return a + b;
}

//add the curry capability
add.curry = curry;

//use curry
add.curry( 1 ); // this is a fn

//invoke a curried fn
add.curry( 1 )( 2 ); // 3
{% endhighlight %}

_curry_ adds itself to the returned function when you use it, so its chainable.

{% highlight js %}
//we can chain it!
add.curry( 1 ).curry( 2 )(); // 3

//additional parameters get ignored, as they should
add.curry( 1 ).curry( 2 ).curry( 3 )( 4 ); // 3
{% endhighlight %}

We can also still use _call_ and _apply_ as we normally would.

{% highlight js %}
function addThis( a, b ) {
  return this + a + b;
}
addThis.curry = curry;

addThis.curry( 1 ).curry( 2 ).call( 3 ); // 6
addThis.curry( 1 ).curry( 2 ).apply( 0, [3] ); // 6
{% endhighlight %}

Our implementation gets even simpler using es6's rest and spread operators

{% highlight js %}
function curry( arg ) {
    var fn = this;
    function curried( ...args ) {
        return fn.apply( this, [arg, ...args] );
    }
    curried.curry = curry;
    return curried;
}
{% endhighlight %}

See the rest of the [gist](https://gist.github.com/autosponge/a2ef935291f99a9b9a93) for tests (es6).