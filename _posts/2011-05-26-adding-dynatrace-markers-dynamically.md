---
published: true
layout: post
category: blog
tags: [javascript]
---

While using dynaTrace with our app, we needed a way to
[inject markers](http://blog.dynatrace.com/2010/04/13/advanced-timing-and-argument-capturing-with-dynatrace-ajax-edtion/)
dynamically.  We also wanted the profiling code to have a minimal footprint, not run in production,
give us the most accurate measurements, and not interact with production code.

This example might help someone else with similar requirements:

{% highlight js %}
/**
 * Creates and manages markers for dynaTrace
 * @class MARKER
 * @param name {String}
 * @param fn {Function} initializing function
 * this should add an event handler eventually calling
 * .wait() or .stop()
 * @param time {Number} the polling interval while
 * trying to add the fn handler (default is 500 ms)
 * @constructor
 * @example MARKER("myMark");
 */
var MARKER = function (name, fn, time) {
    if (!(this instanceof MARKER)) {
        return new MARKER(name, fn, time);
    }
    MARKER.instance = this;
    this.name = name;
    this.start(fn, time);
};
/**
 * Creates a marker or logs to console
 * @method addMessage
 * @param msg {String}
 * @return {Void}
 */
MARKER.prototype.addMessage = function (msg) {
    return (typeof _dt_addMark !== "undefined") ? _dt_addMark(msg) : console.log(msg, this.time || "");
};
/**
 * Starts timing a marker
 * @method start
 * @param fn {Function}
 * @param time {Number}
 */
MARKER.prototype.start = function (fn, time) {
    this.addMessage(this.name + " start");
    this.started = +new Date();
    this.running = fn ? setInterval(fn, time || 500) : true;
};
/**
 * Clears a running interval
 * @method wait
 */
MARKER.prototype.wait = function () {
    if (typeof this.running === "number") {
        clearInterval(this.running);
    }
};
/**
 * Clears a running interval and stops timing
 * @method stop
 */
MARKER.prototype.stop = function () {
    this.wait();
    this.running = false;
    this.stopped = +new Date();
    this.time = (this.stopped - this.started) / 1000;
    this.addMessage(this.name + " stop");
};
/*
 example of usage:
 if dynaTrace is running, invocation adds a jQuery event handler (polling every 500ms until jQuery is available)
 on document.ready to listen for certain user interactions.  New markers are created based on hash.
 */
if (typeof _dt_addMark !== "undefined") {
    new MARKER("inital load", function () {
        if (typeof jQuery !== "undefined") {
            jQuery(function () {
                jQuery(this).delegate("input, select, button", "focus, click", function (e) {
                    var M = MARKER, m = M.instance;
                    if (m && m.running) {
                        m.stop();
                    }
                    if (this.id && this.id === "<<an id that users click to move the app along>>" && e.type === "click") {
                        new M(window.location.hash.replace(/^#\//, ""));
                    }
                });
            });
            MARKER.instance.wait();
            MARKER.initialized = true;
        }
    });
}
{% endhighlight %}



