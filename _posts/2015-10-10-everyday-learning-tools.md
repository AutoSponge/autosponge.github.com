---
published: true
layout: post
category: blog
tags: [javascript, learning, esnext]
---

I find simple, practical code examples very important for learning new things. Tools like [Rx Marbles](http://rxmarbles.com/),
[Promisees](http://bevacqua.github.io/promisees/), and [MetaJS](http://int3.github.io/metajs/)
blow me away. But I can't move past these examples until I can integrate the concept. Connect it
to something else I know and test a hypothesis about how the two components will interact. Often, 
that means cloning some repos, installing some dependencies, or building a project. That can get messy.

Script sharing tools like [Codepen](https://blog.codepen.io/documentation/editor/adding-external-resources/)
let me include external resources. But I have some problems with this approach:

1. I'm limited by the tool's runtime (although [support for Babel](http://blog.codepen.io/2015/05/19/babel-now-on-codepen-write-es6-javascript-and-react-jsx/) is growing :+1:).
1. I often need a CDN version of the library I want to include :-1:.
1. _If_ the CDN version exists, it probably needs to be [browserified](https://www.npmjs.com/package/browserify).

I don't do this in my own code. I just install the dependency and import it.

That leads me to two exciting projects, [replem](https://www.npmjs.com/package/replem)
and [tonic](https://tonicdev.com/) that I have started to use daily. These tools let me combine
different things directly from npm. I like the tonic approach when I'm surfing the web or someone's api
docs. I started working on a script that might become a bookmarklet or a chrome plugin. Let me know if 
you'd like to help or have some implementation ideas to make this easy for beginners.

{% gist 4d7323a156d9ad8b8341 tonicify-lodash.js %}

(This example was made to include `lodash` by default but I'd like to make this dynamic). To try it out,
paste the snippet into the console of a site with a code block you want to play with. Select the codeblock
(or part of it) and "copy" (Ctrl+C). It should move the block to a new tab wrapped with tonic. You may have to allow 
popups :grimacing:.

Also, please let me know if you find issues with particular syntax highlighters. 
I've tested it with `highlight` on a jekyll site, embedded gists, and mdn's code samples but I know there are 
many others.