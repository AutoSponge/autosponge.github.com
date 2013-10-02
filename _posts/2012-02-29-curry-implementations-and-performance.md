---
published: true
layout: post
category: blog
tags: [javascript, functional]
---

By using a simple approach to curry and enforcing a strict signature and return value, we can make
performance improvements to the traditional curry implementation.

### Case 1:
[Currying](http://en.wikipedia.org/wiki/Currying) can be useful in JavaScript for making code
DRYer and more reusable because you can create several derivative functions from a single function.
This differs from Partial Application in that Curry always partially applies a single argument while
Partial Application may fix, or bind, any number of arguments.

This subtle difference can lead us to some performance gains.  For instance, look at Douglas
Crockford's classic extension to the Function prototype from his book,
[JavaScript: The Good Parts](http://shop.oreilly.com/product/9780596517748.do):

{% highlight js %}
Function.prototype.curry = function () {
    var slice = Array.prototype.slice,
        args = slice.apply(arguments),
        that = this;
    return function () {
        return that.apply(null, args.concat(slice.apply(arguments)));
    };
};
{% endhighlight %}

### Performance Improvement 1:
By using the full `arguments` list, Crockford allows for partial application of arguments
in the returned function.  While convenient, most uses of the curry method will probably only
pass a single argument.  If we swap "`slice.apply...`" for "`[arguments[0]]`", curry only
uses the first argument passed and we have no need of transforming arguments into an Array
using the borrowed slice method.

We still can not use concat directly (the reason Crockford sliced his arguments in the first place)
because lacks generic qualities, _see Dr. Rauschmayer's article,
[Array.prototype.concat is not generic](http://www.2ality.com/2012/02/concat-not-generic.html)_.
Although Rauschmayer fails to point out that by applying `concat`, you can achieve the desired
effect--essentially unwrapping the arguments array into separate arguments to be concatenated.

{% highlight js %}
//Sadly, this will now perform slower than Crockford's implementation because concat will receive a longer arguments list.
//an alternate using unshift instead will be about the same speed
Function.prototype.curry = function () {
    var f = this,
        args = [arguments[0]];
    return function () {
        return f.apply(null, Array.prototype.concat.apply(args, arguments));
    };
};
{% endhighlight %}

This leaves us with a `for` loop as an alternative to populating the `args` Array.

Also consider, Crockford prevents the original function from having a context with a `null` parameter
passed first to `apply()`.  If we only plan to curry a single parameter, we can pass a context for use during invocation.

{% highlight js %}
Function.prototype.curry = function (arg, context) {
    var f = this,
        args = [arg],
        count = 1;
    return function () {
        var i, len;
        for (i = 0, len = arguments.length; i < len; i += 1, count += 1) {
            args[count] = arguments[i];
        }
        return f.apply(context, args);
    };
};
{% endhighlight %}

### Case 2:
Ben Alman recently wrote a great article, [Partial Application in JavaScript](http://msdn.microsoft.com/en-us/scriptjunkie/gg575560),
only to fall into the same trap:

{% highlight js %}
function curry(/* n,*/ fn /*, args...*/) {
  var n,
    aps = Array.prototype.slice,
    orig_args = aps.call( arguments, 1 );

  if ( typeof fn === 'number' ) {
    n = fn;
    fn = orig_args.shift();
  } else {
    n = fn.length;
  }

  return function() {
    var args = orig_args.concat( aps.call( arguments ) );

    return args.length < n
      ? curry.apply( this, [ n, fn ].concat( args ) )
      : fn.apply( this, args );
  };
}

var testFn = function (a, b, c, d) {
    return a + b + c + d;
};

curry(testFn)(1)(2)(3)(4); //10
curry(testFn)(1,2,3)(4); //10
{% endhighlight %}

Developers who shun augmenting the native prototypes will find Alman's function especially attractive.
Another convenience of this implementation, apart from allowing partial application, invokes the original
function upon satisfying all arguments.  For instance, compare the invocation syntax:

{% highlight js %}
testFn.curry(1).curry(2).curry(3).curry(4)(); //10
curry(testFn)(1)(2)(3)(4); //10
{% endhighlight %}

While less verbose, one may find the dynamic nature of the return value troublesome to deal with.
Consider a function you would like to curry which returns a function.  How do you type check the
results of calling curry?  Additionally, while the optional "<code>n</code>" parameter may solve
issues with functions which take <code>n</code> arguments, this complicates the maintenance of such
code when curried functions change their signatures.  By all definitions I can find, currying a
function should return a function (as should partial application).  Alman's example mimics the syntax
of languages like Haskell or ML where functions return a curried version if passed less than the
required number of arguments.  Maybe a proper name for this function is `invokeOrCurry`.

### Performance Improvement 2:
Simply removing additional logic to always return a function result enhances curry performance.
In fact, I often make "do less" the first performance enhancement of any exercise, but Crockford published first.

### \[Optional\] Performance Improvement 3:
In cases where you will only pass simple, string/number-style arguments and you will keep the curried
functions around for a while, you may want to use a memoizer to cache the functions generated.
Due to the nature of test harnesses, I'll keep this version generic.

### Style Improvement 1:
With clear parameters and consistent return type, we can properly comment our new curry
method and invite developers to use it:

{% gist 4434651 curry.js %}

see [jsPerf tests](http://jsperf.com/curry-implementations)