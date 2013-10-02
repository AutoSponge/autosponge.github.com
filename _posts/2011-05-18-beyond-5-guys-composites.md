---
published: true
layout: post
category: blog
tags: [javascript]
---

In programming terms, Guy is a simple [composite](http://en.wikipedia.org/wiki/Composite_pattern) of Person.
Think of Guy as the wrapper around Person--Guys protect their inner Person (via encapsulation).
Since this is a pattern, and therefore generic, we need to make the process of building Guy more generic.

{% highlight js %}
//This helper function helps us create the composite Person   	
var compose = function (component) {
    return function (action) {
        var request = Array.prototype.slice.apply(arguments);
        request.shift();
        if (typeof action === "function") {
            //we now *return* so we can make returnable
            //methods outside of the class.
            //It's a little more dangerous to our encapsulation
            return action.apply(component, request);
        } else if (typeof component[action] === "function") {
            component[action].apply(component, request);
        }
    };
};

//generic "has" method because we're using *this* instead of the
//component var, more on this later
var has = function (what) {
    var found = this.find(what), box, label;
    if (found) {
        box = found[0],
        label = found[1];
        return !!box[label];
    }
    return false;
};

//A "real" person, everyone has one of these,
//but you never see someone else's "real"/"inner" person
var Person = function (name, basics) {
    this.name = name;
    this.stuff = basics || {};
    this.index = -1;
};

//People do neat things, like find their stuff
Person.prototype.find = function (where, keep) {
    if (!where) {
        return;
    }
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

//People also figure out where to put things they're given
Person.prototype.keep = function (what, where) {
    var found = this.find(where, true) || [this.stuff, this.index += 1],
        box = found[0],
        label = found[1];
    //prevent someone from overwriting an object
    //to effectively set a "private" value
    if (box[label] == null) {
        box[label] = what;
    }
};

//A generic Guy, all guys protect their inner Person
var Guy = function (name, basics) {
    //if you ask nicely, a Guy might grant special
    //access to the inner Person
    this.please = compose(new Person(name, basics));
};

//A Guy will show you some of his stuff, but he won't
//give you anything you can break (e.g., objects)
Guy.prototype.show = function (what) {
    return this.please(function (what) {
    	var found, box, label;
        if (typeof what === "undefined") {
            //while inside a please function, this refers to 
            //our component--Person, not Guy
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
    }, what);
};

//To know what a Guy has, you need to ask nicely
Guy.prototype.has = function (what) {
    //because we made the generic have method,
    //we can use the reference here instead of the 
    //function literal, just easier to read/reuse
    return this.please(has, what);
};

//Guys keep anything, if you don't tell him where
//to keep it, he'll put it in a numbered box,
Guy.prototype.keep = function (what, where) {
    //Guy can just delegate this to his Person component
    //who already knows how to do this (and doesn't return)
    this.please("keep", what, where);
};

//meet Bob
var bob = new Guy("Bob");
//Give Bob some socks
bob.keep({socks: 2});
//Bob just tossed the socks into the first box he found
bob.show("0"); //"{"socks":2}"
//check Bob's clean shirts
bob.has("top drawer.clean shirts")); //false
//Tell Bob where to put his clean shirts
bob.keep({"clean shirts": 1}, "top drawer");
//Bob will show you the top drawer, but you can't change it
bob.show("top drawer"); //"{"clean shirts":1}"
//Bob tells you about his clean shirts
bob.show("top drawer.clean shirts"); //1
bob.has("top drawer.clean shirts"); //true
{% endhighlight %}



