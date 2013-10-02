---
published: true
layout: post
category: blog
tags: [javascript]
---

Here's our highly evolved Guy:

{% highlight js %}
var Person = function (name, basics) {
    this.name = name;
    this.stuff = basics || {};
    this.index = -1;
};

Person.prototype.find = function (where, keep) {
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
};

Person.prototype.keep = function (what, where) {
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
};

var Guy = function (name, basics) {
    var person = new Person(name, basics);
    this.have = function (what) {
        if (typeof person.find(what) !== "undefined") {
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
            found = person.find(what);
        }
        box = found[0];
        label = found[1];
        if (typeof box[label] === "object") {
            return JSON.stringify(box[label]);
        } else {
            return box[label];
        }
    };
    this.please = function (action) {
        var request = Array.prototype.slice.apply(arguments);
        request.shift();
        if (typeof action === "function") {
            action.apply(person, request);
        } else {
            if (person[action]) {
                person[action].apply(person, request);
            }
        }
    };
};

Guy.prototype.keep = function (what, where) {
    this.please("keep", what, where);
};

var bob = new Guy("Bob");
bob.keep({socks: 2});
bob.show("0");
{% endhighlight %}

During the refactoring of Person, it became obvious that the "keep" method was really
a Person method that Guy implemented.

![methods](/assets/img/2011-01-02_111910.png)

Now, we can safely introduce our Guy to another Guy and trust that their stuff will stay where we put it.



