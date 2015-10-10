---
published: true
layout: post
category: blog
tags: [javascript, es6, es7, babel, aws, lambda]
---

AWS Lambda service offers some great new options to my favorite style of development, single-page apps.
However, the node version used in development should not exceed the 
[aws supported version](http://docs.aws.amazon.com/lambda/latest/dg/current-supported-versions.html). 
Currently, that version is only v0.10.36, hardly good enough to run the more modern ES syntax.

Babel to the rescue. By precompiling the code, you can still write your code the way you want then
compile it, zip it, and upload to Amazon. There are still a few rules to follow, specific to the
Lambda service.

1. [AWS Lambda limits](http://docs.aws.amazon.com/lambda/latest/dg/limits.html).
1. All actions within a lambda must resolve with a `context` method. 
(See [article](https://aws.amazon.com/blogs/compute/getting-nodejs-and-lambda-to-play-nicely/))
1. You have to export your named handler.

Because of the way context is injected, testing the lambda is tricky. I decided to use a `stockContext`
as a default parameter.



{% highlight json %}
  "scripts": {
    "myscript": "./scripts/myscript.js",
    ....
  }
{% endhighlight %}

{% gist aa9c02c25b56abe6dfd1 script_start.js %}

