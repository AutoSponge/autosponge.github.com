---
published: true
layout: post
category: blog
tags: [javascript]
---

While working with some results from an API call, I needed to copy several (but not all) of their
properties onto model objects I would use for application state.  Most _copy_ or _clone_ functions
grab every property from the source object but I wanted to just list a few.

{% gist AutoSponge/7f08954d7f319fec652f copy.js %}

I like code that tells a story.  It's hard for other developers (or future me) to get confused:

`copy( 'some_property' ).from( objectA ).to( objectB );`

When I use it to collect specific property values from API results, I map over a pre-determined list, 
like this:

{% gist AutoSponge/7f08954d7f319fec652f example.js %}