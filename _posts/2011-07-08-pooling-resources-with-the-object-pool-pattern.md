---
published: true
layout: post
category: blog
tags: [javascript]
---

I find the [Object Pool](http://en.wikipedia.org/wiki/Object_pool_pattern) pattern extremely useful.
For starters, I can easily count how many instances of a class instantiated or replace a specific instance
without worrying about which objects might still hold a reference to the old version.

Here's a simple constructor example with a built-in *registration* component.  New instances register
to the class's *static* pool attribute.  This pool, keyed by the object's _id_, will ensure we only
have one copy with that id.  If we new up Class with an id that already exists, we effectively
replace the current instance--there's no need to hold a reference (I do it in the example below to
demonstrate the replacement).

Some pool management methods may be *statics*, like _getInstance_.  Others may be on the *instances*
using prototype inheritance, like the _destroy_ method below.

{% highlight js %}
var P = P || {};

P.Class = (function () {
    var counter = -1; //private variable in case an id is not supplied
    return function Class(options) {
        var prop;
        for (prop in options) { //simple mixin
            if (options.hasOwnProperty(prop)) {
                this[prop] = options[prop];
            }
        }
        if (!this.constructor.pool) {
            this.constructor.pool = {};
        }
        if (!this.id) {
            this.id = counter += 1;
        }
        this.constructor.pool[this.id] = this;
    };
}());

P.Class.getInstance = function (id) {
    return this.pool && this.pool[id];
};

P.Class.prototype.destroy = function () {
    delete this.constructor.pool[this.id];
};

var p1 = new P.Class();
console.log("an instance of Class:", P.Class.getInstance(0));
P.Class.getInstance(0).change = true;
console.log("alter the object in pool:", P.Class.getInstance(0).change);
var p2 = new P.Class({id: "0"});
console.log("replace an instance in pool:", P.Class.getInstance(0) === p2);
P.Class.getInstance(0).destroy();
console.log("destroy instance of Class:", typeof P.Class.getInstance(0) === "undefined");
{% endhighlight %}

