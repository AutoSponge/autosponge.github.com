---
published: true
layout: post
category: blog
tags: [tools,javascript,json,visualization]
---

I work with APIs a lot.  I appreciate the effort some groups put forth to document their API and their
payload formats, especially when tools like [Swagger](https://github.com/wordnik/swagger-js)
can help demonstrate the API in the browser.

While looking at one of the [d3.js](http://bl.ocks.org/robschmuecker/7880033) examples, I wondered if
I could improve the visualization of a typical [JSON schema](http://json-schema.org/).  I *gisted* my first pass
and if anyone else finds this useful or wants to extend it, we can make it a repo.

The node script below should create a file to replace `flare.json` from the
[d3 example]((http://bl.ocks.org/robschmuecker/7880033)).  You'll need to
install traverse first: `npm install traverse` (which I really like, btw).

![sample image](https://gist.github.com/robschmuecker/7880033/raw/eeabbfca4725a69474a828faa79cac586f5a3110/thumbnail.png)

Example:

`node traverse-schema.js my_schema_file.json`

{% gist 8668368 traverse-schema.js %}