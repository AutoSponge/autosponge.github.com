---
published: true
layout: post
category: blog
tags: [javascript,functional,part]
---

`contains`, a method common to many libraries, solves a common problem: the needle in the haystack--or does it?

In the case of jQuery, `contains` searches for a parent DOM element, `$.contains( container, contained );`.
It only works with DOM elements because it starts with the "needle" and traverses up the DOM tree using
`.parentNode` and testing if the latest `parentNode === container`.  This does not work on NodeLists.

Underscore can search objects and also *duck-types* the haystack to use `Array.prototype.indexOf` for arrays and array-like objects.
Which means it searches arrays, Arguments objects, and NodeLists then reports if the index exceeds -1.

Lo-Dash does all of that and also works on strings in a RegExp-sort-of way.

While the libraries share a method name, they don't share implementation, signature, or goals.  At this point,
depending on your needs, you may start using multiple libraries; or you may write your own utility.

If you choose to use libraries, I recommend adding a "bridge" to your app.  A bridge file abstracts your usage of
library code by exposing the methods you use.  If you need to change a particular implementation later on, you
only need to change it in one place.  This works even better if you can load libraries dynamically and hide them
from the global namespace or remove them from global once you cache the methods you want.  Lastly, make sure you
cover your bridge with unit tests that express your use pattern(s).  At first you may just copy unit tests
from the library's unit test.  If your pattern changes, you will have an easy way to test the implementation.

{% highlight js %}
//bridge.js
var myNamespace = {};

//we're happy with jQuery for now
myNamespace.contains = jQuery.contains;

//jQuery doesn't work on nodelists, change it!
//myNamespace.contains = jQuery.contains;
myNamespace.contains = function () { ... };
{% endhighlight %}

If want (or need) to write your own function, keep reading!

Things to consider:

- Method name(s): you may end up with several methods or one overloaded method
- Types of haystacks to search
- Composability: such functional, so amaze
- Target browsers:  Facts of life here, we don't always a choice
- Performance(?): If you worry about this, save it for last

I like to start with `Array.prototype.some` because it can short-circuit and returns the same value as other
`contains` methods.

{% highlight js %}
_part_._borrow( this )( Array.prototype, "some" );

function equals( a ) {
  return function ( b ) {
    return a === b;
  }
}

function contains( thing, it ) {
  return _some( thing )( equals( it ) );
};

contains( [1, 2, 3], 1 ); //true
{% endhighlight %}

###PROS

###CONS


{% highlight js %}
_part_._augment( this )( "contains", Array.prototype.some );

function equals( a ) {
  return function ( b ) {
    return a === b;
  }
}

_contains( [1, 2, 3] )( equals( 1 ) ); //true
contains_( equals( 1 ) )( [1, 2, 3] ); //true
{% endhighlight %}

###PROS

###CONS


{% highlight js %}
_part_._augment( this )( "search", Array.prototype.indexOf );

function gt( a ) {
  return function ( b ) {
    return b > a;
  };
}

var isPositive = gt( -1 );

function contains( haystack, needle ) {
  return isPositive( _search( haystack )( needle ) );
}

contains( [1,2,3], 1 ); //true
{% endhighlight %}

###PROS

###CONS


{% highlight js %}
_part_._augment( this )( "index", Array.prototype.indexOf );
_part_._augment( this )( "test", Array.prototype.some );



function gt( a ) {
  return function ( b ) {
    return b > a;
  };
}

var isPositive = gt( -1 );

var contains = function ( haystack, needle ) {
  if ( typeof needle === "function" ) {
    return _some( thing )( equals( it ) );
  }
  return isPositive( index_( needle )( haystack ) );
};

{% endhighlight %}

###PROS

###CONS

function contains( haystack, needle ) {
  return isFunction( needle ) ?
    matches_( needle )( haystack ) :
    isPositive( find_( needle )( haystack ) );
}



That one line of code makes the concept very understandable--`_some( thing )( equals( it ) )`.  But beauty
alone can not make a method useful.  Two other very common methods include `isNull` and `isUndefined`.
Since we made `equals`, we have a way to define both of those.

{% highlight js %}
var isNull = equals( null );
var isUndefined = equals();
{% endhighlight %}

Functional programming is cool.

Admittedly, this will not do everything the Lo-Dash method does.  It won't work with strings (unless the "`it`" has a
length of 1) and it will not search object values.  But do we want to change it to make that work?  Should we?
It's so beautiful right now.

What if we went the other way and further deconstructed it.

{% highlight js %}
_part_._borrow( this )( Array.prototype, "some" );

function equals( a ) {
  return function ( b ) {
    return a === b;
  }
}

var isNull = equals( null );
var isAnyNull = some_( isNull );
isAnyNull( [1, null, 3] ); //true
isAnyNull( [1, 2, 3] ); //false
_some( [1, 2, 3] )( equals( 1 ) ); //true
{% endhighlight %}

But if you like the verb "contains," by all means, use it.

{% highlight js %}
_part_._borrow( this )( Array.prototype, "some" );

var _contains = _some;
var contains_ = some_;

function equals( a ) {
  return function ( b ) {
    return a === b;
  }
}

_contains( [1, 2, 3] )( equals( 1 ) ); //true
contains_( equals( 1 ) )( [1, 2, 3] ); //true
{% endhighlight %}

I believe the power of creating meaningful syntax lies in using several small, unary functions.
Each of which has utility but also composes with other small functions to make human-readable code.



