---
published: true
layout: post
category: blog
tags: [javascript, polymer, web components]
---

I'm building applications with just ([Polymer](http://www.polymer-project.org/)) web components.  Yes, they're that powerful.

About the time I read [Inheritance and composition with Polymer](http://pascalprecht.github.io/2014/07/14/inheritance-and-composition-with-polymer/),
I noticed several of my custom elements could handle a little refactoring.  Pascal Precht recommends `Platform.mixin`
in this case.  While it has limited documentation, you can find the signature [here](https://github.com/Polymer/docs/issues/353).

The straight-forward approach left a bad taste in my mouth.  Firstly, my mixed-in objects lazed around in the global
scope like they owned the place.  Secondly, I didn't like the code structure of multiple mixins.  Lastly, I wanted 
the new implementation to have the ability to override capabilities (like a prototype).

{% highlight js %}
//can be defined elsewhere and imported
var sharedA = {a:1};
var sharedB = {b:2};
Polymer( 'my-element', Platform.mixin( Platform.mixin( {
  c: 3
}, sharedB ), sharedA ) );
{% endhighlight %}

In the above example, `sharedA`'s properties get copied over to the result of `sharedB`'s mixin over `my-element`.
On a large `my-element` object, you wouldn't notice what capabilities you inherited until the end of the page.

{% highlight js %}
Polymer( 'my-element', [sharedB, sharedA].reduce( Platform.mixin, {
  c: 3
} ) );
{% endhighlight %}

This example accomplishes the same thing and solves the problem of secret "parents."  In this case, `sharedB`
gets overridden by `sharedA`.  To see this, change the second line to `var sharedB = {a:2};`.  Priority goes
from left-to-right.

{% highlight js %}
Polymer('my-element', [sharedB, sharedA, {
  c: 3
}].reduce( Platform.mixin ));
{% endhighlight %}

This pattern shows us our mixin chain upfront and allows our new object get "last say" on what capabilities the
final object will hold.  There's just one, big, problem--we permanently mangled `sharedB`.  Even worse, we may
have messed up `my-element` for future instances because of how Polymer caches non-primitive properties.

So, I wrote a little utility to keep the parts I liked and stop the mangling of my shared code objects.

First, make a component folder called app with an app.html file in the folder.
{% highlight html %}
<script src="scripts/app.js"></script>
{% endhighlight %}

Place the following in components/app/scripts/app.js
{% highlight js %}
(function ( global ) {
    'use strict';
    global.app = global.app || {};

    global.app.extend = function ( /* ...objects */ ) {
        var result = {};
        var objects = [].slice.call( arguments, 0 );

        objects.forEach( function ( mixin ) {
            Object.keys( mixin ).forEach( function ( key ) {
                result[key] = mixin[key];
            } );
        } );

        return result;
    };

}( this ));
{% endhighlight %}

Now, include this feature in your other components using this:

{% highlight html %}
<link rel="import" href="../app/app.html">
{% endhighlight %}

In my component code, I use it like this:
{% highlight js %}
Polymer( 'my-element', app.extend( app.sharedB, app.sharedA, {
    myStuff: 'here'
} ) );
{% endhighlight %}

Comments welcome.