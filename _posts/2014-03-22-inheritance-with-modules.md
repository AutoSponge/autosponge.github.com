---
published: true
layout: post
category: blog
tags: [javascript, quality, process]
---

In the rewrite of [`svg-clock`](http://autosponge.github.io/blog/2014/03/18/flow-toward-maintainability-2/),
I became enamored with the `util.inherits` pattern.  With [Browserify](http://browserify.org/),
it works on the server and in the browser.  I'm a little late to the party.  Several other libraries
I follow already use this pattern and for good reason.

While watching [Eric Elliot's video](http://vimeo.com/89258863) excellent presentation on the subject of modules,
one thing didn't jive with me-- _"Don't Export Constructors"_ (10:06 in the video).  The suggestion to only export
factories because it forces code consumers to use the `new` operator and prevents factory abstractions
later seemed a little heavy-handed.  I tried to do another refactor of `svg-clock` where the `SVG`
module exports a factory.  But because I used an object pool on the constructor, my code broke.

To preserve the ability for module consumers to extend constructors, I want to modify that advice to,
"Export instances or constructors with factories".  I just added a feature to
[`perfget`](https://www.npmjs.org/package/perfget) which I plan to use often.  I created an empty constructor
function and exported an instance of the constructor.  This allows module consumers to inherit the `.get()`
method in their own constructors using `util.inherits( MyConstructor, perfget.constructor )`.

For modules needing more initialization, you might consider exporting a constructor with a _static_ `.factory()`
or `.create()` method.  The module consumer can avoid the `new` operator with
`var factory = require( 'Module' ).factory` and yet retains the power of `util.inherits( fn, Module )`.

__Update__: I figured I should support other patterns for inheritance, so I updated
[perfget](https://www.npmjs.org/package/perfget) to 0.2.1 to support Object.create.  This time, I used a factory
to generate a clean instance from the constructor I added _under the hood_ in 0.2.0.  This means that my old API did
not change, I just have another (better?) way to include perfget's the functionality.

While trying to publish, I kept getting `EPUBLISHCONFLICT` errors.  After updating npm, `npm install npm -g`
I was able to publish again.
