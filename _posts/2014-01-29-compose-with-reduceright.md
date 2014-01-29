---
published: true
layout: post
category: blog
tags: [javascript,functional,part]
---

Poor [`.reduceRight`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/ReduceRight).
This JavaScript late-comer (ES 5.1) does the same thing as `.reduce`, only backwards.  No wonder this seemingly
forgotten function gets no love.

Well, I love you, `.reduceRight`.  With the help of [\_part\_](https://github.com/AutoSponge/_part_), I made
a `compose` function which I find more elegant than my previous versions of composition.

{% highlight js %}
_part_._borrow( this, Array.prototype )( "reduceRight" );

function csub( g, f ) {
    return f( g );
}

var _compose = _part_._create( function ( x ) {
    return reduceRight_( csub )( this , x );
} );
{% endhighlight %}

`csub` represents the [composition operator](http://en.wikipedia.org/wiki/Function_composition).
So, you might read it aloud as "c sub g f" which equates to `f(g)`.  But that's just academic stuffs.
The cool part happens when I make a slight change to an example from [lodash](http://lodash.com/docs#compose):

{% highlight js %}
//this seems weird, but order matters
//flip the compose fn's below to see how
var realNameMap = {
    'pebbles': 'penelope'
};

var format = function(name) {
    name = realNameMap[name.toLowerCase()] || name;
    return name.charAt(0).toUpperCase() + name.slice(1).toLowerCase();
};

var greet = function(formatted) {
    return 'Hiya ' + formatted + '!';
};

//I now prefer arrays of "un-composed" functions over
//only having composed groups as arguments or needing to .apply the array
var welcome = _compose( [greet, format] );
welcome('pebbles');
// → 'Hiya Penelope!'
{% endhighlight %}

This makes me even more excited for the upcoming [rest parameters and spread
operator](http://wiki.ecmascript.org/doku.php?id=harmony:proposals) because I can use them to
collect extra arguments for the first invocation with one small change:

{% highlight js %}
var _compose = _part_._create( function ( ...x ) {
    return reduceRight_( csub )( this , ...x );
} );
{% endhighlight %}

[Try it out, now](http://bit.ly/1gprLgi).

