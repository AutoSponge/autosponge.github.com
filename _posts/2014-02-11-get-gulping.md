---
published: true
layout: post
category: blog
tags: [javascript,functional,perfget,performance]
---

After reading ["And just like that Grunt and RequireJS are out, it’s all about Gulp and Browserify now"](http://www.100percentjs.com/just-like-grunt-gulp-browserify-now/),
I realized the power of using **one module pattern** for both server-side and client-side code.  That makes
me want to start releasing a bunch of my "go-to" snippets as NPM modules.  I seem to use
some form of this code on every other project, why shouldn't I just `require( "mystuff" )`?

I have included a variant of [perfget](https://github.com/AutoSponge/perfget) on at least a dozen
projects now.  The original code, a collaboration between [@CrossEye](https://github.com/CrossEye) and I
when we both worked at Travlers, really comes in handy for traversing large JavaScript objects safely
and quickly.  Because I have used the code for years and trust it, I often drop it into applications
without a second thought.

Well, now you can too because I released it as an [NPM module](https://www.npmjs.org/package/perfget).
The code also works in the browser because there are no dependencies to pull in.  But if,
like [@mgenev](https://github.com/mgenev), you want to use `require( x )` in the browser, you will
need to *Browserify* your files in a build step.

Speaking of build steps--I also want to mention [**Gulp**](http://gulpjs.com/).  Because my project was small but I wanted it
tight, I decided to take Gulp for a spin.  My original tests used [nodeunit](https://github.com/caolan/nodeunit)
because I was only testing property access.  I don't need much more than `assert` and a runner/reporter.
I did not find a Gulp analog of [grunt-contrib-nodeunit](https://github.com/gruntjs/grunt-contrib-nodeunit)
and I was loathe to making my own plugin on *day one* of using Gulp.  So, I switched to Mocha via
[gulp-mocha](https://www.npmjs.org/package/gulp-mocha).

While I appreciated the syntax and lighter *feel* of Gulp, I can't say I'm a convert just yet.  I need
better examples of **simple projects** before I can go all in.