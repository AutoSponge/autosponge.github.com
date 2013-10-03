---
published: true
layout: post
category: blog
tags: [javascript]
---

Inside every Guy, there's a little person.  Person has stuff but could also have hopes and dreams.
So, it makes sense to call person a class--Person.

{% highlight js %}
var Person = function (name, basics) {
    this.name = name;
    this.stuff = basics || {};
    this.index = -1;
    this.find = function (where, keep) {
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
};

Guy.prototype.keep = function (what, where) {
    this.please(function (person, what, where) {
        var found, box, label;
        if (where) {
            found = person.find(where, true);
        } else {
            found = [person.stuff, ++person.index];
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
bob.keep({cash: 50}, "wallet");
bob.show("wallet");
{% endhighlight %}

This restores our ability to tell Bob where to keep his stuff.  And Netbeans finally understands that
Bob is the culmination of Person and Guy:

![netbeans](/assets/img/2011-01-02_111851.png)

However, the Person class is a mess.  I think it could use the same sort of overhaul that Guy got.
When separating Person's properties and methods, we don't have to worry about encapsulation since
Person will only exist inside a Guy.  Stay tuned for the final installment!



