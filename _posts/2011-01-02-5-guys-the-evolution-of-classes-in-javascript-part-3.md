---
published: true
layout: post
category: blog
tags: [javascript]
---

Now that Bob can protect his personal belongings, we have a problem with adding new capabilities.
If we add capabilities through a prototype on Guy, the method will not have access to "stuff."

Introducing the magic word, please.  When you ask Bob nicely, he'll do something with his stuff.
To make sure you don't trick Bob into giving away his stuff, please will return undefined.
This may allow us to move some of Guy's capabilities out of the class and into the prototype for
better code organization and reuse.  Because please does not return, we can only move "keep."

{% highlight js %}
var Guy = function (name, basics) {
    var person = {
        stuff: basics || {},
        index: -1
    },
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
    this.have = function (what) {
        if (typeof find(what) !== "undefined") {
            return true;
        } else {
            return false;
        }
    };
    this.please = function (action) {
        var request = Array.prototype.slice.apply(arguments);
        request.shift();
        request.unshift(person);
        if (typeof action === "function") {
            action.apply(this, request);
        } else {
            if (this[action]) {
                this[action].apply(this, request);
            }
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

Guy.prototype.keep = function (what, where) {
    this.please(function (person, what, where) {
        var found, box, label;
        if (where) {
            found = person.find(where, true);
        } else {
            found = [person.stuff, person.index += 1];
        }
        box = found[0],
        label = found[1];
        if (typeof box[label] === "undefined") {
            box[label] = what;
        }
    }, what, where);
};

var bob = new Guy("Bob");
bob.keep({socks: 2});
bob.show("0");
{% endhighlight %}

This works great and allows us to extend Guy's functionality without modifying our class.
The problem comes when we want access to the "find" method from our prototype methods.

![guy](/assets/img/guy3.png)

We also still have an issue with how Netbeans sees the person object:

![netbeans](/assets/img/2011-01-02_111834.png)

Next we'll finally figure out that Person should be a class too.


