---
published: true
layout: post
category: blog
tags: [javascript, functional]
---

I started putting together many of my "go-to" code snippets this week.  It's something I have tried to
start multiple times but I was never really satisfied with the process of compiling the snippets into
their more ubiquitous form, the 'common.js' file.

In my [utils](https://github.com/AutoSponge/utils) repo, I'm trying out a pattern using
[browserify](http://browserify.org/) transforms that might work.  I can keep the individual snippets
separate, letting node's `require` figure out dependencies, and still have a simple build process
that gives me the `utils.js` file I need--per project.

It works like this:

1. Write a bunch of simple methods or one-liners that you use all the time.
1. Write a transform like my [`build.js`](https://github.com/AutoSponge/utils/blob/master/build.js) file.
1. <s>Then call browserify from the command line, passing the names of files to include to the transform.</s>
1. Then call `node build.js` from the command line, optionally passing the names of files to include

`node build.js ncurry compose output=utils.js`

The source file, `./src/utils.js`, is just an empty file.  The transform reads the list of method/file names
from the <s>_subargs_,</s> parameters, and creates a list of `global.<name> = require(<name>);` strings, and pipes it to
the destination file.  In this case, the transform will generate a file with `ncurry`, `compose`
and any of their dependencies but only the two named methods will get exposed as globals.
Dependencies can even include other npm modules!

<s>I put another simple default in `build.js` to include all files in the `/src` folder if no method names
were passed.  And to make that easier to remember, I included it as another npm task in `package.json`:</s>

<s>`npm run build-all`</s>

<s>I'm trying to learn more about how best to use the transforms, but this step was very rewarding.</s>
Comments, suggestions, and pull requests welcome.

## Update:

The original transform left a lot of boilerplate in the bundle file because I was using the browserify api
incorrectly.  This new version works much better and reduces the wrapper code browserify adds.  I also think
the command line invocation of build is more convenient.  I'm much happier with this version and I will
probably work on making the build file more generic so it can be broken out as its own module.