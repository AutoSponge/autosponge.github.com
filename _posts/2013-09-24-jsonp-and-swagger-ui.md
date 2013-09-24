---
published: true
layout: post
category: blog
tags: [javascript]
---

First, I want to tell everyone how much I like [Swagger](https://github.com/wordnik/swagger-core) and
[Swagger-UI](https://github.com/wordnik/swagger-ui).  That said, Swagger-UI does not support making
[JSONP](http://en.wikipedia.org/wiki/JSONP) calls despite the fact that many APIs support them.

The main culprit seems to be Internet Explorer (see [Using CORS](http://www.html5rocks.com/en/tutorials/cors/) ), but
suffice to say, people need this and it won't get supported in Swagger
[#313](https://github.com/wordnik/swagger-ui/issues/313).  So, I'm posting this in case anyone else needs to make
this change, or better still, can make this change in coffee-script to the source files.

{% highlight js %}
if (opts.responseContentType === "application/javascript") {
    return $.ajax({
        url: decodeURI(this.model.urlify(map)),
        contentType : opts.responseContentType,
        dataType: "jsonp",
        success: (function (showCompleteStatus, parent) {
            return function (data, statusText, request) {
                showCompleteStatus({
                    content: {
                        data: JSON.stringify(data)
                    },
                    status: request.status,
                    getHeaders: function () {return {"Content-Type": "application/javascript"};},
                    request: {
                        url: this.url
                    }
                }, parent);
            };
        }(this.showCompleteStatus, opts.parent)),
        error: (function (showErrorStatus) {
            return function (data) {
                showErrorStatus(data, parent);
            };
        }(this.showErrorStatus, parent))
    });
}
{% endhighlight %}

The code needs to be inserted before the return statement of `OperationView.prototype.submitOperation`.