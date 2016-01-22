---
published: true
layout: post
category: blog
tags: [javascript, publishing, npm, semver]
---

For the prolific authors of the node world, new tools have emerged to assist with the daily toil of
managing publish-release cycles. One tool I'm keeping an eye on is [Greenkeeper.io](http://greenkeeper.io/).
It basically does the combination of `npm outdated` and lots of manual regression testing to keep your
dependencies up-to-date. While that's a terrific idea, most of the things I publish have no
dependencies.

However, sometimes I make mistakes when I publish or release defects. I always have to go through the 
mental checklist of steps to properly update and publish including: update change log, bump version
number, update docs, check test coverage, etc. A continuous integration server (like TravisCI) works
great for running the tests but I always hated doing the separate push to npm.

Enter [semantic-release](https://www.npmjs.com/package/semantic-release). I tried it out for the first time
with [imemoize](https://www.npmjs.com/package/imemoize) and today I realized I made one of the critical
blunders in a memoizer: forgetting to cache falsey values. So the conditional statement `!cache[key]`
fails to actually memoize. It should always be `!(key in cache)`. 

Anyway, after writing the test, passing the test, and pushing (following the preferred process), npm
was automagically updated and the version was bumped. So easy!