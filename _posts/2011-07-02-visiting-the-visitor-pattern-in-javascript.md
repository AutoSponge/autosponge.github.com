---
published: true
layout: post
category: blog
tags: [javascript]
---

The [visitor pattern](http://en.wikipedia.org/wiki/Visitor_pattern) allows you to separate functionality
from the object it functions on.  We can often make use of this pattern when working with
*components and composites*.

For instance, here's a simple visitor used to console out the various property values nested
inside an object:

{% highlight js %}
var V = V || {};

V.visit = function visit(obj) {
    var o, hasOwn = Object.prototype.hasOwnProperty;
    this.call(obj, obj);
    if (typeof obj === "object") {
        for (o in obj) {
            if (hasOwn.call(obj, o)) {
                visit.call(this, obj[o]);
            }
        }
    }
};

V.log = function (e) {
    console.log(e);
};
//give log a static method
V.log.visit = V.visit;

obj = {
    a: "A",
    b: {
        c: ["one", "two"]
    },
    d: true,
    e: /test/,
    f: new Date()
};
V.log.visit(obj)
{% endhighlight %}

A visitor can also *modify object values*.  In this meta example, I will use a createVisitor method
(which is a visitor) to modify a namespaced group of functions--making each a visitor.

{% highlight js %}
var V = V || {};

V.createVisitor = (function () {
    var hasOwn = Object.prototype.hasOwnProperty,
    visit = function (obj) {
        var o;
        this.call(obj, obj);
        if (typeof obj === "object") {
            for (o in obj) {
                if (hasOwn.call(obj, o)) {
                    visit.call(this, obj[o]);
                }
            }
        }
    },
    visitor = function (fn) {
        if (typeof fn === "function") {
            fn.visit = visit;
        }
        return fn;
    };
    visitor.visit = visit;
    return visitor;
}());

V.visitor = {
    log: function (e) {
        console.log(e);
    },
    caps: function (e) {
        if (typeof e === "string") {
            console.log(e.toUpperCase());
        }
    }
};

V.createVisitor.visit(V.visitor);

obj = {
    a: "A",
    b: {
        c: ["one", "two"]
    },
    d: true,
    e: /test/,
    f: new Date()
};

V.visitor.caps.visit(obj);
V.visitor.log.visit(obj);
{% endhighlight %}

If you run the above code in console, you may notice that while visitor.caps reports the obj
strings in all caps, it *does not modify the strings* in place.

Out visit method can only modify *object values*, not the values themselves.  But it
sounds like something worth doing.

Below, I modified the visit method to take an additional parameter for the property.  Our
visitor functions will have access to _this_ (the parent object), the value (in the same
position as before in case they don't need to modify values), and the property name.  The
property name will allow us to modify the value in place using `this[prop]` notation.

Try it out; run the logger before and after running makeCaps:

{% highlight js %}
var V = V || {};

V.createVisitor = (function () {
    var hasOwn = Object.prototype.hasOwnProperty,
    visit = function (obj, prop) {
        var c, child = hasOwn.call(obj, prop) ? obj[prop] : obj;
        this.call(obj, child, prop);
        if (typeof child === "object") {
            for (c in child) {
                if (hasOwn.call(child, c)) {
                    visit.call(this, child, c);
                }
            }
        }
    },
    visitor = function (fn) {
        if (typeof fn === "function") {
            fn.visit = visit;
        }
        return fn;
    };
    visitor.visit = visit;
    return visitor;
}());

V.visitor = {
    log: function (e) {
        console.log(e);
    },
    makeCaps: function (e, i) {
        if (typeof e === "string") {
            this[i] = e.toUpperCase();
        }
    },
    caps: function (e) {
        if (typeof e === "string") {
            console.log(e.toUpperCase());
        }
    }
};

V.createVisitor.visit(V.visitor);

obj = {
    a: "A",
    b: {
        c: ["one", "two"]
    },
    d: true,
    e: /test/,
    f: new Date()
};

//V.visitor.caps.visit(obj);
//V.visitor.log.visit(obj);
//V.visitor.makeCaps.visit(obj);
{% endhighlight %}
