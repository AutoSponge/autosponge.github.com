---
published: true
layout: post
category: blog
tags: [javascript, maintainability, testing, polymer, web components]
---

For the first time in a long time, I find myself working with something completely new.  Web Components will
change the way we build applications.  But the browser support and completed specs will take time to
implement.  In the meanwhile, we can use various projects, like [polymer](http://www.polymer-project.org/),
to shim the functionality of web components into today's latest browsers.

Citing Addy Osmani's latest article, [FIRST](http://addyosmani.com/first/), we need to test our components.

I started with the `yo polymer` generator.  It uses bower while I prefer npm/browserify and it uses grunt
while I prefer gulp.  But it does the job setting up the scaffold to get you started fairly well--except for testing.

I found the default setup incapable of running my tests.  I never quite figured out why, but I always got the same
error:

{% highlight bash %}
Warning: PhantomJS timed out, possibly due to a missing Mocha run() call. Use --force to continue.
{% endhighlight %}

Ultimately, I blame PhantomJS as it also times out using [Testem](https://github.com/airportyh/testem):

{% highlight bash %}
...✗ testem ci -l PhantomJS
not ok 1 PhantomJS 1.9 - guid component "before all" hook
    ---
        message: >
            timeout of 2000ms exceeded
    ...

1..1
# tests 1
# pass  0
# fail  1
{% endhighlight %}

So, my solution for TAP output uses Testem with Chrome (`testem ci -l Chrome`) which runs fairly quickly and
dumps the results to the console.  When I want to see it in the browser or debug, I run the `test/index.html`
page as if it were an application (requires a slight reconfiguration of the Gruntfile.js).

I learned that much of the "windup" of a polymer component in Chrome happens asynchronously, so I often write
a mocha before-all hook like this:

{% highlight js %}
// setup
// var component; declared elsewhere
before( function ( done ) {
    component = document.createElement( 'my-component' );
    document.body.appendChild( component );
    var check = setInterval( function () {
        if ( component._readied ) {
            clearInterval( check );
            done();
        }
    }, 300 );
} );
{% endhighlight %}

I also learned that while adding components to the test harness works easy enough, removing them throws errors
if you don't also disconnect the listeners.  Many of my tests have a mocha after-all hook that looks like this:

{% highlight js %}
// teardown
after( function () {
    // we need this because <some child component> will be nested
    // and throws a TypeError when it gets removed
    component.shadowRoot.children.array().forEach( function ( e ) {
        e._observers = [];
    } );
    component._observers = [];
    document.body.removeChild( component );
} );
{% endhighlight %}

I glossed over a few details to keep this short and get to the harder to figure out bits.  If you need me to
cover other areas, let me know.  Also, let me know if this helped you with your testing efforts.