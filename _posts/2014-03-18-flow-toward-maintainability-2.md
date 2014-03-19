---
published: true
layout: post
category: blog
tags: [javascript, quality, process]
---

Last October, I published a [repo](https://github.com/AutoSponge/svg-clock) and
 [blog article](http://autosponge.github.io/blog/2013/10/08/flow-toward-maintainability/) about
 [Flow-based Programming (FBP)](http://en.wikipedia.org/wiki/Flow-based_programming).
After seeing [Gulp](http://gulpjs.com/) and [Browserify](http://browserify.org/) in action,
I wanted to update the repo and see if I could increase my maintainability score of *135.58*.

To my delight, all of my Grunt plugins were ported to Gulp, including
[docco](https://www.npmjs.org/package/gulp-docco)!  After some copy-pasting of
configs, I rewired my `package.json` to point to gulpy versions of my formerly grunty
packages.  Then I set about refactoring the app without changing much code or
functionality.

The main code bits I changed, as much to demonstrate Browserify as make the code better, was
to use of Node's native `EventEmitter` over the one I wrote.  This eliminated an entire module from the
application.  I used `util.inherits` to give the `.on()` and `.emit()` methods
to a `Clock` constructor.  Now, other processes get invoked with `app` as their only parameter.

When I was done, I had 9 files.  Testing these modules seems much simpler now.  I may decide
to add tests to further demonstrate test frameworks and the use of mocks for this style of
application construction.

Ultimately, I was pleased by the results.  This time the project scored an average *144.79* from
[js-complexity](http://jscomplexity.org/).  Gaining nearly 10 points over the previous,
already high, mark of 135 was not expected.  The future of browser development looks very
maintainable.