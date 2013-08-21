---
layout: post
category: blog
tags: 
  - javascript
  - functional
  - performance
  - "javascript,functional,performance"
published: true
test: "this is a test, of the new pattern, stuff"
---

I recently caught up on some technical reading. I’m particularly enjoying posts related to functional programming.
One downside to functional programming is the performance hit we take jumping scope and making extra function calls
for things we could just do in a single block.

One of the more interesting ideas in functional programming is
[composition][1].
The idea that you can combine two steps,
like `y = g(x);` and `z = f(y);` into a single function `z = f(g(x));`. The typical compose function example I see takes two
arguments, like the one in chapter 6 of [Eloquent JavaScript][2].
If you want to compose a third function, you have to call
compose again, `z = compose(e, compose(f, g));`.

This type of nesting is precisely the scope chaining we don’t need (provided we know all of the functions to compose).
For instance, a compose function designed to take three arguments, `z = compose(e, f, g)`, will outperform the two calls
to the compose function with arity of two. So, based on my work with `get`, I wrote a memoizing compose that takes any
number of functions.

{% gist 4756086 %}

[1]: http://en.wikipedia.org/wiki/Function_composition_(computer_science)
[2]: http://eloquentjavascript.net/chapter6.html