---
published: true
layout: post
category: blog
tags: [javascript]
---

Bob can't protect his stuff.  While we could put some of bob's stuff in private variables, we also
want Netbeans to know Bob is an instance of Guy, so we'll change the pattern a bit to make a proper constructor.

{% highlight js %}
var Guy = function (name, basics) {
    var person = {
        stuff: basics || {}
    },
    count = -1,
    find = function (where, keep) {
        var label = where.split("."),
        box = person.stuff;
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
    };
    this.name = name;
    this.keep = function (what, where) {
        var found, box, label;
        if (where) {
            found = find(where, true);
        } else {
            found = [person.stuff, ++count];
        }
        box = found[0],
        label = found[1];
        if (typeof box[label] === "undefined") {
            box[label] = what;
        }
    };
    this.have = function (what) {
        if (typeof find(what) !== "undefined") {
            return true;
        } else {
            return false;
        }
    };
    this.show = function (what) {
        var found, box, label;
        if (typeof what === "undefined") {
            found = [person, "stuff"];
        } else {
            found = find(what);
        }
        box = found[0];
        label = found[1];
        if (typeof box[label] === "object") {
            return JSON.stringify(box[label]);
        } else {
            return box[label];
        }
    };
};

var bob = new Guy();
bob.keep({socks: 2});
bob.show("0");
{% endhighlight %}

Now, Netbeans sees Bob as a Guy.

![netbeans](/assets/img/2011-01-02_111808.png)

Since Bob may have personal properties other than stuff, we attached stuff to the person object--after all,
everybody has stuff.  The problem now is this person object.  Netbeans does not seem to understand the
relationship of person to Guy or Bob.

On the upside, Bob learned to protect his stuff.  All Guys now can keep something and will not share it,
even with please.

![guy](/assets/img/2011/01/guy2.png)