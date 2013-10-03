---
published: true
layout: post
category: blog
tags: [javascript]
---

I'm demonstrating a few patterns for creating objects in JavaScript.  We often want to make objects to
organize functionality, provide context for our methods, and encapsulate data.  To demonstrate this,
I will show five different versions of the Guy class with the same basic capabilities: store ("keep")
"stuff" and "show" stuff without sharing it.  I'm using the analogy of a guy named bob to help people
not familiar with object-oriented JavaScript keep their bearings.

First, I made a basic closure around an object literal so I can pass in some variables during instantiation: (GUY1)

{% highlight js %}
var Guy = function (name, basics) {
    return {
        name: name,
        stuff: basics || {},
        index: -1,
        find: function (where, keep) {
            var label = where.split("."),
            box = this.stuff;
            while (label.length > 1) {
                if (typeof box[label[0]] === "undefined") {
                    if (keep) {
                        box[label[0]] = {};
                    } else {
                        return;
                    }
                } else {
                    box = box[label.shift()];
                }
            }
            return [box, label[0]];
        },
        keep: function (what, where) {
            var found, box, label;
            if (where) {
                found = this.find(where, true);
            } else {
                found = [this.stuff, this.index += 1];
            }
            box = found[0],
            label = found[1];
            if (typeof box[label] === "undefined") {
                box[label] = what;
            }
        },
        have: function (what) {
            if (typeof this.find(what) !== "undefined") {
                return true;
            } else {
                return false;
            }
        },
        show: function (what) {
            var found, box, label;
            if (typeof what === "undefined") {
                found = [this, "stuff"];
            } else {
                found = this.find(what);
            }
            box = found[0];
            label = found[1];
            if (typeof box[label] === "object") {
                return JSON.stringify(box[label]);
            } else {
                return box[label];
            }
        }
    };
};

var bob = Guy("Bob");
bob.keep({socks: 2});
bob.show("0");
{% endhighlight %}

Running this code in the console will allow you to see that Bob keeps a pair of socks in box zero:

{% highlight js %}
"{"socks":2}"
{% endhighlight %}

Once he keeps the socks, he won't share them (he will not pass the sock object back), only show a
JSON representation of his socks.  In Netbeans, we can see that our constructor function is accurately
identified as a class:

![netbeans](/assets/img/2011-01-02_111714.png)

However, it's not obvious that bob is an instance of Guy because we didn't use the "new" operator.
We have another problem with Bob.  Despite the fact that we told him not to share his socks with others,
his socks are not encapsulated--anyone can take them or change them:

![guy](/assets/img/guy1.png)

While Bob may not care too much if someone else wears his socks, he would care in this scenario:

![wallet](/assets/img/guy1wallet.png)

Someone who knew where Bob keeps his cash was able to set him to zero dollars.  Sad Bob.


