---
published: true
layout: post
category: blog
tags: [javascript, es6, es7, babel]
---

I don't say it often enough, although I did gush on [@sebmck](https://twitter.com/sebmck) at 
[jsconf2015](http://2015.jsconf.us/)), I :heart: [Babel](http://babeljs.io/).

If, like me, you want to...

![Babel all the things](http://i.imgur.com/l5co3W3.jpg)

...then this post is for you. Let's Babelify our npm scripts.

If you haven't used npm scripts for your build/development process yet, this just stands on the 
shoulders of giants like [@linclark](https://twitter.com/linclark)'s articles at the [npm blog](http://blog.npmjs.org/),
[@keithamus](https://twitter.com/keithamus)'s amazing [article](http://blog.keithcirkel.co.uk/how-to-use-npm-as-a-build-tool/),
and numerous examples from the repos of masters like [tj](https://github.com/tj). I'm still a noob at
the commandline stuff, so if you have suggestions for improvement, please let me know!

1. Get a `package.json` if you don't already have one: `npm init`.
1. Install Babel to your project: `npm -i --save-dev babel`.
1. Create scripts in a `/scripts` folder.
1. Wire it up to npm by editing your `package.json` file.

{% highlight json %}
  "scripts": {
    "myscript": "./scripts/myscript.js",
    ....
  }
{% endhighlight %}

At the top of your script file, include the shebang `#! /usr/bin/env babel-node`. Now you can write your
script in ESnext syntax. If you aren't passing flags (`--` style arguments), everything will just work from here.

Here's an example that reads an `.env` file, spins up redis, then spins up a server.

{% gist aa9c02c25b56abe6dfd1 script_start.js %}

Here's the `development.env` file:

{% highlight bash %}
NODE_ENV=development
PORT=4000
REDIS_CONNECTION_STRING=redis://localhost:6379
JWT_TOKEN=super-secret
DEBUG=*
{% endhighlight %}

I can invoke this with `npm start` or `npm start <environment>`. The argument is passed through.

If you need more control over the execution, you can try using [commanderjs](https://www.npmjs.com/package/commander).
 
I created a simple script to create a [jwt token](https://github.com/auth0/node-jsonwebtoken) for my server based 
on the `.env` file specified as well as a user. 

{% gist aa9c02c25b56abe6dfd1 script_jwt.js %}

After adding this to your `package.json` scripts, you have to [pass flags](https://docs.npmjs.com/cli/run-script) using `--`. 
However, node will intercept its flags first, so don't repeat any flags listed in `node --help` (including `--help`).

For example, `npm run jwt -- --help` will output node's help. But `npm run jwt -- -h` will output commanderjs's
help for _our_ script. That's the one flag you can't control because it's assumed by commanderjs. Here, I've 
used `-E` to avoid conflict with node's `-e --eval`. Creating a token is as simple as `npm run jwt -- -u <username>`.

Lastly, I created a script to execute my tests and coverage. While it's a bash one-liner, it's pretty long and 
I didn't want to maintain it in that form. It was hard to break up because of how I
loaded the `.env` file (it was a bash script that `export`ed each variable). It also didn't manage redis.

{% highlight json %}
"scripts": {
  "test-cov": "(. ./test.env && babel-node node_modules/.bin/isparta cover --report text --report html node_modules/.bin/_mocha --compilers js:babel/register)",
  "test": "(. ./test.env && _mocha --compilers js:babel/register --reporter spec)",
}
{% endhighlight %}

Was replaced with:

{% gist aa9c02c25b56abe6dfd1 script_test.js %}

The only take-away here is that because of the execution context, you can't just use the `npm` reference of a `.bin`
file, you have to use the full path. Because this script can run on the CI server, and it already has redis,
I made the redis commands conditional.