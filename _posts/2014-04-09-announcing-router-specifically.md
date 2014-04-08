---
published: true
layout: post
category: blog
tags: [javascript,performance,router]
---

### Introducing [accu-router](https://www.npmjs.org/package/accu-router)

I recently built a webapp from the "ground up" using nothing but [Bootstrap](http://getbootstrap.com/).  I used
a few utilities from previous projects, like [perfget](https://www.npmjs.org/package/perfget), new standards
like [es6-promises](https://www.npmjs.org/package/es6-promise), and a few gists I needed to prove out.

After shipping the demo app, I looked for other examples of routers.  Working without a framework means you
need your own router but I wasn't totally happy with my implementation.  I took inspiration from
[routes](https://www.npmjs.org/package/routes) which returns an object instead of invoking the handler.
I feel like that's a great touch and works with my new, "less opinionated" philosophy.

However, when I looked at the implementation, _routes_, like many other routers, naively iterates over the universe of
routes to find a match.  I decided that my router would use specificity to match routes and store them as
Object keys for faster access.  I came up with an algorithm that turns a route like `'a/b/c'` into a series
of predictable paths, each gradually less specific, until a match is found or the final route `'*'` fails to match.
This means you get a fast match based purely on distance to a viable match with no penalties for late registration
or having thousands of registered routes.

Just like _routes_, params use the `/:<name>` pattern and wildcards use `/*`.  Internally, I convert those to
`_` or `*` where the underscore is more specific than the wildcard.

That series looks like this:

-  `a/b/c`
-  -> `a/b/_` ( `_` represents a param like `/:id` )
-  -> `a/b/*`
-  -> `a/_/_`
-  -> `a/_/*`
-  -> `a/*`
-  -> `_/_/_`
-  -> `_/_/*`
-  -> `_/*`
-  -> `*`

Take it for a spin.  Feel free to comment here or in the github issues.

