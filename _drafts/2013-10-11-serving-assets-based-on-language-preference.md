---
published: false
layout: post
category: blog
tags: [javascript, node]
---

The more I dabble in [Node](http://nodejs.org/) and [Grunt](http://gruntjs.com/), the more impressed I am
with the power they give me as a "front-end" developer.  It took me over a year--and a few major workflow
changes--to push me toward using these tools and learning to love them.

My latest project, [language aware asset server](https://github.com/AutoSponge/language-aware-asset-server),
represents a first for me.  As my first attempt at publishing something purely for the server, it will likely
have flaws.  But I had an "a ha" moment While cleaning up the code--a pattern emerged.

My server starts up from the `http.createServer` invocation, which sends a __request__ and __response__ object
to its callback.  As I separated out various functionality of my server, most processes needed these two
arguments as well as additional information provided by my server's state.  When I went to compose
server processes, I noticed it was a lot easier when the signatures grew to the right and used the
previous arguments.

For instance, `createServer` (req, res) -&gt; `serveAsset` (req, res, asset) OR `errorNotFound` (req, res, asset).
Even though `errorNotFound` does not use __req__ or __asset__, keeping a consistent signature made the
functions composeable.

{% highlight js %}
http.createServer( function(req, res) {

    // We will serve an asset or
    // return "File not found." message.
    maybe( [serveAsset, errorNotFound]
        .map( applyWith )
        .map( bind( slice( arguments )
            .concat( findAsset( req, assets ) ) ) ) );

} ).listen( config.port, config.host );
{% endhighlight %}

`serveAsset` keeps this going by passing all of its arguments to another set of `maybe` processes.

{% highlight js %}
function serveAsset (req, res, asset) {

    if ( asset ) {

        res.writeHead( 200, {"Content-Type": asset.type} );

        maybe( [matchAssetLanguage, serveUnmatchedLanguage, errorNotFound]
            .map( applyWith )
            .map( bind( arguments ) ) );

    }

    return !!asset;
}
{% endhighlight %}

Because I keep the same signature, I can reuse the `errorNotFound` function, and both `matchAssetLanguage`
and `serveUnmatchedLanguage` have enough data to properly invoke `serveLanguageAsset`.  So, I'm left
wondering if the signature should expand as I go into deeper processes or if there should be a state
object passed that holds overall server state.