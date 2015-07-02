---
published: true
layout: post
category: blog
tags: [javascript, es6, es7]
---

I was happy to meetup with other JavaScripters in Iceland. I was also happy to discuss my latest set of tools which includes 
[Babel](http://babeljs.io/) (via [JSPM](http://jspm.io/)) when a discussion topic came up that I thought I had a handle on.

Iterating a [`NodeList`](https://developer.mozilla.org/en/docs/Web/API/NodeList)--we all need to do it from time to time.  Since
I started using tools like [virtual-dom](https://github.com/Matt-Esch/virtual-dom) and [paperclip](http://paperclipjs.com)
I just haven't iterated a `NodeList` that wasn't another collection type (e.g., jquery object). This was evident as I opened
the Babel REPL and starting throwing things at the problem: [for-of](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/for...of),
[spread operator](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Spread_operator), and other ideas
failed (beer on an empty stomach may or may not have played a role). I still don't remember what I was doing wrong.

I slept on it and woke up with `Array.from` which totally works. I read up on some other proposals Babel already 
supports: `Object.entries`, `Object.values`, and a native iterator for `NodeList` (which is what makes the spread operator
work). Now, I have a solution with no problem...

{% highlight js %}
var elms = document.querySelectorAll('a');

//es5 way
Array.prototype.slice.call(elms, 0).map(function (elm) {console.log(elm.href)});

//es6 way(s)
Array.from(elms).map(({href}) => console.log(href));
for (const {href} of elms[Symbol.iterator]()) console.log(href);
for (const {href} of elms) console.log(href);
[...elms].map(({href}) => console.log(href));
[...elms[Symbol.iterator]()].map(({href}) => console.log(href));

//es7 way(s)
Object.values(elms).map(({href}) => console.log(href));
Object.entries(elms).map(([key, {href}]) => console.log(href));

//(mis?)using the function bind operator
const log = function () {console.log(this)};
Object.values(elms).map(({href}) => href::log());
{% endhighlight %}
