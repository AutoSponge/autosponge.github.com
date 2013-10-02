---
published: true
layout: post
category: blog
tags: [javascript, performance]
---

My main project at work uses large JavaScript objects for a data store.
Our team used a very cool method to dynamically create null-safe getter functions and cache them for reuse.
Oh, and did I mention it was fast?

I rewrote the guts to do just the bare minimum--get objects and their primitive property values
from a given context (or `this`).  Future implementations may take some sort of descriptive
notation in the path (mostly likely to filter arrays of objects), but this will work well for most applications.

*note*:  So, slightly more than the bare minimum.  I chose to allow `path` to be a `string` or `array` of `strings`.
This allows me to pass `get.apply` an `arguments` object--which may come in handy--and saves
processing if I already have a split path.

{% gist 3758975 get.js %}

{% gist 3758975 getTests.js %}